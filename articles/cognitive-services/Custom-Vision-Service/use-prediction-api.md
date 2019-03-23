---
title: 'Przykład: Użycie punktu końcowego przewidywania do programowego testowania obrazów przy użyciu klasyfikatora — Custom Vision'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak programowo testować obrazy za pomocą interfejsu API przy użyciu klasyfikatora usługi Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: e50933ea0231b4be22c2d0f82d33fd02dd0918f5
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351613"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically"></a>Użyj endpoint prognoz, aby przetestować obrazy programowe

Po wyszkoleniu modelu można przetestować obrazy programowo, przesyłając je do interfejsu API przewidywania.

> [!NOTE]
> W tym dokumencie przedstawiono przesyłanie obrazu do interfejsu API przewidywania przy użyciu języka C#. Aby uzyskać więcej informacji i przykładów użycia interfejsu API, zobacz [dokumentację interfejsu API przewidywania](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Pobieranie adresu URL i klucza predykcyjnego

Na stronie [Custom Vision ](https://customvision.ai) wybierz swój projekt, a następnie kartę __Wydajność__. Aby wyświetlić informacje o korzystaniu z interfejsu API przewidywania w tym __Klucza predykcyjnego__, wybierz __Adres URL przewidywania__. Dla projektów dołączonych do zasobu platformy Azure Twoje __prognozowania klucz__ można także znaleźć w [witryny Azure portal](https://portal.azure.com) strona skojarzonego zasobu platformy Azure w ramach __klucze__. Skopiuj następujące informacje, aby ich użyć w aplikacji:

* __Adres URL__ do korzystania z __pliku obrazu__.
* Wartość __Klucza predykcyjnego__.

> [!TIP]
> Jeśli masz wiele iteracji, możesz określić, która z nich będzie używana, ustawiając ją jako domyślną. Wybierz iterację w sekcji __Iteracje__, a następnie zaznacz opcję __Ustaw jako domyślną__ w górnej części strony.

![Na karcie wydajności czerwonym prostokątem otoczono adres URL przewidywania.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Tworzenie aplikacji

1. Utwórz nową aplikację konsoli w języku C# w programie Visual Studio.

1. Użyj poniższego kodu jako treści pliku __Program.cs__.

    > [!IMPORTANT]
    > Zmień następujące informacje:
    >
    > * Ustaw __przestrzeń nazw__ na nazwę projektu.
    > * Ustaw wartość __klucza predykcyjnego__ otrzymaną wcześniej w linii, która rozpoczyna się od `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Ustaw wartość __adresu URL__ otrzymanego wcześniej w linii, która rozpoczyna się od `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>Użycie aplikacji

Podczas uruchamiania aplikacji należy wprowadzić ścieżkę do pliku obrazu. Obraz jest przesyłany do interfejsu API, a wyniki są zwracane jako dokument JSON. Następujący kod JSON jest przykładem odpowiedzi

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

[Eksportowanie modelu na potrzeby urządzeń mobilnych](export-your-model.md)
