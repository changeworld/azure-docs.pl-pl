---
title: 'Szybki Start: Rozpoznawanie mowy przy użyciu kognitywnych usług mowy C# zestawu SDK dla systemu Windows | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak rozpoznawanie mowy przy użyciu zestawu SDK języka C# dla usługi mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 9f37547b4b183594410b4e16b872645407ff4a6f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356251"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Szybki Start: Rozpoznawanie mowy przy użyciu kognitywnych mowy usługi C# SDK

W tym artykule dowiesz się tworzenie aplikacji konsolowej C# w systemie Windows przy użyciu zestawu SDK kognitywnych mowy usługi wykonać transkrypcji mowy na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [spróbuj bezpłatnie usługę mowy](get-started.md).
* Visual Studio 2017, Community Edition lub nowszy.
* **Tworzenia klasycznych aplikacji .NET** obciążenia w programie Visual Studio. Możesz je włączyć w **narzędzia** \> **Pobierz narzędzia i funkcje**. 

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio 2017 r Utwórz nową Visual C# Console aplikację. W **nowy projekt** okno dialogowe, w lewym okienku rozwiń **zainstalowana** , a następnie wybierz **aplikacji konsoli (.NET Framework)**. Wprowadź nazwę projektu *CsharpHelloSpeech*.

    ![Tworzenie Visual C# Console aplikacji (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Utwórz aplikację konsoli języka Visual C#")

2. Zainstaluj i odwołują się do pakietu NuGet SDK mowy. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Kliknij prawym przyciskiem myszy Zarządzaj pakietami NuGet dla rozwiązania](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "Zarządzaj pakietami NuGet dla rozwiązania")

3. W prawym górnym rogu w **źródła pakietu** pól, zaznacz **Nuget.org**. Wyszukiwanie i instalowanie `Microsoft.CognitiveServices.Speech` pakietu, a następnie zainstaluj go w **CsharpHelloSpeech** projektu.

    ![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-08-vs-cs-nuget-install.png "pakietu instalowania Nuget")

4. Na ekranie licencji, które pojawia się Zaakceptuj licencji:

    ![Akceptuje](media/sdk/speechsdk-09-vs-cs-nuget-license.png "akceptuje")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Tworzenie konfiguracji platformy dopasowania architektury komputera

W tej sekcji możesz dodać nowe platformy konfiguracji, który pasuje do architektury procesora.

1. Uruchom Menedżera konfiguracji. Wybierz **kompilacji** > **programu Configuration Manager**.

    ![Uruchom Menedżera konfiguracji](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "Uruchom Menedżera konfiguracji")

2. W **programu Configuration Manager** okno dialogowe Dodaj nowej platformie. Z **platformy aktywne rozwiązanie** listy rozwijanej wybierz **nowy**.

    ![Dodaj nowe platformy w oknie Menedżera konfiguracji](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "Dodaj nowe platformy w obszarze okno programu configuration manager")

3. Jeśli używasz 64-bitowego systemu Windows, Utwórz nową konfigurację platformy o nazwie `x64`. Jeśli używasz 32-bitowego systemu Windows, Utwórz nową konfigurację platformy o nazwie `x86`. W tym artykule, należy utworzyć `x64` konfiguracji platformy. 

    ![W 64-bitowego systemu Windows, należy dodać nowa platforma o nazwie "x64"](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Dodaj x64 platformy")

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. W `Program.cs` projektu programu Visual Studio, Zastąp treści `Program` klasy następującym kodem. Upewnij się, że możesz zastąpić klucz subskrypcji i regionu, który został uzyskany dla usługi.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. Po wklejeniu kodu, `Main()` metody musi wyglądać jak pokazano na poniższym zrzucie ekranu:

    ![Metoda główna po wklejeniu kod](media/sdk/speechsdk-17-vs-cs-paste-code.png "końcowego Main — metoda")

3. IntelliSense programu Visual Studio prezentuje odwołań do klas SDK mowy, których nie można rozpoznać. Aby naprawić ten błąd, Dodaj następujący `using` instrukcji na początku kodu (albo ręcznie lub za pomocą programu Visual Studio [szybkie akcje](https://docs.microsoft.com/visualstudio/ide/quick-actions)).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Użyj szybkiego akcji, aby dodać brakujący za pomocą instrukcji](media/sdk/speechsdk-18-vs-cs-add-using.png "IntelliSense Rozwiązywanie problemów")

4. Upewnij się, że wyróżnianie IntelliSense został rozwiązany i Zapisz zmiany do projektu.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Tworzenie aplikacji. Na pasku menu wybierz **kompilacji** > **Kompiluj rozwiązanie**. Kod powinien teraz skompilować bez błędów:

    ![Kompilacje pomyślne](media/sdk/speechsdk-20-vs-cs-build.png "pomyślnego utworzenia kompilacji")

2. Uruchom aplikację. Na pasku menu wybierz **debugowania** > **Rozpocznij debugowanie**, lub naciśnij klawisz **F5**. 

    ![Uruchom aplikację do debugowania](media/sdk/speechsdk-21-vs-cs-f5.png "uruchomić aplikację do debugowania")

3. Okno konsoli wyskakującej, monitując powiedzieć coś (w języku angielskim).
Wynik rozpoznawania jest wyświetlany na ekranie.

    ![Dane wyjściowe konsoli po pomyślnym rozpoznawania](media/sdk/speechsdk-22-cs-vs-console-output.png "dane wyjściowe konsoli po pomyślnym rozpoznawania")

## <a name="download-code"></a>Pobierz kod

Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK kognitywnych usług mowy](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Kolejne kroki

- [Tłumaczenie mowy](how-to-translate-speech.md)
- [Dostosowywanie mowy modeli](how-to-customize-speech-models.md)
