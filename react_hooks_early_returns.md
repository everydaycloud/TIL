# React Hooks and Early Returns

React wants to reder the same number of hooks every time. If it doesn't
it'll throw an error/warning. If an early return will mean that a hook that was
previously rendered won't be there on re-render, then it should be given default
values, so that it can be rendered in that form in case of an early return.
