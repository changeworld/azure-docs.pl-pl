---
title: 'Szybki start: Wizja komputerowa 2.0 i 2.1 - Wyodrębnij tekst drukowany i odręczny - REST, C #'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start można wyodrębnić drukowany i odręczny tekst z obrazu przy użyciu interfejsu API przetwarzania obrazów z języka C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b47c0a87f2b7e4f3fea2d5ed088372cabce2a994
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566101"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-20-and-21-rest-api-and-c"></a>Szybki start: wyodrębnianie drukowanego i odręcznego tekstu za pomocą interfejsu API Computer Vision 2.0 i 2.1 REST i C #

W tym przewodniku Szybki start można wyodrębnić wydrukowany i/lub odręczny tekst z obrazu za pomocą interfejsu API REST wizji komputerowej. Dzięki metodom Wynik operacji odczytu i [odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) [wsadowego](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) można wykryć tekst na obrazie i wyodrębnić rozpoznane znaki do strumienia znaków czytelnego maszynowo. Interfejs API określi, który model rozpoznawania ma być używany dla każdego wiersza tekstu, dzięki czemu obsługuje obrazy z tekstem drukowanym i odręcznym.

W porównaniu z programem Computer Vision 2.0 i 2.1, funkcja Publicznego Podglądu 3.0 zapewnia:

* jeszcze większa dokładność
* zmieniony format wyjściowy
* ocena zaufania dla słów
* obsługa zarówno języka hiszpańskiego, jak i angielskiego z dodatkowym parametrem językowym

#### <a name="version-2"></a>[Wersja 2](#tab/version-2)

> [!IMPORTANT]
> Metoda [odczytu partii](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) działa asynchronicznie. Ta metoda nie zwraca żadnych informacji w treści pomyślnej odpowiedzi. Zamiast tego metoda odczytu wsadowego zwraca identyfikator `Operation-Location` URI w wartości pola nagłówka odpowiedzi. Następnie można wywołać ten identyfikator URI, który reprezentuje interfejs API [wynik operacji odczytu,](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) aby sprawdzić stan i zwrócić wyniki wywołania metody odczytu wsadowego.

#### <a name="version-3-public-preview"></a>[Wersja 3 (publiczna wersja zapoznawcza)](#tab/version-3)

> [!IMPORTANT]
> Metoda [odczytu partii](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005) działa asynchronicznie. Ta metoda nie zwraca żadnych informacji w treści pomyślnej odpowiedzi. Zamiast tego metoda odczytu wsadowego zwraca identyfikator `Operation-Location` URI w wartości pola nagłówka odpowiedzi. Następnie można wywołać ten identyfikator URI, który reprezentuje interfejs API [wynik operacji odczytu,](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) aby sprawdzić stan i zwrócić wyniki wywołania metody odczytu wsadowego.

---


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) przed rozpoczęciem.

- Program [Visual Studio 2015 lub nowszy](https://visualstudio.microsoft.com/downloads/)musi mieć program Visual Studio.
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz próbny z [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Computer Vision i uzyskać klucz. Następnie [należy utworzyć zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu `COMPUTER_VISION_SUBSCRIPTION_KEY` punktu `COMPUTER_VISION_ENDPOINT`końcowego klucza i usługi, odpowiednio o nazwie i ,.

## <a name="create-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

#### <a name="version-2"></a>[Wersja 2](#tab/version-2)

Aby utworzyć próbkę w programie Visual Studio, wykonaj następujące czynności:

1. Utwórz nowe rozwiązanie Visual Studio w programie Visual Studio przy użyciu szablonu aplikacji konsoli na potrzeby środowiska Visual C#.
1. Zainstaluj pakiet NuGet Newtonsoft.Json.
    1. W menu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj**, a następnie w polu **Wyszukiwanie** wpisz ciąg „Newtonsoft.Json”.
    1. Gdy zostanie wyświetlona pozycja **Newtonsoft.Json**, wybierz ją, a następnie kliknij kolejno pole wyboru obok nazwy projektu i pozycję **Zainstaluj**.
1. Uruchom program.
1. Gdy pojawi się monit, wprowadź ścieżkę do obrazu lokalnego.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // the Batch Read method endpoint
        static string uriBase = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze";

        static async Task Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Text Recognition:");
            Console.Write(
                "Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                await ReadText(imageFilePath);
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with text.</param>
        static async Task ReadText(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Assemble the URI for the REST API method.
                string uri = uriBase;

                HttpResponseMessage response;

                // Two REST API methods are required to extract text.
                // One method to submit the image for processing, the other method
                // to retrieve the text found in the image.

                // operationLocation stores the URI of the second REST API method,
                // returned by the first REST API method.
                string operationLocation;

                // Reads the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Adds the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST API method, Batch Read, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(uri, content);
                }

                // The response header for the Batch Read method contains the URI
                // of the second method, Read Operation Result, which
                // returns the results of the process in the response body.
                // The Batch Read operation does not return anything in the response body.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // If the first REST API method completes successfully, the second 
                // REST API method retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Text
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the text.
                // You may need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1);

                if (i == 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

#### <a name="version-3-public-preview"></a>[Wersja 3 (publiczna wersja zapoznawcza)](#tab/version-3)

Aby utworzyć próbkę w programie Visual Studio, wykonaj następujące czynności:

1. Utwórz nowe rozwiązanie Visual Studio w programie Visual Studio przy użyciu szablonu aplikacji konsoli na potrzeby środowiska Visual C#.
1. Zainstaluj pakiet NuGet Newtonsoft.Json.
    1. W menu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj**, a następnie w polu **Wyszukiwanie** wpisz ciąg „Newtonsoft.Json”.
    1. Gdy zostanie wyświetlona pozycja **Newtonsoft.Json**, wybierz ją, a następnie kliknij kolejno pole wyboru obok nazwy projektu i pozycję **Zainstaluj**.
1. Uruchom program.
1. W wierszu polecenia wprowadź ścieżkę do obrazu lokalnego i języka do rozpoznania.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using System.Web;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        // An endpoint should have a format like "https://westus.api.cognitive.microsoft.com"
        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");

        // the Batch Read method endpoint
        static string uriBase = endpoint + "/vision/v3.0-preview/read/analyze";

        static void PrintUsage()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Cognitive Service Batch Read File Sample");
            Console.WriteLine("Usage: ");
            Console.WriteLine("    From Azure Cogntivie Service, retrieve your endpoint and subscription key.");
            Console.WriteLine("    Set environment variable COMPUTER_VISION_ENDPOINT, such as \"https://westus2.api.cognitive.microsoft.com\"");
            Console.WriteLine("    Set environment variable COMPUTER_VISION_SUBSCRIPTION_KEY, such as \"1234567890abcdef1234567890abcdef\"\n");
            Console.WriteLine("    Run the program without argument to enter a file name and a language manually.");
            Console.WriteLine("    Or run the program with a file name for an image file (bmp/jpg/png/tiff) or a PDF file, plus the language. The language can be \"en\" or \"es\".");
            Console.WriteLine("       For example: dotnet Program.dll sample.jpg en");
            Console.WriteLine();
        }

        static void Main(string[] args)
        {
            PrintUsage();

            if (string.IsNullOrEmpty(subscriptionKey) || string.IsNullOrEmpty(endpoint))
            {
                Console.Error.WriteLine("Please set environment variables COMPUTER_VISION_ENDPOINT and COMPUTER_VISION_SUBSCRIPTION_KEY.");
                return;
            }

            string imageFilePath;
            string language;
            if (args.Length == 0)
            {
                Console.Write(
                    "Enter the path to an image (bmp/jpg/png/tiff) or PDF with text you wish to read: ");
                imageFilePath = Console.ReadLine();
            }
            else
            {
                imageFilePath = args[0];
            }

            if (args.Length <= 1)
            {
                Console.Write(
                    "Enter the language to read: \"en\" or \"es\": ");
                language = Console.ReadLine();
            }
            else
            {
                language = args[1];
            }

            Console.WriteLine($"Endpoint:     [{endpoint}]");
            Console.WriteLine($"Subscription: [{subscriptionKey}]");
            Console.WriteLine($"URL:          [{uriBase}]");

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                ReadText(imageFilePath, language).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with text.</param>
        static async Task ReadText(string imageFilePath, string language)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                var builder = new UriBuilder(uriBase);
                builder.Port = -1;
                var query = HttpUtility.ParseQueryString(builder.Query);
                query["language"] = language;
                builder.Query = query.ToString();
                string url = builder.ToString();

                HttpResponseMessage response;

                // Two REST API methods are required to extract text.
                // One method to submit the image for processing, the other method
                // to retrieve the text found in the image.

                // operationLocation stores the URI of the second REST API method,
                // returned by the first REST API method.
                string operationLocation;

                // Reads the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Adds the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST API method, Batch Read, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(url, content);
                }

                // The response header for the Batch Read method contains the URI
                // of the second method, Read Operation Result, which
                // returns the results of the process in the response body.
                // The Batch Read operation does not return anything in the response body.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // If the first REST API method completes successfully, the second 
                // REST API method retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Text
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the text.
                // You may need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 60 && contentString.IndexOf("\"status\":\"succeeded\"") == -1);

                if (i == 60 && contentString.IndexOf("\"status\":\"succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

---

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie konsoli, podobnie jak w poniższym przykładzie:

#### <a name="version-2"></a>[Wersja 2](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[Wersja 3 (publiczna wersja zapoznawcza)](#tab/version-3)


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T16:44:36Z",
  "lastUpdatedDateTime": "2020-02-11T16:44:36Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "es",
        "angle": -0.8011,
        "width": 401,
        "height": 119,
        "unit": "pixel",
        "lines": [
          {
            "language": "es",
            "boundingBox": [
              15,
              42,
              372,
              38,
              373,
              91,
              15,
              97
            ],
            "text": "¡Buenos días!",
            "words": [
              {
                "boundingBox": [
                  15,
                  43,
                  243,
                  40,
                  244,
                  93,
                  17,
                  98
                ],
                "text": "¡Buenos",
                "confidence": 0.56
              },
              {
                "boundingBox": [
                  254,
                  40,
                  370,
                  38,
                  371,
                  91,
                  255,
                  93
                ],
                "text": "días!",
                "confidence": 0.872
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy rozwiązanie Visual Studio nie będzie już potrzebne, usuń je. W tym celu otwórz Eksplorator plików, przejdź do folderu, w którym utworzono rozwiązanie Visual Studio, i usuń folder.

## <a name="next-steps"></a>Następne kroki

Poznaj podstawową aplikację systemu Windows, która używa przetwarzania obrazów do wykonania optycznego rozpoznawania znaków (OCR). Twórz inteligentnie przycięte miniatury oraz wykrywaj, klasyfikuj, taguj i opisuj funkcje wizualne, w tym twarze, na obrazie.

> [!div class="nextstepaction"]
> [Samouczek dla języka C# dotyczący interfejsu API przetwarzania obrazów](../Tutorials/CSharpTutorial.md)
