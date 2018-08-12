# Redux Form 

- a reducer with actions built in

## Setup
- `$ npm install redux-form`
- in the main reducer index file - import the reducer from redux-form and wire it into the root-reducer
```javascript
import { reducer as formReducer } from 'redux-form';

const rootReducer = combineReducers({
  form: formReducer
});
```


- to setup component to Redux-Form 
    - reduxForm - acts similar to the `connect` function in redux 
    - form - name of the form (can be any unique string)
    ```javascript
    /* in component */
    import { Field, reduxForm } from 'redux-form';

    export default reduxForm({
        form: 'PostsNewForm' 
    })(<component name>);
    ```
    - to have both redux form and other reducers/action creators 
        ```javascript
            export default reduxForm({
                form: 'PostsNewForm'
            })(
                connect(null, { createPost })(PostsNew)
            );
        ```
## Creating the form 
- wrap the form in a `form` tag that has an obSubmit property with `handleSubmit` as the value
    - `handleSubmit` - a function built into redux-form, takes an argument which is a custom function that you write 
    - `onSubmit` takes an argument of values, this represents the values of the `Field` components
    ```javascript
    onSubmit(values) {        
        /* this is usually an action creator */
        this.props.createPost(values);
    }

    <form onSubmit={handleSubmit(this.onSubmit)}>
        /* field tags will go here */
    </form>
    ```

## Creating the field tags 
- make one field component per piece of state.  
    - Redux form will handle changes by user - (`onChange` handler, `setState`, etc), validation, and submit 
        - can have its own properties which can then be used in the function that renders the display component (ex: `field.label`)
        - to use the build in properties of field use `...field.input` 
        - the `component` property - the value is a value that renders a display component 
    ```javascript
        /* a function that renders the display of the component */
        renderField(field) {
            return (
                <div>
                    <label>{field.label}</label>
                    <input 
                        type="text"
                        {...field.input}
                    />
                </div>
            )
        }

        <Field 
            label="Title"
            name="title"
            component={this.renderField}
        />
    ```

## Validating forms 
- wire up the validate function to the component 
    - create a validation function and pass to redux form as a config object. 
    - the validate function takes an argument (`values`) that represents the users input. 
    ```javascript
        function validate(values) { }
        
        export default reduxForm({
            form: 'PostsNewForm',
            validate: validate
        })(<component name>);
    ```
- in the `validate` function 
    - create an empty error object 
    - use the `values` arg to parse through the users input, if the values are invalid, give the `errors` object some properties
    - return the errors object; if it is empty the form is valid, otherwise it is invalid and will not submit 
    ```javascript
    function validate(values) {
        const errors = {}

        /* if the user does not enter a title, a property is added to the error object */
        if (!values.title) {
            errors.title = "Enter a title!"
        }

        return errors;
    }
    ```
- show the errors in the form if there is an error with `field.meta.error`
    ```javascript 
    renderField(field) {
        /* ... */
        <div> {field.meta.error} </div>
    }
    ```
- add logic to show form only when the form has been touched by the user with `field.meta.touched`
    ```javascript 
    renderField(field) {
        /* ... */
        <div> {field.meta.touched ? field.meta.error : ''} </div>
    }
    ```