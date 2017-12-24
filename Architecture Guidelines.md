# General Architecture Angular (2+)

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
│   │   ├── ... 
│   ├── services
│   │   ├── ...
│   ├── base
│   │   ├── ...  
│   ├── shared.module.ts
│   ├── ...
├── feature_x
│   ├── smarts
│   │   ├── ...
│   ├── dummies
│   │   ├── smart_x
│   │   │   ├── dummy_y
│   │   │   │   ├── dummy_y.component.ts
│   │   │   │   ├── dummy_y.component.spec.ts
│   │   │   │   ├── dummy_y.component.html
│   │   │   │   ├── dummy_y.component.sass
│   │   │   ├── ...  
│   ├── sandbox.ts  
│   ├── feature_x.module.ts
│   ├── ..
└── app.module.ts
```