---
title: Szybki Start C# dla programu Microsoft — strona główna Maker interfejsu API (V4) - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API usługi Microsoft Translator tekstu w kognitywnych usług Microsoft Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jaswel
ms.openlocfilehash: 15f0415819bc0ae51e8160bb2b4a12c409b13baa
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301342"
---
# <a name="quickstart-for-microsoft-qna-maker-api-with-c"></a>Szybki Start dla programu Microsoft — strona główna Maker interfejsu API w języku C# 
<a name="HOLTop"></a>

W tym artykule przedstawiono sposób użycia [interfejsu API usługi Microsoft — strona główna Maker](../Overview/overview.md) języku C# wykonać następujące czynności.

- [Tworzenie nowej wiedzy.](#Create)
- [Aktualizowanie istniejącej bazy wiedzy knowledge base.](#Update)
- [Pobierz stan żądania do tworzenia lub aktualizowania bazy wiedzy.](#Status)
- [Publikowanie istniejącej bazy wiedzy knowledge base.](#Publish)
- [Zamień zawartość istniejącej bazy wiedzy knowledge base.](#Replace)
- [Pobierz zawartość z bazy wiedzy.](#GetQnA)
- [Uzyskaj odpowiedzi na pytanie, używając bazy wiedzy.](#GetAnswers)
- [Pobiera informacje o bazie wiedzy.](#GetKB)
- [Pobiera informacje o wszystkich baz wiedzy należące do określonego użytkownika.](#GetKBsByUser)
- [Usuwanie bazy wiedzy.](#Delete)
- [Pobieranie bieżącego kluczy punktu końcowego.](#GetKeys)
- [Ponownie wygenerować klucze bieżący punkt końcowy.](#PutKeys)
- [Pobierz bieżący zestaw zmian programu word.](#GetAlterations)
- [Zastąp bieżący zestaw zmian programu word.](#PutAlterations)

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) tego kodu dla systemu Windows. (Bezpłatna wersja Community będzie działać.)

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API usługi Microsoft — strona główna Maker**. Konieczne będzie klucza płatnej subskrypcji, z Twojego [pulpitu nawigacyjnego Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Tworzenie bazy wiedzy

Poniższy kod tworzy podstawowy, za pomocą nowej wiedzy [Utwórz](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/create";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string kb = @"
{
  'name': 'QnA Maker FAQ',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";

        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<Response> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> PostCreateKB(string kb)
        {
            string uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            return await Post(uri, kb);
        }

        async static Task<Response> GetStatus(string operation)
        {
            string uri = host + service + operation;
            Console.WriteLine("Calling " + uri + ".");
            return await Get(uri);
        }

        async static void CreateKB()
        {
            
            var response = await PostCreateKB(kb);
            var operation = response.headers.GetValues("Location").First();
            Console.WriteLine(PrettyPrint(response.response));

            var done = false;
            while (true != done)
            {
                response = await GetStatus(operation);
                Console.WriteLine(PrettyPrint(response.response));

                var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

                String state = fields["operationState"];
                if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
                {
                    var wait = response.headers.GetValues("Retry-After").First();
                    Console.WriteLine("Waiting " + wait + " seconds...");
                    Thread.Sleep(Int32.Parse(wait) * 1000);
                }
                else
                {
                    Console.WriteLine("Press any key to continue.");
                    done = true;
                }
            }
        }

        static void Main(string[] args)
        {
            CreateKB();
            Console.ReadLine();
        }
    }
}

```

**Tworzenie odpowiedzi wiedzy**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Powrót do początku](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Aktualizacja wiedzy

Poniższy kod aktualizuje wiedzy na temat podstawowej za pomocą [aktualizacji](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string new_kb = @"
{
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/en-us/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}
";

        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<Response> Patch(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = new HttpMethod("PATCH");
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                return new Response(response.Headers, responseBody);
            }
        }

        async static Task<Response> PostUpdateKB(string kb, string new_kb)
        {
            string uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            return await Patch(uri, new_kb);
        }

        async static Task<Response> GetStatus(string operation)
        {
            string uri = host + service + operation;
            Console.WriteLine("Calling " + uri + ".");
            return await Get(uri);
        }

        async static void UpdateKB(string kb, string new_kb)
        {
            var response = await PostUpdateKB(kb, new_kb);
            var operation = response.headers.GetValues("Location").First();
            Console.WriteLine(PrettyPrint(response.response));

            var done = false;
            while (true != done)
            {
                response = await GetStatus(operation);
                Console.WriteLine(PrettyPrint(response.response));

                var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

                String state = fields["operationState"];
                if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
                {
                    var wait = response.headers.GetValues("Retry-After").First();
                    Console.WriteLine("Waiting " + wait + " seconds...");
                    Thread.Sleep(Int32.Parse(wait) * 1000);
                }
                else
                {
                    Console.WriteLine("Press any key to continue.");
                    done = true;
                }
            }
        }

        static void Main(string[] args)
        {
            UpdateKB(kb, new_kb);
            Console.ReadLine();
        }
    }
}

```

**Zaktualizuj odpowiedzi wiedzy**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Powrót do początku](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Pobierz stan żądania

Możesz wywołać [operacji](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) , aby sprawdzić stan żądania do tworzenia lub aktualizowania bazy wiedzy. Aby zobaczyć, jak ta metoda jest używana, można znaleźć w przykładowym kodzie dla [Utwórz](#Create) lub [aktualizacji](#Update) metody.

[Powrót do początku](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publikowanie bazy wiedzy

Poniższy kod publikuje wiedzy na temat podstawowej za pomocą [publikowania](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Post(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void PublishKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Post(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            PublishKB();
            Console.ReadLine();
        }
    }
}

```

**Publikowanie bazy wiedzy knowledge base odpowiedzi**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "result": "Success."
}
```

[Powrót do początku](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Zastąp wiedzy

Poniższy kod zastępuje zawartość określonego znajomości podstawowej za pomocą [Zastąp](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_publish) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string new_kb = @"
{
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
}
";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Put(string uri, String body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Put;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void ReplaceKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Put(uri, new_kb);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            ReplaceKB();
            Console.ReadLine();
        }
    }
}

```

**Zastąp odpowiedzi wiedzy**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
    "result": "Success."
}
```

[Powrót do początku](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Pobierz zawartość z bazy wiedzy

Poniższy kod pobiera zawartość określonego znajomości podstawowej za pomocą [Pobierz wiedzy](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_download) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/{0}/{1}/qna/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        // NOTE: Replace this with "test" or "prod".
        static string env = "test";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetQnA()
        {
            var method_with_id = String.Format(method, kb, env);
            var uri = host + service + method_with_id;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetQnA();
            Console.ReadLine();
        }
    }
}

```

**Pobierz odpowiedzi wiedzy**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Powrót do początku](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-by-using-a-knowledge-base"></a>Odpowiedzi na pytania za pomocą bazy wiedzy

Poniższy kod pobiera odpowiedzi na pytanie, przy użyciu określonej bazie wiedzy, używając **Generowanie odpowiedzi** metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
1. Dodaj kod poniżej.
1. Zastąp `host` wartość nazwą witryny sieci Web dla Twojej subskrypcji Maker — strona główna. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Maker — strona główna](../How-To/set-up-qnamaker-service-azure.md).
1. Zastąp `endpoint_key` wartości z klucza prawidłowy punkt końcowy dla Twojej subskrypcji. Należy pamiętać, że nie jest taka sama jak klucz subskrypcji. Można uzyskać przy użyciu kluczy punktu końcowego [pobieranie punktu końcowego kluczy](#GetKeys) metody.
1. Zastąp `kb` wartości z Identyfikatorem wiedzy, aby wysłać zapytanie do odpowiedzi. Należy pamiętać, to wiedzy musi już zostały opublikowane przy użyciu [publikowania](#Publish) metody.
1. Uruchom program.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace QnAMaker
{
    class Program
    {
        // NOTE: Replace this with a valid host name.
        static string host = "ENTER HOST HERE";

        // NOTE: Replace this with a valid endpoint key.
        // This is not your subscription key.
        // To get your endpoint keys, call the GET /endpointkeys method.
        static string endpoint_key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        // Make sure you have published the knowledge base with the
        // POST /knowledgebases/{knowledge base ID} method.
        static string kb = "ENTER KB ID HERE";

        static string service = "/qnamaker";
        static string method = "/knowledgebases/" + kb + "/generateAnswer/";

        static string question = @"
{
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}
";

        async static Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpoint_key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetAnswers()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Post(uri, question);
            Console.WriteLine(response);
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetAnswers();
            Console.ReadLine();
        }
    }
}
```

**Uzyskaj odpowiedzi odpowiedzi**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Powrót do początku](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Pobierz informacje o bazie wiedzy

Poniższy kod pobiera informacje o wiedzy określony podstawowy, za pomocą [uzyskiwanie szczegółowych informacji z bazy wiedzy knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetKB();
            Console.ReadLine();
        }
    }
}

```

**Uzyskaj odpowiedzi szczegóły wiedzy**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Powrót do początku](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Pobierz wszystkie baz wiedzy dla użytkownika

Poniższy kod pobiera informacje o wszystkich baz wiedzy dla określonego użytkownika, za pomocą [uzyskać baz wiedzy dla użytkownika](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasesforuser) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetKBsByUser()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetKBsByUser();
            Console.ReadLine();
        }
    }
}

```

**Pobierz baz wiedzy na odpowiedź użytkownika**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Powrót do początku](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Poniższy kod usuwa określony znajomości podstawowej za pomocą [usunąć wiedzy](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_delete) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/knowledgebases/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with a valid knowledge base ID.
        static string kb = "ENTER ID HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Delete(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void DeleteKB()
        {
            var uri = host + service + method + kb;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Delete(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            DeleteKB();
            Console.ReadLine();
        }
    }
}
```

**Usuń odpowiedź wiedzy**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "result": "Success."
}
```

[Powrót do początku](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Pobieranie punktu końcowego kluczy

Poniższy kod pobiera bieżące klucze punkt końcowy, za pomocą [pobieranie punktu końcowego kluczy](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/endpointkeys/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetEndpointKeys()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetEndpointKeys();
            Console.ReadLine();
        }
    }
}
```

**Uzyskaj odpowiedzi kluczy punktu końcowego**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Powrót do początku](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Odśwież kluczy punktu końcowego

Poniższy kod generuje klucze bieżący punkt końcowy, za pomocą [Odśwież kluczy punktu końcowego](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_refreshendpointkeys) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/endpointkeys/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // NOTE: Replace this with "PrimaryKey" or "SecondaryKey."
        static string key_type = "PrimaryKey";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Patch(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = new HttpMethod("PATCH");
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void RefreshEndpoints()
        {
            var uri = host + service + method + key_type;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Patch(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            RefreshEndpoints();
            Console.ReadLine();
        }
    }
}
```

**Odśwież odpowiedzi kluczy punktu końcowego**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Powrót do początku](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Pobierz zmiany w programie word

Poniższy kod pobiera bieżące zmiany word, przy użyciu [Pobierz zmiany](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/alterations/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Get(string uri)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(uri);
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                return await response.Content.ReadAsStringAsync();
            }
        }

        async static void GetAlterations()
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Get(uri);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            GetAlterations();
            Console.ReadLine();
        }
    }
}
```

**Uzyskaj odpowiedzi zmiany word**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Powrót do początku](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Zastąp zmiany w programie word

Poniższy kod zastępuje bieżące zmiany word, przy użyciu [Zastąp zmiany](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) metody.

1. Utwórz nowy projekt C# w Twoje ulubione IDE.
2. Dodaj kod poniżej.
3. Zastąp `key` wartości z klucza dostępu prawidłową dla Twojej subskrypcji.
4. Uruchom program.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace QnAMaker
{
    class Program
    {
        static string host = "https://westus.api.cognitive.microsoft.com";
        static string service = "/qnamaker/v4.0";
        static string method = "/alterations/";

        // NOTE: Replace this with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string alterations = @"
{
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}
";

        public struct Response
        {
            public HttpResponseHeaders headers;
            public string response;

            public Response(HttpResponseHeaders headers, string response)
            {
                this.headers = headers;
                this.response = response;
            }
        }

        static string PrettyPrint(string s)
        {
            return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
        }

        async static Task<string> Put(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = new HttpMethod("PUT");
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    return "{'result' : 'Success.'}";
                }
                else
                {
                    return await response.Content.ReadAsStringAsync();
                }
            }
        }

        async static void ReplaceAlterations(string alterations)
        {
            var uri = host + service + method;
            Console.WriteLine("Calling " + uri + ".");
            var response = await Put(uri, alterations);
            Console.WriteLine(PrettyPrint(response));
            Console.WriteLine("Press any key to continue.");
        }

        static void Main(string[] args)
        {
            ReplaceAlterations(alterations);
            Console.ReadLine();
        }
    }
}
```

**Zastąp odpowiedzi zmiany word**

Odpowiedź oznaczająca Powodzenie jest zwracany w formacie JSON, jak pokazano w poniższym przykładzie: 

```json
{
  "result": "Success."
}
```

[Powrót do początku](#HOLTop)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST Maker — strona główna (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

## <a name="see-also"></a>Zobacz także 

[Omówienie Maker — strona główna](../Overview/overview.md)
