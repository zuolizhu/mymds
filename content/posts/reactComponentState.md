---
title: "ReactComponentState"
date: 2017-11-28T21:30:21-05:00
draft: true
---

## Components state allows components to manage data.
Think about *an object* with various key value pairs and when that *data(state) changes*, the
component will automatically re-render to reflect those changes.
Component state is essential for interactive applications.

step 1: Setup default state object.
step 2: Component rendered with default state values.
step 3: Change state based on event.
step 4: Component re-rendered using new state values.
step 5: Start again at step 3.

### Try to avoid this.setState usage like below
---
```
this.setState({
    count: 0
});
this.setState({
    count: this.state.count + 1
});
```
This won't reset count and add 1. The result won't be `count : 1`.
Becasuse calling this.setState is *asynchronous*. The count won't change on the very next line.