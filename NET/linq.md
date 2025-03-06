# LINQ

## Wprowadzenie

Czyli Language-Integrated Query: to nazwa zestawu technologii opartego na integracji możliwości zapytań bezpośrednio z językiem C#.

Metody LINQ pozwalają np. filtrować wyniki i budować z nich nowe listy na podstawie własnych predykamentów.

- Pobieranie danych: Where, First, Single, Last,
- Projekcje (przekształcenia) danych: Select, SelectMany
- Podział danych: Skip, Take, SkipWhile, Take While
- Sortowanie: OrderBy, OrderByDescending, ThenBy, ThenByDescending
- Operacje na zbiorach: Distinct, Union, Intersect, Except
- Weryfikajca danych: All, Any

Przykładowo:

    List<Person> youngEmployees = employees.Where(EmployeeIsYoung).ToList(); // znajdź pracowników urodzonych przed daną datą
    Person bob = youngEmployees.FirstOrDefault(EmployeeIsBob); // znajdź pracownika o imieniu Bob
    List<string> employeesFirstNames = employees.Select(GetEmployeeFirstName).ToList(); // zbuduj listę imion pracowników
    DateTime youngestEmployeeDateOfBirth = employees.Max(GetEmployeeDateOfBirth); // znajdź najmłodszego pracownika
    DateTime oldestEmployeeDateOfBirth = employees.Min(GetEmployeeDateOfBirth); // znajdź najstarszego pracownika

Powszechną praktyką jest, aby w miejscu predykatów używać wyrażeń lambda, np.:

    List<Person> youngEmployees = employees.Where(e => e.GetDateOfBirth() > new DateTime(2000, 1, 1)).ToList();