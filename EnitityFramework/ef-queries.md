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

## Usuwanie danych

Metoda Remove usuwa jeden element, metoda RemoveRange usuwa listę.

Przykładowe usuwanie dla powiązanych encji, które mają w pełni ustawione kaskadowe usuwanie.

    app.MapDelete("DeleteWorkItemTags", async (MyBoardsContext db) =>
    {
        var workItemTags = await db.WorkItemTag
            .Where(wit => wit.WorkItemId == 12)
            .ToListAsync();
        db.WorkItemTag.RemoveRange(workItemTags);

        var workItem = await db.WorkItems.FirstAsync(wi => wi.Id == 16);
        db.RemoveRange(workItem);

        await db.SaveChangesAsync();
    });

W przypadku, gdy mamy powiązane encje bez kaskadowego usuwania, musimy najpierw wyczyścić zależności. W poniższym przykładzie chcemy usunąć użytkownika, ale najpierw musimy wyczyścić wszystkie jego komentarze.

    app.MapDelete("DeleteUser", async (MyBoardsContext db) =>
    {
        var user = await db.Users
            .FirstAsync(u => u.Id == Guid.Parse("D5B87CCD-A9F2-4744-CBDF-08DA10AB0E61"));

        var userComments = db.Comments
            .Where(c => c.AuthorId == user.Id)
            .ToList();
        db.RemoveRange(userComments);
        await db.SaveChangesAsync(); // make sure comments get deleted before deleting user!

        db.Users.Remove(user);
        await db.SaveChangesAsync();
    });

Zamiast ręcznego usuwania wszystkich powiązanych relacji (jak w powyższym przykładzie) możemy też zautomatyzować ten proces. W builderze należy ustawić wartość OnDelete(DeleteBehavior.ClientCascade), która sprawi, że kaskadowe usuwanie zostanie zastosowane po stronie EF, ale nie będzie narzucone bezpośrednio na bazę i nie spowoduje tam błędu. Poza tym należy w endpoincie zastosować Include do powiązanych usuwanych danych.

Ustawienie DeleteBehavior.ClientCascade nie wymaga migracji, gdyż nie zmienia schematu bazy danych, jest to wyłącznie zachowanie po stronie EF.

    modelBuilder.Entity<Comment>(entityBuilder =>
    {
        entityBuilder.Property(x => x.CreatedDate).HasDefaultValueSql("getutcdate()");
        entityBuilder.Property(x => x.UpdatedDate).ValueGeneratedOnUpdate();
        entityBuilder
            .HasOne(c => c.Author)
            .WithMany(a => a.Comments)
            .HasForeignKey(c => c.AuthorId)
            .OnDelete(DeleteBehavior.ClientCascade); // kaskadowe usunięcie komentarzy użytkownika po stronie EF
    });

    app.MapDelete("DeleteUserClientCascade", async (MyBoardsContext db) =>
    {
        var user = await db.Users
            .Include(u => u.Comments) // dołączenie usunięcia komentarzy przy usuwaniu użytkownika
            .FirstAsync(u => u.Id == Guid.Parse("68366DBE-0809-490F-CC1D-08DA10AB0E61"));

        db.Users.Remove(user);
        await db.SaveChangesAsync();
    });

## Change Tracker

Wszystkie dane pobierane z db contextu są przez niego śledzone pod kątem wykrycia zmian usunięcia lub dodania konkretnej encji. Za ten mechanizm odpowiedzialny jest Change Tracker w ramach db contextu. Podczas danej operacji będzie on przechowywał:
- Original Value
- Value
- State (czy zostało zmienione) (Unchanged -> -, Modified -> UPDATE, Deleted -> DELETE, Added -> INSERT)

Zapytania mogą być również wykonywane bez żadnego śledzenia, do czego służy metoda AsNoTracking(). Można ją dodać gdy wiemy, że nie zamierzamy wprowadzić żadnych zmian.

    var user = await db.Users
        .AsNoTracking()
        .FirstAsync(u => u.Id == userId);

Stan CT można śledzić poprzez:

    db.ChangeTracker.Entries();

CT może być użyty np. do optymalizacji procesu usuwania - można za jego pomocą wykonać usuwanie bez konieczności wcześniejszego wykonywania selecta. Możemy w endpoincie 'utworzyć' reprezentację encji o id, które chcemy usunąć, a potem dodać ją do change trackera i zmienić jego state.

    app.MapDelete("DeleteWorkItemTracked", async (MyBoardsContext db) =>
    {
        var workItem = new Epic()
        {
            Id = 2
        };

        var entry = db.Attach(workItem);
        entry.State = EntityState.Deleted;

        db.SaveChanges();
    });


## Benchmark

Paczka która może być użyta do wygodnego mierzenia i porównywania wydajności endpointów.

Do testów wydajnościowych można utworzyć dodatkowy konsolowy projekt w tej samej solucji. Projekt ten musi być uruchamiany w trybie Release!

Tworzymy przykładową klasę z testami:

    using Microsoft.EntityFrameworkCore;
    using MyBoards.Entities;
    using BenchmarkDotNet.Attributes;

    namespace MyBoards.Benchmark
    {
        [MemoryDiagnoser]
        public class TrackingBenchmark
        {
            [Benchmark]
            public int WithTracking()
            {
                var optionsBuilder = new DbContextOptionsBuilder<MyBoardsContext>()
                    .UseSqlServer("Server=(LocalDb)\\MSSQLLocalDb;Database=MyBoardsDb;Trusted_Connection=True;");

                var _dbContext = new MyBoardsContext(optionsBuilder.Options);

                var comments = _dbContext.Comments
                    .ToList();

                return comments.Count;
            }

            [Benchmark]
            public int WithNoTracking()
            {
                var optionsBuilder = new DbContextOptionsBuilder<MyBoardsContext>()
                    .UseSqlServer("Server=(LocalDb)\\MSSQLLocalDb;Database=MyBoardsDb;Trusted_Connection=True;");

                var _dbContext = new MyBoardsContext(optionsBuilder.Options);

                var comments = _dbContext.Comments
                    .AsNoTracking()
                    .ToList();

                return comments.Count;
            }
        }
    }

W Program.cs:

    using BenchmarkDotNet.Running;
    using MyBoards.Benchmark;

    BenchmarkRunner.Run<TrackingBenchmark>();

Po uruchomieniu projektu (konsolowego, nie głównego!) w trybie bez debugowania, zostaną automatycznie wykonane testy i wyniki podsumowane w konsoli.