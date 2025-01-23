# Klasy C#

Trzy filary programowania obiektowego to: dziedzieczenie, hermetyzacja, polimorfizm.

    class Person
    {
        public string FirstName;
        public string LastName;

        public DateTime DateOfBirth;
    }

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

## Modyfikatory dostępu

- Public
- Private
- Protected - klasy pochodne będą miały dostęp jako private
- Internal - element jest publiczny tylko w danym assembly (wynik budowania naszego projektu)

Domyślnie właściwości klas mają modyfikator dostępu private!

Z modyfikatorami dostępu wiąże się pojęcie hermetyzacji.

## Klasa abstrakcyjna

Klasa, która powinna być dostępna tylko w ramach dziedziecznia.

    abstract class File {}

    File file = new File(); // Błąd kompilacji - nie można utworzyć instancji klasy abstrakcyjnej.

Klasa abstrakcyjna może posiadać elementy członkowskie abstrakcyjne (metody, właściwości). Elementy członkowskie abstrakcyjne muszą być zaimplementowane w klasach pochodnych.

    public abstract void MyAbstractMethod(); // w klasie abstrakcyjnej

    public override void MyAbstractMethod() {} // w klasie pochodnej

## Interfejs

Dla interfejsu wszystkie elementy są domyślnie publiczne i abstrakcyjne, co oznacza że muszą być zaimplementowane. Zatem słowa public i abstract dla właściwości interfejsu są zbędne.

Konwencja nakazuje zaczynać interfejsy literą "I", np. IFile.

    interface IFile
    {
        string FileName { get; set; }

        void Compress();
    }

    class ExcelFile: IFile
    {
        public string FileName { get; set; }

        public void Compress() // słowo 'override' jest tutaj zbędne
        {
            Console.WriteLine("Compressing");
        }
    }

Jedna klasa może implementować wiele interfejsów, oddzielonych przecinkami.

## Polimorfizm

Polimorfizm to greckie słowo, które oznacza "wiele kształtów" i ma dwa odrębne aspekty:

- W czasie wykonywania obiekty klasy pochodnej mogą być traktowane jako obiekty klasy bazowej w miejscach, takich jak parametry metody i kolekcje lub tablice. Gdy wystąpi ten polimorfizm, zadeklarowany typ obiektu nie jest już identyczny z typem czasu wykonywania.
- Klasy bazowe mogą definiować i implementować metody wirtualne, a klasy pochodne mogą je zastąpić, co oznacza, że zapewniają własną definicję i implementację. (Do tego celu używa się słowa kluczowego 'virtual')


    class Shape
    {
        public int X {  get; set; }
        public int Y { get; set; }

        public virtual void Draw()
        {
            Console.WriteLine("Drawing shape");
        }
    }

W powyższym przykładzie używamy słowa kluczowego 'virtual', aby utworzyć metodę, która może być nadpisana w klasach pochodnych. W odróżnieniu od słowa kluczowego 'abstract', metoda wirtualna może być deklarowana w klasach nie-abstrakcyjnych i posiadać podstawową implementację w klasie bazowej.

Teraz możemy utworzyć klasy dziedziczące z własnymi implementacjami Draw:

    class Circle : Shape {
        public override void Draw() {
            Console.WriteLine("Drawing circle");
        }
    }

    class Rectangle : Shape
    {
        public override void Draw()
        {
            Console.WriteLine("Drawing rectangle");
        }
    }

Gdy utworzymy tablicę obiektów i puścimy na nich pętlę, zostaną wywołane metody dla konkretnych kształtów - jest to przykład polimorfizmu:

    Shape[] shapes = { new Circle(), new Rectangle() };

    foreach (Shape shape in shapes) {
        shape.Draw(); // "Drawing circle", "Drawing rectangle"
    }

Gdybyśmy jednak w klasach dziedziczących usunęli słowo kluczowe 'override', zostałaby wykonana metoda z klasy bazowej shape ("Drawing shape").