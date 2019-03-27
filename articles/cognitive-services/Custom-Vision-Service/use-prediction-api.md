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
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472731"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Model za pomocą interfejsu API prognoz.

Po wyszkoleniu modelu można przetestować obrazy programowo, przesyłając je do interfejsu API przewidywania.

> [!NOTE]
> W tym dokumencie przedstawiono przesyłanie obrazu do interfejsu API przewidywania przy użyciu języka C#. Aby uzyskać więcej informacji i przykładów użycia interfejsu API, zobacz [dokumentację interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikowanie swojej uczonego iteracji

Na stronie [Custom Vision ](https://customvision.ai) wybierz swój projekt, a następnie kartę __Wydajność__.

Do przesyłania obrazów do interfejsu API prognoz, najpierw należy do publikowania swojej iteracji w celu prognozowania, co można zrobić, wybierając __Publikuj__ i określając nazwę opublikowanych iteracji. Spowoduje to włączenie modelu w taki sposób umożliwić dostęp do interfejsu API prognozowania usługi Custom Vision w usłudze Azure resource. 

![Na karcie Wydajność jest wyświetlana z czerwonym prostokątem otaczającego przycisk Publikuj.](./media/use-prediction-api/unpublished-iteration.png)

Po pomyślnym opublikowaniu modelu zobaczysz etykietę "Opublikowano", pojawiają się obok swojej iteracji w lewym pasku bocznym, a także nazwę opublikowanych iteracji w opisie iteracji.

![Na karcie Wydajność jest wyświetlany za pomocą czerwonego prostokąta otaczającego etykiety opublikowano i nazwy opublikowanej iteracji.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Pobieranie adresu URL i klucza predykcyjnego

Po opublikowaniu modelu można pobrać informacji o korzystaniu z interfejsu API prognoz, wybierając __URL prognozowania__. Zostanie otwarte okno dialogowe podobny do przedstawionego poniżej informacje dotyczące korzystania z interfejsu API prognoz, w tym __URL prognozowania__ i __prognozowania klucz__.

![Na karcie Wydajność jest wyświetlany za pomocą czerwonego prostokąta otaczającego przycisk prognozowania adresu URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Na karcie Wydajność jest wyświetlany za pomocą czerwonego prostokąta otaczającego wartość adresu URL prognozy przy użyciu pliku obrazu i wartości klucza prognozowania.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Twoje __klucz prognozowania__ można także znaleźć w [witryny Azure Portal](https://portal.azure.com) strony dla zasobów platformy Azure Custom Vision związanych z projektem, w obszarze __klucze__. 

Okno dialogowe skopiuj następujące informacje do użycia w aplikacji:

* __Adres URL prognozowania__ używania __plik obrazu__.
* __Klucz prognozowania__ wartość.

## <a name="create-the-application"></a>Tworzenie aplikacji

1. Utwórz nową aplikację konsoli w języku C# w programie Visual Studio.

1. Użyj poniższego kodu jako treści pliku __Program.cs__.

    > [!IMPORTANT]
    > Zmień następujące informacje:
    >
    > * Ustaw __przestrzeń nazw__ na nazwę projektu.
    > * Ustaw __klucz prognozowania__ pobrane wcześniej w wierszu, który rozpoczyna się od wartości `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Ustaw __URL prognozowania__ pobrane wcześniej w wierszu, który rozpoczyna się od wartości `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Użycie aplikacji

Po uruchomieniu aplikacji, wprowadź ścieżkę do pliku obrazu, w konsoli. Obraz, który jest przesyłany do interfejsu API prognoz i przewidywane wyniki są zwracane jako dokument JSON. Następujące dane JSON znajduje się przykład odpowiedzi.

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

[Eksportowanie modelu na potrzeby urządzeń mobilnych](export-your-model.md)

[Rozpoczynanie pracy z zestawami SDK platformy .NET](csharp-tutorial.md)

[Rozpoczynanie pracy z zestawami SDK języka Python](python-tutorial.md)

[Rozpoczynanie pracy z zestawami SDK języka Java](java-tutorial.md)

[Rozpoczynanie pracy z zestawami SDK węzła](node-tutorial.md)

[Rozpoczynanie pracy z usługą Przejdź zestawów SDK](go-tutorial.md)
