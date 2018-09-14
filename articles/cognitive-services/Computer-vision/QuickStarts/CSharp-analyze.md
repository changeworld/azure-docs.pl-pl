---
title: Przewodnik Szybki start dla języka C# dotyczący analizowania obrazów lokalnych przy użyciu interfejsu API przetwarzania obrazów | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu lokalnego, korzystając z funkcji przetwarzania obrazów i języka C# w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 8196787df9f7fadedda72d525aee440afe7b7d34
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771979"
---
# <a name="quickstart-analyze-a-local-image---rest-c35"></a>Szybki start: analizowanie obrazu lokalnego — REST, C&#35;

W tym przewodniku Szybki start przeprowadzisz analizę obrazu lokalnego w celu wyodrębnienia elementów wizualnych przy użyciu funkcji przetwarzania obrazów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Żądanie Analyze Image

[Metoda Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) umożliwia wyodrębnienie elementów wizualnych na podstawie zawartości obrazu. Możesz przekazać obraz lub określić adres URL obrazu i wybrać, które elementy mają zostać zwrócone. Może być to na przykład:

* Szczegółowa lista tagów związanych z zawartością obrazu.
* Opis zawartości obrazu w formie pełnego zdania.
* Współrzędne, płeć i wiek wszystkich twarzy znajdujących się na obrazie.
* Typ obrazu (clipart lub rysunek).
* Dominujący kolor, kolor akcentu lub to, czy obraz jest czarno-biały.
* Kategoria zdefiniowana w tej [taksonomii](../Category-Taxonomy.md).
* Czy na obrazie znajduje się zawartość przeznaczona dla osób dorosłych lub o charakterze seksualnym?

Aby uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet Newtonsoft.Json.
    1. W menu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj**, a następnie w polu **Wyszukiwanie** wpisz ciąg „Newtonsoft.Json”.
    1. Gdy zostanie wyświetlona pozycja **Newtonsoft.Json**, wybierz ją, a następnie kliknij kolejno pole wyboru obok nazwy projektu i pozycję **Zainstaluj**.
1. Zastąp ciąg `Program.cs` następującym kodem.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zmień wartość `uriBase` na lokalizację, z której uzyskano klucze subskrypcji, jeśli jest to konieczne.
1. Uruchom program.
1. Gdy pojawi się monit, wprowadź ścieżkę do obrazu lokalnego.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Analyze an image:");
            Console.Write("Enter the path to the image you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeAnalysisRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Make the REST API call.
                    response = await client.PostAsync(uri, content);
                }

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

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
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="analyze-image-response"></a>Odpowiedź na żądanie Analyze Image

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, na przykład:

```json
{
   "categories": [
      {
         "name": "abstract_",
         "score": 0.00390625
      },
      {
         "name": "others_",
         "score": 0.0234375
      },
      {
         "name": "outdoor_",
         "score": 0.00390625
      }
   ],
   "description": {
      "tags": [
         "road",
         "building",
         "outdoor",
         "street",
         "night",
         "black",
         "city",
         "white",
         "light",
         "sitting",
         "riding",
         "man",
         "side",
         "empty",
         "rain",
         "corner",
         "traffic",
         "lit",
         "hydrant",
         "stop",
         "board",
         "parked",
         "bus",
         "tall"
      ],
      "captions": [
         {
            "text": "a close up of an empty city street at night",
            "confidence": 0.7965622853462756
         }
      ]
   },
   "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
   "metadata": {
      "width": 3733,
      "height": 1986,
      "format": "Jpeg"
   },
   "color": {
      "dominantColorForeground": "Black",
      "dominantColorBackground": "Black",
      "dominantColors": [
         "Black",
         "Grey"
      ],
      "accentColor": "666666",
      "isBWImg": true
   }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z podstawową aplikacją dla systemu Windows, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).


> [!div class="nextstepaction"]
> [Use Computer Vision with C# (Korzystanie z funkcji przetwarzania obrazów w języku C#)](../Tutorials/CSharpTutorial.md)
