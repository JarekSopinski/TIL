# C# - Nowości

## FileScopedNamespace (C# wersja 10)

Pozwala zastosować namespace dla pliku bez konieczności opakowywania całego pliku klamrami.

    namespace FileScopedNamespace.Person;

# Global using (C# wersja 10)

Słowo kluczowe 'global' pozwala zadeklarować using dla całego projektu.

    global using System.Text.Json

Globalne usingi można również zadeklarować w pliku csproj.

    <ItemGroup>
        <Using Include="System.Text.Json" />
    </ItemGroup>

W pliku projektowym możemy również ustawić niejawne stosowanie usingów:

    <ImplicitUsings>enable</ImplicitUsings>

## Interpolated const string (C# wersja 10)

    const string path2 = $"{basePath}path2";

## String literal (C# wersja 11)

String zamkniętrzy trzema cudzysłowiami, nie wymaga escape żadnych wartości.

    string filename = """c:\docum""ents\filesname.txt""";

Można też używać wielu linii, deklarując trzy cudzysłowia na początku i końcu:

    string filename = """
        "c:\docum""ents
        \filesname.txt
    """;

## List patterns (C# wersja 11)

Pozwala określić, czy dana lista pasuje do konkretnego wzoru.

    int[] numbers = { 1, 2, 3 };

    var pattern1 = numbers is [1, 2, 3];
    var pattern2 = numbers is [_, 2, 3];
    var pattern3 = numbers is [_, 2, > 2 and < 5>];
    var pattern4 = numbers is [1, 2, ..];
    var pattern5 = numbers is [1, .., 3];

## Interface INumber (C# wersja 11)

Pozwala uwspólnić typy reprezentujące wartości liczbowe. Przykładowo, gdy chcemy utworzyć metodę sumującą różne typy liczbowe:

    int AddAll<T>(T[] values) where T: INumber<T> {}

## File scope (C# wersja 11)

Definicja klasy dostępnej tylko w swoim pliku - słowo kluczowe 'file'.

    namespace FileScoped {
        file class MyFileScopedClass {}
    }

## Required members (C# wersja 11)

Można wymusić właściwości bez definiowania konstruktora.

    public class Person {
        public required string FirstName {get; set;}
        public string? MiddleName {get; set;}
    }

## Aliasy (C# wersja 12)

Można samodzielnie definiować aliasy.

    using Point = (int x, int y);

    var point = new Point(1, 34);

## Collection expressions (C# wersja 12)

Możemy uprościć inicjalizację oraz stosować nawiasy kwadratowe zamiast klamry.

    int[] x1 = [ 1, 2, 3, 4 ];

    List<int> x2 = [ 1, 2, 3, 4 ];

Mamy też dostępny operator spread (..) dla łączenia tablic.

    int[] x3 = [ ..x1, 5, 6 ];

## Domyślne parametry dla wyrażeń lambda (C# wersja 12)

W poniższym przykładzie increment przyjmuje domyślną wartość i może być pominięty.

    var IncrementBy = (int source, int increment = 1) => source + increment;

## Primary constructors (C# wersja 12)

Podobnie jak w rekordach, konstruktor klasy może być utworzony niejawnie.

    class Person(string firstName, string lastName) {
        public string FirstName {get;} = firstName;
    }