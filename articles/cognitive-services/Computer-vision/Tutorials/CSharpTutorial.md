---
title: Komputer wizji interfejsu API języka C# samouczek | Dokumentacja firmy Microsoft
description: Poznaj podstawowe aplikacji Windows, który używa interfejsu API przetwarzania obrazów komputera w kognitywnych usług firmy Microsoft. Wykonać rozpoznawanie, tworzenie miniatur i pracy z funkcjami visual obrazu.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347880"
---
# <a name="computer-vision-api-c35-tutorial"></a>Komputer wizji interfejsu API C&#35; samouczka

Poznaj podstawowe aplikacji systemu Windows, który używa interfejsu API przetwarzania obrazów komputera, aby wykonać OCR (Rozpoznawania), tworzenie przycięte inteligentnych miniatur plus wykrywanie, klasyfikowanie tag i opisz visual funkcje, takie jak kroje, obrazu. Poniżej umożliwia na przykład Prześlij adres URL obrazu lub lokalnie przechowywanych plików. W tym przykładzie typu open source jako szablon służy do tworzenia własnych aplikacji dla systemu Windows przy użyciu interfejsu API wizja i WPF (Windows Presentation Foundation), część .NET Framework.

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniżej przykład został opracowany dla programu .NET Framework za pomocą [programu Visual Studio 2015, Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Subskrybowanie API przetwarzania obrazów komputera i uzyskiwanie klucza subskrypcji 

Przed utworzeniem w przykładzie, należy subskrybować interfejsu API wizji komputera, który jest częścią kognitywnych usług firmy Microsoft (dawniej Oxford projektu). Dla subskrypcji i zarządzania kluczami szczegółów, zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). W tym samouczku można zarówno klucza podstawowego i pomocniczego. 

> [!NOTE]
> Samouczek jest przeznaczony do używania kluczy subskrypcji w **westcentralus** regionu. Klucze subskrypcji wygenerowany używany wolnych dziennik wizji komputera **westcentralus** regionu, dlatego działają prawidłowo. Jeśli generowany klucze subskrypcji przy użyciu konta platformy Azure za pośrednictwem [ https://azure.microsoft.com/ ](https://azure.microsoft.com/), należy określić **westcentralus** regionu. Klucze generowane poza **westcentralus** region nie będą działać.

#### <a name="get-the-client-library-and-example"></a>Pobierz klienta biblioteki i przykładowe

Może klonować komputera wizji aplikacja interfejsu API klienta biblioteki i przykładowe do komputera za pośrednictwem [SDK](https://www.github.com/microsoft/cognitive-vision-windows). Nie pobieraj go jako ZIP.

### <a name="Step1">Krok 1: Instalowanie przykładu</a>

Otwórz przykładowe-WPF\VisionAPI-WPF-Samples.sln w pulpicie GitHub.

### <a name="Step2">Krok 2: Tworzenie przykładu</a>

* Naciśnij klawisze Ctrl + Shift + B, lub kliknij menu wstążki kompilacji, a następnie wybierz Kompiluj rozwiązanie.

### <a name="Step3">Krok 3: Uruchamianie przykładu</a>

1. Po zakończeniu kompilacji, naciśnij klawisz **F5** lub kliknij przycisk **Start** menu wstążki do uruchamiania w przykładzie.
2. Znajdź okno interfejsu API przetwarzania obrazów komputera użytkownika z pole edycji tekstu, Odczyt, "Wkleić klucz subskrypcji w tym miejscu można uruchomić".
Możesz zachować klucz subskrypcji na komputerze lub laptop przez kliknięcie przycisku "Zapisz klucz". Jeśli chcesz usunąć klucz subskrypcji z systemu, kliknij przycisk "Usuń klucz", aby usunąć go z komputera lub laptop.

    ![Wizja subskrypcji klucza](../Images/Vision_UI_Subscription.PNG)

3. W obszarze "Wybierz scenariusz" Kliknij, aby użyć jednego ze scenariuszy sześć, a następnie postępuj zgodnie z instrukcjami na ekranie. Microsoft odbiera obrazów, przekazywanie i może używać ich do poprawy API przetwarzania obrazów komputera i powiązane usługi. Poprzez przesłanie obrazu, należy potwierdzić, że zostały wykonane naszych [Developer Kodeks postępowania](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Analizowanie interfejs obrazów](../Images/Analyze_Image_Example.PNG)

4. Brak przykład obrazów do użycia w tej przykładowej aplikacji. Obrazy te można znaleźć w w repozytorium powierzchni interfejsu API systemu Windows w usłudze Github [folderu danych](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Należy pamiętać, użycie tych obrazów jest licencjonowane zgodnie z umową [obraz licencji](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Przejrzyj i Dowiedz się więcej</a>

Teraz, gdy masz uruchomioną aplikację Daj nam Przejrzyj jak ten przykład aplikacji integruje się z technologii usług kognitywnych. Ułatwi o kontynuowanie budynku na tej aplikacji lub utworzyć własną aplikację przy użyciu interfejsu API przetwarzania obrazów komputera firmy Microsoft.

Ta aplikacja przykład korzysta z komputera wizji interfejsu API biblioteki klienckiej, cienkie C# klienta otokę dla interfejsu API usługi Microsoft komputera wizji. Podczas tworzenia aplikacji przykład, jak opisano powyżej, masz biblioteki klienta od pakietu NuGet. Możesz przejrzeć kod źródłowy biblioteki klienta w folderze zatytułowany "**biblioteki klienta**" w obszarze **wizji**, **Windows**, **biblioteki klienta**, będąca częścią repozytorium pobrany plik wymienionych powyżej wymagań wstępnych.

Możesz także znaleźć informacje o tym, jak użycie kodu biblioteki klienta w Eksploratorze rozwiązań: w obszarze **VisionAPI WPF_Samples**, rozwiń węzeł **AnalyzePage.xaml** zlokalizować **AnalyzePage.xaml.cs**, która jest używana do przysyłania obrazu do punktu końcowego analizy obrazu. Kliknij dwukrotnie. xaml.cs pliki, aby je otworzyć nowego systemu Windows w programie Visual Studio.

Przeglądanie, jak wizja biblioteki klienta jest używany w naszym przykładzie aplikacji, Przyjrzyjmy się dwie wstawki kodu z **AnalyzePage.xaml.cs**. Plik zawiera komentarze w kodzie wskazujący "Klucz przykładowy kod URUCHAMIA tutaj" i "Klucz przykładowy kod kończy się tutaj" Aby zlokalizować kod, który wstawki przedstawionym poniżej.

Analizuj punkt końcowy jest możliwa praca z adres URL obrazu lub danych binarnych obrazu (w formie strumień oktetu) jako danych wejściowych. Po pierwsze Znajdź using dyrektywa, która umożliwia korzystanie z biblioteki klienta wizji.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(...)**  Następujący fragment kodu przedstawia sposób użycia biblioteki klienta można przesłać klucz subskrypcji i przechowywane lokalnie obrazu do punktu końcowego analizowanie usługi interfejsu API przetwarzania obrazów komputera.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(...)**  Następujący fragment kodu przedstawia sposób użycia biblioteki klienta można przesłać klucz subskrypcji i adresu URL zdjęcie do punktu końcowego analizowanie usługi interfejsu API przetwarzania obrazów komputera.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Inne strony i punkty końcowe** jak wchodzić w interakcję z innymi punktami końcowymi udostępnianych przez usługi interfejsu API przetwarzania obrazów komputera, można wyświetlić, analizując innych stron w próbce; na przykład Rozpoznawania punkt końcowy jest wyświetlany jako część kod zawarty w OCRPage.xaml.cs 

### <a name="Related">Tematy pokrewne</a>
 * [Rozpoczynanie pracy z powierzchni interfejsu API](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


