# react-simple-side-effects
Simple side effects middleware for react

## Install

```
npm i react-simple-side-effects
```

## Required peer dependencies

- @reduxjs/toolkit
- rxjs
- typescript

## Usage

### Define side effects (side-effects.ts)
```typescript
import {
  Actions,
  Action1,
  action2
} from './actions';
import { AppState } from './states';
import { beforeDispatch, afterDispatch } from 'react-simple-side-effects';

export const initEffects = () => {
  beforeDispatch(Actions.MY_ACTION_1, () => {
    // do some stuff before state changed
  });

  afterDispatch(Actions.MY_ACTION_1, () => {
    // do some stuff after state changed
  });
  
  afterDispatch<Action1, AppState>(Actions.MY_ACTION_1, ({action, oldState, newState}) => {
    // access action or state
    console.log(action.payload, oldState, newState);
  });
  
  afterDispatch(Actions.MY_ACTION_1, ({dispatch}) => {
    // dispatch a new action
    dispatch(action2());
  });
  
  ...
};
```

### Define actions (actions.ts)
```typescript
import { createAction, PayloadAction } from '@reduxjs/toolkit';

export enum Actions {
  ACTION_1 = 'ACTION_1',
  ACTION_2 = 'ACTION_2',
}

export type Action1 = PayloadAction<boolean>;
export type Action2 = PayloadAction<void>;

export const action1 = createAction<boolean>(Actions.ACTION_1);
export const action2 = createAction<void>(Actions.ACTION_2);
```

### Create Middleware (store.ts)
```typescript
import { createMiddleware } from 'react-simple-side-effects';

...

const store = configureStore<AppState>({
  ...
  middleware: [createMiddleware<AppState>()],
});

```

### Init effects (_app.tsx)
```typescript
import type { AppProps } from 'next/app';
import { useEffect } from 'react';
import { Provider } from 'react-redux';
import { appWithTranslation } from 'next-i18next';
import store from '../lib/store/store';
import { initEffects } from '../lib/store/side-effects';

function MyApp({ Component, pageProps }: AppProps) {
  
  useEffect(() => {
    initEffects();
  }, []);

  return (
    <Provider store={store}>
      <Component {...pageProps} />
    </Provider>
  );
}

export default appWithTranslation(MyApp);

```
