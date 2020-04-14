---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, C# (Xamarin) - usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz wieloplatformową aplikację Xamarin języka C# dla platformy uniwersalnej systemu Windows (UWP), Android i iOS przy użyciu zestawu SDK mowy usług Cognitive Services. Transkrybujesz mowę na tekst w czasie rzeczywistym z mikrofonu urządzenia lub symulatora. Aplikacja jest zbudowana z pakietem NuGet zestawu SDK mowy i programem Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: c9bcd301b19252cedd9ac9a1867ccf132a537587
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275524"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

Jeśli już to zrobiłeś, świetnie. Chodźmy dalej.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Dodaj przykładowy kod dla wspólnego projektu helloworld

Wspólny projekt helloworld zawiera implementacje niezależne od platformy dla aplikacji między platformami. Teraz dodaj kod XAML, który definiuje interfejs użytkownika aplikacji i dodaj kod C# za implementacją.

1. W **Eksploratorze rozwiązań**, `MainPage.xaml`w ramach wspólnego projektu helloworld, otwórz .

1. W widoku XAML projektanta wstaw następujący fragment kodu XAML do `<StackLayout>` `</StackLayout>`znacznika **Siatka** między i:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml.cs`plik źródłowy związany z kodem . Jest pogrupowany `MainPage.xaml`w obszarze .

1. Zastąp cały kod w nim z następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. W programie obsługi `OnRecognitionButtonClicked` pliku źródłowego `YourSubscriptionKey`znajdź ciąg i zastąp go kluczem subskrypcji.


1. W `OnRecognitionButtonClicked` programie obsługi znajdź `YourServiceregion`ciąg i zastąp go **identyfikatorem Region** z [regionu skojarzonego](https://aka.ms/speech/sdkregion) z subskrypcją. (Na przykład `westus` użyj bezpłatnej subskrypcji próbnej).

1. Następnie należy utworzyć [usługę platformy Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), która jest używana do wykonywania zapytań o uprawnienia mikrofonu z różnych projektów platformy, takich jak platformy uniwersalne systemu Windows, Android i iOS. Aby to zrobić, dodaj nowy folder o nazwie *Usługi* w ramach projektu helloworld i utwórz pod nim nowy plik źródłowy języka C#. Możesz kliknąć prawym przyciskiem myszy folder *Usługi* i wybrać polecenie **Dodaj** > nowy**plik kodu****elementu** > . Zmień nazwę `IMicrophoneService.cs`pliku i umieść cały kod z następującego fragmentu kodu w tym pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Dodaj przykładowy `helloworld.Android` kod projektu

Teraz dodaj kod C#, który definiuje część aplikacji specyficzne dla systemu Android.

1. W **Eksploratorze rozwiązań**, pod helloworld. Projekt Android, `MainActivity.cs`otwórz .

1. Zastąp cały kod w nim z następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Następnie dodaj implementację specyficzne `MicrophoneService` dla systemu Android, tworząc nowy folder *Usługi* w ramach helloworld. Projekt Android. Następnie należy utworzyć nowy plik źródłowy języka C#. Zmień nazwę `MicrophoneService.cs`pliku . Skopiuj i wklej do tego pliku fragment kodu w brzmieniu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Następnie otwórz `AndroidManifest.xml` w folderze *Właściwości.* Dodaj następujące ustawienia uprawnień do użycia `<manifest>` `</manifest>`dla mikrofonu między i:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Dodaj przykładowy `helloworld.iOS` kod projektu

Teraz dodaj kod C#, który definiuje część aplikacji dla systemu iOS. Utwórz również konfiguracje specyficzne dla urządzenia firmy Apple do projektu helloworld.iOS.

1. W **Eksploratorze rozwiązań**w ramach `AppDelegate.cs`projektu helloworld.iOS otwórz .

1. Zastąp cały kod w nim z następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Następnie dodaj implementację specyficzne `MicrophoneService` dla systemu iOS, tworząc nowy folder *Usługi* w ramach projektu helloworld.iO. Następnie należy utworzyć nowy plik źródłowy języka C#. Zmień nazwę `MicrophoneService.cs`pliku . Skopiuj i wklej do tego pliku fragment kodu w brzmieniu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otwórz `Info.plist` w ramach projektu helloworld.iOS w edytorze tekstu. Dodaj następującą parę wartości klucza w sekcji dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Ta przykładowa aplikacja wymaga dostępu do mikrofonu</string>

   > [!NOTE]
   > Jeśli budujemy urządzenie z systemem iPhone, upewnij się, że `Bundle Identifier` pasuje do identyfikatora aplikacji profilu inicjowania obsługi administracyjnej urządzenia. W przeciwnym razie kompilacja zakończy się niepowodzeniem. Z iPhoneSimulator, można zostawić go tak, jak jest.

1. Jeśli korzystasz z komputera z systemem Windows, nawiązuj połączenie z urządzeniem Mac do tworzenia za pomocą narzędzi > **iOS** > **Pair to Mac**. **Tools** Postępuj zgodnie z kreatorem instrukcji dostarczonym przez program Visual Studio, aby włączyć połączenie z urządzeniem Mac.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodaj przykładowy `helloworld.UWP` kod projektu

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodaj przykładowy kod dla helloworld. Projekt platformy uniwersalnej systemu zuchwu

Teraz dodaj kod C#, który definiuje część aplikacji specyficzną dla platformy uniwersalnej systemu wizowego.

1. W **Eksploratorze rozwiązań**, pod helloworld. Projekt platformy uniwersalnej systemu otwartego, otwórz `MainPage.xaml.cs`.

1. Zastąp cały kod w nim z następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Następnie dodaj implementacji specyficznej `MicrophoneService` dla platformy uniwersalnej systemu Windows, tworząc nowy folder *Usługi* w ramach helloworld. projekt platformy uniwersalnej systemu i platformy uniwersalnej systemu. Następnie należy utworzyć nowy plik źródłowy języka C#. Zmień nazwę `MicrophoneService.cs`pliku . Skopiuj i wklej do tego pliku fragment kodu w brzmieniu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Następnie kliknij dwukrotnie `Package.appxmanifest` plik pod helloworld. Projekt platformy uniwersalnej systemu Wizualnego w programie Visual Studio. W obszarze **Możliwości**upewnij się, że wybrano **opcję Mikrofon,** i zapisz plik.

1. Następnie kliknij `Package.appxmanifest` dwukrotnie `helloworld.UWP` plik w ramach projektu wewnątrz programu Visual Studio i w obszarze **Mikrofon możliwości** > **jest** zaznaczone i zapisać plik.
   > Uwaga: W przypadku, gdy pojawi się ostrzeżenie: Plik certyfikatu nie istnieje: helloworld. UWP_TemporaryKey.pfx, sprawdź [mowę do](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) tekstu próbki, aby uzyskać więcej informacji.

1. Na pasku menu wybierz **pozycję Zapisz** > **wszystko,** aby zapisać zmiany.

## <a name="build-and-run-the-uwp-application"></a>Tworzenie i uruchamianie aplikacji platformy uniwersalnej systemuśpiłnie

1. Ustaw helloworld. Platformy uniwersalnej systemu Windows jako projekt startowy. Kliknij prawym przyciskiem myszy helloworld. projekt platformy uniwersalnej systemuśposiedzenia i wybierz **kompilację,** aby utworzyć aplikację.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację. Zostanie **wyświetlene okno helloworld.**

   ![Przykładowa aplikacja do rozpoznawania mowy platformy uniwersalnej systemu Windows w języku C# — szybki start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Wybierz **włącz mikrofon**. Po wyświetleniu żądania uprawnień dostępu wybierz pozycję **Tak**.

   ![Żądanie uprawnień dostępu do mikrofonu](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Wybierz **pozycję Rozpocznij rozpoznawanie mowy**i mów w języku angielskim do mikrofonu urządzenia. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

   ![Interfejs użytkownika rozpoznawania mowy](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Tworzenie i uruchamianie aplikacji na Androida i iOS

Tworzenie i uruchamianie aplikacji systemu Android i iOS w urządzeniu lub symulatorze odbywa się w sposób podobny do platformy uniwersalnej systemu Windows. Upewnij się, że wszystkie pakiety SDK są poprawnie zainstalowane zgodnie z wymaganiami w sekcji "Wymagania wstępne" tego artykułu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
