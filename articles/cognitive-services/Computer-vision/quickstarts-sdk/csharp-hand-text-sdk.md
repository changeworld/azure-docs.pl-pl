---
title: 'Szybki start: Wyodrębnianie tekstu odręcznego — SDK, C#'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wyodrębnisz tekst na obrazie za pomocą biblioteki języka C# klienta przetwarzania obrazów dla systemu Windows.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 315e35535c29771fb12a97f26e6e2c294e888d07
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137419"
---
# <a name="quickstart-extract-handwritten-text-using-the-computer-vision-c-sdk"></a>Szybki start: Wyodrębnianie tekstu odręcznego C# przy użyciu zestawu SDK przetwarzanie obrazów

W tym przewodniku Szybki start wyodrębnisz tekst odręczny lub drukowany na obrazie za pomocą zestawu SDK przetwarzania obrazów dla języka C#. Jeśli chcesz, możesz pobrać kod przedstawiony w tym przewodniku jako pełną przykładową aplikację z repozytorium [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji przetwarzanie obrazów. Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz. Następnie [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu punktu końcowego klucza i usługi, odpowiednio `COMPUTER_VISION_SUBSCRIPTION_KEY` nazwane `COMPUTER_VISION_ENDPOINT`i.
* Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).
* Pakiet NuGet biblioteki klienta [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Pobieranie pakietu nie jest konieczne. Instrukcje instalacji znajdują się poniżej.

## <a name="create-and-run-the-sample-app"></a>Tworzenie i uruchamianie przykładowej aplikacji

Aby uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet biblioteki klienta przetwarzania obrazów.
    1. W menu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj**, a następnie w polu **Wyszukiwanie** wpisz ciąg „Microsoft.Azure.CognitiveServices.Vision.ComputerVision”.
    1. Po wyświetleniu pozycji **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** wybierz tę pozycję, a następnie kliknij kolejno pole wyboru obok nazwy projektu i pozycję **Zainstaluj**.
1. Zastąp ciąg `Program.cs` następującym kodem. Metody `BatchReadFileAsync` i`BatchReadFileInStreamAsync` otaczają odpowiednio [interfejs API odczytywania wsadowego](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) dla obrazów zdalnych i lokalnych. Metoda zawija [interfejs API wyniku operacji get Read.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) `GetReadOperationResultAsync`

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<Subscription key>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the westcentralus
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Read text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to read the text
                BatchReadFileHeaders textHeaders =
                    await computerVision.BatchReadFileAsync(
                        imageUrl);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    // Start the async process to recognize the text
                    BatchReadFileInStreamHeaders textHeaders =
                        await computerVision.BatchReadFileInStreamAsync(
                            imageStream);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                ReadOperationResult result =
                    await computerVision.GetReadOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetReadOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var recResults = result.RecognitionResults;
                foreach (TextRecognitionResult recResult in recResults)
                {
                    foreach (Line line in recResult.Lines)
                    {
                        Console.WriteLine(line.Text);
                    }
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Jeśli to konieczne, zmień ciąg `computerVision.Endpoint` na region świadczenia usługi Azure powiązany z kluczami subskrypcji.
1. Zastąp ciąg `<LocalImage>` ścieżką i nazwą pliku lokalnego obrazu.
1. Opcjonalnie możesz ustawić ścieżkę `remoteImageUrl` do innego obrazu.
1. Uruchom program.

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie wyświetlona odpowiedź zawierająca wiersze rozpoznanego tekstu dla każdego obrazu.

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

Zobacz [Szybki start: Wyodrębnianie tekstu odręcznego — REST, C# ](../QuickStarts/CSharp-hand-text.md#examine-the-response), aby zobaczyć przykładowe nieprzetworzone dane wyjściowe w formacie JSON z wywołania interfejsu API.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego.

> [!div class="nextstepaction"]
> [Poznaj interfejsy API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
