---
title: 'Szybki start: Wyodrębnianie tekstu odręcznego — SDK, C#'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wyodrębnisz tekst na obrazie za pomocą biblioteki języka C# klienta przetwarzania obrazów dla systemu Windows.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7ed33290a9c5cede6fd80058d8557eae65c11b28
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578601"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>Szybki start: wyodrębnianie tekstu przy użyciu zestawu SDK przetwarzania obrazów i języka C#

W tym przewodniku Szybki start wyodrębnisz tekst odręczny lub drukowany na obrazie za pomocą biblioteki klienta przetwarzania obrazów dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).
* Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).
* Pakiet NuGet biblioteki klienta [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Pobieranie pakietu nie jest konieczne. Instrukcje instalacji znajdują się poniżej.

## <a name="recognizetextasync-method"></a>Metoda RecognizeTextAsync

> [!TIP]
> Pobierz najnowszy kod jako rozwiązanie programu Visual Studio z witryny [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

Metody `RecognizeTextAsync` i `RecognizeTextInStreamAsync` opakowują [interfejs API rozpoznawania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) odpowiednio w przypadku obrazów zdalnych i lokalnych. Metoda `GetTextOperationResultAsync` opakowuje [interfejs API wyników operacji rozpoznawania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201).  Możesz użyć tych metod, aby wykryć tekst na obrazie i wyodrębnić rozpoznane znaki do strumienia znaków, który może być używany przez maszyny.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet biblioteki klienta przetwarzania obrazów.
    1. W menu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj**, a następnie w polu **Wyszukiwanie** wpisz ciąg „Microsoft.Azure.CognitiveServices.Vision.ComputerVision”.
    1. Po wyświetleniu pozycji **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** wybierz tę pozycję, a następnie kliknij kolejno pole wyboru obok nazwy projektu i pozycję **Zainstaluj**.
1. Zastąp ciąg `Program.cs` następującym kodem.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Jeśli to konieczne, zmień ciąg `computerVision.Endpoint` na region świadczenia usługi Azure powiązany z kluczami subskrypcji.
1. Opcjonalnie ustaw dla elementu `textRecognitionMode` wartość `TextRecognitionMode.Printed`.
1. Zastąp ciąg `<LocalImage>` ścieżką i nazwą pliku lokalnego obrazu.
1. Opcjonalnie możesz ustawić ścieżkę `remoteImageUrl` do innego obrazu.
1. Uruchom program.

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
        private const string subscriptionKey = "<SubscriptionKey>";

        // For printed text, change to TextRecognitionMode.Printed
        private const TextRecognitionMode textRecognitionMode =
            TextRecognitionMode.Handwritten;

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

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
            // Free trial subscription keys are generated in the "westus"
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

        // Recognize text from a remote image
        private static async Task ExtractRemoteTextAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders =
                await computerVision.RecognizeTextAsync(
                    imageUrl, textRecognitionMode);

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
                RecognizeTextInStreamHeaders textHeaders =
                    await computerVision.RecognizeTextInStreamAsync(
                        imageStream, textRecognitionMode);

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
            TextOperationResult result =
                await computerVision.GetTextOperationResultAsync(operationId);

            // Wait for the operation to complete
            int i = 0;
            int maxRetries = 10;
            while ((result.Status == TextOperationStatusCodes.Running ||
                    result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
            {
                Console.WriteLine(
                    "Server status: {0}, waiting {1} seconds...", result.Status, i);
                await Task.Delay(1000);

                result = await computerVision.GetTextOperationResultAsync(operationId);
            }

            // Display the results
            Console.WriteLine();
            var lines = result.RecognitionResult.Lines;
            foreach (Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>Odpowiedź na żądanie RecognizeTextAsync

Po pomyślnym przetworzeniu żądania zostanie wyświetlona odpowiedź zawierająca wiersze rozpoznanego tekstu dla każdego obrazu.

Zobacz [Szybki start: wyodrębnianie tekstu odręcznego — REST, C# ](../QuickStarts/CSharp-hand-text.md#examine-the-response), aby zobaczyć przykładowe nieprzetworzone dane wyjściowe w formacie JSON.

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego.

> [!div class="nextstepaction"]
> [Poznaj interfejsy API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
