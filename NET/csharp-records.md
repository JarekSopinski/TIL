# Rekordy w C#

Rekordy są idealny rozwiązaniem dla prostych modeli dto, dla których chcemy, aby był one niezmienne po ich inicjalizacji.

Rozważmy przykład wypisania i porównania instancji klas:

    public class CurrencyClass
    {
        public string Name { get; set; }
        public decimal Value { get; set; }
    }

    var currency1 = new CurrencyClass()
    {
        Name = "USD",
        Value = 10
    };

    var currency2 = new CurrencyClass()
    {
        Name = "USD",
        Value = 10
    };


    Console.WriteLine(currency1); // CurrencyClass
    Console.WriteLine(currency2); // CurrencyClass
    Console.WriteLine($"curr1 == curr2 ? {currency1 == currency2}"); // FALSE
    Console.WriteLine($"object.ReferenceEquals(curr1, curr2) ? {Object.ReferenceEquals(currency1, currency2)}"); // FALSE

Teraz zamieńmy klasę na identyczny rekord:

    public record CurrencyRecord
    {
        public string Name { get; set; }
        public decimal Value { get; set; }
    }

    var currency1 = new CurrencyRecord()
    {
        Name = "USD",
        Value = 10
    };

    var currency2 = new CurrencyRecord()
    {
        Name = "USD",
        Value = 10
    };


    Console.WriteLine(currency1); // CurrencyRecord { Name = USD, Value = 10 }
    Console.WriteLine(currency2); // CurrencyRecord { Name = USD, Value = 10 }
    Console.WriteLine($"curr1 == curr2 ? {currency1 == currency2}"); // TRUE
    Console.WriteLine($"object.ReferenceEquals(curr1, curr2) ? {Object.ReferenceEquals(currency1, currency2)}"); // FALSE

## Konstruktor rekordu

Jeżeli chcemy utworzyć rekord z publicznym konstruktorem, możemy skrócić jego deklarację do jednej linijki:

    public record CurrencyRecord(string Name, decimal Value);

    var currency1 = new CurrencyRecord("USD", 10);
    var currency2 = new CurrencyRecord("USD", 10);

## Klonowanie rekordów

W poniższym przykładzie currency2 sklonuje całą zawartość currency1, ale będzie posiadał inną referencję.

    var currency1 = new CurrencyRecord("USD", 10);
    var currency2 = currency1 with { };

    Console.WriteLine($"object.ReferenceEquals(curr1, curr2) ? {Object.ReferenceEquals(currency1, currency2)}"); // FALSE

Za pomocą operatora 'with' możemy też zmienić daną właściwość:

    var currency1 = new CurrencyRecord("USD", 10);
    var currency2 = currency1 with { Value = 20 };

## Dekonstrukcja

Dekonstrukcja pozwala w prosty sposób wydobyć właściwości rekordu.

    public record CurrencyRecord(string Name, decimal Value);
    var currency1 = new CurrencyRecord("USD", 10);

    var (name, value) = currency1;

## Interfejsy, dziedziczenie, abstrakcja

Rekordy mogą, tak samo jak klasy, implementować interfejsy.
Rekordy NIE mogą dziedcziczyć klasy, ale mogą dziedziczyć po innym rekordzie.
Klasy NIE mogą dziedziczyć z rekordów.
Rekordy mogą być abstrakcyjne.

Przykład dziedziczenia z rekordu abstrakcyjnego - przy dziedziczeniu właściwości (someValue) rekordy mają własny, uproszczony syntax:

    public abstract record BaseCurrencyRecord(string someValue);
    public record CurrencyRecord(string Name, decimal Value, string someValue): BaseCurrencyRecord(someValue);

## Generyczne rekordy

Rekordy mogą również wykorzystywać typy generyczne:

    public record CurrencyRecord<T>(string Name, T Value, string someValue): BaseCurrencyRecord(someValue);

    var currency1 = new CurrencyRecord<decimal>("USD", 10, "Somevalue");

## Jawna deklaracja typu referencyjnego

Od wersji 10 można jawnie zadeklarować typ referencyjny rekordu oraz zedklarować rekord jako strukturę:

    public record struct CurrencyRecordStruct(string Name, decimal Value);

    public record class CurrencyRecordClass(string Name, decimal Value);