---
title: Użycie punktu końcowego przewidywania do programowego testowania obrazów przy użyciu klasyfikatora — Custom Vision
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak programowo testować obrazy za pomocą interfejsu API przy użyciu klasyfikatora usługi Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816719"
---
# <a name="use-your-model-with-the-prediction-api"></a>Model za pomocą interfejsu API prognoz.

Po już uczenie modelu, możesz przetestować obrazy programowo, przesyłając je do punktu końcowego interfejsu API prognoz.

> [!NOTE]
> W tym dokumencie przedstawiono przesyłanie obrazu do interfejsu API przewidywania przy użyciu języka C#. Aby uzyskać więcej informacji i przykładów, zobacz [dokumentacja interfejsu API prognoz](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikowanie swojej uczonego iteracji

Na stronie [Custom Vision ](https://customvision.ai) wybierz swój projekt, a następnie kartę __Wydajność__.

Do przesyłania obrazów do interfejsu API prognoz, najpierw należy do publikowania swojej iteracji w celu prognozowania, co można zrobić, wybierając __Publikuj__ i określając nazwę opublikowanych iteracji. Dzięki temu model dostępne dla interfejsu API prognozowania usługi Custom Vision w usłudze Azure resource.

![Na karcie Wydajność jest wyświetlana z czerwonym prostokątem otaczającego przycisk Publikuj.](./media/use-prediction-api/unpublished-iteration.png)

Po pomyślnym opublikowaniu modelu zostaną wyświetlone etykiety "Opublikowano", pojawiają się obok swojej iteracji na lewym pasku bocznym, a jej nazwa będzie wyświetlana w opisie iteracji.

![Na karcie Wydajność jest wyświetlany za pomocą czerwonego prostokąta otaczającego etykiety opublikowano i nazwy opublikowanej iteracji.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Pobieranie adresu URL i klucza predykcyjnego

Po opublikowaniu modelu można pobrać wymaganych informacji, wybierając __URL prognozowania__. Zostanie otwarte okno dialogowe informacje dotyczące korzystania z interfejsu API prognoz, w tym __URL prognozowania__ i __prognozowania klucz__.

![Na karcie Wydajność jest wyświetlany za pomocą czerwonego prostokąta otaczającego przycisk prognozowania adresu URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Na karcie Wydajność jest wyświetlany za pomocą czerwonego prostokąta otaczającego wartość adresu URL prognozy przy użyciu pliku obrazu i wartości klucza prognozowania.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Twoje __klucz prognozowania__ można także znaleźć w [witryny Azure portal](https://portal.azure.com) strony dla zasobów platformy Azure Custom Vision związanych z projektem, w obszarze __klucze__ bloku.

W tym przewodniku będzie używać lokalnego obrazu, dlatego skopiuj adres URL, pod **Jeśli masz plik obrazu** do tymczasowej lokalizacji. Skopiuj odpowiedni __klucz prognozowania__ również wartość.

## <a name="create-the-application"></a>Tworzenie aplikacji

1. W programie Visual Studio Utwórz nowy C# aplikacji konsoli.

1. Użyj poniższego kodu jako treści pliku __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

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

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Zmień następujące informacje:
   * Ustaw `namespace` pola Nazwa projektu.
   * Zastąp symbol zastępczy `<Your prediction key>` z wartością klucza pobranym wcześniej.
   * Zastąp symbol zastępczy `<Your prediction URL>` za pomocą adresu URL pobranym wcześniej.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu aplikacji, monit wprowadź ścieżkę do pliku obrazu, w konsoli. Obraz, który następnie jest przesyłany do interfejsu API prognoz i przewidywane wyniki są zwracane jako ciąg w formacie JSON. Poniżej zamieszczono przykładową odpowiedź.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób przesyłania obrazów niestandardowych obrazów klasyfikatora/wykrywanie i otrzymać odpowiedź programowo przy użyciu C# zestawu SDK. Dowiedz się, jak realizować scenariusze end-to-end z C#, lub zacznij pracę, przy użyciu innego języka zestawu SDK.

* [Szybki Start: .NET SDK](csharp-tutorial.md)
* [Szybki start: Python SDK](python-tutorial.md)
* [Szybki start: Java SDK](java-tutorial.md)
* [Szybki start: Węzeł zestawu SDK](node-tutorial.md)
* [Szybki start: Przejdź do zestawu SDK](go-tutorial.md)
