# React Basics (036_React-Basics)
[036_React-Basics](https://github.com/codecygen/036_React-Basics)

- Components
- CSS styling
- Properties (props)
- Events (useState)
- Components as Element Wrappers
- Lifting the State up to Parent Component
- Conditional Rendering
- Dynamic Styles (Eg: <div className="chart-bar__fill" style={{'height': barFillHeight}}>)


# Component Styling (037_React-Component_Styling)
[037_React-Component_Styling](https://github.com/codecygen/037_React-Component_Styling)

Checking if the entered String is all whitespace.

```javascript
String.trim().length === 0
```

unshift is a good Javascript method

```javascript
updatedGoals.unshift({ text: enteredText, id: Math.random().toString() });
```

## Dynamic Inline Styling

Not preferable. It duplicates css codes as you give same codes in a css file and inline as well.

```javascript
<label style={{color: !isValid ? 'red' : 'black'}}>Course Goal</label>
```

## Dynamic CSS Classes

JSX File
```javascript
<div className={`form-control ${!isValid ? 'invalid' : ''}`}>
    ...
    ...
</div>
```

CSS File
```javascript
.form-control.invalid input {
  border-color: red;
  background: #ffd7d7;
}

.form-control.invalid label {
  color: red;
}
```

## Importing CSS in a Normal Way

This method is pretty straightforward but downside is, the classnames are included in the entire HTML. So if someone is working on another component, he may want to edit that component's classnames in a different way but since, lets say he gives the name ".button" to his class in his own css file there will be a classname ".button" duplication which will mess up the things.

Button.js

```javascript
import React from 'react';
import './Button.css';

const Button = props => {
  return (
    <button type={props.type} className="button" onClick={props.onClick}>
      {props.children}
    </button>
  );
};

export default Button;
```

Button.css

```css
.button {
  width: 100%;
  font: inherit;
  padding: 0.5rem 1.5rem;
  border: 1px solid #8b005d;
  color: white;
  background: #8b005d;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.26);
  cursor: pointer;
}

.button:focus {
  outline: none;
}

.button:hover,
.button:active {
  background: #ac0e77;
  border-color: #ac0e77;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.26);
}

@media(min-width: 768px) {
  .button {
    width: auto;
  }
}
```

## Modular CSS Classes

In this method, when you import the css module the actual ".button" class will be renamed as a unique name in actual HTML file that is created. For example it renamed .button class as ".Button_button__2lgkF" in my example when I right click and inspect the entire HTML code on browser.

Only thing you have to take into account is, you have to rename the css modules with ".module." section. Instead of naming your css file as "Button.css", you have to name it as "Button.module.css" so when importing, React will know that it has to assign a unique class name for the class that is written in "Button.module.css".

Button.js
```javascript
import React from 'react';
import classes from './Button.module.css';

const Button = props => {
  return (
    <button type={props.type} className={classes.button} onClick={props.onClick}>
      {props.children}
    </button>
  );
};

export default Button;
```
Button.module.css (you have to put .module. section when renaming otherwise it wont work.)

```css
.button {
  width: 100%;
  font: inherit;
  padding: 0.5rem 1.5rem;
  border: 1px solid #8b005d;
  color: white;
  background: #8b005d;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.26);
  cursor: pointer;
}

.button:focus {
  outline: none;
}

.button:hover,
.button:active {
  background: #ac0e77;
  border-color: #ac0e77;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.26);
}

@media(min-width: 768px) {
  .button {
    width: auto;
  }
}
```

As you have seen, the default class calling is given down below,

```javascript
<button className={classes.button}>
```

In this method, don't forget that if the class name is with dash "form-control", you have to use something like this to call it.

```javascript
<div className={classes['form-control']}>
```

Alternatively for a conditional css rendering, this has to be used.

```javascript
{/* Instead of this, */}
{/* <div className={`form-control ${!isValid ? 'invalid' : ''}`}> */}
{/* Use this, */}
<div className={`${classes['form-control']} ${!isValid && classes.invalid}`}>
```

## Styled-Components Package

This is a package that you can install with
npm i styled-components

styled-components ensures that the classes we create won't interfere with other classes in future as the css modules are all imported one by one to form to page so any prior class that has same name in another css file will interfere with the other css file's same class name. Imagine if a multiple people work on the same project, this ensures that all classes are unique to their components and not inherited to the other components.

Example CourseInput.js

```javascript
import React, { useState } from 'react';
import styled from 'styled-components';
import Button from '../../UI/Button/Button';

// Here form-control class removed on the main section of css
// Also form-control class on other places replaced with &.
const FormControl = styled.div`
  margin: 0.5rem 0;

  & label {
    font-weight: bold;
    display: block;
    margin-bottom: 0.5rem;
  }

  & input {
    display: block;
    width: 100%;
    border: 1px solid #ccc;
    font: inherit;
    line-height: 1.5rem;
    padding: 0 0.25rem;
  }

  & input:focus {
    outline: none;
    background: #fad0ec;
    border-color: #8b005d;
  }

  &.invalid input {
    border-color: red;
    background: #ffd7d7;
  }

  &.invalid label {
    color: red;
  }
`;

const CourseInput = props => {
  const [enteredValue, setEnteredValue] = useState('');
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = event => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = event => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    props.onAddGoal(enteredValue);
  };

  return (
    <form onSubmit={formSubmitHandler}>
      {/* Since form-control class already added
      we have to only add "isValid? && 'invalid
      Check original CourseInput.js for more info on
      how the conditional statement set in normal app." */}
      <FormControl className={isValid? && 'invalid'}>
        <label>Course Goal</label>
        <input 
          type="text" 
          onChange={goalInputChangeHandler} 
        />
      <FormControl />
      <Button type="submit">Add Goal</Button>
    </form>
  );
};

export default CourseInput;
```

## Styled-Components Package with Props

We can also pass props for styled components

```javascript
import React, { useState } from 'react';
import styled from 'styled-components';
import Button from '../../UI/Button/Button';

// Here form-control class removed on the main section of css
// Also form-control class on other places replaced with &.
const FormControl = styled.div`
  margin: 0.5rem 0;

  & label {
    font-weight: bold;
    display: block;
    margin-bottom: 0.5rem;
    color: ${props => (props.invalid ? 'red' : 'black')}
  }

  & input {
    display: block;
    width: 100%;
    border: 1px solid ${props => (props.invalid ? 'red' : '#ccc')};
    background: ${props => (props.invalid ? '#ffd7d7' : 'transparent')}
    font: inherit;
    line-height: 1.5rem;
    padding: 0 0.25rem;
  }

  & input:focus {
    outline: none;
    background: #fad0ec;
    border-color: #8b005d;
  }
`;

const CourseInput = props => {
  const [enteredValue, setEnteredValue] = useState('');
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = event => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = event => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    props.onAddGoal(enteredValue);
  };

  return (
    <form onSubmit={formSubmitHandler}>
      {/* Here we pass props */}
      <FormControl invalid={!isValid}}>
        <label>Course Goal</label>
        <input 
          type="text" 
          onChange={goalInputChangeHandler} 
        />
      <FormControl />
      <Button type="submit">Add Goal</Button>
    </form>
  );
};

export default CourseInput;
```

## Styled-Components Package with Responsive Design

styled-components can also be set up for responsive designs
This is the content of Button.js

```javascript
// import React from 'react';
import styled from 'styled-components';

// import './Button.css';

// we copy pasted the Button.css here with styled-components method
// The only difference is, we deleted the "button" indications
// in css section and replaced button:focus as &:focus

// styled-components ensures that the classes we create won't interfere
// with children component's in future. Imagine if a multiple people work
// on the same project, this ensures that all classes are unique to their
// components and not inherited to the other components.
const Button = styled.button`
  width: 100%;
  font: inherit;
  padding: 0.5rem 1.5rem;
  border: 1px solid #8b005d;
  color: white;
  background: #8b005d;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.26);
  cursor: pointer;

  @media(min-width: 768px) {
    width: auto;
  }

  &:focus
    outline: none;

  &:hover,
  &:active {
    background: #ac0e77;
    border-color: #ac0e77;
    box-shadow: 0 0 8px rgba(0, 0, 0, 0.26);
  }
`;

// const Button = props => {
//   return (
//     <button type={props.type} className="button" onClick={props.onClick}>
//       {props.children}
//     </button>
//   );
// };

export default Button;
```



# Fragments, Portals, Refs (039_React-Fragments-Portals-Refs)
[039_React-Fragments-Portals-Refs](https://github.com/codecygen/039_React-Fragments-Portals-Refs)

## Fragments
Fragments is used just to put return elements together. Instead of using <div></div> you put them inside the brackets of <React.Fragment></React.Fragment> or simply <></>. This will wrap all elements but it will not add an extra layer of div element which is useful to avoid div soup inside the code that is created by React.

```javascript
<>
    <AddUser />
    <Button />
</>
```

## Portals
Portals are used to render the element in a different place in DOM. These are the steps to follow Initially, go to "index.html" then put these lines. Notice that "root" id is the default id and then we created "backdrop-root" and "overlay-root" as ids because we will port an inner DOM element to these locations. The reason we are doing this is, we want our code to look semantically clean in some cases to prevent some errors.

```javascript
    <div id="backdrop-root"></div>
    <div id="overlay-root"></div>
    <div id="root"></div>
```

```javascript
import ReactDOM from 'react-dom';

///////////
///////////

const ErrorModal = (props) => {
  return (
    // Also shown as <></>
    <React.Fragment>
      {ReactDOM.createPortal(<Backdrop onConfirm={props.onConfirm} />, 
      document.getElementById("backdrop-root"))}

      {ReactDOM.createPortal(
        <ModalOverlay 
          title={props.title} 
          message={props.message} 
          onConfirm={props.onConfirm} 
        />, 
        document.getElementById("overlay-root")
      )}
    </React.Fragment>
  );
};
```

Ref hook is different than State hook. If you just want to read an element, Ref hook has less code. If you also want to re-render something, you need to use State hook.

## Refs
Refs (UseRef) are hooks just like States (UseState). You should use Refs if you don't need to re-render an element. If you need to re-render the element use State hooks. You can reset the value of the input elements with Refs as seen in the code down below but keep in mind that it is an uncontrolled component unlike in the case of using State hook for the input value control. See the code down below.

```javascript
// AddUser.js

import React, { useState, useRef } from 'react';

import Card from '../UI/Card';
import Button from '../UI/Button';
import ErrorModal from '../UI/ErrorModal';
import classes from './AddUser.module.css';

const AddUser = (props) => {
  // Using useRef
  const nameInputRef = useRef();
  const ageInputRef = useRef();
  // Using useRef

  // const [enteredUsername, setEnteredUsername] = useState('');
  // const [enteredAge, setEnteredAge] = useState('');
  const [error, setError] = useState();

  const addUserHandler = (event) => {
    event.preventDefault();

    // Using useRef
    const enteredUsernameRef = nameInputRef.current.value;
    const enteredAgeRef = ageInputRef.current.value;
    // Using useRef

    // Using useRef
    if (enteredUsernameRef.trim().length === 0 || enteredAgeRef.trim().length === 0) {
      setError({
        title: 'Invalid input',
        message: 'Please enter a valid name and age (non-empty values).',
      });
      return;
    }

    // Using useRef
    if (+enteredAgeRef < 1) {
      setError({
        title: 'Invalid age',
        message: 'Please enter a valid age (> 0).',
      });
      return;
    }

    // Using useRef
    props.onAddUser(enteredUsernameRef, enteredAgeRef);

    // setEnteredUsername('');
    // setEnteredAge('');

    // This is uncontrolled component access.
    nameInputRef.current.value = '';
    ageInputRef.current.value = '';
  };

  // const usernameChangeHandler = (event) => {
  //   setEnteredUsername(event.target.value);
  // };

  // const ageChangeHandler = (event) => {
  //   setEnteredAge(event.target.value);
  // };

  const errorHandler = () => {
    setError(null);
  };

  return (
    <>
      {error && (
        <ErrorModal
          title={error.title}
          message={error.message}
          onConfirm={errorHandler}
        />
      )}
      <Card className={classes.input}>
        <form onSubmit={addUserHandler}>
          <label htmlFor="username">Username</label>
          <input
            id="username"
            type="text"
            // value={enteredUsername}
            // onChange={usernameChangeHandler}
            // Using useRef
            ref={nameInputRef}
          />
          <label htmlFor="age">Age (Years)</label>
          <input
            id="age"
            type="number"
            // value={enteredAge}
            // onChange={ageChangeHandler}
            // Using useRef
            ref={ageInputRef}
          />
          <Button type="submit">Add User</Button>
        </form>
      </Card>
    </>
  );
};

export default AddUser;

```




















# Rules of React Hooks (040_React-Side_Effects-Reducers-Context_API)
[040_React-Side_Effects-Reducers-Context_API](https://github.com/codecygen/040_React-Side_Effects-Reducers-Context_API)

- Only call React Hooks in React Functions. (React Component Functions or Custom Hooks)
- Only call React Hooks at the top level. (Don't call them in nested functions or block statements)
- Unofficial Rule: Always add everything you refer to inside of useEffect() as a dependency.

## useEffect Hook
useEffect is used to handle side effects.

- Not setting any dependency causes useEffect to run every time the component renders.

```javascript
useEffect(() => {
  console.log('EFFECT RUNNING');
});
```

- Setting the dependency to the empty array will cause useEffect to run only on the first time Rendering of the component.

```javascript
useEffect(() => {
  console.log('EFFECT RUNNING');
}, []);
```

- Using cleanup function ensures that the cleanup function will run on every other call of the useEffect function. In the example down below, It will run "EFFECT RUNNING" when you reload page. The cleanup section runs the second time the useEffect function is called aka when you type 1 character to password field. Then the next console log will be like "EFFECT CLEANUP" and then "EFFECT RUNNING". It first returns the result of last function then runs the function second time.

```javascript
// Login.js in in 040_React-Side_Effects-Reducers-Context_API

useEffect(() => {
  console.log('EFFECT RUNNING');
  
  // Cleanup function
  return () => {
    console.log('EFFECT CLEANUP');
  };

}, [enteredPassword]);
```

- Setting the dependency to an empty array for the example above will cause useEffect function to run only once. So you will see "EFFECT RUNNING" console log when the page loads. You will never see "EFFECT CLEANUP" console log until you remove the entire component this function written into. Lets say the useEffect function runs inside the Login.js component file. Once you login to a page and new page loads, this component gets removed from the DOM and that is when you will see the Cleanup function call thus "EFFECT CLEANUP" console log.

```javascript
// Login.js in 040_React-Side_Effects-Reducers-Context_API

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

```javascript
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

```javascript
// Login.js in 040_React-Side_Effects-Reducers-Context_API

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

```javascript
// Login.js in 040_React-Side_Effects-Reducers-Context_API

// state keeps previous state's snapshot
// action is the action you dispatch in your code
const passwordReducer = (state, action) => {
  if (action.type === 'USER_INPUT') {
    return { value: action.val, isValid: action.val.trim().length > 6 };
  }

  if (action.type === 'INPUT_BLUR') {
    // This literally returns previous state's values.
    return { value: state.value, isValid: state.value.trim().length > 6 };
  }

  return { value: '', isValid: false };
};

const [passwordState, dispatchPassword] = useReducer(passwordReducer, {
    value: '',
    isValid: false
});

const passwordChangeHandler = (event) => {
  // This is sending info into passwordReducer. "USER_INPUT" is a totally made up name.
  // It is only given to match it in the passwordReducer section so that passwordReducer will know
  // what action to take to return values for passwordState.value and passwordState.isValid
  dispatchPassword({ type: 'USER_INPUT', val: event.target.value });

  setFormIsValid(
    emailState.isValid && event.target.value.trim().length > 6
  );
};

// passwordState.value, passwordState.isValid will give the current values of the state.
```

Click [here](https://github.com/codecygen/040_React-Side_Effects-Reducers-Context_API/blob/c4de41856f1416ca03dd43cc6155d97ebebbc27c/src/components/Login/Login.js) for full code.

## Context API
Instead of forwarding props back and forth, you can use Context API to wrap all components that needs a specific data.

Imagine the hierarchy of the components like this:  
App.js > MainHeader.js > Navigation.js

Imagine that the isLoggedIn state hook in App.js is needed in Navigation.js. In the traditional solution, you have to first forward the state to MainHeader.js then forward again to Navigation.js. As the app gets more complicated you will need more complex state forwarding and it might result in too many forwarding in between different components. In order to prevent it, we need to use Context API.

### Method 1 - Wrapping in an <AuthContext.Consumer> Wrapper:

Steps:
- Create a folder /store/auth-context.js and the context of the file will be like this:

```javascript
// auth-context.js in 040_React-Side_Effects-Reducers-Context_API

import React from 'react';

const AuthContext = React.createContext({
    // We add these dummy props so that
    // other files will have autocomplete suggestion
    isLoggedIn: false,
    onLogout: () => {}
});

export default AuthContext;
```

- Go to App.js and wrap every component in the return section of the function like this. As you can see in the snipped down below, forwarding "isLoggedIn" state removed from the MainHeader component via props.

```javascript
// App.js in 040_React-Side_Effects-Reducers-Context_API
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

```javascript
// Navigation.js in 040_React-Side_Effects-Reducers-Context_API

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

### Method 2 - Assigning a ctx Value instead of Wrapping:

In this method, instead of wrapping everyhing inside <AuthContext.Consumer> in Navigation.js, we can import useContext from React and simply use it like this:

```javascript
// Navigation.js in 040_React-Side_Effects-Reducers-Context_API

import React, { useContext } from 'react';
import AuthContext from '../../store/auth-context';

const Navigation = (props) => {
  const ctx = useContext(AuthContext);

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
};

export default Navigation;

```

### Method 3 - Centralizing Context API:
In this method, we centralize everything like this:

```javascript
// auth-context.js in 040_React-Side_Effects-Reducers-Context_API

import React, { useState, useEffect } from 'react';

const AuthContext = React.createContext({
    // We add these dummy props so that
    // other files will have autocomplete suggestion
    isLoggedIn: false,
    onLogin: (email, password) => {},
    onLogout: () => {}
});

export const AuthContextProvider = (props) => {
    const [isLoggedIn, setIsLoggedIn] = useState(false);

    useEffect(() => {
        const storedUserLoggedInInformation = localStorage.getItem('isLoggedIn');
    
        if (storedUserLoggedInInformation === '1') {
          setIsLoggedIn(true);
        }
    }, []);

    const logoutHandler = () => {
        localStorage.removeItem('isLoggedIn');
        setIsLoggedIn(false);
    };

    const loginHandler = () => {
        localStorage.setItem('isLoggedIn', '1');
        setIsLoggedIn(true);
    };

    return (
        <AuthContext.Provider
            value={{
                isLoggedIn: isLoggedIn,
                onLogin: loginHandler,
                onLogout: logoutHandler
            }}
        >
            {props.children}
        </AuthContext.Provider>
    );
}

export default AuthContext;
```

Then in index.js

```javascript
// index.js in 040_React-Side_Effects-Reducers-Context_API

import React from 'react';
import ReactDOM from 'react-dom';
import { AuthContextProvider } from './store/auth-context';

import './index.css';
import App from './App';

ReactDOM.render(
    <AuthContextProvider>
        <App />
    </AuthContextProvider>, 
    document.getElementById('root')
);
```

Then in any file we need, for example in Home.js it will be like this:

```javascript
Home.js

import React, { useContext } from 'react';

import Card from '../UI/Card/Card';
import Button from '../UI/Button/Button';
import classes from './Home.module.css';
import AuthContext from '../../store/auth-context';

const Home = (props) => {
  const authCtx = useContext(AuthContext);

  return (
    <Card className={classes.home}>
      <h1>Welcome back!</h1>
      <Button onClick={authCtx.onLogout}>Logout</Button>
    </Card>
  );
};

export default Home;
```

NOTE: 
- Use Context for state management and props for configuration. For instance, if you want to pass only states, use Context, but if you want to use arguments to control the button type (submit, logout etc.), use props.

- React Context is NOT optimized for high frequency changes. If there are multiple changes per second, then React Context is not built for that.

- React Context should not be used to replace ALL component communications and props. You should still use props and props chains that might not need any replacement.

## Forward Refs
This section shows how to forward a ref to the sub component

```javascript
// Login.js in 040_React-Side_Effects-Reducers-Context_API

import React, { useState, useEffect, useReducer, useContext, useRef } from 'react';


  const emailInputRef = useRef();
  const passwordInputRef = useRef();


  const submitHandler = (event) => {
    event.preventDefault();
    if (formIsValid) {
      authCtx.onLogin(emailState.value, passwordState.value);
    } else if (!emailIsValid) {
      emailInputRef.current.focus();
    } else {
      passwordInputRef.current.focus();
    }
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>

        <Input
          ref={emailInputRef}
          // id="email"
          // label="E-mail"
          // type="email"
          // isValid={emailIsValid}
          // value={emailState.value}
          // onChange={emailChangeHandler}
          // onBlur={validateEmailHandler}
        />

        <Input
          ref={passwordInputRef}
          // id="password"
          // label="Password"
          // type="password"
          // isValid={passwordIsValid}
          // value={passwordState.value}
          // onChange={passwordChangeHandler}
          // onBlur={validatePasswordHandler}
        />

        <div className={classes.actions}>
          <Button type="submit" className={classes.btn}>
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default Login;
```

Then forward this to Input.js

```javascript
// Input.js 

import React, { useRef, useImperativeHandle } from 'react';
import classes from './Input.module.css';

// In order to forward ref from Login.js to Input.js, use React.forwardRef and "ref" argument.
const Input = React.forwardRef((props, ref) => {

    const inputRef = useRef();

    const activate = () => {
        inputRef.current.focus();
    };

    useImperativeHandle(ref, () => {
        return {
            // focus is the name in Login.js, activate is the name of the funtion in Input.js
            focus: activate
        };
    });

    return (
        <div
            className={`${classes.control} ${props.isValid === false ? classes.invalid : ''
                }`}
        >
            <label htmlFor={props.id}>{props.label}</label>
            <input
                ref={inputRef}
                type={props.type}
                id={props.id}
                value={props.value}
                onChange={props.onChange}
                onBlur={props.onBlur}
            />
        </div>
    );

});

export default Input;
```