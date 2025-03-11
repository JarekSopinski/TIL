# Typy generyczne w C#

    internal class Restaurant{}

    internal class User{}

    internal class PaginatedResult<T> // klasa generyczna przyjmująca paramter T
    {
        public List<T> Results { get; set; }
        public int ResultsFrom { get; set; }
        public int ResultsTo { get; set; }
        public int TotalPages { get; set; }
        public int TotalResults { get; set; }
    }

    List<Restaurant> restaurants = new List<Restaurant>();
    PaginatedResult<Restaurant> result = new PaginatedResult<Restaurant>();
    result.Results = restaurants;

    List<User> users = new List<User>();
    PaginatedResult<User> resultUsers = new PaginatedResult<User>();
    resultUsers.Results = users;

## Przykład klasy z generycznymi parametrami

    internal class Repository<TKey, TValue>
    {
        private Dictionary<TKey, TValue> data = new Dictionary<TKey, TValue>();

        public void AddElement(TKey key, TValue element)
        {
            if (element != null)
            {
                data.Add(key, element);
            }
        }

        public TValue GetElement(TKey key)
        {
            if (data.TryGetValue(key, out TValue result))
            {
                return result;
            }
            else
            {
                return default;
            }
        }
    }

    var userRepository = new Repository<string, User>();
    userRepository.AddElement("Bill", new User() { Name = "Bill" });
    User bill = userRepository.GetElement("Bill");

## Ograniczenia generyczne (generic constraints)

Możemy nakładać ograniczenia na to, jaka wartość może zostać podłożona pod typ generyczny.

Przykładowo, możemy wymusić aby generyczny parametr T był typem referencyjnym (aby można było tam podłożyć tylko instancję jakiejś klasy):

    internal class Repository<T> where T : class
    {}

Możemy też wymusić referencję klasy implementującą określony interfejs. Dzięki temu będziemy mogli bezpiecznie odwoływać się do właściwości z tego interfejsu.

    internal class Repository<T> where T : IEntity
    {}

Można nakładać dowolną liczbę takich ograniczeń dla wielu generycznych parametrów:

    internal class Repository<TKey, TValue>
        where TKey : class
        where TValue : class

## Metody generyczne

Oprócz klas, także same metody mogą posiadać generyczne paramtery (i tak samo można nakładać na nie ograniczenia).

    internal class Utils
    {
        public static void Swap<T>(ref T first, ref T second)
        {
            T temp = first;
            first = second;
            second = temp;
        }
    }

    int[] intArray = new[] { 1, 3, 5 };
    Utils.Swap<int>(ref intArray[0], ref intArray[2]);

## Generyczne delegaty

Delegat to typ, który w silnie typowany sposób reprezentuje metodę.

W poniższym przykładzie, delegata o nazwie Display będzie mogła reprezentować dowolną funkcję, która przyjmuje wartość typu string i nic nie zwraca (void). Tym samym do takiej delegaty możemy np. przypisać Console.WriteLine, która spełnia te warunki.

    public delegate void Display(string value);
    Display display = Console.WriteLine;

Delegata może również używać generycznych parametrów, przykładowo:

    public delegate bool GenericPredicate<T>(T value);

Następnie taka generyczna delegata może być wykorzystana w generycznej metodzie:

    static int Count<T>(IEnumerable<T> elements, GenericPredicate<T> predicate)
    {
        int count = 0;
        foreach (T element in elements)
        {
            if (predicate(element))
            {
                count++;
            }
        }
        return count;
    }

    var numbers = new int[] { 10, 30, 50 };
    var count = Count(numbers, (int value) => value > 15);

    var strings = new string[] { "Generic", "Delegate", "test" };
    var stringCount = Count(strings, (string value) => value.Length > 4);