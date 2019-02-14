---
title: 'Szybki start: Rozpoznawanie emocji na twarzach na obrazie — interfejs API rozpoznawania emocji, C#'
titlesuffix: Azure Cognitive Services
description: Uzyskaj informacje i przykładowy kod, które pomogą Ci szybko rozpocząć korzystanie z interfejsu API rozpoznawania emocji w języku C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0874fc7b0c78f93df806b5bf782477efab0c0207
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234162"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Szybki start: Tworzenie aplikacji do rozpoznawania emocji na twarzach na obrazie.

> [!IMPORTANT]
> Interfejs API rozpoznawania emocji zostanie wycofany w dniu 15 lutego 2019 r. Rozpoznawanie emocji jest teraz ogólnie dostępne jako część [interfejsu API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/).

Ten artykuł zawiera informacje i przykładowy kod, które pomogą Ci szybko rozpocząć korzystanie z [metody Recognize interfejsu API rozpoznawania emocji](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) w języku C#. Służy ona do rozpoznawanie emocji wyrażanej przez co najmniej jedną osobę na obrazie.

## <a name="prerequisites"></a>Wymagania wstępne
* Pobierz [zestaw SDK interfejsu API rozpoznawania emocji usług Cognitive Services dla systemu Windows](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Uzyskaj bezpłatny [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Przykładowe żądanie rozpoznania emocji w języku C#

Utwórz nowe rozwiązanie konsoli w programie Visual Studio, a następnie zastąp plik Program.cs poniższym kodem. Zmień wartość `string uri` tak, aby używany był region, z którego masz klucze subskrypcji. Zastąp wartość **Ocp-Apim-Subscription-Key** prawidłowym kluczem subskrypcji. Aby znaleźć klucz subskrypcji, przejdź do witryny Azure Portal. W okienku nawigacji po lewej stronie w sekcji **Klucze** przejdź do zasobu Interfejs API rozpoznawania emocji. Podobnie możesz uzyskać odpowiedni identyfikator URI połączenia, przechodząc do panelu **Przegląd** dla zasobu wymienionego w obszarze **Punkt końcowy**.

![Twoje klucze zasobu interfejsu API](../../media/emotion-api/keys.png)

Aby przetworzyć odpowiedź na żądanie, użyj biblioteki takiej jak `Newtonsoft.Json`. W ten sposób możesz obsłużyć ciąg JSON jako serię zarządzalnych obiektów nazywanych tokenami. Aby dodać tę bibliotekę do swojego pakietu, kliknij prawym przyciskiem myszy swój projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami Nuget**. Następnie wyszukaj nazwę **Newtonsoft**. Pierwszym wynikiem powinna być pozycja **Newtonsoft.Json**. Wybierz pozycję **Zainstaluj**. Możesz teraz odwoływać się do tej biblioteki w swojej aplikacji.

![Instalowanie pakietu Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); //

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Przykładowa odpowiedź dotycząca rozpoznawania emocji
Pomyślne wywołanie zwraca tablicę wpisów twarzy oraz skojarzonych z nimi ocen emocji. Są one uporządkowane według rozmiaru prostokąta twarzy w kolejności malejącej. Pusta odpowiedź wskazuje, że nie wykryto żadnych twarzy. Wpis rozpoznawania emocji zawiera następujące pola:

* faceRectangle: lokalizacja prostokąta twarzy na obrazie
* scores: ocena emocji dla każdej twarzy na obrazie

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
