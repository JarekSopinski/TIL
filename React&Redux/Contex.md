## React Context

Context system allow passing data between parent component and any of its children. Components don't have to be directly related.

There are two ways we can get information into the Context object and two ways we can get them out.

To get information into Context, we can either set up a Default Value or create Provider object.

To get information out of the Context, we can either reference this.context property inside nested component or inside nested component create a Consumer component.

To create simple Context object with some default value:

    import React from "react";
    const defaultValue = 'foo';
    export default React.createContext(defaultValue);

Then we can reference this in some child component:

    import OurContext from "../contexts/OurContext";

    class Bar extends React.Component {
        static contextType = OurContext;
        foo(){
            console.log(this.context)
        }
    }

### Provider

Inside the parent component we can create a Provider, that will set or update Context values. Updated value can (but don't have to) i.e. come from state.

    import OurContext from "../contexts/OurContext";

    class App extends React.Component {

        state = {
            foobar: 'bar'
        };

        render(){
            return (
                <OurContext.Provider value={this.state.foobar}>
                    <OurChildComponent />
                </OurContext.Provider>
            )
        };

    }

### Multiple Providers

Each separate use of OurContext.Provider creates a new, separate 'pipe' of information! In the example below, each child component will recive different Context value. Also, the last child component, not nested inside Provider, will always recive default value.

        render(){
            return (
                <div>
                    <OurContext.Provider value='first'>
                        <OurChildComponent />
                    </OurContext.Provider>
                    
                    <OurContext.Provider value='second'>
                        <OurChildComponent />
                    </OurContext.Provider>
                    
                    <OurChildComponent />
                </div>
            )
        };

Providers can be nested inside each other. It doesn't matter which is parent and which is child.

        <OurContext.Provider value='first'>
            <AnotherContext.Provider value='second'>
                <OurChildComponent />
            </AnotherContext.Provider>
        </OurContext.Provider>

### Consumer

Instead of referencing this.context in a child component, we can create a Consumer. Then we don't have to create contextType property.

We create Consumer 'component' inside our component. Directly inside Consumer, we provide a function, that will be called with context value as its first argument.

    import OurContext from "../contexts/OurContext";

    class Bar extends React.Component {

        render(){
            return (
                <OurContext.Consumer>
                    {(value) => this.doSomethingWithValue(value)}
                </OurContext.Consumer>
            );
        }

    }