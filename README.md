## React Updating Lifecycle

1. Parent `render()` called for updating

   1-1. Parent passes new props to Child

2. Child `componentWillReceiveProps(nextProps)`

   2-1. Child `setState(newState)` according to `nextProps` to update child's state. This won't trigger another `shouldComponentUpdate` but keep proceeding to (3) with the updated state.

3. Child `shouldComponentUpdate(nextProps, nextState)`
   
   3-1. Compare the new/old props and state and return `true` if should proceed to (4), otherwise `false`

4. Child `componentWillUpdate(nextProps, nextState)`

5. Child `render()` to render UI for user

6. Child `setState(newState)` based on some user actions. Go to (3) .
