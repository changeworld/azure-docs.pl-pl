---
title: 'Szybki start: Wykrywanie twarzy na zdjęciach — SDK, C#'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start omówiono wykrywanie twarzy na obrazach przy użyciu korzystającej z usług Cognitive Services biblioteki klienta Face dla aplikacji systemu Windows napisanych w języku C#.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: b5fd10aaab28303079d3eef9c4c5c25207bb672e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344986"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>Szybki start: Wykrywanie twarzy na obrazach przy użyciu C&#35; — Face

W tym przewodniku Szybki start wykryjesz ludzką twarz na obrazie za pomocą biblioteki klienta Face dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

* Do uruchomienia przykładu potrzebny jest klucz subskrypcji. Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* Dowolna wersja programu [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Pakiet NuGet zawierający bibliotekę klienta [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Pobieranie pakietu nie jest konieczne. Instrukcje instalacji znajdują się poniżej.

## <a name="detectwithurlasync-method"></a>Metoda DetectWithUrlAsync

> [!TIP]
> Pobierz najnowszy kod jako rozwiązanie programu Visual Studio z witryny [Github](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

Metody `DetectWithUrlAsync` i `DetectWithStreamAsync` opakowują [interfejs API rozpoznawania twarzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) na potrzeby przetwarzania plików — odpowiednio — zdalnych i lokalnych. Za pomocą tych metod można wykrywać twarze na zdjęciach i zwracać atrybuty twarzy, takie jak:

* Identyfikator Face ID: unikatowy identyfikator używany w kilku scenariuszach dotyczących interfejsu API rozpoznawania twarzy.
* Obrys twarzy: lewy górny róg oraz szerokość i wysokość wskazujące na lokalizację twarzy na obrazie.
* Charakterystyczne punkty twarzy: układ 27 charakterystycznych punktów twarzy wskazujący położenie istotnych części twarzy.
* Atrybuty twarzy, takie jak wiek, płeć, intensywność uśmiechu, położenie głowy i zarost.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nową aplikację konsolową języka Visual C# w programie Visual Studio.
1. Zainstaluj pakiet NuGet biblioteki klienta Face.
    1. W menu u góry ekranu kliknij pozycję **Narzędzia**, a następnie **Menedżer pakietów NuGet** i **Zarządzaj pakietami NuGet rozwiązania**.
    1. Kliknij kartę **Przeglądaj** i wybierz opcję **Uwzględnij wersję wstępną**.
    1. W polu **Wyszukaj** wpisz „Microsoft.Azure.CognitiveServices.Vision.Face”.
    1. Po wyświetleniu pozycji **Microsoft.Azure.CognitiveServices.Vision.Face** wybierz ją, a następnie kliknij pole wyboru obok nazwy projektu i przycisk **Zainstaluj**.
1. Zastąp plik *Program.cs* następującym kodem.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Jeśli to konieczne, zmień ciąg `faceEndpoint` na region świadczenia usługi Azure powiązany z kluczami subskrypcji.
1. Opcjonalnie możesz zastąpić ciąg <`LocalImage>` ścieżką i nazwą lokalnego pliku graficznego (ten ciąg zostanie zignorowany, jeśli go nie zmienisz).
1. Opcjonalnie możesz ustawić ścieżkę `remoteImageUrl` do innego obrazu.
1. Uruchom program.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Odpowiedź DetectWithUrlAsync

Pomyślna odpowiedź zawiera płeć i wiek każdej twarzy na zdjęciu.

W przewodniku po interfejsie API [Szybki start: Wykrywanie twarzy na zdjęciach z użyciem języka C#](CSharp.md) znajduje się przykład surowego wyjścia w formacie JSON.

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć aplikację WPF systemu Windows, która wykrywa twarze na zdjęciach za pomocą usługi rozpoznawania twarzy. Ta aplikacja rysuje ramkę wokół każdej twarzy i wyświetla opis twarzy na pasku stanu.

> [!div class="nextstepaction"]
> [Samouczek: tworzenie aplikacji WPF wykrywającej i oznaczającej ramką twarze na zdjęciach](../Tutorials/FaceAPIinCSharpTutorial.md)
