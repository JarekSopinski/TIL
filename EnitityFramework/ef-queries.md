# Entity Framework - zapytania

## Jak działają zapytania?

1. Procesowanie zapytania LINQ przez EF
2. Przekazanie rezultatu do providera bazy danych
3. Przetworzenie wyników.

## Zapytania o pobranie danych

Przykład prostego endpointu zwracającego wszystkie obiekty z danej encji (w przykładzie 'Tags'):

    app.MapGet("data", (MyBoardsContext db) =>
    {
        var tags = db.Tags.ToList();
        return tags;
    });

Inne proste przykłady:

    app.MapGet("GetEpicAndUser", (MyBoardsContext db) =>
    {
        var epic = db.Epics.First();
        var user = db.Users.First(u => u.FullName == "User One");
        return new { epic, user };
    });

    app.MapGet("GetWorkItems1", (MyBoardsContext db) =>
    {
        var toDoWorkItems = db.WorkItems
            .Where(wi => wi.StateId == 1)
            .ToList();
        return new { toDoWorkItems };
    });

Przykład endpointu z zapytaniem asynchronicznym:

    app.MapGet("GetNewComments", async (MyBoardsContext db) =>
    {
        var newComments = await db.Comments
            .Where(c => c.CreatedDate > new DateTime(2022, 7, 23))
            .ToListAsync();
        return newComments;
    });

Przykład endpointu z zapytaniem asynchronicznym i sortowaniem:

    app.MapGet("GetTop5NewestComments", async (MyBoardsContext db) =>
    {
        var topComments = await db.Comments
            .OrderByDescending(c => c.CreatedDate)
            .Take(5)
            .ToListAsync();
        return topComments;
    });

Przykład endpointu grupującego i zliczającego:

    app.MapGet("GetStatesCount", async (MyBoardsContext db) =>
    {
        var statesCount = await db.WorkItems
            .GroupBy(s => s.StateId)
            .Select(g => new { stateId = g.Key, count = g.Count() })
            .ToListAsync();
        return statesCount;
    });

Zapytanie na bazie podczas pobierania danych jest wykonywane tylko przy wywołaniu tych akcji:
- ToList()
- ToArray()
- Single()
- Count()
- AsEnumerable()
- Pętla for
(oraz ich wersjach asynchronicznych)

## Zapytania o aktualizację danych

Przykład zmiany właściwości bez zmiany relacji:

    app.MapPost("UpdateEpic", async (MyBoardsContext db) =>
    {
        Epic epic = await db.Epics.FirstAsync(epic => epic.Id == 1);

        epic.Area = "Updated area";
        epic.Priority = 1;
        epic.StartDate = DateTime.Now;

        await db.SaveChangesAsync();

        return epic;
    });

Przykład zmiany właściwości ze zmianą relacji:

    app.MapPost("UpdateEpicState", async (MyBoardsContext db) =>
    {
        Epic epic = await db.Epics.FirstAsync(epic => epic.Id == 1);

        epic.StateId = 1;

        await db.SaveChangesAsync();

        return epic;
    });

Drugi sposób - najpierw wyszukanie interesującej encji, potem przypisanie całej encji:

    app.MapPost("UpdateEpicState", async (MyBoardsContext db) =>
    {
        Epic epic = await db.Epics.FirstAsync(epic => epic.Id == 1);

        var rejectedState = await db.WorkItemStates.FirstAsync(a => a.Value == "Rejected");

        epic.State = rejectedState;

        await db.SaveChangesAsync();

        return epic;
    });

Zapytania na bazie są wykonywane dopiero przy wywołaniu SaveChanges / SaveChangesAsync.

## Zapytania o utworzenie danych

Utworzenie jednego elementu:

    app.MapPost("CreateTag", async (MyBoardsContext db) =>
    {
        Tag tag = new Tag()
        {
            // Id is auto created by DB
            Value = "EF"
        };

        // await db.AddAsync(tag);
        await db.Tags.AddAsync(tag);
        await db.SaveChangesAsync();

        return tag;
    });

Utworzenie wielu elementów:

    app.MapPost("CreateMultipleTags", async (MyBoardsContext db) =>
    {
        Tag tagmvc = new Tag()
        {
            Value = "MVC"
        };
        Tag tagasp = new Tag()
        {
            Value = "ASP"
        };

        var tags = new List<Tag>() { tagmvc, tagasp };

        await db.Tags.AddRangeAsync(tags);
        await db.SaveChangesAsync();

        return tags;
    });

Utworzenie powiązanych danych (rzuci błąd przy zwracaniu???)

    app.MapPost("CreateUserWithAddress", async (MyBoardsContext db) =>
    {
        var address = new Address()
        {
            City = "Gdańsk",
            Country = "Poland",
            Street = "Długa"
        };

        var user = new User()
        {
            Email = "user@test.com",
            FullName = "Test User",
            Address = address
        };

        // We don't have to manually add Address - EF will do it
        db.Users.Add(user);
        await db.SaveChangesAsync();

        return user;
    });

Zapytania na bazie są wykonywane dopiero przy wywołaniu SaveChanges / SaveChangesAsync.

## Ładowanie powiązanych danych

Zapytanie takie jak poniżej wywoła niepotrzebnie dwa oddzielne SELECTy i spowoduje błąd serializacji przy zwracaniu odpowiedzi:

    app.MapGet("GetAuthorWithComments", async (MyBoardsContext db) =>
    {
        var user = await db.Users.FirstAsync(u => u.Id == Guid.Parse("68366DBE-0809-490F-CC1D-08DA10AB0E61"));
        var userComments = db.Comments.Where(c => c.AuthorId == user.Id).ToListAsync();

        return user;
    });

Kwestię serializacji można rozwiązać poprzez następującą konfigurację:

    builder.Services.Configure<JsonOptions>(options =>
    {
        options.SerializerOptions.ReferenceHandler = ReferenceHandler.IgnoreCycles;
    });

Natomiast poprawne zapytanie łączące wymaga użycia metody Include:

    app.MapGet("GetAuthorWithComments", async (MyBoardsContext db) =>
    {
        var user = await db.Users
            .Include(u => u.Comments)
            .FirstAsync(u => u.Id == Guid.Parse("68366DBE-0809-490F-CC1D-08DA10AB0E61"));

        return user;
    });

Możemy też użyć metody ThenIclude, aby wyciągnąć dane z dalszej relacji (w przykładzie najpierw pobieramy komentarze użytkownika, a potem WorkItems przypisane do komentarzy)

    app.MapGet("GetAuthorWithOtherData", async (MyBoardsContext db) =>
    {
        var user = await db.Users
            .Include(u => u.Comments).ThenInclude(c => c.WorkItem)
            .Include(u => u.Address)
            .FirstAsync(u => u.Id == Guid.Parse("68366DBE-0809-490F-CC1D-08DA10AB0E61"));

        return user;
    });