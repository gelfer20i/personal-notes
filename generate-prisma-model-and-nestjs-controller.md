# Creating new Prisma model and its corresponding NestJS controller

## Step 1: Prisma

      - use `prisma` in your application to read and write data in your DB

1. add a new model in schema.prisma

```prisma
model Dog {
  id        String   @id @default(cuid())
  name      String
  age       Int
  createdAt DateTime @default(now()) @db.Timestamptz(6)
  updatedAt DateTime @default(now()) @updatedAt @db.Timestamptz(6)

  @@map("dogs")
}
```

2. if using Docker, `docker-compose up` to open port first

3. generate Prisma client when Prisma schema changes e.g. new model is added.

```s
npx prisma generate
```

4. affect the Postgres db so that it is sync with our Prisma schema

```s
npx prisma db push
```

if you want to also generate migration history, use

```s
npx prisma migrate dev --name init
```

5. check Postgres db in Docker terminal to see if a new relation is created

```s
su postgres -> psql -> \dt -> select ...
```

6. create dog.service.ts in libs/server/services/src/lib

```s
nest g service dog
```

And add DogService to providers and exports in services.module.ts

7. update the code in dog.service.ts

```ts
import { Injectable } from "@nestjs/common";
import { Dog } from "@prisma/client";
import { PrismaService } from "./prisma.service";

@Injectable()
export class DogService {
  constructor(private readonly prisma: PrismaService) {}

  async getAllDogs(): Promise<Dog[]> {
    return await this.prisma.dog.findMany();
  }

  async getDog(id: string): Promise<Dog | null> {
    return await this.prisma.dog.findUnique({
      where: {
        id
      }
    });
  }
}
```

8. export the new service in index.ts so that we can import it as "@applications/services" elsewhere in the project

9. To add data to the db,

```s
insert into dogs (id, name, age) VALUES('01', 'Arthur', 2);
```

---

## Step 2: NestJS

1. generate a new controller in the server-api app

```s
nx g @nrwl/nest:controller animal
```

2. in app.module.ts, add AnimalController to controllers

   - controllers: [AppController, ExampleController, AnimalController]

3. in the new animal folder, create a dto file to avoid passing db response directly to user. This way, we can filter out some sensitive data or form a new response to our liking.

animal-dog.dto.ts

```ts
import { Dog } from "@prisma/client";

export class AnimalDogDto {
  id!: string;
  name!: string;
  age!: number;
  breed!: string | null;

  static fromDatabase(dog: Dog): AnimalDogDto {
    // format data or create computed fields here
    const name = `${dog.name as string} from Earth`;
    const age = dog.age;

    return {
      id: dog.id,
      name,
      age,
      breed: "a new property we just created"
    };
  }
}

export class AnimalDogsDto {
  // this class will return the default properties
  // id, name, age, createdAt, updatedAt
  static fromDatabase(dogs: Dog[]): AnimalDogsDto[] {
    return dogs;
  }
}
```

4. update code in animal.controller.ts

```ts
import { Public } from "@20i/cognito-nestjs";
import { DogService } from "@applications/services";
import { Controller, Get, NotFoundException, Param } from "@nestjs/common";
import { AnimalDogDto } from "./animal-dog.dto";

@Controller("/animal")
export class AnimalController {
  constructor(private readonly dogService: DogService) {}

  @Public()
  @Get("/dog/:id")
  async getDog(@Param("id") id: string): Promise<AnimalDogDto> {
    const dog = await this.dogService.getDog(id);
    if (!dog) {
      throw new NotFoundException("No dog found");
    }

    return AnimalDogDto.fromDatabase(dog);
  }
  
  @Public()
  @Get("/dog")
  async getAllDogs(): Promise<AnimalDogsDto> {
    const dogs = await this.dogService.getAllDogs()
    if (!dogs) {
      throw new NotFoundException("Dogs not found")
    }
    return AnimalDogsDto.fromDatabase(dogs)
  }
}
```

5. with the Docker still running,

```s
      npx nx serve server-api
```

or

```s
      yarn start server-api
```

5. go to http://localhost:4001/animal/dog/id
   or http://localhost:4001/animal/dog to see all items
