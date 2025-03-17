# Entity Framework - podstawy

DbContext - klasa, która stanowi odzwierciedlenie konkretnej instancji bazy danych.
W ramach DbContextu mamy specjalne właściwości typu DbSet z generycznym parametrem reprezentującym klasę, odpowiadającą definicji tabeli, np.

    DbSet<User>
    DbSet<Address>

EF wymaga konkretnego rozszerzenia powiązanego z używanym serwerem bazy danych, np. Microsoft.EntityFrameworkCore.SqlServer.

Code First Approach - podejście, w którym najpierw piszemy kod, na podstawie którego powstanie baza danych.

## Konfiguracja startowa

Instalacja paczek:

    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="9.0.3" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="9.0.3" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="9.0.3">

Utworzenie connection string w appsettings.Development:

    "ConnectionStrings": {
      "MyBoardsConnectionString": "Server=(LocalDb)\\MSSQLLocalDb;Database=MyDbName;Trusted_Connection=True;"
    },

Utworzenie klasy dziedziczącej z DbContext z klasami reprezentującymi poszczególne tabele:

    using Microsoft.EntityFrameworkCore;

    namespace MyBoards.Entities
    {
        public class MyBoardsContext : DbContext
        {
            public MyBoardsContext(
                DbContextOptions<MyBoardsContext> options
                ) : base( options )
            {

            }
            public DbSet<WorkItem> WorkItems { get; set; }
            public DbSet<User> Users { get; set; }
            public DbSet<Tag> Tags { get; set; }
            public DbSet<Comment> Comments { get; set; }
            public DbSet<Address> Addresses { get; set; }

        }
    }

Podłączenie do buildera w Program.cs:

    builder.Services.AddDbContext<MyBoardsContext>(
        option => option.UseSqlServer(builder.Configuration.GetConnectionString("MyBoardsConnectionString"))
    );

## Klucze główne

W klasach reprezentujących tabele możemy utworzyć właściwości Guid lub int o nazwie 'Id' lub 'MojaEncjaId', które EF rozpozna jako klucz główny. Możemy też oznaczyć inną dowolną właściwość atrybutem [Key].

    public class User
    {
        public Guid Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Email { get; set; }
    }

lub - gdybyśmy chcieli, aby email był kluczem:

    public class User
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
        [Key]
        public string Email { get; set; }
    }

Jeżeli chcemy utworzyć złożony klucz główny, z począczenia wielu właściwości, musimy nadpisać klasę OnModelCreating w DbContext:

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .HasKey( x => new { x.Email, x.LastName } );
    }

## Konfiguracja modelu - atrybuty

Domyślnie EF przypisuje domyślnie pewne typy bazy danych na podstawie typów C#. Jeżeli chcemy dla danej kolumny zmienić to zachowanie lub np. założyć, że kolumny mają mieć inne nazwy niż właściwości klas, możemy np. użyć adnotacji właściwości.

    [Column(TypeName = "varchar(200)")]
    public string Area { get; set; }

    [Column("Iteration_Path")]
    public string IterationPath { get; set; }

    [Precision(3)]
    public DateTime? EndDate { get; set; }

    [Column(TypeName = "decimal(5,2)")]
    public decimal Effort { get; set; }

    [MaxLength(200)]
    public string Activity { get; set; }

    [Precision(14,2)]
    public decimal RemainingWork { get; set; }

To, czy EF domyślnie zakłada, czy właściwości są nullowalne, zależy od ustawnienia w pliku projektowym. Gdy to ustawienie jest włączone, EF domyślnie NIE zezwoli na wartości null.

    <Nullable>enable</Nullable>

Jeżeli to ustawienie jest włączone i chcemy zezwolić na nullowalną kolumnę:

    public string? State { get; set; }

Jeżeli to ustawienie jest wyłączone, i chcemy nie zezwolić na daną nullowalną kolumnę, musimy dodać atrybut Required:

    [Required]
    public string State { get; set; }

## Konfiguracja modelu - Fluent API

Alternatywnym względem powyższego sposobem jest wykorzystanie Fluent API i nadpisanie metody OnModelCreating w DbContext. Ustawienia danych encji mogą być deklarowane pojedynczo lub zbiorczo, z wykorzystaniem EntityTypeBuilder.

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<WorkItem>()
            .Property(x => x.State)
            .IsRequired();

        modelBuilder.Entity<WorkItem>()
            .Property(x => x.Area)
            .HasColumnType("varchar(200"));

        // ustawienie zbiorcze
        modelBuilder.Entity<WorkItem>(entityBuilder =>
        {
            entityBuilder.Property(wi => wi.IterationPath).HasColumnName("Iteration_Path");
            entityBuilder.Property(wi => wi.Effort).HasColumnType("decimal(5,2)");
            entityBuilder.Property(wi => wi.EndDate).HasPrecision(3);
            entityBuilder.Property(wi => wi.Activity).HasMaxLength(200);
            entityBuilder.Property(wi => wi.RemainingWork).HasPrecision(14, 2);
        });
    }

## Wartości generowane domyślnie

Wykorzystując Entity Builder, możemy również ustawić domyślne wartości:

        entityBuilder.Property(wi => wi.Priority).HasDefaultValue(1);

Jak również wartość ustawiane automatycznie przez bazę danych:

        entityBuilder.Property(x => x.CreatedDate).HasDefaultValueSql("getutcdate()");
        entityBuilder.Property(x => x.UpdatedDate).ValueGeneratedOnUpdate();

## Konfiguracja relacji

### Relacja 1 do 1

    public class Address
    {
        public Guid Id { get; set; }
        //...
        public Guid UserId { get; set; }
    }

    public class User
    {
        public Guid Id { get; set; }
        //...
        public Address Address { get; set; }
    }

    modelBuilder.Entity<User>()
        .HasOne(user => user.Address)
        .WithOne(address => address.User)
        .HasForeignKey<Address>(address => address.UserId);

### Relacja 1 do wielu

    public class WorkItem
    {
        public int Id { get; set; }
        //...
        public List<Comment> Comments { get; set; } = new List<Comment>();
    }

    public class Comment
    {
        public int Id { get; set; }
        //...
        public WorkItem WorkItem { get; set; }
        public int WorkItemId { get; set; }

    }

    modelBuilder.Entity<WorkItem>(entityBuilder =>
        {
            entityBuilder
                .HasMany(wi => wi.Comments)
                .WithOne(comment => comment.WorkItem)
                .HasForeignKey(comment => comment.WorkItemId);
        });

### Relacja wiele do wielu

Starsze wersje wymagały utworzenia tabeli łączącej, przykładowo:

    public class WorkItemTag
    {
        public WorkItem WorkItem { get; set; }
        public int WorkItemId { get; set; }

        public Tag Tag { get; set; }
        public int TagId { get; set; }
    }

    modelBuilder.Entity<WorkItemTag>()
        .HasKey(c => new { c.TagId, c.WorkItemId });

W nowszych wersjach może to być uproszczone i tabelę łączącą można pominąć. Zostanie ona wówczas automatycznie utworzona 'pod spodem'.

    public class WorkItem
    {
        public int Id { get; set; }
        //...
        public List<Tag> Tags { get; set; }
    }

    public class Tag
    {
        public int Id { get; set; }
        //...
        public List<WorkItem> WorkItems { get; set; }
    }

    modelBuilder.Entity<WorkItem>(entityBuilder =>
    {
        entityBuilder
            .HasMany(wi => wi.Tags)
            .WithMany(tag => tag.WorkItems);
    });

Jeżeli jednak chcemy zawrzeć np. jakąś własną właściwość dla tabeli łączącej (w przykładzie - data dołączenia taga do komentarza, PublicationDate) - możemy wciąż ręcznie utworzyć klasę takiej tabeli łączącej oraz skonfigurować ją w entity builderze:

    public class WorkItemTag
    {
        public WorkItem WorkItem { get; set; }
        public int WorkItemId { get; set; }

        public Tag Tag { get; set; }
        public int TagId { get; set; }

        public DateTime PublicationDate { get; set; }
    }

    entityBuilder
        .HasMany(wi => wi.Tags)
        .WithMany(tag => tag.WorkItems)
        .UsingEntity<WorkItemTag>(

        // relation WorkItemTag -> Tag
        wit => wit
            .HasOne(wit => wit.Tag)
            .WithMany()
            .HasForeignKey(wit => wit.TagId),

        // relation WorkItemTag -> WorkItem
        wit => wit
            .HasOne(wit => wit.WorkItem)
            .WithMany()
            .HasForeignKey(wit => wit.WorkItemId),

        // WorkItemTag composite key
        wit =>
        {
            wit.HasKey(x => new { x.TagId, x.WorkItemId });
            wit.Property(x => x.PublicationDate).HasDefaultValue("getutcdate()");
        }

    );

## Dziedziczenie a EF

### Table-per-hierarchy

Domyślne zachowanie, które sprawi, że dla typu bazowego jak i dla typów pochodnych, będzie utworzona jedna duża tabela, zawierająca w sobie wszystkie właściwości tych typów.

Wszystkie typy będą współdzielić klucz główny, a żeby odróżnić konkretny typ z tej wspólnej tabeli, EF utworzy kolumnę 'Discriminator', w której będzie przechowywać informację o typie danego rekordu.

Jeżeli jakaś właściwość występuje w jednej z dziedziczonych klas, a nie występuje w innych, będzie musiała być nullowalna.

Może być szybsze, ze względu na ograniczenie łączeń tabel, co potwierdziły testy Microsoftu.

### Table-per-type

Konfiguracja, która sprawi, że dla typu bazowego jak i dla typów pochodnych, zostaną utworzone osobne tabele w bazie danych.

Dla klas pochodnych zostaną utworzone tabele zawierające TYLKO właściwości konkretnego typu pochodnego oraz referencje (klucz obcy) do tabeli bazowej, która równocześnie będzie kluczem głównym danej tabeli.

Aby taką konfigurację utworzyć, potrzebne będzie jawne zdefiniowanie, do jakich tabel zmapować konkretne typy.

Wymaga utworzenia dodatkowej konfiguracji, określającej na jakie tabele zostaną zmapowane typy pochodne:

    modelBuilder.Entity<ChildA>().ToTable("ChildAs");
    modelBuilder.Entity<ChildB>().ToTable("ChildBs");

Może być wolniejsze, ze względu na to, że zapytania muszą łączyć wiele tabel.