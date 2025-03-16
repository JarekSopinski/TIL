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