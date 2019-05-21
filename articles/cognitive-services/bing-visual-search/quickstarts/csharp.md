---
title: 'Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka C#'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: scottwhi
ms.openlocfilehash: b1518af9c37ffe0b8175e741b363d79941e3caaf
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905707"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka C#

Ten przewodnik Szybki Start pokazano, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i Wyświetl szczegółowe dane, które zwraca.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* [Struktury Json.NET](https://www.newtonsoft.com/json), która jest dostępna jako pakiet NuGet.
* Jeśli używasz systemu Linux/MacOS można uruchomić tej aplikacji za pomocą [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. W programie Visual Studio należy utworzyć nowe rozwiązanie konsoli o nazwie BingSearchApisQuickStart. Dodaj następujące przestrzenie nazw do pliku głównego kodu:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Dodaj zmienne dla klucz subskrypcji, punktu końcowego i ścieżkę do obrazu, który chcesz przekazać:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Utwórz metodę o nazwie `GetImageFileName()` można uzyskać ścieżki obrazu:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Utwórz metodę, aby uzyskać dane binarne obrazu:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Tworzenie danych formularza

Aby przekazać lokalny obraz, najpierw tworzy dane formularza do wysłania do interfejsu API. Mogą zawierać dane formularza `Content-Disposition` nagłówka, jego `name` parametru musi być równa "image", a `filename` parametr może być ustawiony na dowolny ciąg. Zawartość formularza zawiera dane binarne obrazu. Rozmiar maksymalny obrazu, które można przekazać to 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Dodaj granicę ciągi do formatowania danych POST formularza. Ciągi granic określają znaki rozpoczęcia, zakończenia i nowego wiersza dla danych:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Aby dodać parametry do danych formularza, należy użyć następujących zmiennych:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Tworzenie funkcji o nazwie `BuildFormDataStart()` do utworzenia początkowego dane formularza za pomocą ciągów granic i ścieżka obrazu:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Tworzenie funkcji o nazwie `BuildFormDataEnd()` utworzyć końca danych formularza za pomocą ciągów granic:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Wywoływanie interfejsu API wyszukiwania wizualnego Bing

1. Utwórz funkcję, która ma wywołać punkt końcowy wyszukiwania wizualnego Bing i zwracać odpowiedź w formacie JSON. Funkcja przyjmuje początek i koniec okresu danych formularza, tablica bajtów zawierająca dane obrazu i `contentType` wartość.

2. Użyj żądania `WebRequest` do przechowywania identyfikatora URI, wartości contentType i nagłówków.  

3. Użyj `request.GetRequestStream()` zapisać danych formularza i obraz, a następnie pobrać odpowiedzi. Funkcja powinny wyglądać podobnie do poniższego:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Utwórz metodę Main

1. W `Main` metoda aplikacji, pobierz nazwę pliku i danych binarnych obrazu:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Skonfiguruj treść żądania POST przez sformatowanie dla niego ograniczenia. Następnie wywołaj `startFormData()` i `endFormData` do utworzenia danych formularza:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Tworzenie `ContentType` wartość formatując `CONTENT_TYPE_HEADER_PARAMS` i granic danych formularza:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Uzyskaj odpowiedzi interfejsu API przez wywołanie metody `BingImageSearch()` i drukowanie odpowiedzi:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Korzystanie z obiektu HttpClient

Jeśli używasz `HttpClient`, możesz użyć `MultipartFormDataContent` klasa do tworzenia danych formularza. Wystarczy użyć następujące fragmenty kodu, aby zastąpić odpowiednie metody w poprzednim przykładzie.

Zastąp `Main` metody przy użyciu tego kodu:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Zastąp `BingImageSearch` metody przy użyciu tego kodu:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
