# Podstawy C#

## Typy wbudowane

### String

Deklaracje:

    string message1; // string może być zadeklarowany bez inicjalizacji (taka zmienna nie będzie mogła być użyta)
    message1 = "some value";
    string message2 = null; // string może być zadeklarowany jako null
    string message3 = string.Empty; // string może być zadeklarowany jako brak znaków

Uwzględnianie znaków specjalnych:

    string textWithQuotes = "He said \"Hi\"";
    string windowsLocation = "c:\\windows";
    string fontsFolder = @"c:\windows\fonts"; // verbatim string prefiksowany @

Łączenie stringów:

    string concatenated = string.Concat(textWithQuotes, " to me");
    string concatenated2 = textWithQuotes + " to me";
    string interploated = $"{textWithQuotes} to me";

String builder

    StringBuilder sb = new StringBuilder("This");
    sb.Append(" is");
    sb.Append(" a");
    sb.Append(" long");
    sb.Append(" text");
    string result = sb.ToString();

### Char

    char jChar = 'j';
    char jCharUnicode = '\u006A';

### Bool

    bool isUserReady = true;

### DateTime

    DateTime now = DateTime.Now;
    DateTime dateOfBirth = new DateTime(year: 1990, month: 6, day: 6);

Data może zostać sparsowana ze stringa:

    DateTime myDate = DateTime.Parse("01.01.2000");

### TimeSpan

    TimeSpan timeSpan = DateTime.Now - myDate;
    double daysAgo = timeSpan.TotalDays;

### Typy liczbowe

    byte byteNumber = 200;
    int intNumber = 500 ;
    float flotNumber = 1.5F; // Niższa precyzja, potrzebny suffix F po liczbie
    double doubleNumber = 1.5; // Średnia precyzja, nie potrzeba suffixa
    decimal decimalNumber = 1.5M; // Najwyższa precyzja, potrzebny suffix M po liczbie

    +---------+----------------+---------+----------+---------------------------------------------------------+
    | C#      | .Net Framework | Signed? | Bytes    | Possible Values                                         |
    | Type    | (System) type  |         | Occupied |                                                         |
    +---------+----------------+---------+----------+---------------------------------------------------------+
    | sbyte   | System.Sbyte   | Yes     | 1        | -128 to 127                                             |
    | short   | System.Int16   | Yes     | 2        | -32,768 to 32,767                                       |
    | int     | System.Int32   | Yes     | 4        | -2,147,483,648 to 2,147,483,647                         |
    | long    | System.Int64   | Yes     | 8        | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
    | byte    | System.Byte    | No      | 1        | 0 to 255                                                |
    | ushort  | System.Uint16  | No      | 2        | 0 to 65,535                                             |
    | uint    | System.UInt32  | No      | 4        | 0 to 4,294,967,295                                      |
    | ulong   | System.Uint64  | No      | 8        | 0 to 18,446,744,073,709,551,615                         |
    | float   | System.Single  | Yes     | 4        | Approximately ±1.5e-45 to ±3.4e38                       |
    |         |                |         |          |  with ~6-9 significant figures                          |
    | double  | System.Double  | Yes     | 8        | Approximately ±5.0e-324 to ±1.7e308                     |
    |         |                |         |          |  with ~15-17 significant figures                        |
    | decimal | System.Decimal | Yes     | 16       | Approximately ±1.0e-28 to ±7.9e28                       |
    |         |                |         |          |  with 28-29 significant figures                         |
    | char    | System.Char    | N/A     | 2        | Any Unicode character (16 bit)                          |
    | bool    | System.Boolean | N/A     | 1 / 2    | true or false                                           |
    +---------+----------------+---------+----------+---------------------------------------------------------+

## Powszechne operatory

### Inkrementacja

++ przez zmienną spowoduje inkrementację w tej samej linii.
++ po zmiennej spowoduje inkrementację dopiero w kolejnej linii.

    int value1 = 10;
    int value2 = 10;

    Console.WriteLine(value1); // 10
    Console.WriteLine(++value1); // 11 (już zmienione)

    Console.WriteLine(value2); // 10
    Console.WriteLine(value2++); // 10 (wciąż nie zmienione)
    Console.WriteLine(value2); // 11

## Konwersje typów i rzutowanie

### Konwersja string->int ze sprawdzaniem

    int yearOfBirth;
    if (int.TryParse(userInput, out yearOfBirth))
    {
        int age = DateTime.Now.Year - yearOfBirth;
        Console.WriteLine("You are " + age);
    }
    else
    {
        Console.WriteLine("Incorrect value");
    }

### Konwersja byte/int

    byte byteValue = 100;
    int intValue = byteValue; // byte->int
    byte byteValue2 = (byte)intValue; // int->byte (trzeba użyć jawnego rzutowania)

### Konwersja float/int

    double doubleValue = 3.5;
    int intValue2 = (int)doubleValue; // zwrócone 3, trzeba użyć jawnego rzutowania

## Tablice

Przykład tablicy stringów:

    string[] cars = { "Volvo", "BMW", "Mazda" };

Uwaga! Domyślna tablica ma stałą długość, tj. nie można usuwać ani dodawać elementów. Natomiast elementy pod danymi indeksami wciąż mogą zostać nadpisane.

Przykład pętli while z warunkowym zatrzymaniem:

    int i = 0;
    while (i < cars.Length)
    {
        Console.WriteLine(cars[i]);
        if (cars[i] == "BMW") {
            Console.WriteLine("Bye");
            break;
        }
        i++;
    }

Przykład pętli do while:

    Console.WriteLine("To exit, type x");
    string userInput;

    do
    {
        userInput = Console.ReadLine();
        Console.WriteLine($"Echo: {userInput}");
    } while (userInput != "x");

Przykład pętli for z warunkowym zatrzymaniem:

    for(int i = 0; i < cars.Length; i++)
    {
        Console.WriteLine(cars[i]);
        if (cars[i] == "BMW")
        {
            Console.WriteLine("Bye");
            break;
        }
    }

Przykład pętli foreach z warunkowym zatrzymaniem:

    foreach (string car in cars)
    {
        Console.WriteLine(car);
        if (car == "BMW")
        {
            Console.WriteLine("Bye");
            break;
        }
    }

## Enum

    enum Gender : byte
    {
        Male = 1,
        Female = 2
    }

    Console.WriteLine("What is your gender? 1 - Male, 2 - Female");
    string userInput = Console.ReadLine();
    Gender userGender = (Gender)Enum.Parse(typeof(Gender), userInput); // Wykonujemy typecast dla własnego enuma

### Nullable

Wartości, które mogą przymować null. Mogą być zapisywane na dwa sposoby.

    Nullable<int> favoriteNumber = null;
    favoriteNumber = 10;

    int? favoriteNumber2 = null; // Alternatywna, uproszczona składnia.
    favoriteNumber2 = 10;

    Console.WriteLine("Favorite number: " + (favoriteNumber.HasValue ? favoriteNumber.Value.ToString() : "---"));