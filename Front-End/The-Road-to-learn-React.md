# The Road to learn React

- JS in JSX
    - don’t make the mistake of using index of the item in the array; when. the list changes its order, React will have a hard time to identify the items properly
        
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
    
- JS `filter` function doesn’t mutate the old list, it returns a new one

### Bindings

- avoid bind this in `render()`, it would bind multiple times whenever components update
- **Event Handler**
    
    ```
    <button onClick={this.onDismiss(item.objectID)} type="button">
        Dismiss
    </button>
    ```
    
    - the `onDismiss()` would execute immediately when you open the application in your browser. The expression in the handler is **evaluated**
    - using `onClick={this.onDismiss}` can avoid being evaluated, but we cannot input params
- **Interactions with Forms and Events**
    - React’s `this.setState()` is a shallow merge, it preserves the sibling. properties in the state object when updating one sole property in it.
- **controlled components**
    - `<input>` `<textarea>` `<select>` hold their own state in plain HTML — **uncontrolled component**, in React, you should make sure to make those elements **controlled components**
        
        ```jsx
        render() {
            const { searchTerm, list } = this.state;
            return (
              <div className="App">
                <form>
                    <input type="text" value={searchTerm} onChange={this.onSearchChange />
                </form>
                ...
            </div>);
        }
        ```
        
- **Component Declarations**
    - **Functional Stateless Components:** don’t have lifecycle methods
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

- **LifeCycle Methods**[ReactJS: Why is the convention to fetch data on componentDidMount?](https://stackoverflow.com/questions/39338464/reactjs-why-is-the-convention-to-fetch-data-on-componentdidmount)
    - `constructor()` ⇒ `componentWillMount()` ⇒ `render()` ⇒ `componentDidMount()`
    - update lifecycle:
        
        `componentWillReceiveProps()` ⇒ `shouldComponentUpdate()` ⇒ `componentWillUpdate()` ⇒ `render()` ⇒ `componentDidUpdate()`
        
    - unmounting lifecycle:
        
        `componentWillUnmount()`
        
- ES6 Spread Operators
    
    ```jsx
    // use Object.assign() to update
    onDismiss(id) {
      const isNotId = item => item.objectID !== id;
      const updatedHits = this.state.result.hits.filter(isNotId);
      this.setState({
        result: Object.assign({}, this.state.result, { hits: updatedHits })
    	}); 
    }
    
    // use ES6 spread operator
    onDismiss(id) {
      const isNotId = item => item.objectID !== id;
      const updatedHits = this.state.result.hits.filter(isNotId);
      this.setState({
        result: { ...this.state.result, hits: updatedHits }
    	}); 
    }
    ```
    
- Conditional Rendering
    
    ```jsx
    { result ? <Table list={result.hits} pattern={searchTerm}
                onDismiss={this.onDismiss}/> : null }
    ```
    
    ```jsx
    { result && <Table list={result.hits} pattern={searchTerm} 
    						 onDismiss={this.onDismiss}/> }
    ```
    
- **Error Handling**
    - Error Handling: local state and conditional rendering. Basically the error is only **another state** in React
- **Axios instead of fetch**
    - prevent setting state on unmounted component
        - request in `componentDidMount()` is not completed when navigating to a new page. It will attempt to use `this.setState()` eventually in the `then()` or `catch()` promise. And. you will see the warning
            
            ```jsx
            Warning: Can only update a mounted or mounting component. This usually means you\
             called setState, replaceState, or forceUpdate on an unmounted component. This i\
            s a no-op.
            ```
            
        - Introduce a class field which holds the lifecycle state of your component
            
            ```jsx
            class App extends Component {
              _isMounted = false;
              constructor(props) {
                ...
            	}
            
            	componentDidMount() {
                this._isMounted = true;
                const { searchTerm } = this.state;
                this.setState({ searchKey: searchTerm });
                this.fetchSearchTopStories(searchTerm);
            	}
            
            	componentWillUnmount() {
                this._isMounted = false;
            	}
            
            	fetchSearchTopStories(searchTerm, page = 0) {
                axios(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}\
            ${page}&${PARAM_HPP}${DEFAULT_HPP}`)
                  .then(result => this._isMounted && this.setSearchTopStories(result.data))
                  .catch(error => this._isMounted && this.setState({ error }));
            	}
            }
            ```
            

### Code organization and Testing

- as many as unit tests; a few end-to-end tests to simulate critical scenarios

### Higher-order Component

- **HOCs** are an equivalent to **higher-order functions**. They take any input - most of the time a component, but also optional arguments - and return a component as output.
    
    ```jsx
    function withFoo(Component) { 
    	return function(props) {
    		return <Component { ...props } />; 
    	}
    }
    
    const withFoo = (Component) => (props) => <Component {...props} />
    ```
    
- use cases: prepare properties; manage state or. alter the representation of a component