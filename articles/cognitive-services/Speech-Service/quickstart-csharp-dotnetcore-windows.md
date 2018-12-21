---
title: 'Szybki start: rozpoznawanie mowy, .NET Core (Windows) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku C# na platformie .NET Core w systemie Windows za pomocą zestawu SDK usługi Mowa
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: e82e39eb3fc6c7ebaf4798ad10038bfd2fa9a41b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085488"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku C# na platformie .NET Core w systemie Windows za pomocą zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację konsolową w języku C# dla platformy .NET Core w systemie Windows za pomocą zestawu [Speech SDK](speech-sdk.md) usługi Cognitive Services. Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym z mikrofonu komputera. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

> [!NOTE]
> .NET Core jest międzyplatformową platformą .NET typu open source, która wdraża specyfikację [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Do ukończenia kroków tego przewodnika Szybki start potrzebujesz klucza subskrypcji usługi rozpoznawania mowy. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje, zobacz temat [Try the Speech service for free](get-started.md) (Wypróbuj bezpłatnie usługę rozpoznawania mowy).


## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Uruchom program Visual Studio 2017.

1. Upewnij się, że jest dostępny pakiet roboczy do **tworzenia aplikacji na wiele platform dla platformy .NET**. Wybierz kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje** na pasku menu programu Visual Studio, aby otworzyć instalator programu Visual Studio. Jeśli ten pakiet roboczy jest już włączony, zamknij okno dialogowe.

    ![Zrzut ekranu Instalatora programu Visual Studio z wyróżnioną kartą Pakiety robocze](media/sdk/vs-enable-net-core-workload.png)

    W przeciwnym razie zaznacz pole obok pozycji **Tworzenie aplikacji na wiele platform dla platformy .NET Core** i wybierz opcję **Modyfikuj** w prawym dolnym rogu okna dialogowego. Instalowanie nowej funkcji chwilę potrwa.

1. Utwórz nową aplikację konsolową w języku Visual C# dla platformy .NET Core. W oknie dialogowym **Nowy projekt** w lewym okienku rozwiń pozycje **Zainstalowane** > **Visual C#** > **.NET Core**. Następnie wybierz pozycję **Aplikacja konsolowa (.NET Core)**. Jako nazwę projektu podaj *helloworld*.

    ![Zrzut ekranu okna dialogowego Nowy projekt](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Tworzenie aplikacji konsolowej w języku Visual C# (dla platformy .NET Core)")

1. Instalowanie [zestawu Speech SDK pakietu NuGet](https://aka.ms/csspeech/nuget) i odwoływanie się do niego. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Zrzut ekranu Eksploratora rozwiązań z wyróżnioną opcją Zarządzaj pakietami NuGet rozwiązania](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Zarządzaj pakietami NuGet rozwiązania")

1. W prawym górnym rogu wybierz w polu **Źródło pakietu** wartość **nuget.org**. Wyszukaj pakiet `Microsoft.CognitiveServices.Speech` i zainstaluj go w projekcie **helloworld**.

    ![Zrzut ekranu okna dialogowego Zarządzaj pakietami rozwiązania](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instaluj pakiet NuGet")

1. Zaakceptuj wyświetloną licencję, aby rozpocząć instalowanie pakietu NuGet.

    ![Zrzut ekranu okna dialogowego Akceptacja licencji](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Akceptacja licencji")

Po zainstalowaniu pakietu w konsoli Menedżera pakietów zostanie wyświetlone potwierdzenie.


## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik `Program.cs` i zastąp cały jego kod poniższym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z monitem o wypowiedzenie słów. Wypowiedz zwrot lub zdanie w języku angielskim. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym samym oknie.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w folderze `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Recognize intents from speech by using the Speech SDK for C#](how-to-recognize-intents-from-speech-csharp.md) (Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK dla języka C#)

## <a name="see-also"></a>Zobacz też

- [Translate speech with the Cognitive Services Speech SDK for C#](how-to-translate-speech-csharp.md) (Tłumaczenie mowy za pomocą zestawu Speech SDK usługi Cognitive Services dla języka C#)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
