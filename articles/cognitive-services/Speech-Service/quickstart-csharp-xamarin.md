---
title: 'Szybki Start: Rozpoznawanie mowy C# , (Xamarin) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz międzyplatformową C# aplikację Xamarin dla systemów Windows platformy UWP, Android i iOS za pomocą zestawu Speech SDK Cognitive Services. Transkrypcja mowę na tekst w czasie rzeczywistym z mikrofonu urządzenia lub symulatora. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387387"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy przy użyciu międzyplatformowej aplikacji Xamarin przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [zamiany mowy na tekst](quickstart-csharp-uwp.md), [zamiany tekstu na mowę](quickstart-text-to-speech-csharp-uwp.md) i [Tłumaczenie mowy](quickstart-translate-speech-uwp.md).

W tym artykule opisano tworzenie aplikacji dla wielu platform za pomocą C# platformy Xamarin dla platforma uniwersalna systemu Windows (platformy UWP), Android i iOS przy użyciu [zestawu Speech SDK](speech-sdk.md). Program przekształca mowę na tekst w czasie rzeczywistym z mikrofonu Twojego urządzenia. Aplikacja jest zbudowana z [pakietem NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019 (w dowolnej wersji).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* KOMPUTER z systemem Windows z aktualizacją dla twórców systemu Windows 10 (10,0; Kompilacja 16299) lub nowsza oraz z działającym mikrofonem.
* [Instalacja platformy Xamarin w programie Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Instalacja platformy Xamarin Android w systemie Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Instalacja platformy Xamarin iOS w systemie Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Aby docelowa wersja systemu Android: 
   * Urządzenie z systemem Android (ARM32/64, x86; Interfejs API 23: system Android 6,0 Marshmallow lub nowszy) [włączony do programowania](https://developer.android.com/studio/debug/dev-options) przy użyciu działającego mikrofonu.
* Aby docelowa Grupa systemu iOS: urządzenie z systemem iOS (ARM64) lub symulator systemu iOS (x64) z [włączoną obsługą programowania](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) przy użyciu działającego mikrofonu.
* Aby uzyskać pomoc techniczną kompilacji systemu Windows ARM64, zainstaluj [opcjonalne narzędzia kompilacji oraz zestaw Windows 10 SDK dla ARM/arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Dodaj przykładowy kod dla wspólnego projektu `helloworld`

Typowy projekt `helloworld` zawiera implementacje niezależne od platformy dla aplikacji dla wielu platform.
Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj C# kod związany z implementacją.

1. W **Eksplorator rozwiązań**, w obszarze Projekt `helloworld` otwarty `MainPage.xaml`.

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do znacznika **Grid** (między `<StackLayout>` i `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs`. (Jest on pogrupowany pod `MainPage.xaml`).

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. W programie obsługi `OnRecognitionButtonClicked` w pliku źródłowym Znajdź ciąg `YourSubscriptionKey` i zastąp go kluczem subskrypcji.

1. W obsłudze `OnRecognitionButtonClicked` Znajdź ciąg `YourServiceRegion` i zastąp go [regionem](regions.md) skojarzonym z subskrypcją. (Na przykład użyj `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Następnie należy utworzyć [usługę platformy Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), która jest używana do wykonywania zapytań dotyczących uprawnień mikrofonu z różnych projektów platformy (platformy UWP, Android i iOS). Aby to zrobić, Dodaj nowy folder `Services` w projekcie `helloworld` i Utwórz nowy C# plik źródłowy w tym obszarze (kliknij prawym przyciskiem myszy folder `Services` i **Dodaj** > **nowy element**@no__t **-7)** i zmień jego nazwę na `IMicrophoneService.cs` i umieść cały kod z następujący fragment kodu w tym pliku:

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Dodaj przykładowy kod dla projektu `helloworld.Android`

Teraz Dodaj C# kod, który definiuje część aplikacji dla systemu Android.

1. W **Eksplorator rozwiązań**w projekcie `helloworld.Android` otwartym `MainActivity.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Kolejne Dodawanie implementacji dla systemu Android dla `MicrophoneService` przez utworzenie nowego folderu `Services` w projekcie `helloworld.Android`. Po utworzeniu nowego C# pliku źródłowego w obszarze i zmień jego nazwę na `MicrophoneService.cs` i Skopiuj poniższy fragment kodu do tego pliku.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Po tym otwarciu `AndroidManifest.xml` w folderze `Properties` i dodanie następujących ustawień uprawnień dla mikrofonu między `<manifest>` i `</manifest>`.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Dodaj przykładowy kod dla projektu `helloworld.iOS`

Teraz można dodać C# kod definiujący część aplikacji dla systemu iOS, a także utworzyć konfiguracje specyficzne dla urządzeń firmy Apple w projekcie HelloWorld. iOS.

1. W **Eksplorator rozwiązań**w projekcie `helloworld.iOS` otwartym `AppDelegate.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu iOS dla `MicrophoneService`, tworząc nowy folder `Services` w projekcie `helloworld.iOS`. Po utworzeniu nowego C# pliku źródłowego w obszarze i zmień jego nazwę na `MicrophoneService.cs` i Skopiuj poniższy fragment kodu do tego pliku.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otwórz info. plist pod `helloworld.iOS` projektu do edytora tekstów i Dodaj następującą parę klucz-wartość w sekcji DICT <key>NSMicrophoneUsageDescription</key>
   <string>Ta przykładowa aplikacja wymaga dostępu do mikrofonu</string>
   > Uwaga: Jeśli zamierzasz tworzyć urządzenia dla urządzeń iPhone, upewnij się, że `Bundle Identifier` pasuje do identyfikatora aplikacji profilu aprowizacji urządzenia, w przeciwnym razie kompilacja zakończy się niepowodzeniem. Za pomocą iPhoneSimulator można pozostawić ją.

1. W przypadku kompilowania na komputerze z systemem Windows należy nawiązać połączenie z urządzeniem Mac w celu kompilowania za pośrednictwem **narzędzi** > **iOS** > **par na komputerze Mac**. Aby włączyć połączenie z urządzeniem Mac, postępuj zgodnie z instrukcjami wyświetlanymi przez program Visual Studio.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodaj przykładowy kod dla projektu `helloworld.UWP`

Teraz Dodaj C# kod, który definiuje platformy UWP określoną część aplikacji.

1. W **Eksplorator rozwiązań**w projekcie `helloworld.UWP` otwartym `MainPage.xaml.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Następnie Dodaj platformy UWP specyficzną dla `MicrophoneService`, tworząc nowy folder `Services` w projekcie `helloworld.UWP`. Po utworzeniu nowego C# pliku źródłowego w obszarze i zmień jego nazwę na `MicrophoneService.cs` i Skopiuj poniższy fragment kodu do tego pliku.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Następnie kliknij dwukrotnie plik `Package.appxmanifest` w projekcie `helloworld.UWP` w programie Visual Studio i w obszarze **możliwości**@no__t-**3 jest** zaznaczone i Zapisz plik.
   > Uwaga: w przypadku wyświetlenia ostrzeżenia: plik certyfikatu nie istnieje: HelloWorld. UWP_TemporaryKey. pfx, zapoznaj [się z próbką zamiany mowy na tekst](quickstart-csharp-uwp.md) , aby uzyskać więcej informacji.

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-uwp-application"></a>Kompilowanie i uruchamianie aplikacji platformy UWP

1. Ustaw `helloworld.UWP` jako projekt startowy, a następnie kliknij prawym przyciskiem myszy myszą w projekcie `helloworld.UWP` i wybierz polecenie **Kompiluj** , aby skompilować aplikację. 

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja do C# rozpoznawania mowy platformy UWP — Szybki Start](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Wybierz pozycję **Włącz mikrofon**i po włączeniu żądania uprawnienia dostępu wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Wybierz kolejno pozycje **Uruchom rozpoznawanie mowy**i mów do mikrofonu w języku angielskim lub zdania. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu, który pojawia się w oknie.

   ![Interfejs użytkownika rozpoznawania mowy](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Kompiluj i Uruchamiaj aplikacje dla systemów Android i iOS

Kompilowanie i uruchamianie aplikacji dla systemów Android i iOS na urządzeniu lub symulatorze odbywa się w podobny sposób niż w przypadku programu platformy UWP. Ważne jest, aby upewnić się, że wszystkie zestawy SDK są poprawnie zainstalowane w sekcji `Prerequisites` tego dokumentu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)
