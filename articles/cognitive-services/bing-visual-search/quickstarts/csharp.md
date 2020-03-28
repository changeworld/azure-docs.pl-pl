---
title: 'Szybki start: uzyskaj szczegółowe informacje o obrazie przy użyciu interfejsu API REST i języka C# — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 07ecac46ab13058d308c17c5747701ee5ed577fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446682"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Szybki start: uchodź szczegółowe informacje o obrazie za pomocą interfejsu API REST wyszukiwania wizualnego Bing i języka C #

Ten przewodnik Szybki start pokazuje, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i wyświetlić szczegółowe informacje, które zwraca.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna edycja [programu Visual Studio 2019](https://www.visualstudio.com/downloads/).
* [Json.NET framework](https://www.newtonsoft.com/json), dostępne jako pakiet NuGet.
* Jeśli używasz Linuksa / MacOS, możesz uruchomić tę aplikację za pomocą [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. W programie Visual Studio utwórz nowe rozwiązanie konsoli o nazwie BingSearchApisQuickStart. Dodaj następujące obszary nazw do głównego pliku kodu:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Dodaj zmienne klucza subskrypcji, punktu końcowego i ścieżkę do obrazu, który chcesz przekazać. `uriBase`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlany w witrynie Azure portal dla zasobu:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Utwórz metodę `GetImageFileName()` o nazwie, aby uzyskać ścieżkę dla obrazu:
    
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

Aby przekazać obraz lokalny, należy najpierw utworzyć dane formularza do wysłania do interfejsu API. Dane formularza muszą `Content-Disposition` zawierać nagłówek, jego `name` parametr musi być `filename` ustawiony na "image", a parametr można ustawić na dowolny ciąg. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przesłać, to 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Dodaj ciągi granic, aby sformatować dane formularza POST. Ciągi graniczne określają znaki początkowe, końcowe i newline dla danych:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Użyj następujących zmiennych, aby dodać parametry do danych formularza:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Utwórz funkcję `BuildFormDataStart()` o nazwie, aby utworzyć początek danych formularza przy użyciu ciągów granic i ścieżki obrazu:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Utwórz funkcję `BuildFormDataEnd()` o nazwie, aby utworzyć koniec danych formularza przy użyciu ciągów granic:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Wywoływanie interfejsu API wyszukiwania wizualnego Bing

1. Utwórz funkcję, aby wywołać punkt końcowy wyszukiwania wizualnego Bing i zwrócić odpowiedź JSON. Funkcja przyjmuje początek i koniec danych formularza, tablicy bajtowej zawierającej `contentType` dane obrazu i wartość.

2. Użyj żądania `WebRequest` do przechowywania identyfikatora URI, wartości contentType i nagłówków.  

3. Służy `request.GetRequestStream()` do zapisywania danych formularza i obrazu, a następnie uzyskać odpowiedź. Funkcja powinna być podobna do poniższej funkcji:
        
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

## <a name="create-the-main-method"></a>Tworzenie metody Głównej

1. W `Main` metodzie aplikacji pobierz nazwę pliku i dane binarne obrazu:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Skonfiguruj treść żądania POST przez sformatowanie dla niego ograniczenia. Następnie `startFormData()` zadzwoń `endFormData` i utwórz dane formularza:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Utwórz `ContentType` wartość według `CONTENT_TYPE_HEADER_PARAMS` formatowania i granicy danych formularza:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Uzyskaj odpowiedź interfejsu `BingImageSearch()` API, wywołując i drukując odpowiedź:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Korzystanie z obiektu HttpClient

Jeśli używasz `HttpClient`, można `MultipartFormDataContent` użyć klasy do tworzenia danych formularza. Wystarczy użyć następujących sekcji kodu, aby zastąpić odpowiednie metody w poprzednim przykładzie.

Zastąp `Main` metodę tym kodem:

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

Zastąp `BingImageSearch` metodę tym kodem:

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
