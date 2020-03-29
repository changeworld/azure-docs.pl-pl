---
title: Użycie punktu końcowego przewidywania do programowego testowania obrazów przy użyciu klasyfikatora — Custom Vision
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak programowo testować obrazy za pomocą interfejsu API przy użyciu klasyfikatora usługi Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169949"
---
# <a name="use-your-model-with-the-prediction-api"></a>Używanie modelu z interfejsem API przewidywania

Po przeszkoleniu modelu można programowo przetestować obrazy, przesyłając je do punktu końcowego interfejsu API przewidywania.

> [!NOTE]
> W tym dokumencie przedstawiono przesyłanie obrazu do interfejsu API przewidywania przy użyciu języka C#. Aby uzyskać więcej informacji i przykładów, zobacz [odwołanie do interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikowanie wyszkolonej iteracji

Na stronie [Custom Vision ](https://customvision.ai) wybierz swój projekt, a następnie kartę __Wydajność__.

Aby przesłać obrazy do interfejsu API przewidywania, należy najpierw opublikować iteracji do przewidywania, co można zrobić, wybierając __publikuj__ i określając nazwę dla opublikowanej iteracji. Dzięki temu model będzie dostępny dla interfejsu API przewidywania zasobu platformy Azure usługi Azure.

![Zostanie wyświetlona karta wydajności z czerwonym prostokątem otaczającym przycisk Publikuj.](./media/use-prediction-api/unpublished-iteration.png)

Po pomyślnym opublikowaniu modelu obok iteracji pojawi się etykieta "Opublikowane", a jej nazwa pojawi się w opisie iteracji.

![Karta wydajności jest wyświetlana z czerwonym prostokątem otaczającym opublikowaną etykietę i nazwą opublikowanej iteracji.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Pobieranie adresu URL i klucza predykcyjnego

Po opublikowaniu modelu można pobrać wymagane informacje, wybierając __adres URL przewidywania__. Spowoduje to otwarcie okna dialogowego z informacjami o użyciu interfejsu API przewidywania, w tym __adresu URL przewidywania__ i __prediction-key__.

![Karta wydajności jest wyświetlana z czerwonym prostokątem otaczającym przycisk Adresu URL przewidywania.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Karta wydajności jest wyświetlana z czerwonym prostokątem otaczającym wartość adresu URL przewidywania przy użyciu pliku obrazu i wartości Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


W tym przewodniku użyjesz obrazu lokalnego, więc skopiuj adres URL w obszarze **Jeśli masz plik obrazu** do lokalizacji tymczasowej. Skopiuj również odpowiednią wartość __Prediction-Key.__

## <a name="create-the-application"></a>Tworzenie aplikacji

1. W programie Visual Studio utwórz nową aplikację konsoli języka C#.

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
   * Ustaw `namespace` to pole na nazwę projektu.
   * Zastąp `<Your prediction key>` symbol zastępczy wartością klucza pobraną wcześniej.
   * Zastąp `<Your prediction URL>` symbol zastępczy adresem URL pobranym wcześniej.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu aplikacji zostanie wyświetlony monit o wprowadzenie ścieżki do pliku obrazu w konsoli. Obraz jest następnie przesyłany do interfejsu API przewidywania, a wyniki przewidywania są zwracane jako ciąg w formacie JSON. Poniżej przedstawiono przykładową odpowiedź.

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

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak przesyłać obrazy do niestandardowego klasyfikatora/detektora obrazu i odbierać odpowiedź programowo za pomocą SDK języka C#. Następnie dowiedz się, jak ukończyć scenariusze end-to-end za pomocą języka C#lub rozpocząć korzystanie z innego języka SDK.

* [Szybki start: plik SDK .NET](csharp-tutorial.md)
* [Szybki start: SDK języka Python](python-tutorial.md)
* [Szybki start: java sdk](java-tutorial.md)
* [Szybki start: SDK węzła](node-tutorial.md)
* [Szybki start: przejdź do SDK](go-tutorial.md)
