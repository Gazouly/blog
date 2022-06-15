## Bring Vue Named Slots to React

While I was working on a React.js side project using [Semantic UI React](https://react.semantic-ui.com/) as a UI library, I noticed that its components are written in a way that reminds me of Vue.js components I write.

If you’ve been writing Vue.js web apps or you took a tour at their documentations, you probably know slots. If not, then let me take you on my tour through this article.

## What are Vue Slots?
> *Vue implements a content distribution API inspired by the [Web Components spec draft](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), using the `<slot />` element to serve as distribution outlets for content.*

Let’s take an example. If you want to pass a DOM into a child component, we can do the following:

```jsx
<navigation-link url="/profile">
  Your Profile
</navigation-link>
``` 
then in the component of `navigation-link`, you might have

```jsx
<a 
  :href="url"
  class="nav-link"
>
   <slot></slot>
</a>
``` 
When the component renders, `<slot></slot>` will be replaced by “Your Profile”.

You may wonder and tell yourself where is the difference? It’s doable, and I can access my DOM elements via `props.children`

![](https://media.giphy.com/media/AZ1PPDF8uO9MI/giphy.gif)
Okay, you’re right you can do this, but there’s a cool feature called **named slots** that you don’t know about. Let’s find out what it is.

## Vue Named Slots
There are times when it’s useful to have multiple slots. For example, in a `<default-layout>` component with the following structure:

```jsx
<div class="container">
  <header>
    <!-- We want header content here -->
    <slot name="header"></slot>
  </header>
  <main>
    <!-- We want main content here -->
    <slot name="body"></slot>
  </main>
  <footer>
    <!-- We want footer content here -->
    <slot name="footer"></slot>
  </footer>
</div>
``` 
We need to pass a piece of code to every slot. For example, the header slot will be replaced with `<NavbarComponent />` and so on with the rest of the slots.

To provide content to these slots to `v-slot` directive on a `<template>` like the following snippet:

```jsx
<default-layout>
  <template v-slot:header>
    <NavbarComponent />
  </template>
  
  <template v-slot:body>
    <!-- <router-view> is component that renders 
         the matched component for the given path -->
    <router-view>
  </template>

  <template v-slot:footer>
    <FooterComponent />
  </template>
</default-layout>
``` 
Now, everything inside `<template>` elements will be passed to the corresponding slots. Cool right?
![](https://media.giphy.com/media/62PP2yEIAZF6g/giphy.gif)

The question that is running on your mind now is How can I do that in React? Let’s take a look at how we could do it in React

Since `props.children` is an array of elements, we can implement this cool feature in React by using `Array.prototype.find` like this:

```jsx
const Header = () => null

const Body = () => null

const Footer = () => null

const DefaultLayout  = ({ children }) => {
  
    const header = children.find(el => el.type === Header)
    const body = children.find(el => el.type === Body)
    const footer = children.find(el => el.type === Footer)
    
    return (
       <div class="container">
        <header>
          {header ? header.props.children : null}
        </header>
        <main>
          {body ? body.props.children : null}
        </main>
        <footer>
          {footer ? footer.props.children : null}
        </footer>
      </div>     
    )
}

DefaultLayout.Header = Header
DefaultLayout.Body = Body
DefaultLayout.Footer = Footer

export default DefaultLayout
``` 
If you use class-based components it’ll be like this:

```jsx
function Header() {
  return null
}

function Body() {
  return null
}

function Footer() {
  return null
}

class DefaultLayout extends Component {
  static Header = Header
  static Body = Body
  static Footer = Footer

  render() {
    const { children } = this.props
    const header = children.find(el => el.type === Header)
    const body = children.find(el => el.type === Body)
    const footer = children.find(el => el.type === Footer)
    
    return (
      <div class="container">
        <header>
          {header ? header.props.children : null}
        </header>
        <main>
          {body ? body.props.children : null}
        </main>
        <footer>
          {footer ? footer.props.children : null}
        </footer>
      </div>      
    )
  }
}

export default DefaultLayout
``` 
So, we’ve just implemented or slots? Let’s use them

```jsx
import React from 'react'
// Layout
import DefaultLayout from '@layouts/DefaultLayout'
import NavbarComponent from '@layouts/components/NavbarComponent'
import FooterComponent from '@layouts/components/FooterComponent'
// Containers
import Home from '@containers/Home/Home'
import NotFound from '@containers/404/NotFound'

import { Route, Switch, Redirect } from 'react-router-dom'

const App = () => (
<>
	<DefaultLayout>
	  <DefaultLayout.Header>
		<NavbarComponent />
	  </DefaultLayout.Header>
	  <DefaultLayout.Body>
		<Switch>
		  <Route exact path='/' component={Home} />
		  <Route path='/404' component={NotFound} />
		  <Redirect to='/404' />
		</Switch>
	  </DefaultLayout.Body>
	  <DefaultLayout.Footer>
		<FooterComponent />
	  </DefaultLayout.Footer>
	</DefaultLayout>
</>
)

export default App
``` 
Now your React components are more readable. Happy Hacking! 🎉🍻








