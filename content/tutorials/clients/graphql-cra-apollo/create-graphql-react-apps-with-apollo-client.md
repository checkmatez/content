---
alias: aidae4aeg5
path: /docs/tutorials/create-react-apps-with-apollo-client
layout: TUTORIAL
preview: create-graphql-react-apps.png
description: Connecting to a GraphQL backend is really simple with create react app and Apollo Client.
tags:
  - react
  - clients
  - apollo
  - instagram
  - open-source
related:
  further:
    - koo4eevun4
    - nia9nushae
    - ol0yuoz6go
  more:
    - pheiph4ooj
    - ga2ahnee2a
---

# How to use `create-react-app` with GraphQL & Apollo

The easiest way to get started with React apps that are using a GraphQL backend is with [`create-react-app`](https://github.com/facebookincubator/create-react-app) and [Apollo](http://dev.apollodata.com/).
In this tutorial we will learn how to build an Instagram app, where users can view and post images. The complete code for this tutorial is available on [GitHub](https://github.com/graphcool-examples/react-apollo-instagram-example).

## Getting your GraphQL endpoint

For this tutorial you'll need a GraphQL project with the following schema:

```graphql
type Post {
  id: ID!
  description: String!
  imageUrl: String!
}
```

Using the [Graphcool CLI](https://www.npmjs.com/package/graphcool), you can generate a fully-fledged GraphQL server from your command line by providing this schema as an input argument to the `graphcool init` command:

1. In case you haven't already, install the Graphcool CLI: `npm install -g graphcool`
2. Save the schema from above in a file called `instagram.schema`
3. Create the GraphQL server: `graphcool init instagram.schema`

That's it, copy the endpoint for the `Simple API` since we'll need it later on. Notice that you can now also manage this project in the [Graphcool console](https://console.graph.cool) or explore it in a [GraphQL playground](https://www.graph.cool/docs/faq/tips-and-tricks-graphql-playground-ook6luephu/).


## Getting started with`create-react-app`

### Installation

With `create-react-app`, it's super easy to start out with a new React application! It comes with a lot of nice features out-of-the-box, such as a preconfigured [Webpack](https://github.com/webpack/webpack) and [Babel](https://babeljs.io/) setup for zero build configurations. Furthermore, features like [JSX](https://jsx.github.io/) and [ES6](http://es6-features.org/) syntax as well as static typechecking with [Flow](https://flowtype.org/) are already included. The generated boilerplate code also gives you a head-start with your new app.

If you don't have `create-react-app` installed on your machine yet, now is the time to do so with the following command:

```sh
npm install -g create-react-app
```

### Creating a new React app

After installing, we can easily create and run a new project from the command line:

```sh
create-react-app react-apollo-instagram-example
cd react-apollo-instagram-example
npm start # open http://localhost:3000
```

![](./cra.png)

Hot-reloading and linting is included as well, the terminal window keeps us updated about errors and linter problems.


## Integrating Apollo in a React Application

#### Installing the Dependencies

[Apollo Client](http://dev.apollodata.com/react/) is one of the most popular GraphQL clients available at the moment. It implements features like caching, optimistic UI, [query batching](!alias-ligh7fmn38) as well as [realtime updates using subscriptions](!alias-ui0eizishe) and generally makes interacting with a GraphQL backend a breeze.

We will need three dependencies to use it in our React application:

1. [`apollo-client`](https://github.com/apollographql/apollo-client): Contains the general functionality of Apollo Client
2. [`react-apollo`](https://github.com/apollographql/react-apollo): Implements React-specific bindings for Apollo
3. [`graphql-tag`](https://github.com/apollographql/graphql-tag): Provides functionality for parsing the [JavaScript template literals](http://exploringjs.com/es6/ch_template-literals.html) that will contain our GraphQL queries and mutations

You can install all three dependencies at once:

```sh
npm install apollo-client react-apollo graphql-tag --save
```

Additionally, we're using [React Router](https://github.com/ReactTraining/react-router) to deal with navigation in our app, let's go ahead install that as well:

```sh
npm install react-router --save
```

For styling, we're using [tachyons](http://tachyons.io/):

```sh
npm install tachyons --save
```

#### Mocking the needed Components

Let's first build the components needed for our app, where we want to display, create or delete posts. Afterwards, we'll inject the required data using Apollo and wire everything up with React Router.

These are the three components that we need:

* `ListPage` in `src/components/ListPage.js` that will list all posts in our backend

```js
import React from 'react'
import { Link } from 'react-router'

class ListPage extends React.Component {

  render () {

    return (
      <div className='w-100 flex justify-center'>
        <Link to='/create' className='fixed bg-white top-0 right-0 pa4 ttu dim black no-underline'>
          + New Post
        </Link>
        <div className='w-100' style={{ maxWidth: 400 }}>
          TODO: Display all posts...
        </div>
      </div>
    )
  }
}

export default ListPage
```

* `CreatePost` in `src/components/CreatePage.js` to create new posts

```js
import React from 'react'
import { withRouter } from 'react-router'

class CreatePage extends React.Component {

  state = {
    description: '',
    imageUrl: '',
  }

  render () {
    return (
      <div className='w-100 pa4 flex justify-center'>
        <div style={{ maxWidth: 400 }} className=''>
          <input
            className='w-100 pa3 mv2'
            value={this.state.description}
            placeholder='Description'
            onChange={(e) => this.setState({description: e.target.value})}
          />
          <input
            className='w-100 pa3 mv2'
            value={this.state.imageUrl}
            placeholder='Image Url'
            onChange={(e) => this.setState({imageUrl: e.target.value})}
          />
          {this.state.imageUrl &&
            <img src={this.state.imageUrl} role='presentation' className='w-100 mv3' />
          }
          {this.state.description && this.state.imageUrl &&
            <button className='pa3 bg-black-10 bn dim ttu pointer' onClick={this.handlePost}>Post</button>
          }
        </div>
      </div>
    )
  }

  handlePost = () => {
    // TODO: create post before going back to ListPage
    console.log(this.state)
    this.props.router.push('/')
  }
}

export default withRouter(CreatePage)
```

* `Post` in `src/componens/Post.js` to display and delete a single post

```js
import React from 'react'

class Post extends React.Component {

  render () {
    return (
      <div className='pa3 bg-black-05 ma3'>
        <div
          className='w-100'
          style={{
            backgroundImage: `url(${this.props.post.imageUrl})`,
            backgroundSize: 'cover',
            paddingBottom: '100%',
          }}
        />
        <div className='pt3'>
          {this.props.post.description}&nbsp;
          <span className='red f6 pointer dim' onClick={this.handleDelete}>Delete</span>
        </div>
      </div>
    )
  }

  handleDelete = () => {
    // TODO: delete post before reloading posts
    this.props.refresh()
  }
}

export default Post
```

#### Setting up React Router and Apollo Client

First, add these styles in `src/index.ccs`:

```js
@import 'https://fonts.googleapis.com/css?family=Open+Sans:300,400';

body {
  margin: 0;
  padding: 0;
  font-family: 'Open Sans', sans-serif;
}

html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
```

Now we're importing the needed packages in `src/index.js`

```js
import React from 'react'
import ReactDOM from 'react-dom'
import ListPage from './components/ListPage'
import CreatePage from './components/CreatePage'
import { Router, Route, browserHistory } from 'react-router'
import ApolloClient, { createNetworkInterface } from 'apollo-client'
import { ApolloProvider } from 'react-apollo'
import 'tachyons'
import './index.css'
```

Next we can create a new instance of `ApolloClient` below the import statements:

```js
const networkInterface = createNetworkInterface({
  uri: 'https://api.graph.cool/simple/v1/__PROJECT_ID__'
})

const client = new ApolloClient({
  networkInterface
})
```

> The `uri` that we have to pass to the `createNetworkInterface` call is the GraphQL endpoint for the `Simple API` that we generated in the first step using `graphcool init`. You can also retrieve that endpoint from the [Graphcool console](https://console.graph.cool) by selecting your project and then clicking the `ENDPOINTS`-button in the bottom-left corner.

Let's now setup the routes for our application:

```js
ReactDOM.render((
  <ApolloProvider client={client}>
    <Router history={browserHistory}>
      <Route path='/' component={ListPage} />
      <Route path='/create' component={CreatePage} />
    </Router>
  </ApolloProvider>
  ),
  document.getElementById('root')
)
```

Note that the `ApolloProvider` is wrapping `Router`, which enables all child components to access the functionality from Apollo Client to send queries and mutations.


## Using Apollo Client for Queries and Mutations

Now we are ready to use Apollo in our components to interact with the GraphQL API!

#### Querying all Posts in `ListPage`

To display all posts in `ListPage`, we're adding three new imports in `src/component/ListPage.js`:

```js
import Post from '../components/Post'
import { graphql } from 'react-apollo'
import gql from 'graphql-tag'
```

Apart from the `Post` component that renders a single post, we import `gql` and `graphql`. `gql` is used to create queries and mutations. [`graphql`](http://dev.apollodata.com/react/api.html#graphql) actually is a higher-order component that takes as input arguments one or more queries and/or mutations that were created with `gql` as well as a React component and injects the data from the query and/or the mutation function into the component as a prop.

First, let's think about the query to display all posts:

```graphql
---
endpoint: https://api.graph.cool/simple/v1/ciwce5xw82kh7017179gwzn7q
disabled: true
---
query allPosts {
  allPosts(orderBy: createdAt_DESC) {
    id
    imageUrl
    description
  }
}
---
{
  "data": {
    "allPosts": [
      {
        "id": "ciwcegunr21380122lvmexoga",
        "imageUrl": "https://images.unsplash.com/photo-1457518919282-b199744eefd6",
        "description": "#food"
      },
      {
        "id": "ciwcefwzhzhzg01229xcoi51f",
        "imageUrl": "https://images.unsplash.com/photo-1442407144300-e48b9dfe446b",
        "description": "#buildings"
      },
      {
        "id": "ciwcefmbs7tkz01260g21ncts",
        "imageUrl": "https://images.unsplash.com/photo-1450977894548-1b13203524f5",
        "description": "#nature"
      }
    ]
  }
}
```

At the end of the file, outside of the `Post` class, we are now adding the `FeedQuery` with `gql` which queries information about all our posts:

```js
const FeedQuery = gql`query allPosts {
  allPosts(orderBy: createdAt_DESC) {
    id
    imageUrl
    description
  }
}`
```

We're sorting the posts in descending order, so the latest posts appear on top of the list.

Now we're replacing the current `export` statement with this:

```js
export default graphql(FeedQuery)(ListPage)
```

This injects a new prop called  `data` to `ListPage`. Back in the `render` method of `ListPage`, we can first check if the data has already been loaded with `this.props.data.loading`. If `loading` is set to `false`, the data has arrived and we can `map` over `this.props.data.allPosts` to display the posts. We're also passing the `this.props.data.refetch` method to every post to reexecute the query after a post has been deleted.

Putting it all together, this is the `render` method that we end up with:

```js
render () {
  if (this.props.data.loading) {
    return (<div>Loading</div>)
  }

  return (
    <div className='w-100 flex justify-center'>
      <Link to='/create' className='fixed bg-white top-0 right-0 pa4 ttu dim black no-underline'>
        + New Post
      </Link>
      <div className='w-100' style={{ maxWidth: 400 }}>
        {this.props.data.allPosts.map((post) =>
          <Post key={post.id} post={post} refresh={() => this.props.data.refetch()} />
        )}
      </div>
    </div>
  )
}
```

#### Creating Posts in `CreatePage`

Adding mutations to React components is similar to adding queries, but instead of injected data, functions are injected for each mutation. Again, we need to import the Apollo related packages at the top of `src/components/CreatePage.js`:

```js
import { graphql } from 'react-apollo'
import gql from 'graphql-tag'
```

The mutation to create a new post looks as follows:

```graphql
---
endpoint: https://api.graph.cool/simple/v1/ciwce5xw82kh7017179gwzn7q
disabled: true
---
mutation addPost($description: String!, $imageUrl: String!) {
  createPost(description: $description, imageUrl: $imageUrl) {
    description
    imageUrl
  }
}
---
{
  "description": "#relax",
  "imageUrl": "https://images.unsplash.com/photo-1444492156724-6383118f4213"
}
---
{
  "data": {
    "createPost": {
      "description": "#relax",
      "imageUrl": "https://images.unsplash.com/photo-1444492156724-6383118f4213"
    }
  }
}
```

Now, at the end of the file, outside of the `CreatePage` class, but before the `export default withRouter(CreatePage)` statement, we can add a new mutation with `gql`:

```js
const addMutation = gql`
  mutation addPost($description: String!, $imageUrl: String!) {
    createPost(description: $description, imageUrl: $imageUrl) {
      id
      description
      imageUrl
    }
  }
`
```

Similar to the `FeedQuery` before, we now have to replace the `export` statement with the following code snippet:

```js
export default graphql(addMutation, {
  props: ({ ownProps, mutate }) => ({
    addPost: ({ description, imageUrl }) =>
      mutate({
        variables: { description, imageUrl },
      })
  })
})(withRouter(CreatePage))
```

This injects a new function to the props of `CreatePage` accessible with `this.props.addPost`. This function takes a `description` and the `imageUrl` as arguments so that we can provide the necessary info for each post that's being created. Using that, we can implement the `handlePost` method of the `CreatePage` class to create a post:

```js
handlePost = () => {
  const {description, imageUrl} = this.state
  this.props.addPost({ description, imageUrl })
    .then(() => {
      this.props.router.push('/')
  })
}
```

#### Deleting Posts in `Post`

To delete a post, we again need to import the Apollo Client related packages at the top of `src/components/Post.js`:

```js
import { graphql } from 'react-apollo'
import gql from 'graphql-tag'
```

Let's take a look at the mutation that we can use to delete a post from a playground:

```graphql
---
endpoint: https://api.graph.cool/simple/v1/ciwce5xw82kh7017179gwzn7q
disabled: true
---
mutation deletePost($id: ID!) {
  deletePost(id: $id) {
    id
  }
}
---
{
  "id": "ciwcegunr21380122lvmexoga"
}
---
{
  "data": {
    "deletePost": {
      "id": "ciwcegunr21380122lvmexoga"
    }
  }
}
```

Now we can define that `deletePost` mutation in our JavaScript code and then inject it into our component by wrapping it with the `graphql` function. As before, we'll do that right before the `export default Post` statement:

```js
const deleteMutation = gql`
  mutation deletePost($id: ID!) {
    deletePost(id: $id) {
      id
    }
  }
`

const PostWithMutation = graphql(deleteMutation, {
  props: ({ ownProps, mutate }) => ({
    deletePost: ({ id }) =>
      mutate({
        variables: { id },
      })
  })
})(Post)
```

Let's implement `handleDelete` method on our `Post` component:

```js
  this.props.deletePost({ id: this.props.post.id }).then(() => {
    this.props.refresh()
  })
```

Finally, we need to adjust the `export` statement like so:

```js
export default PostWithMutation
```


## Conclusion

That's it! Using `create-react-app` and Apollo Client, it's easy to write React applications that work with a GraphQL backend. If you want to dive deeper in the example code, you can check it out on [GitHub](https://github.com/graphcool-examples/react-apollo-instagram-example).

For a more comprehensive tutorial, checkout [Learn Apollo](https://learnapollo.com), a hands-on guide for Apollo Client created by Graphcool.
