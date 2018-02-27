---
title: "ReactStatelessFunctionalComponent"
date: 2017-12-21T16:58:40-05:00
draft: true
---

This is different from **class based component**.



---

What is stateless function

A stateless function is simply a function without keep tracking state. The idea is to simplify the simply presentational (class based) components, without managing complex state. 

- It can still accept props.
- Its faster than class based function.




A comparison example show the difference between class based and stateless.


```react
// Stateless functional components
const Action = (props) => {
    return (
        <div>
            <button 
                onClick={props.handlePick} 
                disabled={!props.hasOptions}>
                Todays menu!
            </button>
        </div>
    );
};
```

Note that, there is no `this.` in front of `props` anymore, since we can access `props` directly.

```react
// Class based components
class Action extends React.Component {
    render() {
        return (
            <div>
                <button 
                    onClick={this.props.handlePick} 
                    disabled={!this.props.hasOptions}>
                    Todays menu!
                </button>
            </div>
        );
    }
}
```





One more comparison example:

```react
// Stateless functional component
const Header = (props) => {
    return (
        <div>
            <h1>{props.title}</h1>
            <h2>{props.subtitle}</h2>
            <h3>Show me something interesting!</h3>
        </div>
    );
};

// Class based component
class Header extends React.Component {
    render() {
        return (
            <div>
                <h1>{this.props.title}</h1>
                <h2>{this.props.subtitle}</h2>
                <h3>Show me something interesting!</h3>
            </div>
        );
    }
}
```

