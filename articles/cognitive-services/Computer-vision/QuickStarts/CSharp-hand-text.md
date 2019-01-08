---
title: 'Szybki start: wyodrębnianie tekstu odręcznego — REST, C#'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wyodrębnisz z obrazu tekst odręczny przy użyciu interfejsu API przetwarzania obrazów i języka C#.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 892f66d13544d9bc53d62673143c5a3af3ee8af1
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578873"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-c35-in-computer-vision"></a>Szybki start: wyodrębnianie tekstu odręcznego przy użyciu interfejsu API REST i języka C&#35 w przetwarzaniu obrazów

W tym przewodniku Szybki start wyodrębnisz tekst odręczny z obrazu przy użyciu interfejsu API REST przetwarzania obrazów. Metody [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) i [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) umożliwiają wykrywanie tekstu odręcznego na obrazie i wyodrębnianie rozpoznanych znaków do strumienia znaków, który może być używany przez maszyny.

> [!IMPORTANT]
> W odróżnieniu od metody [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metoda [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) jest uruchamiana asynchronicznie. Ta metoda nie zwraca żadnych informacji w treści pomyślnej odpowiedzi. Zamiast tego metoda Recognize Text zwraca identyfikator URI w wartości pola nagłówka odpowiedzi `Operation-Content`. Następnie możesz wywołać ten identyfikator URI, który reprezentuje metodę [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), aby sprawdzić stan i zwrócić wyniki wywołania metody Recognize Text.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć program [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) lub nowszy.
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Aby uzyskać klucz subskrypcji, zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

Aby utworzyć próbkę w programie Visual Studio, wykonaj następujące czynności:

1. Utwórz nowe rozwiązanie Visual Studio w programie Visual Studio przy użyciu szablonu aplikacji konsoli na potrzeby środowiska Visual C#.
1. Zainstaluj pakiet NuGet Newtonsoft.Json.
    1. W menu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj**, a następnie w polu **Wyszukiwanie** wpisz ciąg „Newtonsoft.Json”.
    1. Gdy zostanie wyświetlona pozycja **Newtonsoft.Json**, wybierz ją, a następnie kliknij kolejno pole wyboru obok nazwy projektu i pozycję **Zainstaluj**.
1. Zastąp kod w `Program.cs` poniższym kodem, a następnie w odpowiednich miejscach wprowadź następujące zmiany:
    1. Zastąp wartość `subscriptionKey` kluczem subskrypcji.
    1. W razie potrzeby zastąp wartość `uriBase` adresem URL punktu końcowego dla metody [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) z regionu świadczenia usługi Azure, z którego uzyskano klucze subskrypcji.
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
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/recognizeText";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Handwriting Recognition:");
            Console.Write(
                "Enter the path to an image with handwritten text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                ReadHandwrittenText(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the handwritten text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with handwritten text.</param>
        static async Task ReadHandwrittenText(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameter.
                string requestParameters = "mode=Handwritten";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Two REST API methods are required to extract handwritten text.
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

                    // The first REST API method, Recognize Text, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(uri, content);
                }

                // The response header for the Recognize Text method contains the URI
                // of the second method, Get Recognize Text Operation Result, which
                // returns the results of the process in the response body.
                // The Recognize Text operation does not return anything in the response body.
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
                // Note: The response may not be immediately available. Handwriting
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the handwritten text.
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

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie konsoli, podobnie jak w poniższym przykładzie:

```json
{
    "status": "Succeeded",
    "recognitionResult": {
        "lines": [
            {
                "boundingBox": [
                    99,
                    195,
                    1309,
                    45,
                    1340,
                    292,
                    130,
                    442
                ],
                "text": "when you write them down",
                "words": [
                    {
                        "boundingBox": [
                            152,
                            191,
                            383,
                            154,
                            341,
                            421,
                            110,
                            458
                        ],
                        "text": "when"
                    },
                    {
                        "boundingBox": [
                            436,
                            145,
                            607,
                            118,
                            565,
                            385,
                            394,
                            412
                        ],
                        "text": "you"
                    },
                    {
                       "boundingBox": [
                            644,
                            112,
                            873,
                            76,
                            831,
                            343,
                            602,
                            379
                        ],
                        "text": "write"
                    },
                    {
                        "boundingBox": [
                            895,
                            72,
                            1092,
                            41,
                            1050,
                            308,
                            853,
                            339
                        ],
                        "text": "them"
                    },
                    {
                        "boundingBox": [
                            1140,
                            33,
                            1400,
                            0,
                            1359,
                            258,
                            1098,
                            300
                        ],
                        "text": "down"
                    }
                ]
            },
            {
                "boundingBox": [
                    142,
                    222,
                    1252,
                    62,
                    1269,
                    180,
                    159,
                    340
                ],
                "text": "You remember things better",
                "words": [
                    {
                        "boundingBox": [
                            140,
                            223,
                            267,
                            205,
                            288,
                            324,
                            162,
                            342
                        ],
                        "text": "You"
                    },
                    {
                        "boundingBox": [
                            314,
                            198,
                            740,
                            137,
                            761,
                            256,
                            335,
                            317
                        ],
                        "text": "remember"
                    },
                    {
                        "boundingBox": [
                            761,
                            134,
                            1026,
                            95,
                            1047,
                            215,
                            782,
                            253
                        ],
                        "text": "things"
                    },
                    {
                        "boundingBox": [
                            1046,
                            92,
                            1285,
                            58,
                            1307,
                            177,
                            1068,
                            212
                        ],
                        "text": "better"
                    }
                ]
            },
            {
                "boundingBox": [
                    155,
                    405,
                    537,
                    338,
                    557,
                    449,
                    175,
                    516
                ],
                "text": "by hand",
                "words": [
                    {
                        "boundingBox": [
                            146,
                            408,
                            266,
                            387,
                            301,
                            495,
                            181,
                            516
                        ],
                        "text": "by"
                    },
                    {
                        "boundingBox": [
                            290,
                            383,
                            569,
                            334,
                            604,
                            443,
                            325,
                            491
                        ],
                        "text": "hand"
                    }
                ]
            }
        ]
    }
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy rozwiązanie Visual Studio nie będzie już potrzebne, usuń je. W tym celu otwórz Eksplorator plików, przejdź do folderu, w którym utworzono rozwiązanie Visual Studio, i usuń folder.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z podstawową aplikacją dla systemu Windows, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API C&#35; Tutorial (Samouczek dla języka C# dotyczący interfejsu API przetwarzania obrazów)](../Tutorials/CSharpTutorial.md)
