---
title: 'Szybki start: rozpoznawanie mowy w języku C# w aplikacji platformy UWP przy użyciu zestawu Speech SDK w usłudze Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w aplikacji platformy UWP przy użyciu zestawu Speech SDK w usłudze Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: d9a90869e060d2f8f1a1c522a4528e74841caada
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339618"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w aplikacji platformy UWP przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP) przy użyciu zestawu [Speech SDK](speech-sdk.md) usługi Cognitive Services. Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym podczas korzystania z mikrofonu urządzenia. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia kroków tego przewodnika Szybki start potrzebujesz klucza subskrypcji usługi rozpoznawania mowy. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje, zobacz temat [Try the Speech service for free](get-started.md) (Wypróbuj bezpłatnie usługę rozpoznawania mowy).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Uruchom program Visual Studio 2017.

1. Upewnij się, że jest dostępny pakiet roboczy **tworzenia platformy uniwersalnej systemu Windows**. Wybierz kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje** na pasku menu programu Visual Studio, aby otworzyć instalator programu Visual Studio. Jeśli ten pakiet roboczy jest już włączony, zamknij okno dialogowe. 

    ![Zrzut ekranu Instalatora programu Visual Studio z wyróżnioną kartą Pakiety robocze](media/sdk/vs-enable-uwp-workload.png)

    W przeciwnym razie zaznacz pole obok pozycji **Tworzenie aplikacji na wiele platform dla platformy .NET** i wybierz opcję **Modyfikuj** w prawym dolnym rogu okna dialogowego. Instalowanie nowej funkcji chwilę potrwa.

1. Utwórz pustą aplikację uniwersalną dla systemu Windows w języku C#. Najpierw wybierz w menu pozycje **Plik** > **Nowy** > **Projekt**. W oknie dialogowym **Nowy projekt** w lewym okienku rozwiń pozycje **Zainstalowane** > **Visual C#** > **Aplikacje uniwersalne systemu Windows**. Następnie wybierz pozycję **Pusta aplikacja (platforma uniwersalna systemu Windows)**. Jako nazwę projektu podaj *helloworld*.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowy projekt](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Zestaw Speech SDK wymaga, by aplikacja została utworzona dla systemu Windows 10 Fall Creators Update lub jego nowszej wersji. W wyświetlonym oknie **Nowy projekt platformy uniwersalnej systemu Windows** wybierz pozycję **Windows 10 Fall Creators Update (10.0; kompilacja 16299)** dla opcji **Wersja minimalna**. W polu **Wersja docelowa** wybierz tę lub dowolną nowszą wersję, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno Nowy projekt platformy uniwersalnej systemu Windows](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Jeśli korzystasz z 64-bitowego systemu Windows, możesz przełączyć platformę kompilacji na `x64` za pomocą menu rozwijanego na pasku narzędzi programu Visual Studio. (64-bitowy system Windows może obsługiwać aplikacje 32-bitowe, więc jeśli wolisz, możesz pozostawić wartość `x86`.)

   ![Zrzut ekranu paska narzędzi programu Visual Studio z wyróżnioną opcją x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Zestaw Speech SDK obsługuje wyłącznie procesory zgodne z technologią Intel. Architektura ARM nie jest obecnie obsługiwana.

1. Instalowanie i odwoływanie się do [zestawu Speech SDK pakietu NuGet](https://aka.ms/csspeech/nuget). W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Zrzut ekranu Eksploratora rozwiązań z wyróżnioną opcją Zarządzaj pakietami NuGet rozwiązania](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. W prawym górnym rogu wybierz w polu **Źródło pakietu** wartość **nuget.org**. Wyszukaj pakiet `Microsoft.CognitiveServices.Speech` i zainstaluj go w projekcie **helloworld**.

    ![Zrzut ekranu okna dialogowego Zarządzaj pakietami rozwiązania](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instaluj pakiet NuGet")

1. Zaakceptuj wyświetloną licencję, aby rozpocząć instalowanie pakietu NuGet.

    ![Zrzut ekranu okna dialogowego Akceptacja licencji](media/sdk/qs-csharp-uwp-06-nuget-license.png "Akceptacja licencji")

1. W konsoli Menedżera pakietów zostaje wyświetlona linia z następującymi danymi wyjściowymi.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.1' to helloworld
   ```

1. Ze względu na to, że aplikacja używa mikrofonu do danych wejściowych mowy, dodaj do projektu funkcję **Mikrofon**. W Eksploratorze rozwiązań kliknij dwukrotnie opcję **Package.appxmanifest**, aby edytować manifest aplikacji. Następnie przejdź na kartę **Capabilities** (Funkcje) i zaznacz pole obok funkcji **Mikrofon**, a następnie zapisz wprowadzone zmiany.

   ![Zrzut ekranu manifestu aplikacji programu Visual Studio z wyróżnionymi opcjami Capabilities (Funkcje) i Mikrofon](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Interfejs użytkownika aplikacji jest definiowany przy użyciu języka XAML. Otwórz `MainPage.xaml` w Eksploratorze rozwiązań. W widoku języka XAML dla projektanta wstaw poniższy fragment kodu XAML do tagu siatki (między `<Grid>` i `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otwórz plik źródłowy code-behind `MainPage.xaml.cs` (znajdź go w grupie w obszarze `MainPage.xaml`). Zastąp cały zawarty w nim kod poniższym kodem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W procedurze obsługi `SpeechRecognitionFromMicrophone_ButtonClicked` w tym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. W procedurze obsługi `SpeechRecognitionFromMicrophone_ButtonClicked` zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz wszystkie zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-uwp-08-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-uwp-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno podręczne. Wybierz opcję **Włącz mikrofon** i potwierdź żądanie uprawnień, które zostanie wyświetlone.

    ![Zrzut ekranu żądania uprawnień](media/sdk/qs-csharp-uwp-10-access-prompt.png "Uruchom aplikację do debugowania")

1. Wybierz opcję **Speech recognition with microphone input** (Rozpoznawanie mowy z użyciem danych wejściowych z mikrofonu) i wypowiedz zwrot lub zdanie w języku angielskim do mikrofonu urządzenia. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

    ![Zrzut ekranu interfejsu użytkownika rozpoznawania mowy](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w folderze `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Recognize intents from speech by using the Speech SDK for C#](how-to-recognize-intents-from-speech-csharp.md) (Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK dla języka C#)

## <a name="see-also"></a>Zobacz też

- [Translate speech with the Cognitive Services Speech SDK for C#](how-to-translate-speech-csharp.md) (Tłumaczenie mowy za pomocą zestawu Speech SDK usługi Cognitive Services dla języka C#)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
