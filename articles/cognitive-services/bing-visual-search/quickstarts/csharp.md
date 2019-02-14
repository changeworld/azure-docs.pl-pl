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
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: d4a1ff43f6165e2620bbc3a5fbbf381ca114062e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884260"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i języka C#

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania wizualnego Bing i wyświetlenie wyników wyszukiwania. Ta prosta aplikacja C# przekazuje obraz do interfejsu API i wyświetla zwrócone informacje na jego temat.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet.
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowe rozwiązanie konsolowe o nazwie `BingSearchApisQuickStart` w programie Visual Studio. Dodaj następujące przestrzenie nazw do głównego pliku kodu.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Dodaj zmienne dla klucza subskrypcji, punkt końcowy i ścieżkę dla obrazu, który chcesz przekazać.

    ```csharp
        const string accessKey = "<yoursubscriptionkeygoeshere>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<pathtoimagegoeshere>";
    ```


1. Utwórz metodę o nazwie `GetImageFileName()` do uzyskiwania ścieżki obrazu
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

2. Utwórz metodę do uzyskiwania znaków binarnych obrazu.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Tworzenie danych formularza

Podczas przekazywania obrazu lokalnego dane formularza wysyłane do interfejsu API muszą być poprawnie sformatowane. Muszą zawierać nagłówek Content-Disposition, ich parametr `name` musi mieć wartość „image”, a parametr `filename` może być ustawiony na dowolny ciąg. Zawartość formularza obejmuje dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Aby sformatować dane formularza, dodaj ciągi graniczne na potrzeby poprawnego formatowania danych formularza POST, które określają znaki początku, końca i nowego wiersza dla danych.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Następujące zmienne posłużą do dodawania parametrów do danych formularza. 

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Utwórz funkcję o nazwie `BuildFormDataStart()`, aby utworzyć część początkową wymaganych danych formularza za pomocą ciągów granicznych i ścieżki obrazu.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Utwórz funkcję o nazwie `BuildFormDataEnd()`, aby utworzyć część końcową wymaganych danych formularza za pomocą ciągów granicznych.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Wywoływanie interfejsu API wyszukiwania wizualnego Bing

1. Utwórz funkcję, aby wywołać punkt końcowy wyszukiwania wizualnego Bing i zwrócić odpowiedź w formacie JSON. Ta funkcja powinna pobierać części początkową i końcową danych formularza, tablicę bajtów zawierającą dane obrazu oraz wartość contentType.

2. Użyj żądania `WebRequest` do przechowywania identyfikatora URI, wartości contentType i nagłówków.  

3. Użyj funkcji `request.GetRequestStream()`, aby zapisać dane formularza i obrazu. Następnie pobierz odpowiedź. Kod tej funkcji powinien wyglądać następująco:
        
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

## <a name="create-the-main-method"></a>Tworzenie metody main

1. W metodzie main aplikacji pobierz nazwę pliku i dane binarne obrazu. 

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Skonfiguruj treść żądania POST przez sformatowanie dla niego ograniczenia. Następnie wywołaj funkcje `startFormData()` i `endFormData` w celu utworzenia danych formularza. 

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Utwórz wartość ContentType, formatując element `CONTENT_TYPE_HEADER_PARAMS` i ograniczenie danych formularza.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Uzyskaj odpowiedź interfejsu API przez wywołanie funkcji `BingImageSearch()`. Następnie wydrukuj odpowiedź.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Korzystanie z obiektu HttpClient

Jeśli używasz obiektu HttpClient, możesz użyć obiektu MultipartFormDataContent w celu utworzenia danych formularza. Wystarczy użyć następujących fragmentów kodu i zastąpić nimi metody o takiej samej nazwie w poprzednim przykładzie.

Zastąp metodę Main następującym kodem:

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

Zastąp metodę BingImageSearch następującym kodem:

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
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../tutorial-bing-visual-search-single-page-app.md)
