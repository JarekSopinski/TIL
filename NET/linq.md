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

## Pobieranie danych

Znajdowanie danych spełniających predykatę:

    var highRatedApps = googleApps.Where(app => app.Rating > 4.6);

First vs. FirstOrDefault: First rzuci wyjątek, jeżeli predykata nie znajdzie niczego. FirstOrDefault nie rzuci wyjątku i zwróci domyślną wartość (np. null).

    var firstHighRatedBeautyApp = highRatedBeautyApps.First(app => app.Reviews < 300);
    var firstHighRatedBeautyApp = highRatedBeautyApps.FirstOrDefault(app => app.Reviews < 300);

Metoda Single jest bardziej rozbudowaną wersją First. Dodatkowo sprawdzi, czy w sekwencji elementów spełniających predykatę znajduje się więcej niż 1 element - jeżeli jest ich więcej, rzuci ona błąd. Analogicznie do First, można też użyć SingleOrDefault, zabezpieczający przed błedem gdy nie znaleziono niczego (ale nie przed błędem, gdy znaleziono więcej niż 1!).

    var firstHighRatedBeautyApp = highRatedBeautyApps.Single(app => app.Reviews < 300);
    var firstHighRatedBeautyApp = highRatedBeautyApps.SingleOrDefault(app => app.Reviews < 300);

W sposób analogiczny do First, działa metoda Last (i LastOrDefault), znajdująca ostatni element spełniający predykatę.

    var lastHighRatedBeautyApp = highRatedBeautyApps.Last(app => app.Reviews < 300);

## Projekcja danych

Przykład wydobycia samych nazw:

    var highRatedBeautyApps = googleApps.Where(app => app.Rating > 4.6 && app.Category == Category.BEAUTY);
    var highRatedBeautyAppsNames = highRatedBeautyApps.Select(app => app.Name);

Przykład wydobycia tablic i spłaszczenia ich na jedną tablicę:

    var genres = highRatedBeautyApps.SelectMany(app => app.Genres);

Przykład przekształcenia obiektów na inne obiekty:

    var dtos = highRatedBeautyApps.Select(app => new GoogleAppDto()
    {
        Reviews = app.Reviews,
        Name = app.Name
    });

Można również dokonać przekształcenie na typ anonimowy, nie będący instancją żadnej istniejącej klasy:

    var annonymousDtos = highRatedBeautyApps.Select(app => new
    {
        Reviews = app.Reviews,
        Name = app.Name,
        Category = app.Category
    });

## Dzielenie danych

Przykład ograniczenia wyników do danej liczby:

    var highRatedBeautyApps = googleApps.Where(app => app.Rating > 4.6 && app.Category == Category.BEAUTY);
    var first5HighRatedBeautyApps = highRatedBeautyApps.Take(5);

Alternatywnie do Take, można użyć TakeWhile, które przyjmuje predykatę:

    var beautyAppsWithManyReviews = highRatedBeautyApps.TakeWhile(app => app.Reviews > 1000);

W odwrotny sposób działa metoda Skip, która pomija element, zamiast go uwzględniać.

    var skippedResults = highRatedBeautyApps.Skip(5);

## Sortowanie danych

    var sortedResultsAsc = highRatedBeautyApps.OrderBy(app => app.Rating);
    var sortedResultsDesc = highRatedBeautyApps.OrderByDescending(app => app.Rating);

Można również zastosować dodatkowe sortowanie (dla wyników, które miały tę samą wartość w pierwszym sortowaniu - np. najpierw sortujemy przez Rating, potem przez Name):

    var sortedResultsDesc = highRatedBeautyApps.OrderByDescending(app => app.Rating)
        .ThenBy(app => app.Name);

## Operacje na zbiorach

Operator Distinct pozwala zwrócić tylko unikalne wartości:

    var paidAppsCategories = googleApps.Where(a => a.Type == Type.Paid)
        .Select(a => a.Category)
        .Distinct();

Operator Union łączy zbiory, a jego wynikiem będzie połączenie tych zbiorów (elementy powtarzające się nie będą powielone).

    var setA = googleApps.Where(a => a.Rating > 4.7 && a.Type == Type.Paid && a.Reviews > 1000);
    var setB = googleApps.Where(a => a.Name.Contains("Pro") && a.Rating > 4.6 && a.Reviews > 10000);
    var appsUnion = setA.Union(setB);

Operator Intersect łączy zbiory, a jego wynikiem będzie część wspólna tych zbiorów (tylko elementy będące zarówno w zbiorze A, jak i zbiorze B).

    var appsIntersect = setA.Intersect(setB);

Operator Except - dla zbiorów A i B wynikiem będzie część zbioru A, która nie pokrywa się ze zbiorem B.

    var appsExcept = setA.Except(setB);

## Weryfikacja danych

Operatory All i Any zwracają wartości boolean na podstawie zadanego predykatu.

    bool allOperatorResult = googleApps.Where(a => a.Category == Category.WEATHER)
        .All(a => a.Reviews > 10);

    bool anyOperatorResult = googleApps.Where(a => a.Category == Category.WEATHER)
        .Any(a => a.Reviews > 2_000_000);

## Grupowanie danych

Operator GroupBy pozwala grupować elementy według jakiegoś klucza. Zwracana jest lista IGrouping, zawierająca klucz grupującego elementu. Tym kluczem możemy się potem posłużyć, aby znaleźć konkretne elementy.

    var categoryGroup = googleApps.GroupBy(a => a.Category);

    var artAndDesignGroup = categoryGroup.First(g => g.Key == Category.ART_AND_DESIGN);
    var artAndDesignApps = artAndDesignGroup.ToList();

Możemy np. iterować po wszystkich elementach z każdej grupy osobno:

    foreach (var group in categoryGroup)
    {
        var apps = group.ToList();
        Console.WriteLine($"Displaying elements for group {group.Key}");
        Display(apps);
    }

Możliwe jest również grupowanie złożone po wielu właściwościach. Wówczas klucz będzie obiektem anonimowym, zawierającym te właściwości.

    var categoryGroup = googleApps.GroupBy(a => new { a.Category, a.Type });

    foreach (var group in categoryGroup)
    {
        var key = group.Key;
        var apps = group.ToList();
        Console.WriteLine($"Displaying elements for group {group.Key.Category}");
        Display(apps);
    }

## Operacje na grupach

    var categoryGroup = googleApps.GroupBy(a => a.Category);

    foreach (var group in categoryGroup)
    {
        var averageReviews = group.Average(g => g.Reviews);
        var minReviews = group.Min(g => g.Reviews);
        var maxReviews = group.Max(g => g.Reviews);
        var reviewsCount = group.Sum(g => g.Reviews)
    }

## Łączenie danych

### Join

Operator Join pozwala połączyć zbiory danych na podstawie określonych relacji. W poniższym przykładzie łączymy zbiory 'addresses' i 'people', gdzie adres jest połączony do person kluczem PersonId. W ostatnim (czwartym) parametrze wskazujemy funkcję budującą wynikowy, anonimowy obiekt.

Jeżeli dany obiekt nie będze mógł być połączony, to nie zostanie on uwzględniony w wyniku końcowym.

Tę operację można porównać do operacji SQL INNER_JOIN.

    var joinedData = people.Join(
        addresses,
        person => person.Id,
        address => address.PersonId,
        (person, address) => new { person.Name, address.Street, address.City }
    );

### GroupJoin

Operator GroupJoin spowoduje połączenie danego elementu z kolekcją (w powyższym przykładzie - powiązanie osoby nie z adresem, ale z kolekcją adresów).

    var joinedData = people.Join(
        addresses,
        person => person.Id,
        address => address.PersonId,
        (person, addresses) => new { person.Name, Addresses = addresses } // addresses zamiast address!
    );