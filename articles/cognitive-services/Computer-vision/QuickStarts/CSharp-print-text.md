---
title: Przewodnik Szybki start dla języka C# dotyczący optycznego rozpoznawania znaków przy użyciu interfejsu API przetwarzania obrazów | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Z tego przewodnika Szybki start dowiesz się, jak wyodrębnić z obrazu tekst drukowany za pomocą interfejsu API przetwarzania obrazów i języka C# w usługach Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 46193e7aa27285a887005a67bb662449e18d7b36
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772332"
---
# <a name="quickstart-extract-printed-text-ocr---rest-c35"></a>Szybki start: wyodrębnianie tekstu drukowanego (OCR) — REST, C&#35;

W tym przewodniku Szybki start dowiesz się, jak wyodrębnić z obrazu tekst drukowany przy użyciu przetwarzania obrazów. Czasem nazywa się to optycznym rozpoznawaniem znaków (OCR, Optical Character Recognition).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="ocr-request"></a>Żądanie OCR

[Metoda optycznego rozpoznawania znaków (OCR)](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) pozwala wykrywać na obrazie tekst wydrukowany i wyodrębniać rozpoznane znaki do strumienia znaków, którego mogą używać komputery.

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
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeOCRRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                string requestParameters = "language=unk&detectOrientation=true";

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

## <a name="ocr-response"></a>Odpowiedź na żądanie OCR

Po pomyślnym przetworzeniu żądania są zwracane wyniki OCR obejmujące tekst oraz pole ograniczenia dla regionów, wierszy i słów, na przykład:

```json
{
   "language": "en",
   "textAngle": -1.5000000000000335,
   "orientation": "Up",
   "regions": [
      {
         "boundingBox": "154,49,351,575",
         "lines": [
            {
               "boundingBox": "165,49,340,117",
               "words": [
                  {
                     "boundingBox": "165,49,63,109",
                     "text": "A"
                  },
                  {
                     "boundingBox": "261,50,244,116",
                     "text": "GOAL"
                  }
               ]
            },
            {
               "boundingBox": "165,169,339,93",
               "words": [
                  {
                     "boundingBox": "165,169,339,93",
                     "text": "WITHOUT"
                  }
               ]
            },
            {
               "boundingBox": "159,264,342,117",
               "words": [
                  {
                     "boundingBox": "159,264,64,110",
                     "text": "A"
                  },
                  {
                     "boundingBox": "255,266,246,115",
                     "text": "PLAN"
                  }
               ]
            },
            {
               "boundingBox": "161,384,338,119",
               "words": [
                  {
                     "boundingBox": "161,384,86,113",
                     "text": "IS"
                  },
                  {
                     "boundingBox": "274,387,225,116",
                     "text": "JUST"
                  }
               ]
            },
            {
               "boundingBox": "154,506,341,118",
               "words": [
                  {
                     "boundingBox": "154,506,62,111",
                     "text": "A"
                  },
                  {
                     "boundingBox": "248,508,247,116",
                     "text": "WISH"
                  }
               ]
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z podstawową aplikacją dla systemu Windows, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsami API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API C&#35; Tutorial (Samouczek dla języka C# dotyczący interfejsu API przetwarzania obrazów)](../Tutorials/CSharpTutorial.md)
