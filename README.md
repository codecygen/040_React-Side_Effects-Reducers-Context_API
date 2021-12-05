## useEffect Hook
useEffect is used to handle side effects.

- Not setting any dependency causes useEffect to run every time the component renders.

```
useEffect(() => {
  console.log('EFFECT RUNNING');
});
```

- Setting the dependency to the empty array will cause useEffect to run only on the first time Rendering of the component.

```
useEffect(() => {
  console.log('EFFECT RUNNING');
}, []);
```

- Using cleanup function ensures that the cleanup function will run on every other call of the useEffect function. In the example down below, It will run "EFFECT RUNNING" when you reload page. The cleanup section runs the second time the useEffect function is called aka when you type 1 character to password field. Then the next console log will be like "EFFECT CLEANUP" and then "EFFECT RUNNING". It first returns the result of last function then runs the function second time.

```
useEffect(() => {
  console.log('EFFECT RUNNING');
  
  // Cleanup function
  return () => {
    console.log('EFFECT CLEANUP');
  };

}, [enteredPassword]);
```

- Setting the dependency to an empty array for the example above will cause useEffect function to run only once. So you will see "EFFECT RUNNING" console log when the page loads. You will never see "EFFECT CLEANUP" console log until you remove the entire component this function written into. Lets say the useEffect function runs inside the Login.js component file. Once you login to a page and new page loads, this component gets removed from the DOM and that is when you will see the Cleanup function call thus "EFFECT CLEANUP" console log.

```
useEffect(() => {
  console.log('EFFECT RUNNING');
  
  // Cleanup function
  return () => {
    console.log('EFFECT CLEANUP');
  };

}, []);
```

Setting dependencies means that check the dependencies (which are hook values in the example code down below) and only run useEffect function if any of these dependencies have changed.

Notes:
- You DON'T need to add state updating functions (as we did in the last lecture with setFormIsValid : React guarantees that those functions never change, hence you don't need to add them as dependencies (you could though)

- You also DON'T need to add "built-in" APIs or functions like fetch(), localStorage etc (functions and features built-into the browser and hence available globally): These browser APIs / global functions are not related to the React component render cycle and they also never change

- You also DON'T need to add variables or functions you might've defined OUTSIDE of your components (e.g. if you create a new helper function in a separate file): Such functions or variables also are not created inside of a component function and hence changing them won't affect your components (components won't be re-evaluated if such variables or functions change and vice-versa)

```
import { useEffect, useState } from 'react';
 
let myTimer;
 
const MyComponent = (props) => {
  const [timerIsActive, setTimerIsActive] = useState(false);
 
  const { timerDuration } = props; // using destructuring to pull out specific props values
 
  useEffect(() => {
    if (!timerIsActive) {
      setTimerIsActive(true);
      myTimer = setTimeout(() => {
        setTimerIsActive(false);
      }, timerDuration);
    }
  }, [timerIsActive, timerDuration]);
};
```

In this example,

- timerIsActive is added as a dependency because it's component state that may change when the component changes (e.g. because the state was updated)

- timerDuration is added as a dependency because it's a prop value of that component - so it may change if a parent component changes that value (causing this MyComponent component to re-render as well)

- setTimerIsActive is NOT added as a dependency because it's that exception: State updating functions could be added but don't have to be added since React guarantees that the functions themselves never change

- myTimer is NOT added as a dependency because it's not a component-internal variable (i.e. not some state or a prop value) - it's defined outside of the component and changing it (no matter where) wouldn't cause the component to be re-evaluated

- setTimeout is NOT added as a dependency because it's a built-in API (built-into the browser) - it's independent from React and your components, it doesn't change

## Debouncing
Debouncing in Javascript is an exercise to enhance browser performance during any time-consuming computations.

```
useEffect(() => {
    // Debouncing in Javascript is an exercise to enhance browser performance 
    // during any time-consuming computations. 
    const identifier = setTimeout(() => {
      console.log('Checking form validity!');
      setFormIsValid(
        enteredEmail.includes('@') && enteredPassword.trim().length > 6
      );
    }, 500);

    // This is a cleanup function
    return () => {
      console.log('CLEANUP');
      clearTimeout(identifier);
    };
  }, [enteredEmail, enteredPassword]);
```

## Reducers (useReducer)
useState handles a state change in a component and re-renders it. Sometimes you have to have multiple effects which needs multiple useState functions. Due to that the code might get buggy. In these circumstances you need to use reducers. It is more powerful but ofcourse it comes with cons just like anything else. You need more setup to do.

Action Object => Dispatch => Reducer => State

const [state, dispatch] = useReducer(reducer, initialState);

```
const passwordReducer = (state, action) => {
  if (action.type === 'USER_INPUT') {
    return { value: action.val, isValid: action.val.trim().length > 6 };
  }

  if (action.type === 'INPUT_BLUR') {
    return { value: state.value, isValid: state.value.trim().length > 6 };
  }

  return { value: '', isValid: false };
};

const [passwordState, dispatchPassword] = useReducer(passwordReducer, {
    value: '',
    isValid: false
});

const passwordChangeHandler = (event) => {
  dispatchPassword({ type: 'USER_INPUT', val: event.target.value });

  setFormIsValid(
    emailState.isValid && event.target.value.trim().length > 6
  );
};
```

## Context API
Instead of forwarding props back and forth, you can use Context API to wrap all components that needs a specific data.

Imagine the hierarchy of the components like this:  
App.js > MainHeader.js > Navigation.js

Imagine that the isLoggedIn state hook in App.js is needed in Navigation.js. In the traditional solution, you have to first forward the state to MainHeader.js then forward again to Navigation.js. As the app gets more complicated you will need more complex state forwarding and it might result in too many forwarding in between different components. In order to prevent it, we need to use Context API.

Steps:
- Create a folder /store/auth-context.js and the context of the file will be like this:

```
import React from 'react';

const AuthContext = React.createContext();

export default AuthContext;
```

- Go to App.js and wrap every component in the return section of the function like this. As you can see in the snipped down below, forwarding "isLoggedIn" state removed from the MainHeader component via props.

```
// Codes removed for convenience
// Codes removed for convenience
// Codes removed for convenience

import AuthContext from './store/auth-context';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  // Codes removed for convenience
  // Codes removed for convenience
  // Codes removed for convenience
  
  return (
    <AuthContext.Provider 
      value={{
        isLoggedIn: isLoggedIn
      }}
    >
      <MainHeader onLogout={logoutHandler} />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </AuthContext.Provider>
  );
}
```

- Finally edit Navigation.js as given,

```
import React from 'react';

import classes from './Navigation.module.css';
import AuthContext from '../../store/auth-context';

const Navigation = (props) => {
  return (
    <AuthContext.Consumer>
      {(ctx) => {
        return (
          <nav className={classes.nav}>
            <ul>
              {ctx.isLoggedIn && (
                <li>
                  <a href="/">Users</a>
                </li>
              )}
              {ctx.isLoggedIn && (
                <li>
                  <a href="/">Admin</a>
                </li>
              )}
              {ctx.isLoggedIn && (
                <li>
                  <button onClick={props.onLogout}>Logout</button>
                </li>
              )}
            </ul>
          </nav>
        );
      }}
    </AuthContext.Consumer>
  );


  // return (
  //   <nav className={classes.nav}>
  //     <ul>
  //       {props.isLoggedIn && (
  //         <li>
  //           <a href="/">Users</a>
  //         </li>
  //       )}
  //       {props.isLoggedIn && (
  //         <li>
  //           <a href="/">Admin</a>
  //         </li>
  //       )}
  //       {props.isLoggedIn && (
  //         <li>
  //           <button onClick={props.onLogout}>Logout</button>
  //         </li>
  //       )}
  //     </ul>
  //   </nav>
  // );
};

export default Navigation;

```