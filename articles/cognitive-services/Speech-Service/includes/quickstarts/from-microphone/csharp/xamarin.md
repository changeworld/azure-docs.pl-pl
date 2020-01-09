---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C# (Xamarin) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz międzyplatformową C# aplikację Xamarin dla platforma uniwersalna systemu Windows (platformy UWP), Android i iOS przy użyciu zestawu SDK Cognitive Services Speech. Transkrypcja mowę na tekst w czasie rzeczywistym z mikrofonu urządzenia lub symulatora. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 66391f7b9282781902723b0153a5797a5f7ae82b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466511"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=xamarin)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

Jeśli to już zrobione, świetnie. Kontynuujmy.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Dodaj przykładowy kod dla wspólnego projektu HelloWorld

Typowy projekt HelloWorld zawiera implementacje niezależne od platformy dla aplikacji dla wielu platform. Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj C# kod związany z implementacją.

1. W **Eksplorator rozwiązań**, w obszarze popularne projekty helloworld Otwórz `MainPage.xaml`.

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do znacznika **Grid** między `<StackLayout>` i `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs`. Jest on pogrupowany pod `MainPage.xaml`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. W programie obsługi `OnRecognitionButtonClicked` pliku źródłowego Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji.


1. W obsłudze `OnRecognitionButtonClicked` Znajdź ciąg `YourServiceRegion`i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. (Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej).

1. Następnie należy utworzyć [usługę platformy Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), która jest używana do wykonywania zapytań dotyczących uprawnień mikrofonu z różnych projektów platformy, takich jak platformy UWP, Android i iOS. W tym celu należy dodać nowy folder o nazwie *Services* w projekcie HelloWorld i utworzyć w nim nowy C# plik źródłowy. Możesz kliknąć prawym przyciskiem myszy folder *usługi* , a następnie wybrać pozycję **Dodaj** > **nowy element** > **pliku kodu**. Zmień nazwę pliku `IMicrophoneService.cs`i umieść cały kod z następującego fragmentu kodu w tym pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabx-android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Dodawanie przykładowego kodu dla projektu `helloworld.Android`

Teraz Dodaj C# kod definiujący część aplikacji dla systemu Android.

1. W **Eksplorator rozwiązań**w obszarze HelloWorld. Projekt systemu Android, Otwórz `MainActivity.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu Android dla `MicrophoneService`, tworząc nowe *usługi* folderów w obszarze HelloWorld. Projekt systemu Android. Następnie utwórz w nim nowy C# plik źródłowy. Zmień nazwę pliku `MicrophoneService.cs`. Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Następnie otwórz `AndroidManifest.xml` w folderze *Właściwości* . Dodaj następujące ustawienia dotyczące uprawnień dla mikrofonu między `<manifest>` i `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Dodawanie przykładowego kodu dla projektu `helloworld.iOS`

Teraz Dodaj C# kod definiujący część aplikacji określoną dla systemu iOS. Utwórz także konfiguracje specyficzne dla urządzenia firmy Apple w projekcie HelloWorld. iOS.

1. W **Eksplorator rozwiązań**w projekcie HelloWorld. iOS otwórz `AppDelegate.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Następnie Dodaj implementację specyficzną dla systemu iOS dla `MicrophoneService`, tworząc nowe *usługi* folderów w projekcie HelloWorld.IO. Następnie utwórz w nim nowy C# plik źródłowy. Zmień nazwę pliku `MicrophoneService.cs`. Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otwórz `Info.plist` w projekcie HelloWorld. iOS w edytorze tekstów. Dodaj następującą parę klucz-wartość w sekcji Dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Ta przykładowa aplikacja wymaga dostępu do mikrofonu</string>

   > [!NOTE]
   > W przypadku kompilowania urządzenia iPhone upewnij się, że `Bundle Identifier` jest zgodny z IDENTYFIKATORem aplikacji profilu aprowizacji urządzenia. W przeciwnym razie kompilacja zakończy się niepowodzeniem. Za pomocą iPhoneSimulator można pozostawić to.

1. Jeśli tworzysz na komputerze z systemem Windows, nawiąż połączenie z urządzeniem Mac, aby skompilować za pośrednictwem **narzędzi** > **iOS** > **para do komputerów Mac**. Aby włączyć połączenie z urządzeniem Mac, postępuj zgodnie z instrukcjami wyświetlanymi przez program Visual Studio.

#### <a name="uwptabhelloworlduwp"></a>[PLATFORMY UNIWERSALNEJ SYSTEMU WINDOWS](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodawanie przykładowego kodu dla projektu `helloworld.UWP`

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Dodaj przykładowy kod dla HelloWorld. Projekt platformy UWP

Teraz Dodaj C# kod, który definiuje część aplikacji platformy UWP.

1. W **Eksplorator rozwiązań**w obszarze HelloWorld. PLATFORMY UWP projekt, Otwórz `MainPage.xaml.cs`.

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Następnie Dodaj do `MicrophoneService` implementację specyficzną dla platformy UWP, tworząc nowe *usługi* folderów w obszarze HelloWorld. Projekt platformy UWP. Następnie utwórz w nim nowy C# plik źródłowy. Zmień nazwę pliku `MicrophoneService.cs`. Skopiuj i wklej następujący fragment kodu do tego pliku:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Następnie kliknij dwukrotnie plik `Package.appxmanifest` w obszarze HelloWorld. PLATFORMY UWP projekt w programie Visual Studio. W obszarze **możliwości**upewnij się, że wybrano **mikrofon** i Zapisz plik.

1. Następnie dwukrotnie kliknij `Package.appxmanifest` plik w `helloworld.UWP` projekcie w programie Visual Studio i w obszarze **możliwości** > **mikrofon** jest zaznaczone i Zapisz plik.
   > Uwaga: w przypadku wyświetlenia ostrzeżenia: plik certyfikatu nie istnieje: HelloWorld. UWP_TemporaryKey. pfx, zapoznaj [się z próbką zamiany mowy na tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) , aby uzyskać więcej informacji.

1. Na pasku menu wybierz pozycję **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-uwp-application"></a>Kompilowanie i uruchamianie aplikacji platformy UWP

1. Ustaw HelloWorld. PLATFORMY UWP jako projekt startowy. Kliknij prawym przyciskiem myszy plik HelloWorld. PLATFORMY UWP projekt i wybierz opcję **Kompiluj** , aby skompilować aplikację.

1. Wybierz pozycję **debuguj** > **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja do C# rozpoznawania mowy platformy UWP — Szybki Start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Wybierz pozycję **Włącz mikrofon**. Gdy zostanie wyświetlone żądanie uprawnienia dostępu, wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Wybierz kolejno pozycje **Uruchom rozpoznawanie mowy**i mów do mikrofonu w języku angielskim lub zdania. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

   ![Interfejs użytkownika rozpoznawania mowy](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Kompiluj i Uruchamiaj aplikacje dla systemów Android i iOS

Kompilowanie i uruchamianie aplikacji dla systemów Android i iOS na urządzeniu lub symulatorze odbywa się w podobny sposób do platformy UWP. Upewnij się, że wszystkie zestawy SDK są poprawnie zainstalowane, zgodnie z wymaganiami w sekcji "wymagania wstępne" tego artykułu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
