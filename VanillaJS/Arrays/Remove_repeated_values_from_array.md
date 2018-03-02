# How to remove repeated values from an array (without changing order)?

> const repeatedArray = [1,3,2,5,2,1,4,2,1];
> const uniqueArray = repeatedArray.filter((item, position) => {
>        return repeatedArray.indexOf(item) === position;
>    });

Result: [1, 3, 2, 5, 4]

## Explanation: 
For each element, we check if its first position is equal to current position.
This is false for repeated items, so they're removed.


