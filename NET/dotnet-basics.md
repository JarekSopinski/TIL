# Podstawy .NET

## .NET - wprowadzenie

### Base Class Library

- Dostarcza podstawowe klasy do powszechnych zadań
- Bez zewnętrznych pakietów
- Utrzymana i testowana przez innych developerów

### Common Language Runtime

- Wykonuje kod
- Zarządza pamięcią programu
- Komunikuje się z systemem operacyjnym

### NET CLI

Sprawdzenie wersji SDK:

    dotnet --info

Pomoc:

    dotnet --help

Utworzenie projektu:

    dotnet new <TYP PROJEKTU> --name <NAZWA>

Utworzenie projektu z plikiem sln dla Visual Studio:

    dotnet new sln --name <NAZWA>

Skompilowanie i uruchomienie:

    dotnet run