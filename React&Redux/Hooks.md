## React Hooks

Hooks allow functional components to use more advanced functionalities, similar to class-bases components. 

#### useState

Function that lets you use state in a functional component.

    const [
        activeItem, // represents piece of state
        setActiveItem // function called to update piece of state
    ] = useState(null); // initial value for piece of state

#### useEffect

Function that lets you use something like lifecycle methods in a functional components.
useEffect() takes two arguments: a callback function and an array that defines, when this is executed:
- [] - Run at initial render
- nothing (no array) - Run at initial render AND run after every rerender
- [dataOne, dataTwo] - Run at initial render AND run after every rerender IF any of the provided values has changed since the last render. This array can keep multiple values.

    useEffect(() => {
        console.log('I ONLY RUN ONCE');
    }, []);

    useEffect(() => {
        console.log('I RUN AFTER EVERY RENDER AND AT INITIAL RENDER');
    });

    useEffect(() => {
        console.log('I RUN AFTER EVERY RENDER AND AT INITIAL RENDER');
    }, [dataOne, dataTwo]);

useEffect() can return another function, that can be used to run some cleanup. This returned function will be called at the start of useEffect in two scenarios:
any time useEffect runs AGAIN 
OR
when component is removed from DOM. 
It will not run the first time useEffect() runs!

    useEffect(() => {
        console.log('I RUN AFTER EVERY RENDER AND AT INITIAL RENDER');
        return () => {
            console.log('CLEANUP);
        }
    }, [data]);

#### useRef

Function that lets you create a 'ref' in a function component.

#### Custom Hooks

Custom Hooks can be created by extracting hook-related code out of a function component. Custom hooks always make use of at least one primitive hook internally. Each custom hook should have one purpose.

To create a custom hook:
- Identify each line of code related to some single purpose;
- Identify the inputs to that code;
- Identify the outputs to that code;
- Extract all of the code into a separate function, receiving the inputs as arguments, and returning the outpusts