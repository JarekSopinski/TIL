# Entity Framework - zaawansowane zagadnienia

## Zapytania z użyciem czystego SQL

Czyste zapytania SQL można wywoływać metodami FromSqlRaw lub FromSqlInterpolated - ta druga może przyjmować parametry, zabezpieczając przed sql injection.

    app.MapGet("GetWorkItemStatesWithRawSQL", async (MyBoardsContext db) =>
    {
        var minWorkItemsCount = 85;

        var states = await db.WorkItemStates
            .FromSqlInterpolated($@"
                SELECT wis.Id, wis.Value
                FROM WorkItemStates wis
                JOIN WorkItems wi on wi.StateId = wis.Id
                GROUP BY wis.Id, wis.Value
                HAVING COUNT(*) > {minWorkItemsCount}
            ")
            .ToListAsync();

        return states;

    });

Należy liczyć się z tym, że metody muszą zwracać dokładnie taki model, dla jakiego db setu wykonujemy dane polecenie.

Gdy mamy zapytanie, które chcemy wykonać niezależnie od konkretnego db setu, to możemy skorzystać z metody

    db.Database.ExecuteSQLRaw()
    db.Database.ExecuteSQLInterpolated()

## Utworzenie widoku SQL

Musimy utworzyć klasę reprezentującą dany widok oraz dodać ją do Db Context:

    public DbSet<TopAuthor> ViewTopAuthors { get; set; }

    modelBuilder.Entity<TopAuthor>(eb =>
    {
        eb.ToView("View_TopAuthors");
        eb.HasNoKey();
    });

Następnie generujemy migrację (która będzie pusta, gdyż nie następują zmiany w schemacie) i w pliku migracji ręcznie wprowadzamy kod SQL odpowiedzialny za stworzenie modelu:

    public partial class ViewTopAuthorsAdded : Migration
    {
        /// <inheritdoc />
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.Sql(@"
                CREATE VIEW View_TopAuthors AS
                SELECT TOP 5 u.FullName, COUNT(*) as [WorkItemsCreated]
                FROM Users u
                JOIN WorkItems wi on wi.AuthorId = u.Id
                GROUP BY u.Id, u.FullName
                ORDER BY [WorkItemsCreated] DESC
            ");
        }

        /// <inheritdoc />
        protected override void Down(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.Sql(@"
                DROP VIEW View_TopAuthors
            ");
        }
    }

## Owned

Jeżeli chcemy wydzielić jakieś wartośc do odrębnej klasy, ale nie chcemy tworzyć z tej klasy nowej tabeli w bazie (nie definiujemy klucza głównego i obcego), to możemy oznaczyć taką klasę jako Owned.

    public class Address
    {
        public Guid Id { get; set; }
        // ...
        public Coordinate Coordinate { get; set; }
    }

    [Owned]
    public class Coordinate
    {
        public decimal? Longitude { get; set; }
        public decimal? Latitude { get; set; }
    }

Analogiczny efekt możemy uzyskać w db context. Możemy wówczas dodatkowo wprowadzić konfigurację dla kolumn klasy Owned:

    modelBuilder.Entity<Address>()
        .OwnsOne(a => a.Coordinate, cmb =>
        {
            cmb.Property(c => c.Latitude).HasPrecision(18, 7);
            cmb.Property(c => c.Longitude).HasPrecision(18, 7);
        });

## Lazy loading

Gdy stosujemy metodę Include, wykonujemy tzn. eager loading, czyli wczesne ładowanie danych. Alternatywnie możemy wykonać lazy loading, czyli opóźnienie ładowania danych dopiero do momentu, gdy tak naprawdę będą nam one potrzebne. Gdybyśmy np. wykonywali jakąś czasochłonną logikę, zamiast pobierać powiązane dane od razu, moglibyśmy je pobrać dopiero po przeprocesowaniu tej logiki. Inny przypadek to warunkowe dołączenie konkretnych powiązanych danych.

Użycie LL wymaga w pierwszej kolejności instalacji paczki Microsoft.EntityFrameworkCore.Proxies.

    builder.Services.AddDbContext<MyBoardsContext>(
            option => option
                .UseLazyLoadingProxies()
                .UseSqlServer(builder.Configuration.GetConnectionString("MyBoardsConnectionString"))
        );

W rezultacie tej konfiguracji, we wszystkich klasach encji bazodanowych, tam gdzie występują referencje do encji powiązanej, należy dodać słowo kluczowe virtual, aby umożliwić wykorzystanie tam LL. Nie ustawienie tego spowoduje błąd.

    public class Address
    {
        public Guid Id { get; set; }
        // ...
        public virtual User User { get; set; }
    }

Wówczas możemy przygotować endpoint, w którym dane z powiązanej tabeli mogą np. zostać wczytane warunkowo:

    app.MapGet("GetUserWithAddressLazyLoad", async (MyBoardsContext db) =>
    {
        var withAddress = true;

        var user = db.Users
            .First(u => u.Id == Guid.Parse("0E6D4C9B-E5B8-4541-CBC3-08DA10AB0E61"));

        if (withAddress)
        {
            var result = new { FullName = user.FullName, Address = $"{user.Address.Street} {user.Address.City}" };
            return result;
        }


        return new { FullName = user.FullName, Address = "---" };
    });

Trzeba mieć jednak na uwadze, że przypadkowo możemy wielokrotnie wywoływać zapytanie SELECT (odnosząc się wielokrotnie do jakiejś właściwości), podczas gdy w podejściu eager loading SELECT wystąpiłby tylko raz. Może to prowadzić do nieświadomego generowania poważnych problemów wydajnościowych.

## Grupowanie konfiguracji - refaktor

Konfigurację poszczególnych encji można wydzielić z metody OnModelCreating do własnych klas, przykładowo:

    using Microsoft.EntityFrameworkCore;
    using Microsoft.EntityFrameworkCore.Metadata.Builders;

    namespace MyBoards.Entities.Configurations
    {
        public class AddressConfiguration: IEntityTypeConfiguration<Address>
        {
            public void Configure(EntityTypeBuilder<Address> builder)
            {
                builder.OwnsOne(a => a.Coordinate, cmb =>
                {
                    cmb.Property(c => c.Latitude).HasPrecision(18, 7);
                    cmb.Property(c => c.Longitude).HasPrecision(18, 7);
                });
            }
        }
    }

Następnie takie klasy można rejestrować w OnModelCreating dla poszczególnej encji z osobna:

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        new AddressConfiguration().Configure(modelBuilder.Entity<Address>());
    }

Lub, alternatywnie, można zezwolić na skanowanie projektu i automatyczną rejestrację wszystkich klas implementujących IEntityTypeConfiguration:

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.ApplyConfigurationsFromAssembly(this.GetType().Assembly);
    }

Po przeprowadzeniu refaktora można puścić testową migrację i sprawdzić, czy nie złapała ona żadnych zmian (jeżeli nie ma zmian to znaczy, że refaktor został wykonany poprawnie).

## Scaffold istniejącej bazy

    Scaffold-DbContext "Name=ConnectionStrings:ScaffoldTestConnectionString" Microsoft.EntityFrameworkCore.SqlServer -OutputDir  Entities

## Generacja danych dla seeda - Bogus

    using Bogus;
    using MyBoards.Entities;

    namespace MyBoards
    {
        public class DataGenerator
        {
            public static void Seed(MyBoardsContext context)
            {
                var locale = "pl";

                Randomizer.Seed = new Random(911); // hardcoded int will cause to generate always the   same data

                var addressGenerator = new Faker<Address>(locale)
                    //.StrictMode(true) // will force to populate all fields
                    .RuleFor(a => a.City, f => f.Address.City())
                    .RuleFor(a => a.Country, f => f.Address.Country())
                    .RuleFor(a => a.PostalCode, f => f.Address.ZipCode())
                    .RuleFor(a => a.Street, f => f.Address.StreetName());

                var userGenerator = new Faker<User>()
                    .RuleFor(u => u.Email, f => f.Person.Email)
                    .RuleFor(u => u.FullName, f => f.Person.FullName)
                    .RuleFor(u => u.Address, f => addressGenerator.Generate());

                var users = userGenerator.Generate(100);

                context.AddRange(users);
                context.SaveChanges();
            }
        }
    }

Program.cs:

    DataGenerator.Seed(dbContext);