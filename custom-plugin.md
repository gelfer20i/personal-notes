# Objective

Step 1: Install a plugin in an existing workspace

```s
yarn add -D @20i/nx
```

Step 2: Add a new app or lib through that plugin

```s
nx g @20i/nx:auth my-auth
```

---

# Nx plugins

Nx plugins are npm packages that contain "generators" and "executors" to extend the capabilities of an Nx workspace

# Generators

- Generators are blueprints to create new files from templates
- Anytime you run nx generate ..., you invoke a generator.
- They are used to create/update applications, libraries, components, and more.

ref: https://nx.dev/generators/creating-files

## Types of Generators

1. "Plugin Generators" are available when an Nx plugin has been installed in your workspace, e.g. @nrwl/express, @nrwl/react, @nrwl/nest.

```s
yarn add -D @nrwl/nest
```

```s
nx g @nrwl/nest:lib my-nest-lib
```

2. "Workspace Generators" are generators that you can create for your own workspace. Workspace generators allow you to codify the processes that are unique to your own organization.

```s
nx generate @nrwl/workspace:workspace-generator my-generator
```

That will create the my-generator folder in /tools/generators
You can edit/add files in my-generator.
To run our workspace generator, execute...

```s
nx workspace-generator my-generator mylib
```

That will create a new library "mylib" in /libs of your workspace.
You can then use any functions in libs/mylib in /apps by importing them

import {someFn} from '@your-workspace-name/mylib';

to remove a library from the workspace, execute...

```s
nx g remove mylib
```

---

# Executors

- The executors that are available for each project are defined and configured in the project's project.json file
- Each project has its executors defined in the targets property.
- Executors run those files created by Generators.
- Executors define how to perform an action on a project.
- You invoke an executor with nx run ... (or nx test, nx build).
- They are used to build applications and libraries, test them, serve them, lint them, and more.

nx run [project]:[command]

```s
nx run cart:build
```

nx [command] [project]

```s
nx build cart
```

nx [command] [project] --configuration=[configuration]

```s
nx build cart --configuration=production
```

ref: https://nx.dev/executors/using-builders#simplest-executor

# Creating a custom plugin

1. Create a brand-new workspace with a pre-configured plugin

```s
npx create-nx-plugin 20i --pluginName auth
```

The command above will create a plugin with the package name set to @20i/auth.
The new plugin is created with a default generator, executor, and e2e app.

to add more generators, execute...

```s
nx generate @nrwl/nx-plugin:generator [generatorName] --project=[pluginName]
nx generate @nrwl/nx-plugin:generator library --project=auth
nx generate @nrwl/nx-plugin:generator component --project=auth

```

to add more executors, execute...

```s
nx generate @nrwl/nx-plugin:executor [executor] --project=[pluginName]
```
