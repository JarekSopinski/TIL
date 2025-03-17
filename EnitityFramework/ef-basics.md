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