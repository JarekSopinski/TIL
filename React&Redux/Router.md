## React Router

### Introduction - setting up Router

Routing in React is handled by react-router-dom module. To use it, we have to import BrowserRouter to our root component and put all its content inside BrowserRouter tags.

    import { BrowserRouter } from 'react-router-dom';

    class App extends Component {

      render(){
        return (
          <BrowserRouter>
            <div className="my-app">
              <Navbar />
              <Footer />
            </div>
          </BrowserRouter>
        );
      }

    }

### Adding Routes

Now, if we want to add component for specific pages with specific routes, we have to import Route and use it to set path for each component.

    import { BrowserRouter, Route } from 'react-router-dom';
    import Home from './components/Home';
    import About from './components/About';
    import Contact from './components/Contact';

    class App extends Component {

      render(){
        return (
          <BrowserRouter>
            <div className="my-app">
              <Navbar />
                <Route exact path='/' component={Home} />
                <Route path='/about' component={About} />
                <Route path='/contact' component={Contact} />
              <Footer />
            </div>
          </BrowserRouter>
        );
      }

    }

Note the 'exact' keyword on home path. If it wasn't added, Home component would be also loaded on other paths.

### Adding Links

Now, let's say that inside Navbar component we have a menu with a couple of links. If we'd use normal anchors, we would still make new requests to the server. To prevent this, we have to use React's Links instead.

    import { Link } from 'react-router-dom';

    const Navbar = () => {
        return (
            <nav>
                <ul>
                    <li><Link to="/">Home</Link></li>
                    <li><Link to="/about">About</Link></li>
                    <li><Link to="/contact">Contact</Link></li>
                </ul>
            </nav>
        )
    }

As an alternative we can use NavLink. The main difference is that NavLinks anchors will have 'active' class added for current routes.

    import { NavLink } from 'react-router-dom';
    ...
    <li><NavLink to="/">Home</NavLink></li>

### Accessing Router data

Any component that was added as a Route will automaticly have access to routing data and methods, that are added to its props. They are contained in objects: history, location and match.

Let's say that we want to perform a redirect inside a functional component:

    const Contact = (props) => {
        props.history.push('/somepage');
    }

If a component isn't added as a Route, it won't have access to this data by default. A solution is to export this component inside withRouter - a higher order component, that can be imported from 'react-router-dom':

    import { withRouter } from 'react-router-dom';

    const Contact = (props) => {
        ...
    }

    export default withRouter(Contact);

### Route params

A Route component can have path based on our custom parameter. For example:

    <Route path="/:post_id" component={Post} />

If we want to generate some specific content based on param's value (in this example, post_id), we can do it be accessing routing data, as it was described in a previous section. All params are available inside props.match.params object.

class Post extends Component {

    state = {
        id: null
    }

    componentDidMount(){
        let id = this.props.match.params.post_id;
        this.setState({
            id: id
        })
    }

    render (){
        return (
            <div>
                <p>This post id is {this.state.id}</p>
            </div>
        )
    }

}

### Switch tag

If we want the Router to load only one (first) component that matches the path, we can additionally wrap our Routes inside Switch, that is imported from 'react router-dom'.

    class App extends Component {

      render(){
        return (
          <BrowserRouter>
            <div className="my-app">
              <Navbar />
                <Switch>
                    <Route exact path='/' component={Home} />
                    <Route path='/about' component={About} />
                    <Route path='/contact' component={Contact} />
                    <Route path="/:post_id" component={Post} />
                </Switch>
              <Footer />
            </div>
          </BrowserRouter>
        );
      }

    }

In this example on the path '/contact' only Contact component will be mounted. If Switch wasn't used, Post component would also be mounted, because Router would consider 'contact' a possible post_id param from the Post component.