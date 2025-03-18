# Entity Framework - migracje

Migracja to reprezentacja w postaci kodu konkretnych procedur, które muszą być wykonane po stronie serwera bazy danych, aby odzwierciedlić naszą klasę DbContext do rzeczywistej bazy danych.

Aby wykonać migrację:

    Tools -> Nuget Packege Manager -> Package Manager Console
    (wymagana instalacja Microsoft.EntityFrameworkCore.Tools)
    add-migration <Nazwa>

Alternatywnie można wykonać komendę z CLI:

    dotnet ef migrations add <Nazwa>

Zostaną utworzone pliki migracji - są to automatycznie generowane pliki i nie powinny być ręcznie edytowane. Wyjątkiem od tej reguły może być potrzeba przeprowadzenia customowej migracji, gdy automatyczna migracja spowodowałaby utratę istniejących danych.

## Aplikowanie migracji - aktualizacja bazy

Sama migracja nie oznacza jeszcze wykonania tych procedur na bazie. Aby przeprowadzić aktualizację bazy, należy w konsoli nuget wykonać komendę:

    update-database -v // flaga -v pokaże wykonywane komendy

Alternatywnie można wykonać komendę z CLI:

    dotnet ef database update

### Generacja skryptów SQL

Powyższe podejście można stosować na lokalnym środowisku, jednak nie sprawdzi się to na produkcji.
Na produkcji, Microsoft zaleca korzystać z metody generacji skryptów SQL.

Aby wygenerować skrypt migracyjny, należy w w konsoli nuget wprowadzić komendę:

    script-migration

Wygeneruje to plik SQL na podstawie istniejących migracji. Taki plik można na spokojnie przeanalizować i następnie zaaplikować do bazy danych.

Można podać nazwę danej migracji, aby wygenerować skrypt tylko od tej migracji:

    script-migration <Nazwa>

lub zakres migracji:

    script-migration <Nazwa 1> <Nazwa 2>

Dodanie flagi -idempotent sprawi, że przy każdej operacji zostanie sprawdzone, czy dana migracja została już zaaplikowana (idempotentność). Spowoduje to uniknięcie problemów, gdyby ten sam skrypt migracyjny został wielokrotnie zaaplikowany na tej samej bazie.

### Automatyzacja migracji poprzez korzystanie bezpośrednio z DbContext

Możemy automatycznie wykonywać migracje, które nie zostały jeszcze zaaplikowane, przed uruchomieniem aplikacji. W tym celu można dodać kod do Program.cs, przez komendą app.Run():

    using var scope = app.Services.CreateScope();
    var dbContext = scope.ServiceProvider.GetService<MyBoardsContext>();

    var pendingMigrations = dbContext.Database.GetPendingMigrations();
    if (pendingMigrations.Any())
    {
        dbContext.Database.Migrate();
    }

## Cofanie migracji

Aby cofnąć migrację, która nie została jeszcze zaaplikowana, możemy ręcznie cofnąć zmiany w katalogu /Migrations lub wykonać w konsoli nuget komendę:

    remove-migration

Aby cofnąć migrację, która została już zaaplikowana na bazie, możemy wykonać komendę update-database z dodaniem nazwy poprzedniem migracji, do której chcemy się cofnąć (poprzedniej przed tą, którą usuwamy!), a po niej remove-migration, aby usunąć sam plik migracji i cofnąć snapshot.

    update-database <Nazwa>
    remove-migration