# The Road to learn React

- JS in JSX
    - don't make the mistake of using index of the item in the array;  when. the list changes its order, React will have a hard time to identify the items properly
        
        ```jsx
        // don't do this
        {list.map(function(item, key) { return (
            <div key={key}>
              ...
        </div> );
        })}
        ```
        
    - A good rule of thumb is that elements inside the `map()` call need keys.
        
        ```jsx
        function ListItem(props) {
          const value = props.value;
          return (
            // Wrong! There is no need to specify the key here:
            <li key={value.toString()}>
              {value}
            </li>
          );
        }
        
        function NumberList(props) {
          const numbers = props.numbers;
          const listItems = numbers.map((number) =>
            // Wrong! The key should have been specified here:
            <ListItem value={number} />
        		// Correct! Key should be specified inside the array.
            <ListItem key={number.toString()} value={number} />
          );
          return (
            <ul>
              {listItems}
            </ul>
          );
        }
        ```
        

### Basics in React

- everytime you change your component state, the `render()` method will run again; you have to use `setState()` to modify your state
- ES6 feature
    
    ```jsx
    // ES5
    var userService = { 
    	getUserName: function (user) {
    		return user.firstname + ' ' + user.lastname; 
    	},
    };
    // ES6
    const userService = { 
    	getUserName(user) {
    		return user.firstname + ' ' + user.lastname; 
    	},
    };
    ```
    
- JS `filter` function doesn't mutate the old list, it returns a new one

### Bindings

- avoid bind this in `render()`, it would bind multiple times whenever components update
- **Event Handler**
    
    ```jsx
    <button onClick={this.onDismiss(item.objectID)} type="button"
    >
    	Dismiss
    </button>
    ```
    
    - the `onDismiss()` would execute immediately when you open the application in your browser. The expression in the handler is **evaluated(get the return value of onDismiss)**
    - using `onClick={this.onDismiss}` can avoid being evaluated, but cannot input params
- **Interactions with Forms and Events**
    - React's `this.setState()` is a shallow merge, it preserves the sibling. properties in the state object when updating one sole property in it.
- **controlled components**
    - `<input>` `<textarea>` `<select>` hold their own state in plain HTML — **uncontrolled component**, in React, you should make sure to make those elements **controlled components**
        
        ```jsx
        render() {
            const { searchTerm, list } = this.state;
            return (
              <div className="App">
                <form>
        					<input type="text" 
        						value={searchTerm} onChange={this.onSearchChange /> 
        				</form>
        				...
        			</div>); 
        }
        ```
        
- **Component Declarations**
    - **Functional Stateless Components:** don't have lifecycle methods
    - A rule of thumb is to use functional stateless components when you don’t need local state or component lifecycle methods
        
        ```jsx
        const Search = ({ onChange, value, children }) => (
          <form>
            {children}
            <input type="text" onChange={onChange} value={value} />
          </form>
        );
        ```
        

### Getting Real with an API

- **LifeCycle Methods**
    - `constructor()` ⇒ `componentWillMount()` ⇒ `render()` ⇒ `componentDidMount()`
    - update lifecycle:
        
        `componentWillReceiveProps()` ⇒ `shouldComponentUpdate()` ⇒ `componentWillUpdate()` ⇒ `render()` ⇒ `componentDidUpdate()`
        
    - unmounting lifecycle:
        
        `componentWillUnmount()`
        
    
    [ReactJS: Why is the convention to fetch data on componentDidMount?](https://stackoverflow.com/questions/39338464/reactjs-why-is-the-convention-to-fetch-data-on-componentdidmount)