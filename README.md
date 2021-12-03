useEffect is used to handle side effects.

Not setting any dependency causes useEffect to run every time the component renders.

Setting the dependency to the empty array will cause useEffect to run only on the first time Rendering of the component.

Setting dependencies means that check the dependencies (which are hook values in the example code down below) and only run useEffect function if any of these dependencies have changed.

Notes:
- You DON'T need to add state updating functions (as we did in the last lecture with setFormIsValid : React guarantees that those functions never change, hence you don't need to add them as dependencies (you could though)

- You also DON'T need to add "built-in" APIs or functions like fetch(), localStorage etc (functions and features built-into the browser and hence available globally): These browser APIs / global functions are not related to the React component render cycle and they also never change

- You also DON'T need to add variables or functions you might've defined OUTSIDE of your components (e.g. if you create a new helper function in a separate file): Such functions or variables also are not created inside of a component function and hence changing them won't affect your components (components won't be re-evaluated if such variables or functions change and vice-versa)

```
const [enteredEmail, setEnteredEmail] = useState('');
const [emailIsValid, setEmailIsValid] = useState();
const [enteredPassword, setEnteredPassword] = useState('');
const [passwordIsValid, setPasswordIsValid] = useState();
const [formIsValid, setFormIsValid] = useState(false);

// setFormIsValid not included in dependencies because state updating functions never change in react.

useEffect(() => {
setFormIsValid(
    enteredEmail.includes('@') && enteredPassword.trim().length > 6
    );
}, [enteredEmail, enteredPassword]);
```