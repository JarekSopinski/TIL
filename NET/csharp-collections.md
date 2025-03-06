# Kolekcje C#

W odróżnieniu od tablic, kolekcje mogą rosnąć lub zmniejszać się w cyklu życia aplikacji.

Rodzaje generycznych kolekcji:
- Dictionary, Hashtable - pary typu klucz-wartość, gdy potrzebny jest szybki dostęp do danego elementu.
- List
- Queue, Stack

## Lista i jej funkcje

        List<int> intList = new List<int>() { 6, 1, 20 };
        instList.Add(6);

Funkcje usuwania:
- RemoveAt(1) - usuwa element na danym indeksie
- RemoveAll(IsGreaterThan5) - usuwa wszystkie spełniające dany warunek
- Remove('foo') - uwuwa pierwsze wystąpienie określonej wartości z listy
- RemoveRange(2,3) - usuwa zakres listy (indeks początkowy, liczba usuniętych elementów)

## Słownik

Słownik to kolekcja przechowująca elementy jako pary typu klucz-wartość. Przyjmuje ona dwa parametry generyczne: typ klucza słownika i typ wartości danego elementu.

    Dictionary<TKey, TValue>

Zalety używania słownika względem listy to:
- możliwość wymuszenia unikalnych kluczy
- wydajność (O(1) na dostęp do elementu, gdy dla listy to O(n))

Przykładowo:

    static Dictionary<string, Currency> GetCurrencies()
    {
        return new Dictionary<string, Currency>
        {
            {"usd", new Currency("usd", "United States Dollar", 1) },
            {"eur", new Currency("eur", "Euro", 0.83975) }
        };
    }

Należy pamiętać, że odowłanie do elementu słownika w sytuacji podania błędnego klucza spowoduje exception.

    Currency selectedCurrency = currencies['usd']; // ok
    Currency selectedCurrency = currencies['foo']; // exception

Aby zabezpieczyć się przed błędem, należy użyć wbudowanej metody słownika TryGetValue.

    Currency selectedCurrency = null;
    if (currencies.TryGetValue(userInput, out selectedCurrency))
    {
        Console.WriteLine($"Rate is {selectedCurrency.Rate}");
    } else
    {
        Console.WriteLine("Currency not found");
    }

Elementy mogą być dodawane i usuwane ze słownika poprzez odwołanie do klucza. Bezpieczną metodą dodania elementu (na wypadek duplikacji kluczy) jest TryAdd.

    currencies.Remove("usd");
    currencies.TryAdd("usd", new Currency("usd", "Dollar", 1));

## Zwracanie iteratora - yield

Tworząc własny iterator z użyciem słowa kluczowego 'yield', możemy, zamiast zwracać z całej kolekcji na raz, będziemy zwracać konkretne elementy tak długo, jak będą one potrzebne.

    public static IEnumerable<int> GetYieldedData()
    {
        Console.WriteLine("GetYieldedData start");
        for (int i = 0; i < 9; i++)
        {
            Console.WriteLine($"GetYieldedData element: {i}");
            yield return i;
        }
        Console.WriteLine("GetYieldedData stop");
    }

    var yieldedData = GetYieldedData(); // 7 will not be printed when running GetYieldedData()
    foreach (int elemenet in yieldedData)
    {
        Console.WriteLine($"Main GetYieldedData element: {elemenet}");
        if (elemenet > 5)
        {
            break;
        }
    }

Iterator może zostać przerwany za pomocą słowa kluczowego:

    yield break;