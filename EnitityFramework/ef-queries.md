# Entity Framework - zapytania

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