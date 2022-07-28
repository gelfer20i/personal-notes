# START-45/App Generator: Scripts

1. Branched off Nikki's develop.

2. Created a new plugin in the existing typescript-workspace project, using

```s
nx g @nrwl/nx-plugin:generator scripts --project=typescript-workspace
```

Before there was only the preset folder in the generators folder, now a new one called scripts was added.

3. Changed the projectType in generator.ts of the new plugin from 'library' to 'application'

and 'getWorkspaceLayout(tree).libsDir' to 'getWorkspaceLayout(tree).appsDir'

4. Added NestJS templates and other templates to the scripts folder

5. Configured the project.json file through the addProjectConfiguration function in generator.ts so that we can build, serve, and lint the scripts app.

6. Added the following deps: @nestjs/common, @nestjs/config, nestjs-console, reflect-metadata, rxjs, rimraf, @nestjs/core, @nrwl/node, @nrwl/linter, @nrwl/jest

When you install the scripts plugin in your workspace, those will also get installed.

7. Added a few more util files in the utils folder.

8. Made paths in .eslint.json, tsconfig.json, tsconfig.app.json, and tsconfig.spec.json in the scripts folder relative instead of hardcoding the paths because someone might use this plugin and set the app name like server/scripts, so we have to go up one more level to be able to extend the base json in root.

path in jest.config.ts needs the same treatment but hasn't been done.

## _Test Plan_

Once this is published to NPM, you may use the plugin in a new workspace or an existing one.

To install the plugin in a workspace, run

```s
yarn add -D @20i/nx-typescript-workspace
```

then, 

```s
nx g @20i/nx-typescript-workspace:scripts server/scripts
```

Once the server is up, run 

```s
nx serve server-scripts --args="test"
```

and you should receive the message saying "If you have received this message, that means the NestJS scripts app is working!"

## _Loose ends? Issues? Questions?_ <!-- optional -->

we have build, serve, and lint defined in its project.json but test is currently commented out. When setting up a new workspace using our 20i preset, jest.preset.js is not there in root and the scripts app's jest.config.ts wants to use it as the preset. Not sure if its absence is intentional and we have something else in mind to use instead?
