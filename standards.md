*“All code in any code-base should look like a single person typed it, no matter how many people contributed.”*

# **Table of Contents**
1. [Introduction](#Introduction)
2. [Imports](#Imports)
3. [JS & JSX Operators](#JS-&-JSX-Operators)
4. [Function Components](#Function-Components)
5. [Function Forms](#Function-Forms)
6. [React Hooks](#React-Hooks)
7. [Asynchronus Functions](#Asynchronous-Functions)
8. [Conditional Rendering](#Conditional-Rendering)
9. [Alphabetization](#Alphabetization)
10. [Naming](#Naming)

# Introduction

The overarching princple of these React Standards is AHA, which stands for "Avoid Hasty Abstraction." This principle prefers duplication over wrong abstractions because duplication is far cheaper than wrong abstraction.

See the blogs below for more about AHA:

[AHA Programming 💡](https://kentcdodds.com/blog/aha-programming)

[The Wrong Abstraction - Sandi Metz](https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction)

### Core Values

- Readability - Favor understandability at the expense of time-to-write
    - Code should be easily readable and understandable for software engineers of all levels
    - Prefer *explicit-but-verbose* over *concise-but-implicit*
        - Example: `initialState` instead of `initSt`
- Maintainability
    - Code should be written in a way it can be easily refactored or changed for the future
    - Favor patterns that make the code more robust

        *"It’s desirable to reduce the odds that a future change (or a conflicting change in another branch) introduces a non-obvious bug and to facilitate finding bugs, because we spend far more time extending and debugging than implementing."*

See the article below for more details about coding guidlines:

[Bungie C++ Guidelines & Razors > News | Bungie.net](https://www.bungie.net/en/News/Article/50666)

---

# Imports

```
1. React
2. PropTypes
3. React Router
4. Redux
6. Internal Dependencies
7. Other Dependencies
8. Files from this project (absolute relative to src)
9. Files from this project relative to this file (using ..)
```

This import order is mostly based on frequency and importance of use across the whole project to it's easier for us to find things in more or less the same spot in every file. The pattern above is what I saw in many files, and so I tried to do it that way.

**Do:**

```jsx
import React from 'react'
import PropTypes from 'prop-types'
import { Route, useParams } from 'react-router-dom'
import { bindActionCreators } from 'redux'
import { LazyImage } from '@youversion/react'
import moment from 'moment'
import * as AttachmentActions from 'state/actions/attachments'
import StatusBadge from 'components/Plans/StatusBadge'
import PlanEdit from './edit'

```

**Do Not:**

```jsx
import PlanEdit from './edit'
import * as AttachmentActions from 'state/actions/attachments'
import { bindActionCreators } from 'redux'
import { LazyImage } from '@youversion/react'
import React from 'react'
import moment from 'moment'
import PropTypes from 'prop-types'
import StatusBadge from 'components/Plans/StatusBadge'
import { Route, useParams } from 'react-router-dom'
```

---

# JS & JSX Operators

Use `Boolean()` over `!!` for explicit readability.
See this article for mor edetails - [!! vs. Boolean()](https://gist.github.com/arthurvi/66cb1e2bcfc92f99f465e0db04264367)

---

Use template literals ``${ }`` over string concatenation `+` for explicit readability.

**Do:**

```jsx
let name = "Notion"
let string = `Hello ${name}`
```

**Do Not:**

```jsx
let name = "Notion"
let string = "Hello " + name
```

---

Use Fragments if the wrapper `div` does not have styles

- This keeps the code clean and is also beneficial for performance because one less node is created in the virtual DOM.
- Note that `React.Fragment` can only take in `key` as a prop

**Do:**

```jsx
return (
  <>
     <Component1 />
     <Component2 />
  </>  
)
```

**Do Not:**

```jsx
return (
  <div>
     <Component1 />
     <Component2 />
  </div>  
)
```

---

Use self-closing tags for elements without children for clean code.

**Do:**

```jsx
<input />
<Divider />
```

**Do Not:**

```jsx
<input></input>
<Divider></Divider>
```

---

Use strict equality operators,  `===` over `==` and `!==` over `!=` for accuracy. `==` and `!=` converts the operands into the same type if they are different and then compares the values. When making comparisons, we want the types to already be equal for accuracy and consistency across the project. 

---

Use **Nullish Coalescing** `??` over `||` for checking specificially for `null` or `undefined` for accuracy and readability. The OR operator `||` can be problematic if the left value might contain `""` `0` or `false` 

**Do:**

```jsx
const environment = process.env.NODE_ENV ?? 'development'
```

**Do Not:**

```jsx
const environment = process.env.NODE_ENV || 'development'
```

---

Use **Optional Chaining** for checks related to objects for clean code and readability

**Do:**

```jsx
if (window?.location?.href) {
	// do something
}
```

**Do Not:**

```jsx
if (typeof window !== undefined && !!window.location && !!window.location.href) {
	// do something
}
```

---

# Function Components

Classes in JavaScript are not pure Classes. They are a pseudo-class, just *syntactic sugar*. Although syntactically identical to the class signature of other languages, JavaScript classes are intrinsically different. When `Class` is used, JavaScript runs `Object` methods under-the-hood to create that "class."

See this blog for more about under-the-hood for `Class` - [How JavaScript Classes Work Under the Hood](https://medium.com/siliconwat/how-javascript-classes-work-80b0cf483b1d)

For all components, we will use function components over Class ***in order to use Hooks***. 

> *["Conceptually, React components have always been closer to functions. Hooks embrace functions, but without sacrificing the practical spirit of React."](https://reactjs.org/docs/hooks-intro.html)*

The following reasons are from [React documentation](https://reactjs.org/docs/hooks-intro.html) for using Hooks:

1. It’s hard to reuse stateful logic between components. 
    - Hooks allow you to reuse stateful logic without changing your component hierarchy.
2. Complex components become hard to understand
    - Hooks let you split one component into smaller functions based on what pieces are related (such as setting up a subscription or fetching data)
3. Classes confuse both people and machines
    - Hooks let you use more of React’s features without classes.

See this documentation for more details about hooks - [Introducing Hooks - React](https://reactjs.org/docs/hooks-intro.html)

---

# Function Forms

We use two types of function forms: function expression (arrow function) and function declaration.

When to use arrow function:

- Function is being passed as a ***callback***
- Leverage the implicit return or lexical scope feature

For every other cases, we use a ***function declaration***. The reason for using function declaration is **hoisting**. Function declarations are moved to the top of their scope before code execution regardless of whether their scope is global or local. Arrow functions can sometimes cause `Uncaught TypeError: undefined is not a function`

**Do:**

```jsx
function Plan() { 
  const [state, setState] = React.useState(() => window.localStorage('state'))
 
  function handleClick(event) {
   // handle the click event
  }

 return (
    <>
      <PlanContainer />
      <Button
        onClick={handleClick}
      />
    </>
  )
}
```

**Do Not:**

```jsx
const Plan = () => { 
	const [state, setState] = React.useState(window.localStorage.getItem('state'))
	
	const handleClick = (event) => {
		// handle the click event
	}

	return (
		<>
			<PlanContainer />
			<Button
				onClick={handleClick}
			/>
		</>
	)
}
```

See the article below for more details:

[Function forms](https://kentcdodds.com/blog/function-forms)

---

# React Hooks

When using React Hooks, we import React without destructuring the hook. This is for:

**Readability:** The explicit `React.useState()` makes is very obvious that we are using React Hook. When there is usage of a function prefixed by `use` it is immediately obvious that it is a custom hook.

**Maintainability:** When adding or removing a specific hook, we can make that change in just that code without having to change the import statement.

Here is an example of how Hooks simplify code in reading, writing, and understanding:

```jsx
// With Hooks
React.useEffect(()=>{
	organizationMemberships.load({organization_id: orgId})
}, [orgId])

// Without Hooks
componentWillMount() {
	const {
		orgId,
		actions: { organizationMemberships}
	} = this.props
	organizationMemberships.load({ organization_id: orgId })
}

componentWillReceiveProps(nextProps) {
	// When organization is changed, reload users
	const {
		orgId,
		actions: { organizationMemberships }	
	} = this.props

	if (nextProps.orgId && nextProps.orgId !== orgId) {
		organizatioNMemberships.load({ organization_id: orgId })
	}
}
```

**Do:**

```jsx
import React from 'react'

function App() {
	const [plans, setPlans] = React.useState({})

	// return App
}
```

**Do Not:**

```jsx
import React, { useState } from 'react' // Don't import useState here

function App() {
	const [plans, setPlans] = useState({})

	// return App
}
```

---

### Fetching Data

Use the pattern in the example below when fetching data from an API. This pattern ensures that the components waits to load until API response is complete and be more protected bugs related to render ordering. (Need to find better wording for this).

```jsx

import { useRouteMatch } from 'react-router-dom'
import { statusTypes } from '@youversion/react' // Helper object for status enums 'pending', 'idle', 'resolved', 'rejected'

function MyComponent() {
  const { path } = useRouteMatch()

  const [loadingStatus, setLoadingStatus] = React.useState(statusTypes.PENDING)
  const [data, setData] = React.useState()
  
  React.useEffect(() => {
    async function loadData() {
      setLoadingStatus(statusTypes.PENDING)
      try {
        const response = await fetchData()
        setData(response)
        setLoadingStatus(statusTypes.RESOLVED)
      } catch (error) {
        setLoadingStatus(statusTypes.REJECTED)
      }
    }
    
    loadData()
  }, [path]) // Effect will run on initial load, and when `path` changes.
  
  if (loadingStatus === statusTypes.PENDING) {
    return <LoaderOverlay />
  }
    
  if (loadingStatus === statusTypes.REJECTED) {
    return <ErrorComponent />
  }
    
  return (
    <>
	    { data.length ? data.map(/* do stuff here */) : 'no data' }
    </>
  )
}\
```

---

# Asynchronous Functions

### API Calls

This is a question of using `async/await` vs. `then/catch` for making API calls.

Both methods do the same thing, but using `async/await` gives better flow control and leads to cleaner code and keeps it reading synchronously. 

In addition, we want to avoid *callback hell* that can potentially come with `then/catch`
*See this video for more about using `async/await` - [Async + Await in JavaScript, talk from Wes Bos](https://www.youtube.com/watch?v=DwQJ_NPQWWo)*

When making multiple, independent API calls, we can batch them together by using `Promise.all` with `async/await`
*See this article for more details - [Async Await vs then/catch](https://medium.com/@dio.hamidou/async-await-vs-then-catch-4f64d42e6392)*

**Do:**

```jsx
async function handleSubmit() {
	try {
		const planResponse = await API.get({path, token})
		// planResponse can be used anywhere following inside this `try` block
	} catch(error){
    // Handle error as needed.
		console.error(error.message)
	}
}
```

**Do Not:**

```jsx
function handleSubmit() {
	fetch(path, options)
		.then(data => data.json)
		.then(planResponse => {
			// planResponse is only accessible inside this `then` block
		})
		.catch(error => {
      // Handle error as needed.
			console.error(error.message)
		})
}
```

---

# Conditional Rendering

When writing `if` statements, always use curly brackets, even if it is one line. When looking at it by itself, it's easy to understand, but when looking at numerous lines, the curly brackets makes it much easier to follow the logic of what's happening before & after. In addition, it is quicker to debug with curly brackets with `console.log` or `debugger`

When rendering with conditionals, use ternaries `condition ? true : null` rather than `&&` . Using `&&` can be prone to errors. For example, in `firstExpression && secondExpression` , according to MDN, 

*"If `firstExpression` can be converted to `true`, return `secondExpression`; else, return `firstExpression` ."* 

For cases when the first expression is `array.length` which evaluates to `0`, the conditional will render `0` instead of the desired "nothing."

**Guard Clause** - If a child component expects a state from its parent component, there should be a check for that state that returns null if false

```jsx
if (!stateFromParent) {
	return null
}
```

See the article for more details - [Use ternaries rather than && in JSX](https://kentcdodds.com/blog/use-ternaries-rather-than-and-and-in-jsx)

`Boolean(array.length) && <Component />` or `array.length > 0 && <Component />` can solve this issue but we prefer to be *explicit but verbose* for readability and maintainability. 

**Do:**

```jsx
// best
function Plan () {
	if (!plan.id) {
		return <LoaderOverlay />
	}
	
	return (
		<PlanDays />
	)
}

function PlanDays(planDays){ // In its own file.
	if (planDays.length === 0) {
		return null
	}
	// return component
}

// good as a last resort
function Plan () {
	return (
		{ 
			array.length 
				?	<PlanDays />
				: null
		}
	)
}

```

**Do Not:**

```jsx

return (
		planDays.length && <PlanDays /> // Don't use && for returning component
)
```

---

# Alphabetization

Keys in objects and props should always be ordered alphabetically (including in HTML and in CSS). This makes it easier and faster for developers to find variables throughout all files. ESLint should alphabetize for us.

**Do:**

```jsx
function Plan ({
	categories,
	comments,
	organization,
	planId,
	user
}) { 

	return (
		<PlanContainer
			class={classes.plan}
			days={plan.days}
		/>
	)
}
```

**Do Not:**

```jsx
function Plan ({
	user,
	categories,
	planId,
	comments,
	organization,
}) { 

	return (
		<PlanContainer
			days={plan.days}
			class={classes.plan}
		/>
	)
}
```

---

# Naming

We follow the naming convention set in this guide - [GitHub - kettanaito/naming-cheatsheet](https://github.com/kettanaito/naming-cheatsheet)

---