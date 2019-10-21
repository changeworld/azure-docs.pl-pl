---
title: 'Szybki Start: Rozpoznawanie mowy C# , (Xamarin) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz międzyplatformową C# aplikację Xamarin dla platforma uniwersalna systemu Windows (platformy UWP), Android i iOS przy użyciu zestawu SDK Cognitive Services Speech. Transkrypcja mowę na tekst w czasie rzeczywistym z mikrofonu urządzenia lub symulatora. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675608"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy przy użyciu międzyplatformowej aplikacji Xamarin korzystającej z zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [zamiany mowy na tekst](quickstart-csharp-uwp.md), [zamiany tekstu na mowę](quickstart-text-to-speech-csharp-uwp.md)i [Tłumaczenie mowy](quickstart-translate-speech-uwp.md).

W tym artykule opisano tworzenie aplikacji dla wielu platform za C# pomocą platformy Xamarin dla platforma uniwersalna systemu Windows (platformy UWP), Android i iOS, które używają zestawu Azure Cognitive Services [Speech SDK](speech-sdk.md). Program przekształca mowę na tekst w czasie rzeczywistym z mikrofonu Twojego urządzenia. Aplikacja jest zbudowana z [pakietem NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i dowolnym wydaniem Microsoft Visual Studio 2019.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* KOMPUTER z systemem Windows z aktualizacją dla twórców systemu Windows 10 (10,0; Kompilacja 16299) lub nowsza oraz z działającym mikrofonem.
* [Instalacja programu Xamarin w programie Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Instalacja platformy Xamarin Android w systemie Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Instalacja platformy Xamarin iOS w systemie Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Aby docelowa wersja systemu Android: 
   * Urządzenie z systemem Android (ARM32/64, x86; Interfejs API 23: system Android 6,0 Marshmallow lub nowszy) [włączony do programowania](https://developer.android.com/studio/debug/dev-options) przy użyciu działającego mikrofonu.
* Aby określić docelowy system iOS: 
    * Urządzenie z systemem iOS (ARM64) lub symulator systemu iOS (x64) z [włączoną obsługą programowania](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) przy użyciu mikrofonu działającego.
* Aby uzyskać pomoc techniczną kompilacji systemu Windows ARM64, zainstaluj [opcjonalne narzędzia kompilacji oraz zestaw Windows 10 SDK dla ARM/arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Dodaj przykładowy kod dla wspólnego projektu HelloWorld

Typowy projekt HelloWorld zawiera implementacje niezależne od platformy dla aplikacji dla wielu platform. Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj C# kod związany z implementacją.

1. W **Eksplorator rozwiązań**, w obszarze popularne projekty helloworld Otwórz `MainPage.xaml`.

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do znacznika **Grid** między `<StackLayout>` i `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs`. Jest on pogrupowany pod `MainPage.xaml`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. W programie obsługi `OnRecognitionButtonClicked` w pliku źródłowym Znajdź ciąg `YourSubscriptionKey` i zastąp go kluczem subskrypcji.

1. W obsłudze `OnRecognitionButtonClicked` Znajdź ciąg `YourServiceRegion` i zastąp go [regionem](regions.md) skojarzonym z subskrypcją. Na przykład w subskrypcji bezpłatnej wersji próbnej Użyj `westus`.

1. Następnie należy utworzyć [usługę platformy Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), która jest używana do wykonywania zapytań dotyczących uprawnień mikrofonu z różnych projektów platformy, takich jak platformy UWP, Android i iOS. W tym celu należy dodać nowy folder o nazwie *Services* w projekcie HelloWorld i utworzyć w nim nowy C# plik źródłowy. Możesz kliknąć prawym przyciskiem myszy folder *usługi* , a następnie wybrać pozycję **Dodaj**  > **nowy element**  > **pliku kodu**. Zmień nazwę pliku `IMicrophoneService.cs` i umieść cały kod z następującego fragmentu kodu w tym pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Dodaj przykładowy kod dla HelloWorld. Projekt systemu Android

Teraz Dodaj C# kod definiujący część aplikacji dla systemu Android.

1. W **Eksplorator rozwiązań**w obszarze HelloWorld. Projekt systemu Android, Otwórz `MainActivity.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu Android dla `MicrophoneService`, tworząc nowe *usługi* folderów w obszarze HelloWorld. Projekt systemu Android. Następnie utwórz w nim nowy C# plik źródłowy. Zmień nazwę pliku `MicrophoneService.cs`. Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Następnie otwórz `AndroidManifest.xml` w folderze *Właściwości* . Dodaj następujące ustawienia dotyczące uprawnień dla mikrofonu między `<manifest>` i `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Dodawanie przykładowego kodu dla projektu HelloWorld. iOS

Teraz Dodaj C# kod definiujący część aplikacji określoną dla systemu iOS. Utwórz także konfiguracje specyficzne dla urządzenia firmy Apple w projekcie HelloWorld. iOS.

1. W **Eksplorator rozwiązań**w projekcie HelloWorld. iOS otwórz `AppDelegate.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu iOS dla `MicrophoneService`, tworząc nowe *usługi* folderów w projekcie HelloWorld.IO. Następnie utwórz w nim nowy C# plik źródłowy. Zmień nazwę pliku `MicrophoneService.cs`. Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otwórz `Info.plist` w projekcie HelloWorld. iOS w edytorze tekstów. Dodaj następującą parę klucz-wartość w sekcji Dict:

   <key>NSMicrophoneUsageDescription</key> 
   <string>Ta przykładowa aplikacja wymaga dostępu do mikrofonu</string>

   > [!NOTE] 
   > W przypadku kompilowania urządzenia iPhone upewnij się, że `Bundle Identifier` jest zgodny z IDENTYFIKATORem aplikacji profilu aprowizacji urządzenia. W przeciwnym razie kompilacja zakończy się niepowodzeniem. Za pomocą iPhoneSimulator można pozostawić to.

1. Jeśli tworzysz na komputerze z systemem Windows, nawiąż połączenie z urządzeniem Mac, aby skompilować za pośrednictwem **narzędzi**  > **iOS**  > **para do komputerów Mac**. Aby włączyć połączenie z urządzeniem Mac, postępuj zgodnie z instrukcjami wyświetlanymi przez program Visual Studio.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodaj przykładowy kod dla HelloWorld. Projekt platformy UWP

Teraz Dodaj C# kod, który definiuje część aplikacji platformy UWP.

1. W **Eksplorator rozwiązań**w obszarze HelloWorld. PLATFORMY UWP projekt, Otwórz `MainPage.xaml.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Następnie Dodaj do `MicrophoneService` implementację specyficzną dla platformy UWP, tworząc nowe *usługi* folderów w obszarze HelloWorld. Projekt platformy UWP. Następnie utwórz w nim nowy C# plik źródłowy. Zmień nazwę pliku `MicrophoneService.cs`. Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Następnie kliknij dwukrotnie plik `Package.appxmanifest` w obszarze HelloWorld. PLATFORMY UWP projekt w programie Visual Studio. W obszarze **możliwości**upewnij się, że wybrano **mikrofon** i Zapisz plik.

   > [!NOTE] 
   > Jeśli zobaczysz ostrzeżenie "plik certyfikatu nie istnieje: HelloWorld. UWP_TemporaryKey. pfx, "Sprawdź przykład [zamiany mowy na tekst](quickstart-csharp-uwp.md) , aby uzyskać więcej informacji.

1. Na pasku menu wybierz pozycję **plik**  > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-uwp-application"></a>Kompilowanie i uruchamianie aplikacji platformy UWP

1. Ustaw HelloWorld. PLATFORMY UWP jako projekt startowy. Kliknij prawym przyciskiem myszy plik HelloWorld. PLATFORMY UWP projekt i wybierz opcję **Kompiluj** , aby skompilować aplikację. 

1. Wybierz pozycję **debuguj**  > **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja do C# rozpoznawania mowy platformy UWP — Szybki Start](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Wybierz pozycję **Włącz mikrofon**. Gdy zostanie wyświetlone żądanie uprawnienia dostępu, wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Wybierz kolejno pozycje **Uruchom rozpoznawanie mowy**i mów do mikrofonu w języku angielskim lub zdania. Twoja Zamiana jest przesyłana do usługi mowy i uzyskanego do tekstu, który pojawia się w oknie.

   ![Interfejs użytkownika rozpoznawania mowy](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Kompiluj i Uruchamiaj aplikacje dla systemów Android i iOS

Kompilowanie i uruchamianie aplikacji dla systemów Android i iOS na urządzeniu lub symulatorze odbywa się w podobny sposób do platformy UWP. Upewnij się, że wszystkie zestawy SDK są poprawnie zainstalowane, zgodnie z wymaganiami w sekcji "wymagania wstępne" tego artykułu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)
