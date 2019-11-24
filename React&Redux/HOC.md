## Higher order components (HOC)

A higher order components are essentialy functions that wrap other components and modify them or give them some additional capabilites.

A HOC takes wrapped component as a parameter and returns it with its props, that can be modified. It can also wrap this component inside other elements.

    import React from 'react';

    const exampleHOC = (WrappedComponent) => {
        // some modifications can happen here
        return (props) => {
            return (
                <div className="someHocWrapper">
                    <WrappedComponent {...props} />
                </div>
            )
        }
    }

    export default exampleHOC;

To use HOC we have to import it in a wrapped component and than export it with wrapped component as an argument:

    import exampleHOC from '../hoc/exampleHOC';

    const ExampleWrappedComponent = () => {
        ...
    }

    export default exampleHOC(ExampleWrappedComponent);