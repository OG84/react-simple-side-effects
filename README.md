# react-simple-side-effects
Simple side effects middleware for react. Enables you to execute async side effects when a store action is dispatched.

[![Node.js CI](https://github.com/OG84/react-simple-side-effects/actions/workflows/node.js.yml/badge.svg)](https://github.com/OG84/react-simple-side-effects/actions/workflows/node.js.yml)

## Install

```
npm i @og84/react-simple-side-effects
```

## Required peer dependencies

- @reduxjs/toolkit
- rxjs

## Usage

### Define side effects (side-effects.ts)
```typescript
import {
  Actions,
  Action1,
  action2
} from './actions';
import { AppState } from './states';
import { EffectsHandler } from 'react-simple-side-effects';

export const sideEffects: EffectsHandler<AppState> = ({ beforeDispatch, afterDispatch }) => {
  beforeDispatch(Actions.MY_ACTION_1, () => {
    // do some async stuff before state changed
  });

  afterDispatch(Actions.MY_ACTION_1, () => {
    // do some async stuff after state changed
  });
  
  afterDispatch<Action1>(Actions.MY_ACTION_1, ({action, oldState, newState}) => {
    // access action or state
    console.log(action.payload, oldState, newState);
  });
  
  afterDispatch(Actions.MY_ACTION_1, ({dispatch}) => {
    // dispatch a new action
    dispatch(action2());
  });
  
  afterDispatch(
    [Actions.SET_PACKAGE, Actions.UPDATE_PACKAGE], () => {
      // multi action side effect
    }
  );
  
   afterDispatch('', () => {
    // after any action
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
import { sideEffects } from './side-effects';

...

const store = configureStore<AppState>({
  ...
  middleware: [createMiddleware<AppState>(sideEffects)],
});
```

