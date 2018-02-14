# General Architecture Angular (2+) with State Management

## Project Folder Structure

```bash
app
├── core
│   ├── store
│   │   ├── actions
│   │   │   ├── ...
│   │   ├── reducers
│   │   │   ├── ...
│   │   ├── effects
│   │   │   ├── ...
│   ├── services
│   │   ├── http
│   │   │   ├── x.services.ts
│   │   │   ├── ...
│   │   ├── ...
│   ├── models
│   │   ├── ...
│   ├── core.module.ts
│   ├── ...
├── shared
│   ├── directives
│   │   ├── ...
│   ├── pipes
│   │   ├── ...
│   ├── components
│   │   ├── feature_x
│   │   │   ├── ...
│   │   ├── ...
│   ├── services
│   │   ├── ...
│   ├── base
│   │   ├── ...  
│   ├── shared.module.ts
│   ├── ...
├── feature_x
│   ├── containers
│   │   ├── ...  
│   ├── sandbox.ts  
│   ├── feature_x.module.ts
│   ├── ..
└── app.module.ts
```

### Project Folder Structure Description

The folder structure described here is complemented with the architecture showed in the complementary document `General Architecture, Angular and @ngrx` also by the **ANT Team**.

This folder structure is the pragmatic way of represent the architecture chosen by the **ANT Team**. It's main purpose is the separation of concerns, making easier the testability, maintainability and scalability of the software.

Every project following this architecture it's gonna be composed by 3 main modules:

* **Core Module:** This module will contain the essential functionalities for the correct working of the app. Between other it will contain
  * Store and State Management
  * Http and other Services
  * Domain Models
  * etc.
* **Shared Module:** Here is the place to put all the common logic that can be reused across the app. It could contain
  * Base Classes
  * General Utils (Directives, Pipes, Services)
  * Dummies components, separeted by the feature that they are more related to. The why of the location of the Dummies Component in this module is reusability.
* **Feature Module:** This not actually a module but several, each one describing a very specific domain area and inside each one we should have

  * `Containers` (Smart Components), this are the components in charge of manage the logic of the feature, they communicate with the **Core Module** through `Sandboxes`.
  * `Sandboxes`, they are an abstraction layer between the **Core Module** and the `Containers`. They are in charge of dispatch actions to the store, select state slices, communicate with services, etc. They can also be seen as a facade to the logic of the app.

## Bibliog

* [@ngrx StyleGuide ](https://github.com/orizens/ngrx-styleguide/tree/master/docs)
