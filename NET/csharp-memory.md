# Zarządzanie pamięcią w C#

W pamięci znajdują się typy wartościowe (np. int, char) oraz typy referencyjne (np. zainicjalizowany obiekt klasy).

Pamięć programu można podzielić na stos i stertę.

## Stos

Statycznie ograniczone miejsce pamięci, gdzie dostęp do danych jest bezpośredni.

Na stosie przechowywane są zmienne typu wartościowego, czyli np. int, char, w kolejności w jakiej zostały zadeklarowane. Oraz wskaźniki do zmiennych typu referencyjnego.

Po zakończeniu metody, która utworzyła dane zmienne, zostaną one usunięte ze stosu.

## Sterta

Może przechowywać tyle danych, ile miejsca zostało wirtualnie zarezerwowane dla programu i dostęp do danych odbywa się na podstawie odniesienia do komórki w pamięci pod konkretnym adresem, przez jakiś wskaźnik (pointer).

Na stercie znajdują się konkretne wartości typów referencyjnych, czyli np. zainicjalizowany obiekt klasy. Dotyczy to również właściwości klas.

Zainicjalizowany obiekt wciąż może pozostawać na stercie, mimo że metoda, która go utworzyła, została zakończona.

Zwalnianiem pamięci ze sterty zajmuje się garbage collector.

## Przekazywanie referencji do zmiennych wartościowych - ref & out

### ref

Aby przekazać referencję do zmiennej wartościowej (co przy przypisaniu zmiennej pozwoli operować na rzeczywistej zmiennej, zamiast na jej kopii) możemy posłużyć się słowem kluczowym ref.

Domyślne zachowanie:

        static void Double(int value)
        {
            value = 2 * value;
            Console.WriteLine($"Doubled value: {value}");
        }

        static void Main(string[] args)
        {
            int someValue = 5;
            Double(someValue); // Console.WriteLine w metodzie Double wyświetli 10

            Console.WriteLine($"some value: {someValue}"); // wciąż 5, bo nie ma tutaj referencji!
        }

Po zastosowaniu słowa kluczowego ref:

        static void Double(ref int value)
        {
            value = 2 * value;
            Console.WriteLine($"Doubled value: {value}");
        }

        static void Main(string[] args)
        {
            int someValue = 5;
            Double(ref someValue); // Console.WriteLine w metodzie Double wyświetli 10

            Console.WriteLine($"some value: {someValue}"); // tym razem też 10, bo jest referencja
        }

### out

Out działa podobnie jak ref, tyle że kompilator wymusi ustawienie wartości danego parametru w metodzie.

        static bool IsDivisible(int value, int factor, out int reminder)
        {
            reminder = value % factor;

            return reminder == 0;
        }

        int value = 5;
        int factor = 2;
        int reminder;
        if (IsDivisible(value, factor, out reminder)) // tutaj IsDivisible przestawi reminder na 1 (5%2)
        {
            Console.WriteLine($"{value} is divisible by {factor}");
        }
        else
        {
            Console.WriteLine($"{value} is NOT divisible by {factor}. Reminder: {reminder}"); // reminder: 1, dzięki 'out'!
        }

## Porównywanie wartości typu referencyjnego

Porównując obiekty referencyjne w domyślny sposób (==) tak naprawdę sprawdzamy same referencje, to czy wskazują one na ten sam adres pamięci (to zawsze zwróci false). Takie porównanie wymaga własnej implementacji.

## Słowa kluczowe const & readonly

const - zmienna nie może nigdy zostać nadpisana

    private const string foobar = 'foobar1';
    foobar = 'foobar2'; // błąd kompilacji

readonly - zmienna może zostać nadpisana tylko w konstruktorze

    private readonly string foobar = 'foobar1';
    foobar = 'foobar2'; // błąd kompilacji

Typy wartościowe mogą być zadeklarowane zarówno const, jak i readonly.
Typy referencyjne mogą (spomiędzy tych dwóch opcji) być zadeklarowane tylko jako readonly.