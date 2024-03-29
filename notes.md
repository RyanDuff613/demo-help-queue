# REACT FUNDAMENTALS NOTES:

### REACT COMPONENTS:
  - React components are either Javascript classes or functions, though most often they're functions. 
  - Components return JSX code that is compiled into Javascript and ultimately is used to create the DOM elements that the user sees in the browser.
  - Components can also return other components. We call these "child-components".
  - "Parent components" can pass data and methods to their children via `props`. 

    #### FUNCTIONAL COMPONENTS
    - Functional components `return` JSX, which is ultimately compiled to create DOM elements. 
    - Function components cannot contain `state` objects. They can however use `hooks` which allow them to use state (we'll learn about these later).
    - We should always use function components where possible. `State` can get very complex in a React application, which is exactly why we want to minimize the number of components that use it.
      
      <details><summary><code>Example: Basic Functional Component</code></summary> 

      ```javascript
      import React from "react";

      function ThisIsAFunctionalComponent(){
        return (
          <p>I am a functional component.</p> // This is JSX
        );
      }

      export default ThisIsAFunctionalComponent;
      ```
      </details>

    #### CLASS COMPONENTS
    - Class components can contain `state`, a Javascript object that contains data about the application. 
    - Only define a component as a class if it absolutely requires state.
      <details><summary><code>Example: Basic Class Component</code></summary> 

      ```javascript
      import React, { Component } from 'react';

      class ThisIsAClassComponent extends React.Component {
      
        constructor(props) {
          super(props);     // required so that the component can inherit functionality from react
          this.state = {};  // the state object!
        }

        render() {   // class components must have a render() method that returns JSX
          return (
            <p>I am a class component</p> // This is JSX
          );
        }
      }

      export default ThisIsAClassComponent;

      ```
      </details>
      
      - `super(props)` is called to access a parent class' constructor. Without using the `super` keyword, our component won't be able to inherit the   functionality of the parent `React.Component` class.
      - `this.state = {};` is standard convention for declaring state in ES6  React classes. It's a JavaScript object defined in literal notation. It  will include state values in the form of key-value pairs.
      - `render()` - Class components must always include a render() method. This method will return any JSX content that React should add to its virtual DOM.

      #### State in Class Components:
      - State is data in an application that we need to store and change. 
      - Only class components can contain `state`. 
      - We should only define a component as a class if it absolutely requires state. If a component does not require state, it should instead be a function component.
      - Class components have a constructor that looks like this:

        ```javascript
        constructor(props) {
          super(props);
          this.state = {}; // this is the only place we should define state in a pure React application
                           // it's just a regular Javascript object and can contain as many key-value pairs as you wish. 
                           // 
        }
        ```

        #### Making changes to state with setState()
        - THIS IS VERY IMPORTANT: Whenever we want to change state in a class component, we need to use the `setState()` method provided by React. Calling `setState()` updates the state value and triggers a re-render of the component that contains the state slice. If you mutate state directly (without using the `setState()` method) the component will not rerender, the app will appear unchanged despite the fact that something in `state` has changed. This will cause errors and be difficult to debug. 

        - `setState()` can take either an object or a function as an argument, most of the time you'll provide an object. The object provided as an argument contains a key-value pair describing which slice of state should be updated and with what value.
          - When you need to make a change to `state` do this:
            ```javascript
            this.setState({property: update})
            ```
          - Don' do this!
            ```javascript
            this.state.property = "new value";
            ```
          - When a child component needs to alter a value of state that lives in it's parent, the parent component needs a method for making the state change and it needs to pass the method to the child component via `props`. Here's the simplest example of this from the Help Queue:
            <details><summary><code>In TicketControl.js</code></summary>

            ```javascript
            ...

            // this method handles setting the value of this.state.editing to "true"
            
            handleEditClick = () => {
              this.setState({ editing: true });
            }

            ...

            // when TicketControl renders the TicketDetail component it passes 
            // handleEditClick as a prop called onClickingEdit
            
            currentlyVisibleState = <TicketDetail ticket={this.state.selectedTicket}
                                          onClickingDelete={this.handleDeletingTicket}
                                          onClickingEdit={this.handleEditClick} />
            ...
            ```
            </details>

            <details><summary><code>In TicketDetail.js</code></summary>

            ```javascript
            ...
            return (
              <>
                <h3>{ticket.location}</h3>
                <h3>{ticket.names}</h3>
                <p><em>{ticket.issue}</em></p>
                <br />
                    // when the "Edit Ticket" button is clicked, it will 
                    // call the prop onClickingEdit(ticket.id)
                    // This calls handleEditClick(ticket.id) in TicketControl
                <button onClick={() => props.onClickingEdit(ticket.id)}>Edit Ticket</button>
                <button onClick={() => props.onClickingDelete(ticket.id)}>Close Ticket</button>
                <br />
              </>
            )
            ...
            ```
            </details>
          - Methods for altering state should always be arrow functions because they preserve the meaning of `this` within the scope in which they're created (a concept called binding).
            - Do this:

              ```javascript
              // handleClick() is an arrow function.
              handleClick = () => {
                this.setState(prevState => ({
                  formVisibleOnPage: !prevState.formVisibleOnPage
                }));
              }
              ```
            - Don't do this:

              ```javascript
              handleClick(){
                this.setState(prevState => ({
                  formVisibleOnPage: !prevState.formVisibleOnPage
                }));
              }
              ```
          -  Alternatively, you can also pass an arrow function as an argument to `setState()`. The arrow function passed as an argument takes the current `state` and `props` as arguments. This is useful when you need to know a current value within `state` before making a change. In the Help Queue we have an example of this with the `handleClick()` method. Within it, `setState()` is given an arrow function as an argument The arrow function takes `prevState` as an argument and returns an object that defines the change we want to make to `state`. In effect, this code is saying: "set the value of `formVisibleOnPage` to the opposite of what is was previously".  

            <details><summary><code>Example From TicketControl.js</code></summary>

            ```javascript
              ...
              // when called, this function sets the value of formVisibleOnPage (a boolean) 
              // to the opposite of it's current value.
              handleClick = () => {
                this.setState(prevState => ({
                  formVisibleOnPage: !prevState.formVisibleOnPage
                }));
              }
              ...
            ```
            </details>

        - Methods for altering state should always be arrow functions because they preserve the meaning of `this` within the scope in which they're created (a concept called binding).
          - Do this:

            ```javascript
            // handleClick() is an arrow function.
            handleClick = () => {
              this.setState(prevState => ({
                formVisibleOnPage: !prevState.formVisibleOnPage
              }));
            }
            ```
          - Don't do this:

            ```javascript
            handleClick(){
              this.setState(prevState => ({
                formVisibleOnPage: !prevState.formVisibleOnPage
              }));
            }
            ```
    

### JSX:
  - JSX is a declarative language that combines JavaScript with HTML.
  - JSX is compiled into Javascript and that Javascript ultimately creates the DOM elements that the end-user sees in their browser. 
  - Within JSX, we can add Javascript code within curly-braces`{ }`. This allows us to dynamically change what's rendered in the browser.
  - In order to return multiple elements, all the code in a component's `return` statement must be wrapped in a single JSX element. Typically, that will be a `<React.Fragment>`. Alternatively, you can also use `<> </>`, it's just shorthand for the same `<React.Fragment>`.
    <details><summary><code>Example:</code></summary> 

      ```javascript
      import React from "react";
          
      function App(){
        const studentNames = "Thato and Haley"
        return (
          <> 
            <h1>Help Queue</h1>
            <h3>3a</h3>
            <h3>{studentNames}</h3> 
            <p><em>Firebase entries not saving!</em></p>
            <hr/>
          </>
        );
      }
      
      export default App;
      ```
    </details>
    
    #### Looping in JSX:
    
    - Often, we'll want a component to render many instances of a child component. Instagram, Facebook and similar apps are just long lists of components, each populated with unique data. 
    - We can use familiar Javascript tools for looping in JSX. In the Help-Queue we use `.map()` to loop over `mainTicketList` - which is an array of objects containing "Ticket" data. 
    - If we're mapping over an array to create multiple instances of a component each should have a unique `key` prop. This helps React manage updates to the DOM. We'll get a console warning if we forget to include it.

      <details><summary><code>Example: src/TicketList.js</code></summary> 

        ```javascript
        ...
        return (
          <>
            <hr/>
            {mainTicketList.map((ticket) =>
              <Ticket names={ticket.names}
                location={ticket.location}
                issue={ticket.issue}
                key={ticket.id}/> 
            )}
          </>
        );
        ...
        ```
      </details>

### PROPS:
- React components accept properties (known as props) passed down from a parent.
- We pass props down to a child component using JSX.
- Because props is an object, we access its properties just like we would with any other object like this: `props.location`.
- Props are read-only, a child component cannot alter the props passed-in by a parent component.
  <details><summary><code>Parent Component</code></summary>

  ```javascript
  import React from "react";
  import Ticket from "./Ticket";

  function TicketList(){
    // this component returns a Ticket component and passes data into it via  'props' 
    return (
      <Ticket
        location="3A"
        names="Thato and Haley"
        issue="Firebase will not save record!"/>
    );
  }

  export default TicketList;
  ```
  </details>

  <details><summary><code>Child Component</code></summary>

  ```javascript
  import React from "react";

  function Ticket(props){
    // this component has access to the data passed in by the parent component  "TicketList" via props
    // we can access that data using { } in JSX
    return (
      <>
        <h3>{props.location} - {props.names}</h3>
        <p><em>{props.issue}</em></p>
        <hr/>
      </>
    );
  }

  export default Ticket;
  ```
  </details>

  #### PropTypes
  - It's a best practice to assign strict data types to props with a special `propTypes` property. This is helpful for debugging when application grow in size. 
  - We will always assign strict data types to all of our properties (including independent projects)

    <details><summary><code>Ticket Component with PropTypes</code></summary>
  
    ```javascript
    import React from "react";
    import PropTypes from "prop-types"; // import PropTypes library
  
    function Ticket(props){
      return (
        <>
          <h3>{props.location} - {props.names}</h3>
          <p><em>{props.issue}</em></p>
          <hr/>
        </>
      );
    }
  
    // Add a propTypes property to the Ticket component. 
    // Ticket.propTypes is an object that declares the names 
    // and data types of all props that will be passed into the component
    Ticket.propTypes = {
      names: PropTypes.string,
      location: PropTypes.string,
      issue: PropTypes.string.isRequired // You can make a prop required simply   by adding .isRequired
    };
  
    export default Ticket;
    ```
    </details>