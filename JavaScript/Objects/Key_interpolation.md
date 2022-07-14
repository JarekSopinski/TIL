## Key interpolation

With key interpolation we can use a variable or an expression to reference a property.

    const animalSounds = { cat: 'meow', dog: 'bark' };
    const newAnimal = 'lion';
    const newSound = 'roar';
    const updatedSounds = { ...animalSounds, [newAnimal]: newSound }
    
    updatedSounds.newAnimal // undefined
    updatedSounds.lion // 'roar'
    updatedSounds[newAnimal] // 'roar'