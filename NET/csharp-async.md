# Asynchroniczność w C#

Kod synchroniczny:

    var FileContent = reader.ReadToEnd();

Kod asynchroniczny:

    var FileContent = await Task.Run(() => reader.ReadToEnd());

Metoda wywołująca ten kod musi mieć dodane słowo kluczowe async oraz być zadeklarowana jako Task:

    private async Task ProcessFilesAsync()

Jeżeli metoda asynchroniczna coś zwraca:

    private async Task<int> ProcessFilesAsync() {}

    var result = await ProcessFilesAsync();

## Równoległe zadania

W sytuacji, gdy mamy listę asynchronicznych tasków w pętli, i chcemy aby wywołały się wszystkie równolegle (a nie czekały na siebie), możemy użyć metody WhenAll, która przyjmuje kolekcję tasków:

    List<Task> tasks = new List<Task>();

    for (...) {
        var Task = Task.Run(() => {
            // do something
        });

        tasks.Add(task);
    }

    await Task.WhenAll(tasks);

## Wstrzymywanie wykonania

Pierwszym sposobem wstrzymania działania programu jest Thread.Sleep(), które blokuje wątek na określoną ilość czasu:

    Thread.Sleep(3000);

Jeżeli chcemy zrobić to samo w sposób asynchroniczny, bez blokowania głównego wątku:

    await Task.Delay(3000);

## Asynchroniczne wyjątki

        static async Task FooAsync()
        {
            Console.WriteLine("Foo start");

            await Task.Delay(2000);

            throw new Exception();

            Console.WriteLine("Foo end");
        }

        static async Task Main(string[] args)
        {
            Console.WriteLine("Main started");

            try
            {
                await FooAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception");
            }

            Console.ReadKey();
        }