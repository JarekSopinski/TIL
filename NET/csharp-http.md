# Komunikacja przez HTTP w C#

## Podstawowe przykłady zapytań

        static async Task Main(string[] args)
        {
            using (var httpClient = new HttpClient())
            {
                string address = "https://jsonplaceholder.typicode.com/posts";

                // GET
                var result = await httpClient
                    .GetAsync(address);
                var json = await result.Content.ReadAsStringAsync();

                // using Newtonsoft.Json;
                var posts = JsonConvert.DeserializeObject<List<Post>>(json);

                var selectedPost = posts.First(p => p.ID == 30);
                Console.WriteLine(selectedPost.Title);
                Console.WriteLine(selectedPost.Body);

                // POST
                var postJsonContent = new StringContent(JsonConvert.SerializeObject(selectedPost));
                var postResult = await httpClient
                    .PostAsync(address, postJsonContent);

                // POST - created in different way with headers
                using (var postRequestMessage = new HttpRequestMessage(HttpMethod.Post, address))
                {
                    postRequestMessage.Headers.Add("content-type", "application/json");
                    postRequestMessage.Headers.Add("someheader", "somevalue");
                    postRequestMessage.Content = postJsonContent;

                    var post2Result = await httpClient.SendAsync(postRequestMessage);
                }

                // Adding request params
                var queryParams = HttpUtility.ParseQueryString(string.Empty);
                queryParams["postId"] = "1";
                queryParams["someParam"] = "someValue";

                var formattedParams = queryParams.ToString();

            }
        }

## Przykłady zapytań z wykorzystaniem biblioteki flurl

        public static async Task Main()
        {
            string address = "https://jsonplaceholder.typicode.com/posts";

            var result = await address
                .GetAsync()
                .ReceiveJson<List<Post>>();

            var selectedPost = result.First(p => p.ID == 30);

            var postResult = await address
                .WithHeaders(new
                {
                    myheader = "value",
                    myheader2 = "value2",
                    some_header = "some-value"
                }, true) // Replace underscore with hypen
                .SetQueryParams(new
                {
                    postId = 1,
                    someValue = "value"
                })
                .PostJsonAsync(selectedPost);
        }