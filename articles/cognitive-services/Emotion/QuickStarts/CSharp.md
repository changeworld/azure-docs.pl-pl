---
title: Emocji interfejsu API języka C# szybki start | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod, aby szybko rozpocząć pracę przy użyciu interfejsu API rozpoznawania emocji — warstwa w języku C# w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: acf30e7aded92c6d07331089fabd02836a8f3e9a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347565"
---
# <a name="emotion-api-c-quick-start"></a>Emocji interfejsu API języka C# szybki start

> [!IMPORTANT]
> Wersja zapoznawcza interfejsu API wideo była dostępna do 30 października 2017. Można łatwo wyodrębnić szczegółowe informacje z plików wideo, spróbuj nowe [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/). Możesz również służy do ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. Aby dowiedzieć się więcej, zobacz [Podgląd indeksatora wideo](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) omówienie.

Ten artykuł zawiera informacje i przykładowy kod, aby szybko rozpocząć pracę za pomocą [metody rozpoznaje interfejsu API rozpoznawania emocji — warstwa](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) języku C#. Służy on do rozpoznawania emocji wyrażonych przez osoby na obrazie. 

## <a name="prerequisites"></a>Wymagania wstępne
* Pobierz usługi kognitywnych [interfejsu API rozpoznawania emocji — warstwa zestaw Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Pobierz bezpłatny [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Przykładowe żądanie, na emocji rozpoznawania C#

Utwórz nowe rozwiązanie konsoli w programie Visual Studio, a następnie zastąp plik Program.cs następującym kodem. Zmień `string uri` do użycia regionu, w którym uzyskać klucze subskrypcji. Zastąp **Ocp-Apim-subskrypcji — klucz** wartości z klucza ważnej subskrypcji. Aby znaleźć klucz subskrypcję, przejdź do portalu Azure. W okienku nawigacji po lewej stronie w obszarze **klucze** sekcji, przejdź do zasobu interfejsu API rozpoznawania emocji — warstwa. Podobnie można uzyskać odpowiednią połączyć identyfikator URI w **omówienie** panelu zasobu kategorii **punktu końcowego**.

![Klucze interfejsu API zasobów](../../media/emotion-api/keys.png)

Aby przetwarzać żądania odpowiedzi, należy użyć biblioteki, takich jak `Newtonsoft.Json`. W ten sposób można obsługiwać ciągu JSON jako serię zarządzanych obiektów o nazwie tokenów. Aby dodać tej biblioteki do pakietu, kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz **Zarządzaj pakietami Nuget**. Następnie wyszukaj **Newtonsoft**. Pierwszy wynik powinien być **Newtonsoft.Json**. Wybierz pozycję **Zainstaluj**. Teraz możesz odwoływać tej biblioteki w aplikacji.

![Zainstaluj Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

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

## <a name="recognize-emotions-sample-response"></a>Rozpoznaje, że emocji przykładowa odpowiedź
Pomyślnego połączenia zwraca tablicę wpisów krój oraz wyniki emocji skojarzone. Są one uporządkowane według krój rozmiar prostokąta w kolejności malejącej. Pustą odpowiedź wskazuje, że zostały wykryte nie kroje. Wpis emocji zawiera następujące pola:

* faceRectangle: lokalizacji prostokąta powierzchni w obrazie
* wyniki: emocji wyników dla każdej powierzchni w obrazie 

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
