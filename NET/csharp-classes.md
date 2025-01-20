# Klasy C#

    class Person
    {
        public string FirstName;
        public string LastName;

        public DateTime DateOfBirth;
    }

Domyślnie właściwości klas mają modyfikator dostępu private!

Właściwości prywatne powinny być zapisywane z małej litery.

Dla metod zwracających wartość można używać expression value:

    public DateTime getDateOfBirth() => dateOfBirth;

## Konstruktory

    class Person {
        public Person(string firstName, string lastName)
        {
            FirstName = firstName;
            LastName = lastName;
        }
    }


Jedna klasa może posiadać wiele konstruktorów, o ile różnią się one parametrami. Konstruktor może wywoływać inne konstruktory za pomocą ": this".

    // Pierwszy konstruktor
    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }

    // Drugi konstruktor wywołuje poprzedni
    public Person(DateTime dateOfBirth, string firstName, string lastName) : this(firstName, lastName)
    {
        SetDateOfBirth(dateOfBirth);
    }

## Właściwości

Właściwości (akcesory) pozwalają w uproszczony sposób definiować gettery i settery.

    class Person
    {
        private string contactNumber;

        public string ContactNumber
        {
            get { return contactNumber; }
            set { contactNumber = value; }
        }
    }

Gdy getter i setter nie stosuje żadnej wewnętrznej logiki, deklaracja może zostać uproszczona:

    public string ContactNumber {  get; set; }

### Właściwości statyczne

Właściwości statyczne wywoływane są bezpośrednio z klasy, nie z instancji.

    class Person
    {
        public static int Count = 0;
    }

    Console.WriteLine($"Objects of Person type count: {Person.Count}");

## Dziedziczenie

    class File
    {
        public string FileName { get; set; }
        public int Size { get; set; }
        public DateTime CreatedOn { get; set; }
    }

    class ExcelFile: File
    {
        public void GenerateReport()
        {
            Console.WriteLine($"{FileName} report...");
        }
    }