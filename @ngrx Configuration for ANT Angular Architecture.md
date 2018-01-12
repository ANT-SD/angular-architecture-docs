# @ngrx Configuration for ANT Angular Architecture

This document describes the necessary configuration for **State Management** using `@ngrx/store` using the Architecture developed by the **ANT Team**. More information about the **ANT Angular Architecture** can be found in `Doc1` and `Doc2`.

Since we are using a centralized **Store Architecture** (Unified Source of Truth), we have chosen the `Core Module` as the **Store** location.

```code
app
├── core
│   ├── store
│   │   ├── actions
│   │   │   ├── ...
│   │   ├── reducers
│   │   │   ├── index.ts
│   │   │   ├── ...
│   │   ├── effects
│   │   │   ├── ...
```

Inside the store we can see the **actions, reducers and effects** folders. These folder defines the entire ecosystem of the **State Management**, in other words, the **Store**.

* **actions:** Defines the strongly typed events that can be dispatched by the store and it's payloads. The payload is the extra data that can be of importance for an event. Example:

```typescript
export const DO_LOGIN = "[Account] Do Login";

export class DoLoginAction implements Action {
  public readonly type = DO_LOGIN;

  constructor(public payload: { username: string; password: string }) {}
}
```

    It's payload represents the **LoginForm** needed for Authenticate.

* **reducers:** Inside every **Reducer** there is the section of state of the domain they define.

  ```typescript
  export interface State {
    loading: boolean;
    loaded: boolean;
    failed: boolean;
    user: User;
    userClaims: any;
  }

  const INITIAL_STATE: State = {
    loading: false,
    loaded: false,
    failed: false,
    user: JSON.parse(localStorage.getItem(APP_USER)),
    userClaims: JSON.parse(localStorage.getItem(APP_USER))
  };
  ```

  There is also the reducer function witch contains a switch statement matching the differents actions of it's particular domain that are intended to change the state of the app.

  ```typescript
  export function reducer(
    state = INITIAL_STATE,
    action: actions.Actions
  ): State {
    if (!action) return state;

    switch (action.type) {
      case actions.DO_LOGIN_SUCCESS:
      case actions.DO_REGISTER_SUCCESS: {
        console.log("Success Login: ");
        console.log(action);

        return Object.assign({}, state, {
          loaded: true,
          loading: false,
          failed: false,
          user: action.payload,
          userClaims: action.payload
        });
      }

      default: {
        return state;
      }
    }
  }
  ```

  Finally it contains a util `const` that returns a slice of the domain state

  ```typescript
  export const getUserClaims = (state: State) => state.userClaims;
  ```

* **effects:** Effects are in charge of managing the side effects of the store workflow. In other words they perform the operations related to the managing of the state but without changing the state by themself. Commonly they are use to perform service calls. Like reducers they react to dispatched events and there is a list of bestpractices oriented to message architecture that fits very well to they design.

  This is a very simple effect used to refresh the auth token:

  ```typescript
  @Effect()
    public doRefreshToken$: Observable<Action> = this.actions$
      .ofType(actions.DO_REFRESH_TOKEN)
      .map((action: actions.DoRefreshTokenAction) => action.payload)
      .switchMap(state => {
        return Observable.fromPromise(this.oauthService.refreshToken())
          .map(() => new actions.DoRefreshTokenSuccessAction(null) as Action)
          .catch(error => of(new actions.DoRefreshTokenFailAction()));
      });
  ```

* **reducers/index.ts:** This file is in charge of configuring the store. Because the State of the App is segmented in small classes, one for every domain reducer, it's necessary to merge all the parts in order to create the State of the app. The of an App is only one and represents all the data related to the domains of the App.

  ```typescript
  export interface State {
    account: fromAccount.State;
    company: fromCompany.State;
    router: fromRouter.RouterReducerState<RouterStateUrl>;
  }

  export const reducers: ActionReducerMap<State> = {
    account: fromAccount.reducer,
    company: fromCompany.reducer,
    // search: fromSearch.reducer,
    // books: fromBooks.reducer,
    // collection: fromCollection.reducer,
    // layout: fromLayout.reducer,
    router: fromRouter.routerReducer
  };
  ```

  It Also contains the selector necessary to take a slice of the state (take some data like the userLogged)

  ```typescript
  export const getAccountState = (state: State) => state.account;

  export const getAccountLoggedUser = createSelector(
    getAccountState,
    fromAccount.getLoggedUser
  );
  ```

  ### Advanced configurations

  Inside **reducers/index.ts** there are other configurations for more advanced use of the store.

  #### Store freeze

  The store-freeze is a tool for the development environmnet where an exception is throw if the state is modified (State is immuntable).

  ```typescript
  export const metaReducers: Array<MetaReducer<State>> = !environment.production
    ? [storeFreeze]
    : [];
  ```

  #### Route State

  `@ngrx/router-state` is a library that handles the state of the **URL** for you. In order to work with **storeFreeze** and **reduxDevTools** is necessary to create a **customSerializer**, because the default one, collects to many info and makes the mentioned tools to malfunction.

  ```typescript
  export class CustomSerializer
    implements fromRouter.RouterStateSerializer<RouterStateUrl> {
    public serialize(routerState: RouterStateSnapshot): RouterStateUrl {
      const { url } = routerState;
      const queryParams = routerState.root.queryParams;

      // Only return an object including the URL and query params
      // instead of the entire snapshot
      return { url, queryParams };
    }
  }
  ```

### Configuration in AppModule

In order to configure the store we need to import and inject it int AppModule. The first we need to do is to import the reducer and metareducer, along with the effects

```typescript
imports: [
    (...)
    StoreModule.forRoot(reducers, { metaReducers }),
    EffectsModule.forRoot(effects),
    (...)
  ]
```

To be able to use the ReduxDevTools we need to import these module too, ReduxDevTools allow us to debug the store and time travel through the state.

```typescript
imports: [
    (...)
    StoreDevtoolsModule.instrument({
      maxAge: 25 //  Retains last 25 states
    }),
    (...)
  ]
```

Finally we need to connect the route state analyser

```typescript
imports: [
    (...)
      StoreRouterConnectingModule,
    (...)
  ]
```

And substitute the RouteSerializer for the custom one created in index.ts

```typescript
providers: [
  ENV_PROVIDERS,
  APP_PROVIDERS,
  { provide: RouterStateSerializer, useClass: CustomSerializer }
];
```
