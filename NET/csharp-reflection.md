# Refleksja i atrybuty w C#

Za pomocą refleksji można w dynamiczny sposób odczytać wszystkie właściwości z danego typu.
W poniższym przykładzie metoda Display może wypisać właściwości z każdego typu obiektu, jeżeli właściwość jest typem prymitywnym.

    static void Display (object obj)
    {
        Type objType = obj.GetType();
        var properties = objType.GetProperties();

        foreach ( var property in properties )
        {
            var propValue = property.GetValue( obj );
            var propType = propValue.GetType(); // check if primitive type
            if (propType.IsPrimitive || propType == typeof( string ) )
            {
                Console.WriteLine($"{property.Name}: {propValue}");
            }
        }
    }

Następnie może ona być użyta do odczytania dwóch całkowicie różnych typów:

    Address address = new Address()
    {
        City = "Kraków",
        PostalCode = "31-556",
        Street = "Grodzka 5"
    };

    Person person = new Person()
    {
        FirstName = "John",
        LastName = "Doe",
        Address = address
    };

    Display(person);
    Display(address);

## Atrybuty

Na klasy możemy nakładać własne atrybuty, definiujące dodatkowe metadane. Np. określą, jak w refleksji zostaną wyświetlone (sformatowane) poszczególne właściwości. W poniższym przykładzie możemy chcieć wyświetlić właściwości ze spacją (First Name zamiast FirstName).

    [AttributeUsage(AttributeTargets.Property)] // restriction
    internal class DisplayPropertyAttribute: Attribute
    {
        public string DisplayName { get; set; }

        public DisplayPropertyAttribute(string displayName)
        {
            DisplayName = displayName;
        }
    }

    internal class Person
    {
        [DisplayProperty("First Name")]
        public string FirstName { get; set; }
        [DisplayProperty("Last Name")]
        public string LastName { get; set; }
        public Address Address { get; set; }

    }

    static void Display (object obj)
        {
            Type objType = obj.GetType();
            var properties = objType.GetProperties();

            foreach ( var property in properties )
            {
                var propValue = property.GetValue( obj );
                var propType = propValue.GetType(); // check if primitive type
                if (propType.IsPrimitive || propType == typeof( string ) )
                {
                    var displayPropertyAttribute = property.GetCustomAttribute<DisplayPropertyAttribute>();
                    if (displayPropertyAttribute != null)
                    {
                        Console.WriteLine($"{displayPropertyAttribute.DisplayName}: {propValue}");
                    } else
                    {
                        Console.WriteLine($"{property.Name}: {propValue}");
                    }
                }
            }
    }

## Przypisywanie wartości

Za pomocą refleksji możemy również w dynamiczny sposób zmieniać lub przypisywać wartości, zarówno dla obiektów, jak i dla właściwości tych obiektów. Załóżmy, że podczas trwania programu, użytkownik będzie chciał za pomocą konsoli zmienić daną właściwość.

Generyczna metoda przyjmują aktualizowany obiekty oraz nazwę i nową wartość aktualizowanej właściwości może wyglądać następująco:

    static void SetValue<T>(T obj, string propName, string value)
    {
        var objType = typeof(T);
        var propertyToUpdate = objType.GetProperty(propName);
        if (propertyToUpdate != null)
        {
            propertyToUpdate.SetValue( obj, value );
        }
    }