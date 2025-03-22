# Wydajność Enitity Framework

## Zdefiniowanie indeksów dla encji

Indeks prosty:

    builder
        .HasIndex(user => user.Email);

Indeks złożony:

    builder
        .HasIndex(user => new { user.Email, user.FullName });

## Operator Select

Częstym błędem jest pobieranie wszystkich kolumn z danej tabeli, gdy do rezultatu potrzebujemy tylko konkretną kolumnę lub kolumny. Np w poniższym przypadku zostaną pobrane wszystkie kolumny, ponieważ Select jest wykonywany dopiero po ToListAsync:

    app.MapGet("GetUsersFromCountry", async (MyBoardsContext db) =>
    {
        var users = await db.Users
            .Include(u => u.Address)
            .Where(u => u.Address.Country == "Albania")
            .ToListAsync();

        return users.Select(u => u.FullName);
    });

Poprawiona wersja tego zapytania będzie wyglądać następująco - operator Select został wykonany wcześniej, a zatem zbędne kolumny nie zostaną pobrane.

    app.MapGet("GetUsersFromCountry", async (MyBoardsContext db) =>
    {
        var usersFullNames = await db.Users
            .Include(u => u.Address)
            .Where(u => u.Address.Country == "Albania")
            .Select(u => u.FullName)
            .ToListAsync();

        return usersFullNames;
    });

Analogicznie dla pobierania danych z powiązanej tabeli:

    app.MapGet("GetUsersCommentsFromCountry", async (MyBoardsContext db) =>
    {
        var comments = await db.Users
            .Include(u => u.Address)
            .Include(u => u.Comments)
            .Where(u => u.Address.Country == "Albania")
            .SelectMany(u => u.Comments)
            .Select(c => c.Message)
            .ToListAsync();

        return comments;
    });

## Problem n + 1

Problem ten występuje gdy aplikacja pobiera dane z bazy, a następnie przegląda wyniki w pętli - co oznacza ciągłe odwoływanie się do bazy.

Taki problem może wystąpić np. przy takim endpoincie - gdy mamy włączony lazy loading i nie deklarujemy jawnie Include:

    app.MapGet("GetData", async (MyBoardsContext db) =>
    {
        var users = await db.Users
            .Where(u => u.Address.Country == "Albania")
            .ToListAsync();

        foreach (var user in users)
        {
            foreach (var comment in user.Comments)
            {
                // Process comment
            }
        }
    });

Rozwiązaniem jest jawne zadeklarowanie Include, co wciąż możemy zrobić, nawet gdy mamy włączony lazy loading.

    app.MapGet("GetData", async (MyBoardsContext db) =>
    {
        var users = await db.Users
            .Where(u => u.Address.Country == "Albania")
            .Include(u => u.Comments)
            .ToListAsync();

        foreach (var user in users)
        {
            foreach (var comment in user.Comments)
            {
                // Process comment
            }
        }
    });

## Nadużywanie Include

## Bulk update / Bulk delete

Przy masowych aktualizacjach błędem może być najpierw pobranie wszystkich wpisów, a potem zmiana ich w pętli - spowoduje to wykonanie poleceń UPDATE dla wszystkich wpisów w tabeli.

Optymalizacja masowych zapytań aktualizacji lub usuwania danych może być przeprowadzona z wykorzystaniem paczki linq2db.EntityFrameworkCore.

    app.MapPut("BulkUpdateLinq2Db", async (MyBoardsContext db) =>
    {
        var users = db.Users
            .Where(u => u.FullName.StartsWith("Anthony"));

        await LinqToDB.LinqExtensions.UpdateAsync(users.ToLinqToDB(), x => new User
        {
            FullName = "Banthony",
        });

        // we don't have to run db.SaveChanges() - LinqToDB already does it!
    });