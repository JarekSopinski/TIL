## A guide to CSS Animations

### Transforms

Transforms take two arguments - the first for X axis and the second for Y axis. If only one argument was added, it'll be applied for both X and Y.
Various transforms can be chained, i.e "transform: rotateZ(90deg) translateY(200px) scale(2)". Chained transform will happen in the order they were declared.

* transform: translate()

    Moves an element from on position to another. I.e. transform: translateX(200px) will move an element 200px 'ahead' (to the right) on the X axis. Negative value, i.e. -200px, would move it 'backwards' (to the left).

* transfrom: scale()

    Makes an element bigger or smaller. I.e. scale(2) will make and element two times bigger in both dimensions.

* transform: rotate()

    rotateX() and rotateY() transform an element as if it was a 3D object. rotateZ() moves an element around in a clockwise direction (this is also default for rotate()). An argument must be a 'deg' unit.

### Keyframes / animation property

* Keyframes block is opend with keyword @keyframes and the name of animation. Inside the block we can specify starting conditions (from{}) and ending conditions (to{}) If we want more than 2 steps, we can use precentages in place of 'from' and 'to'.

* Animation keyframes can be called from element's styles by using 'animation-name' property.

* 'animation-fill-mode' property controls the behaviour before and after running keyframes (outside the animation window). 'forwards' value will apply keyframes' final state as a new style after animation was finished, so that element will not 'reset' itself. With 'backwards' value an element will initially have keyframes' beginning state. 'Both' will apply both 'backwards' and 'forwards'.

* 'animation-delay' will delay running keyframes by the specified amount of time.

* 'animation-iteration-count' controls how many times an animation is repeated. 'Infinite' value will cause an animation to repeat itself infinitely.

* 'animation-direction' controls wheter keyframes should run from a start to an end. Its value can be 'normal', 'reverse' or 'alternate' (toggle between 'normal' and 'reverse' at each run).

* 'animation-timing-function' controls animation's timing. The default value is 'ease' - animation will start slower, than speed up in the middle and than again slow down near the end. With 'linear' value animation will run with constant speed all the time. 'ease-in' - starts slow, than speeds up. 'ease-out - animation starts at normal speed and slows down near the end. Aside from this, we can also apply our own timing function - the cubic-bezier function. Its behaviour with various arguments can be previewed using i.e. cubic-bezier.com site.

* Above properties can be applied as a shorthand 'animation' property. Its values can be: name, duration, timing, delay, iteration count and direction. I.e. 'animation: myanimation 80s linear 2s infinite reverse'. Delay's value can be apllied anywhere after duration's value. Apart from that, values' order doesn't matter.

* Chaining: various animations can be chained, i.e.: "animation: firstanimation 3s, secondanimation 0.3s".