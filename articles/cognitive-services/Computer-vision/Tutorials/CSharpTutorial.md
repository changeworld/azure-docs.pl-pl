---
title: 'Samouczek: Tworzenie obrazów, przetwarzania aplikacji — C#'
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Zapoznaj się z podstawowej aplikacji Windows, który używa interfejsu API przetwarzania obrazów w usługach Microsoft Cognitive Services. Wykonaj optyczne rozpoznawanie znaków, tworzenia miniatur i korzystania z funkcji visual w obrazie.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: d1fa468874cdc51bad3421f700a69f3ffb268635
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342657"
---
# <a name="tutorial-build-an-image-processing-app---c35"></a>Samouczek: Tworzenie obrazów, przetwarzania aplikacji - C&#35;

Poznaj podstawowe aplikacji Windows, która używa przetwarzania obrazów, aby wykonywać optyczne rozpoznawanie znaków (OCR), tworzenie przycięte inteligentne miniatury oraz wykrywanie, klasyfikowanie tagu i opisano funkcje wizualne, twarzy, w tym obrazie. Poniżej umożliwia na przykład przesyłasz adres URL obrazu lub lokalnie przechowywanego pliku. W tym przykładzie "open source" jako szablon służy do tworzenia własnych aplikacji dla Windows przy użyciu interfejsu API przetwarzania obrazów i Windows Presentation Foundation (WPF), część .NET Framework.

> [!div class="checklist"]
> * Pobierz przykładową aplikację z usługi GitHub
> * Otworzyć i skompilować przykładową aplikację w programie Visual Studio
> * Uruchom przykładową aplikację i korzystać z niego do wykonywania różnych scenariuszy
> * Poznaj różne scenariusze, które są dołączone do przykładowej aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z przykładowej aplikacji, upewnij się, że zostały spełnione następujące wymagania wstępne:

* Konieczne jest posiadanie [programu Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) lub nowszej.
* Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Aby uzyskać klucz subskrypcji, zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-the-sample-app"></a>Pobieranie przykładowej aplikacji

Przetwarzania obrazów w przykładowej aplikacji jest dostępna w usłudze GitHub z poziomu `Microsoft/Cognitive-Vision-Windows` repozytorium. To repozytorium zawiera również `Microsoft/Cognitive-Common-Windows` repozytorium jako modułu podrzędnego usługi Git. Możesz rekursywnie Klonuj repozytorium, w tym module podrzędnym, przy użyciu `git clone --recurse-submodules` polecenia z wiersza polecenia lub przy użyciu GitHub Desktop.

Na przykład aby rekursywnie Klonuj repozytorium przykładowej aplikacji komputerowej w wierszu polecenia, uruchom następujące polecenie:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Nie pobieraj to repozytorium jako ZIP. Git nie zawiera moduły podrzędne, podczas pobierania repozytorium w formie pliku ZIP.

### <a name="get-optional-sample-images"></a>Pobierz opcjonalny przykładowe obrazy

Opcjonalnie możesz skorzystać z przykładowe obrazy dołączone do [twarzy](../../Face/Overview.md) przykładowej aplikacji, dostępne w serwisie GitHub z `Microsoft/Cognitive-Face-Windows` repozytorium. Tej przykładowej aplikacji zawiera folder, `/Data`, który zawiera wiele obrazów osób. Możesz rekursywnie Klonuj repozytorium, jak również za pomocą metod opisanych dla przykładowej aplikacji komputerowej.

Na przykład aby rekursywnie Klonuj repozytorium na potrzeby rozpoznawania twarzy przykładową aplikację z poziomu wiersza polecenia, uruchom następujące polecenie:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Otworzyć i skompilować przykładową aplikację w programie Visual Studio

Należy najpierw utworzyć przykładową aplikację tak, aby program Visual Studio może rozpoznać zależności, zanim będzie można uruchomić lub zapoznaj się z przykładową aplikację. Aby otworzyć i skompilować aplikację przykładową, wykonaj następujące czynności:

1. Otwórz plik rozwiązania Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`, w programie Visual Studio.
1. Upewnij się, że rozwiązanie programu Visual Studio zawiera dwa projekty:  

   * SampleUserControlLibrary
   * Przykłady interfejsów WPF VisionAPI  

   Jeśli projekt SampleUserControlLibrary jest niedostępny, upewnij się, że pomyślnie dodano rekursywnie sklonowany `Microsoft/Cognitive-Vision-Windows` repozytorium.
1. W programie Visual Studio, naciśnij klawisze Ctrl + Shift + B lub wybierz **kompilacji** menu wstążki, a następnie wybierz **Kompiluj rozwiązanie** do skompilowania rozwiązania.

## <a name="run-and-interact-with-the-sample-app"></a>Uruchom i interakcję z przykładowej aplikacji

Możesz uruchomić przykładową aplikację, aby zobaczyć sposób jej interakcji z Tobą i za pomocą biblioteki klienckiej przetwarzania obrazów podczas wykonywania różnych zadań, takich jak generowanie miniatur lub znakowania obrazów. Aby uruchomić i interakcję z przykładowej aplikacji, wykonaj następujące czynności:

1. Po ukończeniu kompilacji albo naciśnij **F5** lub wybierz **debugowania** menu wstążki, a następnie wybierz **Rozpocznij debugowanie** uruchamianie przykładowej aplikacji.
1. Gdy przykładowa aplikacja jest wyświetlana, wybierz **zarządzanie kluczami subskrypcji** z okienka nawigacji można wyświetlić strony Zarządzanie kluczami subskrypcji.
   ![Strona zarządzania klucz subskrypcji](../Images/Vision_UI_Subscription.PNG)  
1. Wprowadź klucz subskrypcji w **klucz subskrypcji**.
1. Wprowadź adres URL punktu końcowego, pomijając `/vision/v1.0`, zasobu wizualizacji komputerowej dla klucza subskrypcji w **punktu końcowego**.  
   Na przykład jeśli jest używany klucz subskrypcji z bezpłatnej wersji próbnej przetwarzania obrazów, wprowadź następujący adres URL punktu końcowego dla regionu zachodnie centralnej nam platformy Azure: `https://westcentralus.api.cognitive.microsoft.com`
1. Jeśli nie chcesz wprowadzić klucz subskrypcji i punktu końcowego adresu URL przy następnym uruchomieniu aplikacji przykładowej, wybierz opcję **Zapisz ustawienia** można zapisać subskrypcji key i punktu końcowego adresu URL do komputera. Jeśli chcesz usunąć z uprzednio zapisany klucz i punkt końcowy adres URL subskrypcji, wybierz **Usuń ustawienie**.

   > [!NOTE]
   > Przykładowa aplikacja korzysta z wydzielonej pamięci masowej i `System.IO.IsolatedStorage`, aby Twoja subskrypcja key i punktu końcowego adresu URL Sklepu.

1. W obszarze **wybierz scenariusz** w okienku nawigacji wybierz jednego ze scenariuszy obecnie dołączone do przykładowej aplikacji:  

   | Scenariusz | Opis |
   |----------|-------------|
   |Analizowanie obrazu | Używa [analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) operacji do analizowania obraz lokalnym lub zdalnym. Wybierz funkcje programu visual i język dla analizy i zobacz obrazu i wyniki.  |
   |Analizowanie obrazu za pomocą modelu domeny | Używa [modele specyficzne dla domeny listy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) na liście modeli domeny, z których możesz wybrać, operacji i [rozpoznawanie domeny określonej zawartości](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) na analizowanie obrazów lokalnych lub zdalnych przy użyciu operacji model wybranej domeny. Można także język dla analizy. |
   |Opisz obrazu | Używa [opisują obraz](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) operację, aby utworzyć zrozumiałą dla użytkownika opis obrazu lokalnym lub zdalnym. Można także język opisu. |
   |Generowanie tagi | Używa [Tag obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) operację, aby oznaczyć cech wizualnych obrazów lokalnych lub zdalnych. Można także język używany do tagów. |
   |Rozpoznawanie tekstu (OCR) | Używa [optyczne rozpoznawanie znaków](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) operację, aby rozpoznawać i wyodrębnianie drukowanych tekstu z obrazu. Możesz wybrać język do użycia lub umożliwić przetwarzanie obrazów, automatyczne wykrywanie języka. |
   |Rozpoznawanie tekstu w wersji 2 (w języku angielskim) | Używa [Rozpoznaj tekst](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) i [uzyskać rozpoznaje wynik operacji tekstu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) operacje asynchroniczne rozpoznaje i wyodrębnianie tekstu drukowanego na klawiaturze lub odręcznie z obrazu. |
   |Pobierz miniaturę | Używa [Pobierz miniaturę](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) operację, aby wygenerować miniatury obrazów lokalnych lub zdalnych. |

   > [!IMPORTANT]
   > Firma Microsoft odbierze obrazów, przekazywanie i może używać ich w celu ulepszenia interfejsu API przetwarzania obrazów i powiązanych usług. Po przesłaniu obrazu, potwierdzasz, że zostały wykonane naszych [Developer Kodeks postępowania](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

   Poniższy zrzut ekranu przedstawia stronę przewidzianych w scenariuszu analizowanie obrazów, po przeanalizowaniu przykładowy obraz.
   ![Analizowanie stronie scenariusz obrazu](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Zapoznaj się z przykładową aplikację

Rozwiązanie dla przykładowej aplikacji komputerowej dla programu Visual Studio zawiera dwa projekty:

* SampleUserControlLibrary  
  Projekt SampleUserControlLibrary zapewnia funkcje wspólne dla wielu próbkach usług Cognitive Services. Projekt zawiera następujące informacje:
  * SampleScenarios  
    UserControl, zapewniający ustandaryzowane prezentacji, takich jak pasek tytułu okienka nawigacji i w okienku zawartości, przykłady. Przetwarzanie obrazów Przykładowa aplikacja używa tego formantu w oknie MainWindow.xaml do wyświetlania stron scenariusza i dostęp do informacji o współdzielone w scenariuszach, takich jak adres URL key i punktu końcowego subskrypcji.
  * SubscriptionKeyPage  
    Strona, która udostępnia standardowy układ wprowadzenie subskrypcji key i punktu końcowego adresu URL dla przykładowej aplikacji. Komputerowej Przykładowa aplikacja korzysta z tej strony do zarządzania klucz subskrypcji i adres URL punktu końcowego używane przez strony scenariusza.
  * VideoResultControl  
    UserControl, zapewniający ustandaryzowane prezentacja wideo informacji. Ten formant nie korzysta z przykładowej aplikacji komputerowej.
* Przykłady interfejsów WPF VisionAPI  
  Głównego projektu dla przykładowej aplikacji komputerowej, ten projekt zawiera wszystkie ciekawe funkcje do przetwarzania obrazów. Projekt zawiera następujące informacje:
  * AnalyzeInDomainPage.xaml  
    Strona scenariusz analizowanie obrazu ze scenariuszem modelu domeny.
  * AnalyzeImage.xaml  
    Strona scenariusza dla scenariusza analizowanie obrazu.
  * DescribePage.xaml  
    Strona scenariusza dla scenariusza opis obrazu.
  * ImageScenarioPage.cs  
    Klasa ImageScenarioPage, z której pochodzą wszystkie strony scenariusza w przykładowej aplikacji. Ta klasa zarządza funkcje, takie jak dostarczanie poświadczeń i formatowanie danych wyjściowych, które są współużytkowane przez wszystkie strony scenariusza.
  * MainWindow.xaml  
    Główne okno przykładowej aplikacji, kontrolki SampleScenarios używa do przedstawienia strony SubscriptionKeyPage i scenariusza.
  * OCRPage.xaml  
    Strona scenariusza dla scenariusza rozpoznaje tekst (optyczne rozpoznawanie znaków).
  * RecognizeLanguage.cs  
    Klasa RecognizeLanguage zawiera informacje o językach obsługiwanych przez różne metody w przykładowej aplikacji.
  * TagsPage.xaml  
    Strona scenariusza dla scenariusza generowania tagów.
  * TextRecognitionPage.xaml  
    Strona scenariusza dla scenariusza (angielski) rozpoznaje V2 tekstu.
  * ThumbnailPage.xaml  
    Strona scenariusza dla scenariusza Pobierz miniaturę.

### <a name="explore-the-sample-code"></a>Omówimy przykładowy kod

Kluczowych części przykładowego kodu są obramowane przy użyciu bloków komentarza, które zaczyna się `KEY SAMPLE CODE STARTS HERE` i kończyć się znakiem `KEY SAMPLE CODE ENDS HERE`, aby ułatwić zapoznaj się z przykładową aplikację. Tych kluczowych części przykładowego kodu zawierają kod, które są najbardziej potrzebne podczas nauki korzystania z biblioteki klienta interfejsu API przetwarzania obrazów do wykonywania różnych zadań. Możesz wyszukać `KEY SAMPLE CODE STARTS HERE` w programie Visual Studio do przenoszenia między najbardziej istotne sekcje kodu w przykładowej aplikacji komputerowej. 

Na przykład `UploadAndAnalyzeImageAsync` przedstawiona metoda następujące i uwzględniane w AnalyzePage.xaml, pokazuje, jak analizować lokalny obraz za pomocą wywołania za pomocą biblioteki klienta `ComputerVisionClient.AnalyzeImageInStreamAsync` metody.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Zapoznaj się z biblioteki klienta

Ta przykładowa aplikacja używa biblioteki klienta interfejsu API przetwarzania obrazów, cienka C# klienta otokę dla interfejsu API przetwarzania obrazów w usługach Azure Cognitive Services. Biblioteka klienta jest dostępna z pakietów NuGet w [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) pakietu. Podczas tworzenia aplikacji programu Visual Studio z odpowiedniego pakietu NuGet jest pobierany z biblioteki klienta. Można również wyświetlić kod źródłowy biblioteki klienta w `/ClientLibrary` folderu `Microsoft/Cognitive-Vision-Windows` repozytorium.

Funkcje biblioteki klienckiej koncentruje się wokół `ComputerVisionClient` klasy w `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` przestrzeni nazw podczas modeli używanych przez `ComputerVisionClient` klasy podczas interakcji z przetwarzania obrazów znajdują się w `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` przestrzeni nazw. Na różnych stronach scenariusz XAML dołączone do przykładowej aplikacji, znajdziesz następujące `using` dyrektywy dla tych przestrzeni nazw:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Dowiesz się więcej na temat różnych metod, które są dołączone do `ComputerVisionClient` klasy, gdy eksplorujesz scenariusze z przykładowej aplikacji komputerowej.

## <a name="explore-the-analyze-image-scenario"></a>Poznaj scenariusz analizowanie obrazu

Ten scenariusz jest zarządzane przez stronę AnalyzePage.xaml. Wybierz funkcje programu visual i język dla analizy i zobacz obrazu i wyniki. Na stronie scenariusz ma następujące efekty przy użyciu jednej z następujących metod, w zależności od źródła obrazu:

* UploadAndAnalyzeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.AnalyzeImageInStreamAsync` metody.
* AnalyzeUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.AnalyzeImageAsync` metody.

`UploadAndAnalyzeImageAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Ponieważ Przykładowa aplikacja jest analizowanie lokalny obraz, ma do wysłania zawartości obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego, określone w `imageFilePath` do odczytu jako `Stream`, następnie pobiera funkcje visual i język wybrany na stronie scenariusz. Wywołuje `ComputerVisionClient.AnalyzeImageInStreamAsync` jest metoda `Stream` dla pliku, funkcje visual i język, a następnie zwraca wynik w postaci `ImageAnalysis` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`AnalyzeUrlAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Pobiera funkcje visual i język wybrany na stronie scenariusz. Wywołuje `ComputerVisionClient.AnalyzeImageInStreamAsync` metody, przekazując adres URL obrazu, funkcje visual i język, następnie zwraca wynik w postaci `ImageAnalysis` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Zapoznaj się z obrazu analizowanie ze scenariuszem modelu domeny

Ten scenariusz jest zarządzane przez stronę AnalyzeInDomainPage.xaml. Można wybrać model domeny, takich jak `celebrities` lub `landmarks`i język, aby przeprowadzić analizę specyficznego dla domeny obrazu i zobacz obrazu i wyniki. Na stronie scenariusz korzysta z następujących metod, w zależności od źródła obrazu:

* GetAvailableDomainModelsAsync  
  Ta metoda pobiera na liście modeli domeny dostępne z przetwarzania obrazów i wypełnia `_domainModelComboBox` ComboBox — formant na stronie przy użyciu `ComputerVisionClient.ListModelsAsync` metody.
* UploadAndAnalyzeInDomainImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metody.
* AnalyzeInDomainUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.AnalyzeImageByDomainAsync` metody.

`UploadAndAnalyzeInDomainImageAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Ponieważ Przykładowa aplikacja jest analizowanie lokalny obraz, ma do wysłania zawartości obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego, określone w `imageFilePath` do odczytu jako `Stream`, następnie pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` jest metoda `Stream` dla pliku, nazwę modelu domeny i języka, następnie zwraca wynik w postaci `DomainModelResults` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`AnalyzeInDomainUrlAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.AnalyzeImageByDomainAsync` metody, przekazując adres URL obrazu, funkcje visual i język, następnie zwraca wynik w postaci `DomainModelResults` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

## <a name="explore-the-describe-image-scenario"></a>Poznaj scenariusz opisują obrazu

Ten scenariusz jest zarządzane przez stronę DescribePage.xaml. Wybierz język, który chcesz utworzyć zrozumiałą dla użytkownika opis obrazu i wyświetlić wyniki i obraz. Na stronie scenariusz korzysta z następujących metod, w zależności od źródła obrazu:

* UploadAndDescribeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.DescribeImageInStreamAsync` metody.
* DescribeUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.DescribeImageAsync` metody.

`UploadAndDescribeImageAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Ponieważ Przykładowa aplikacja jest analizowanie lokalny obraz, ma do wysłania zawartości obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego, określone w `imageFilePath` do odczytu jako `Stream`, następnie pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.DescribeImageInStreamAsync` jest metoda `Stream` dla pliku, maksymalna liczba kandydatów (w tym przypadku 3) i język, następnie zwraca wynik w postaci `ImageDescription` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`DescribeUrlAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.DescribeImageAsync` metody, przekazując adres URL obrazu, maksymalna liczba kandydatów (w tym przypadku 3) i język, następnie zwraca wynik w postaci `ImageDescription` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

## <a name="explore-the-generate-tags-scenario"></a>Poznaj scenariusz Generowanie tagów

Ten scenariusz jest zarządzane przez stronę TagsPage.xaml. Wybierz język, który chcesz oznaczyć cech wizualnych obrazów i wyświetlić wyniki i obraz. Na stronie scenariusz korzysta z następujących metod, w zależności od źródła obrazu:

* UploadAndGetTagsForImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.TagImageInStreamAsync` metody.
* GenerateTagsForUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.TagImageAsync` metody.

`UploadAndGetTagsForImageAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Ponieważ Przykładowa aplikacja jest analizowanie lokalny obraz, ma do wysłania zawartości obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego, określone w `imageFilePath` do odczytu jako `Stream`, następnie pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.TagImageInStreamAsync` jest metoda `Stream` pliku i język, a następnie zwraca wynik w postaci `TagResult` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`GenerateTagsForUrlAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.TagImageAsync` metody, przekazując adres URL obrazu i język, następnie zwraca wynik w postaci `TagResult` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Poznaj scenariusz rozpoznaje tekst (optyczne rozpoznawanie znaków)

Ten scenariusz jest zarządzane przez stronę OCRPage.xaml. Wybierz język do rozpoznawania i wyodrębnianie tekstu drukowanego z obrazu i wyświetlić obraz i wyniki. Na stronie scenariusz korzysta z następujących metod, w zależności od źródła obrazu:

* UploadAndRecognizeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metody.
* RecognizeUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.RecognizePrintedTextAsync` metody.

`UploadAndRecognizeImageAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Ponieważ Przykładowa aplikacja jest analizowanie lokalny obraz, ma do wysłania zawartości obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego, określone w `imageFilePath` do odczytu jako `Stream`, następnie pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metody, wskazujący, że orientacji nie została wykryta i przekazanie `Stream` pliku i język, a następnie zwraca wynik w postaci `OcrResult` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`RecognizeUrlAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Pobiera języka wybranego na stronie scenariusz. Wywołuje `ComputerVisionClient.RecognizePrintedTextAsync` metody, wskazujący, że orientacji nie została wykryta i przekazanie adresu URL obrazu i języka, a następnie zwraca wynik w postaci `OcrResult` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Poznaj scenariusz (angielski) rozpoznaje V2 tekstu

Ten scenariusz jest zarządzane przez stronę TextRecognitionPage.xaml. Wybierz tryb rozpoznawania i język, który chcesz asynchronicznie rozpoznaje i wyodrębnianie tekstu drukowanych lub pisma odręcznego z obrazów i zobacz obrazu i wyniki. Na stronie scenariusz korzysta z następujących metod, w zależności od źródła obrazu:

* UploadAndRecognizeImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `RecognizeAsync` metody i przekazywanie sparametryzowane delegata dla `ComputerVisionClient.RecognizeTextInStreamAsync` metody.
* RecognizeUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `RecognizeAsync` metody i przekazywanie sparametryzowane delegata dla `ComputerVisionClient.RecognizeTextAsync` metody.
* RecognizeAsync ta metoda obsługuje wywołania asynchronicznego dla obu `UploadAndRecognizeImageAsync` i `RecognizeUrlAsync` metody, a także sondowania wyników, wywołując `ComputerVisionClient.GetTextOperationResultAsync` metody.

W przeciwieństwie do innych scenariusze przetwarzania obrazów w przykładowej aplikacji ten scenariusz jest asynchroniczny, w tym jedna metoda jest wywoływana, aby uruchomić proces, ale inna metoda jest wywoływana, aby sprawdzić stan i zwracają wyniki tego procesu. Logiczny przepływ w tym scenariuszu jest nieco inne niż w innych scenariuszach.

`UploadAndRecognizeImageAsync` Metoda otwiera plik lokalny, określone w `imageFilePath` do odczytu jako `Stream`, następnie wywołuje `RecognizeAsync` jest metoda:

* Wyrażenie lambda do sparametryzowanej delegat asynchroniczne `ComputerVisionClient.RecognizeTextInStreamAsync` metody, za pomocą `Stream` pliku i trybu rozpoznawania jako parametry w `GetHeadersAsyncFunc`.
* Wyrażenie lambda do delegata można pobrać `Operation-Location` wartości nagłówka odpowiedzi, `GetOperationUrlFunc`.

`RecognizeUrlAsync` Wywołania metody `RecognizeAsync` jest metoda:

* Wyrażenie lambda do sparametryzowanej delegat asynchroniczne `ComputerVisionClient.RecognizeTextAsync` metody z adresem URL zdalnego obrazu i trybu rozpoznawania jako parametry w `GetHeadersAsyncFunc`.
* Wyrażenie lambda do delegata można pobrać `Operation-Location` wartości nagłówka odpowiedzi, `GetOperationUrlFunc`.

Gdy `RecognizeAsync` metoda zostanie zakończona, zarówno `UploadAndRecognizeImageAsync` i `RecognizeUrlAsync` metody zwracają wynik jako `TextOperationResult` wystąpienia. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`RecognizeAsync` Metoda wywołuje delegata sparametryzowane dla dowolnego `ComputerVisionClient.RecognizeTextInStreamAsync` lub `ComputerVisionClient.RecognizeTextAsync` przekazanej metody `GetHeadersAsyncFunc` i czeka na odpowiedź. Następnie metoda wywołuje delegata przekazanej `GetOperationUrlFunc` można pobrać `Operation-Location` wartości nagłówka odpowiedzi z odpowiedzi. Ta wartość jest adres URL używany do pobierania wyników metoda przekazanej `GetHeadersAsyncFunc` z przetwarzania obrazów.

`RecognizeAsync` Następnie wywołuje metodę `ComputerVisionClient.GetTextOperationResultAsync` metody, przekazując adres URL, pobierane `Operation-Location` nagłówek odpowiedzi, można pobrać stanu i wynik metody przekazanej `GetHeadersAsyncFunc`. Jeśli stan nie oznacza, że metoda zakończona, pomyślnie lub niepomyślnie, `RecognizeAsync` wywołania metody `ComputerVisionClient.GetTextOperationResultAsync` 3 więcej razy oczekiwania 3 sekundy między wywołaniami. `RecognizeAsync` Metoda zwraca wyniki do metody, która nazwała go.

## <a name="explore-the-get-thumbnail-scenario"></a>Poznaj scenariusz uzyskiwanie miniatur

Ten scenariusz jest zarządzane przez stronę ThumbnailPage.xaml. Można wskazać, czy ma być używany, inteligentne przycinanie i określić żądaną wysokość i szerokość, do generowania miniatur za pomocą obrazu i zobacz obrazu i wyniki. Na stronie scenariusz korzysta z następujących metod, w zależności od źródła obrazu:

* UploadAndThumbnailImageAsync  
  Ta metoda jest używana do obrazów lokalnych, w których obraz musi być zakodowany jako `Stream` i wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.GenerateThumbnailInStreamAsync` metody.
* ThumbnailUrlAsync  
  Ta metoda jest używana dla zdalnego obrazów, w których adres URL obrazu jest wysyłane do przetwarzania obrazów, wywołując `ComputerVisionClient.GenerateThumbnailAsync` metody.

`UploadAndThumbnailImageAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Ponieważ Przykładowa aplikacja jest analizowanie lokalny obraz, ma do wysłania zawartości obrazu do przetwarzania obrazów. Spowoduje to otwarcie pliku lokalnego, określone w `imageFilePath` do odczytu jako `Stream`. Wywołuje `ComputerVisionClient.GenerateThumbnailInStreamAsync` jest metoda szerokości, wysokości `Stream` dla pliku oraz czy ma być używany, inteligentne przycinanie, a następnie zwraca wynik w postaci `Stream`. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

`RecognizeUrlAsync` Metoda tworzy nowy `ComputerVisionClient` wystąpienia, przy użyciu określonej subskrypcji key i punktu końcowego adresu URL. Wywołuje `ComputerVisionClient.GenerateThumbnailAsync` jest metoda szerokość, wysokość, adres URL obrazu i czy ma być używany, inteligentne przycinanie, następnie zwraca wynik w postaci `Stream`. Odziedziczone po metody `ImageScenarioPage` klasy przedstawić zwracane wyniki na stronie scenariusz.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń folder, do którego zostało sklonowane `Microsoft/Cognitive-Vision-Windows` repozytorium. Jeśli została wybrana do użycia przykładowe obrazy, również usunąć folder, do którego zostało sklonowane `Microsoft/Cognitive-Face-Windows` repozytorium.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do interfejsu API rozpoznawania twarzy](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)