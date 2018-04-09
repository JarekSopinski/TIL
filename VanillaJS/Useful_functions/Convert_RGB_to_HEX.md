## Convert RGB to HEX

	const convertRgbToHex = (rgb) => {
	// argument should be a standard rgb notation string, i.e. "rgb(191, 192, 35)"

	    const rgbNumbers = rgb.slice(4, -1);
	    const rgbNumbersArray = rgbNumbers.split(' ');
	// creating an array of three rgb numbers

	    rgbNumbersArray[0] = rgbNumbersArray[0].slice(0, -1);
	    rgbNumbersArray[1] = rgbNumbersArray[1].slice(0, -1);
	// getting rid of commas

	    rgbNumbersArray[0] = parseInt(rgbNumbersArray[0], 10);
	    rgbNumbersArray[1] = parseInt(rgbNumbersArray[1], 10);
	    rgbNumbersArray[2] = parseInt(rgbNumbersArray[2], 10);
	// converting strings to numbers

	    const hexArray = rgbNumbersArray
		.map(rgbNumber => {
		const hex = rgbNumber.toString(16);
		return hex.length === 1 ? "0" + hex : hex;});
	// converting rgbs to hexes

	    hexArray.unshift("#");
	    return hexArray.join('')
	// adding # at the beginning and joining into a string
	};
	
