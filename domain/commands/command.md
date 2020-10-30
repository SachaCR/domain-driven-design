# Commands

> Change the state of a system but do not return a value.
>
> It's the opposite of queries : Return a result and do not change the observable state of the system (are free of side effects).
>
> The really valuable idea in this principle is that it's extremely handy if you can clearly separate methods that change state from those that don't. This is because you can use queries in many situations with much more confidence, introducing them anywhere, changing their order. You have to be more careful with modifiers.
