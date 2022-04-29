React Redux 用法

class component用法：

```
const mapStateToProps = (state) => ({ todos: state.todos })
const mapDispatchToProps = (dispatch) => {
  return {
    // dispatching plain actions
    increment: () => dispatch({ type: 'INCREMENT' }),
    decrement: () => dispatch({ type: 'DECREMENT' }),
    reset: () => dispatch({ type: 'RESET' }),
  }
}

class A {
...
}

export default connect(mapStateToProps, mapDispatchToProps)(A)
```

hook用法：

```
import React from 'react'
import { useSelector, useStore } from 'react-redux'

export const CounterComponent = () => {
  const counter = useSelector(state => state.counter)
  const dispatch = useDispatch()
  const store = useStore()
  store.dispatch({type: 'increment-counter'})

  return <div onClick={() => dispatch({ type: 'increment-counter' })}>{counter}{store.getState()}</div>
}
```

