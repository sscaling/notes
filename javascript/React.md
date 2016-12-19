# React

Mainly ripped / condensed from: [https://facebook.github.io/react/docs]()


## Design / Impl Process

[https://facebook.github.io/react/docs/thinking-in-react.html]()

1. Workout the layout
2. Statically design site (no interaction / connectivity to datasource)
    1. Components only implement render()
    2. No use of this.state (only this.props - state is reserved for interactivity)
3. Questions to ask to determine state
    1. Is it passed in from a parent via props? If so, it probably isn't state.
    2. Does it remain unchanged over time? If so, it probably isn't state.
    3. Can you compute it based on any other state or props in your component? If so, it isn't state.

## Component

* Component names must always start with a capital letter
* Components must return single root element

Example:

	function Test(props) {
		return <h4>Testing for {props.name}</h4>
	}
	const element = <Test name="foo”/>;


### Internal

* this.props - the attributes of the component syntax, converted to a JS Object
    *
    <Thing foo={{purple:{monkey:’dishwasher’}}} /> -> this.props.purple.monkey etc…
* this.state - internal state of component that is used for things that will make it to the render() function
    * Can only assign in the constructor (i.e. this.state = {})
    * Should only be updated by calling the this.setState({}) method
    * Everything else can just be a class property

### Dom elements with style

	<h1 style={{color: 'red'}}>Hello</h1>

* outside-braces are defining a java script expression
* inside-braces define the javascript style object

### CSS Classes

	.monkey { color: blue; }
	<p className="monkey">foo</p>

(className is used to reference a CSS class)


### Components as classe

Provided additional functionality.

* state
* constructor
* lifecycle

Example:

	class Clock extends React.Component {
	  constructor(props) {
	    super(props);
	  }

	  //  lifecycle: added to DOM
	  componentDidMount() { }

	  // lifecycle: removed from DOM
	  componentWillUnmount() { }

	  render() {
	    return <h1>Foo</h1>;
	  }
	}

**setState** prototypes

* setState(newState)
* setState(function(prevState) { return newState; })
* setState(function(prevState, props) { return newState; })

React can batch multiple calls together. Cannot rely on current value for calculations. Second form of setState, takes previous state and props.

	// ES6
	this.setState((prevState, props) => ({
	  counter: prevState.counter + props.increment
	}));

	// ES5
	this.setState(function(prevState, props) {
	  return {
	    counter: prevState.counter + props.increment
	  };
	});

Changes are merged into current this.state object

### React Events

* camelCased naming
* must preventDefault explicitly in the function

Example:

	function ActionLink() {
	  function handleClick(e) {
	    e.preventDefault();
	    console.log('The link was clicked.');
	  }

	  return <a href="#" onClick={handleClick}>Click me</a>;
	}

### Synthetic Events

* support preventDefault / stopPropagation
* Wraps events to provide consistent interface
* Supported events: [https://facebook.github.io/react/docs/events.html]()

### Multi element keys

* Provide a stable id for elements inside an array
* A key must only be unique amongst siblings
* 'key' is not passed in as a prop value

Preferred:

	const todoItems = todos.map((todo) =>
		<li key={todo.id}>{todo.text}</li>
	);

If data doesn’t have a natural key for key:

	const todoItems = todos.map((todo, index) =>
		<li key={index}>{todo.text}</li>
	);

### Controlled Components

*  Giving React control of state of external objects (such as forms)

### Containment

* For when components don’t know their children ahead of time. use props.children

Example:

	function Test(props) {
		return <div>{props.children}</div>;
	}

Used in situations such as:

	// one + two divs get passed as props.children
	<Test><div>one</div><div>two</div></Test>
