## Get user's location using HTML5 Geolocation API

Geolocation.getCurrentPosition() method returns current position of user's device. It takes two arguments: a success callback and an error callback (optional). In case of success, it returns an object structured like this:

	Position {
		coords: {latitude: 54.3830244, longitude: 18.6335527 ...},
		timestamp: 1523278996428}
	}

The browser will ask user for an agreement to show geolocation data (for privacy reasons).

Below is an example of asynchronously running this method, including error handling:

	const userLocation = {}
	// in case of success, user's coordinates will be passed to above object

	const runBrowserGeolocation = () => {

	    	return new Promise((resolve, reject) => {
		navigator.geolocation.getCurrentPosition(resolve, reject);
	    	})

	};

	const getUserLocation = () => {

	    runBrowserGeolocation()
		.then(data => passDataToUserLocationObject(data))
		.then(userLocation => doSomethingElseWithData(userLocation))
		.catch(error => handleError(error))

	};

	const passDataToUserLocationObject = (data) => {

	    userLocation.latitude = data.coords.latitude;
	    userLocation.longitude = data.coords.longitude;
	    return userLocation

	};

	    const handleError = (error) => {
		switch(error.code) {
		    case error.PERMISSION_DENIED:
		        alert("This app requires your coordinates to run. Please allow geolocation in your browser.");
		        break;
		    case error.POSITION_UNAVAILABLE:
		        alert("Location information is unavailable");
		        break;
		    case error.TIMEOUT:
		        alert("The request to get user location timed out");
		        break;
		    default:
		        alert("An unknown error occurred.");
		        break;
		}
	    };

	window.addEventListener("load", getUserLocation);
