---
title: Używać punktu końcowego prognozowania usługi wizji niestandardowe - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak programowo testować obrazów za pomocą klasyfikatora z usługi wizji niestandardowych za pomocą interfejsu API.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 54f9d9fec1f40c167341dec6a8699b6a558419da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348153"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Umożliwia testowanie obrazów programowo z klasyfikatora niestandardowe wizji usługi punktu końcowego prognozowania

Po nauczenia modelu, można przetestować obrazy programowo, poprzez przesłanie ich do prognozowania interfejsu API. 

> [!NOTE]
> W tym dokumencie przedstawiono przy użyciu języka C# przesyłanie obrazu do interfejsu API prognozowania. Aby uzyskać więcej informacji oraz przykłady korzystania z interfejsu API, zobacz [dokumentacja interfejsu API prognozowania](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Pobieranie adresu URL i prognozowanie klucza

Z [wizji niestandardowe strony sieci web](https://customvision.ai), wybierz projekt, a następnie wybierz __wydajności__ kartę. Aby wyświetlić informacje dotyczące korzystania z interfejsu API prognozowania, wybierz __URL prognozowania__. Skopiuj następujące informacje do użycia w aplikacji:

* __Adres URL__ dla przy użyciu __plik obrazu__.
* __Klucz prognozowania__ wartość.

> [!TIP]
> Jeśli masz wiele iteracji, można kontrolować, które z nich jest używana przez ustawienie go jako domyślny. Wybierz iteracji z __iteracji__ sekcji, a następnie wybierz __domyślnym__ w górnej części strony.

![Na karcie Wydajność jest wyświetlana z czerwonym prostokątem otaczającego prognozowania adres URL.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Tworzenie aplikacji

1. W programie Visual Studio Utwórz nową aplikację konsoli języka C#.

2. Użyj następującego kodu jako treść __Program.cs__ pliku.

    > [!IMPORTANT]
    > Zmień następujące informacje:
    >
    > * Ustaw __przestrzeni nazw__ nazwę projektu.
    > * Ustaw __klucza prognozowania__ otrzymane wcześniej w wierszu, który rozpoczyna się od wartości `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Ustaw __adres URL__ otrzymane wcześniej w wierszu, który rozpoczyna się od wartości `string url =`.

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
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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

## <a name="use-the-application"></a>Korzystanie z aplikacji

Podczas uruchamiania aplikacji, możesz wprowadź ścieżkę do pliku obrazu. Obraz jest przesyłany do interfejsu API, a wyniki są zwracane jako dokument JSON. Następujący kod JSON jest przykładem odpowiedzi

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

[Eksportowanie modelu do użytku w przenośnych](export-your-model.md)