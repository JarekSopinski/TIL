## Przykład zastosowania paginacji z sortowaniem i filtrowaniem w Entity Framework

Klasa generyczna PagedResult:

    public class PagedResult<T>
    {
        public List<T> Items { get; set; }
        public int TotalPages { get; set; }
        public int ItemsFrom { get; set; }
        public int ItemsTo { get; set; }
        public int TotalItemsCount { get; set; }

        public PagedResult(List<T> items, int totalCount, int pageSize, int pageNumber)
        {
            Items = items;
            TotalItemsCount = totalCount;
            ItemsFrom = pageSize * (pageNumber - 1) + 1;
            ItemsTo = ItemsFrom + pageSize - 1;
            TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize); // 12 / 5 = 2.4 => 3
        }
    }

Endpoint z przykładowym użyciem encji User:

    app.MapGet("GetUsersPaginated", (MyBoardsContext db) =>
    {
        // example user input
        var filter = "a";
        string sortBy = "FullName"; // "FullName", "Email", null
        bool sortByDescending = false;
        int pageNumber = 1;
        int pageSize = 10;

        var query = db.Users
            .Where(u => filter == null || 
                u.Email.ToLower().Contains(filter.ToLower()) || 
                u.FullName.ToLower().Contains(filter.ToLower())
            );

        var totalCount = query.Count();

        // apply sortBy and sortByDescending
        if (sortBy != null)
        {
            var columnsSelector = new Dictionary<string, Expression<Func<User, object>>>
            {
                { nameof(User.Email), user => user.Email },
                { nameof(User.FullName), user => user.FullName },
            };

            var sortByExpression = columnsSelector[sortBy];

            if (sortByDescending)
            {
                query = query.OrderByDescending(sortByExpression);
            } else
            {
                query = query.OrderBy(sortByExpression);
            }
        }

        // apply pagination
        var result = query
            .Skip(pageSize * (pageNumber - 1))
            .Take(pageSize)
            .ToList();

        var pagedResult = new PagedResult<User>(result, totalCount, pageSize, pageNumber);

        return pagedResult;
    });