---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 5db99a9d500b05fa6886dce2f29087920f8a1790
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658510"
---
Aby utworzyć projekt programu Visual Studio dla rozwoju platformy systemu Windows (UWP), należy skonfigurować opcje rozwoju programu Visual Studio, utworzyć projekt, wybrać architekturę docelową, skonfigurować przechwytywanie dźwięku i zainstalować zestaw SDK mowy.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji tworzenia programu Visual Studio

Aby rozpocząć, upewnij się, że poprawnie skonfigurowane w programie Visual Studio dla rozwoju platformy uniwersalnej systemu Windows:

1. Otwórz program Visual Studio 2019, aby wyświetlić okno **Start.**

   ![Okno Start — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Wybierz **kontynuuj bez kodu,** aby przejść do środowiska IDE programu Visual Studio.

1. Na pasku menu programu Visual Studio wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje,** aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie.**

   ![Karta Obciążenia, okno dialogowe Modyfikowanie, Instalator programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na karcie **Obciążenia w** obszarze **Windows**znajdź obciążenie **deweloperów platformy uniwersalnej systemu Windows.** Jeśli pole wyboru obok tego obciążenia jest już zaznaczone, zamknij okno dialogowe **Modyfikowanie** i przejdź do kroku 6.

1. Zaznacz pole wyboru **Tworzenie uniwersalnej platformy systemu Windows,** wybierz pozycję **Modyfikuj**, a następnie w oknie dialogowym **Przed rozpoczęciem** wybierz pozycję **Kontynuuj,** aby zainstalować obciążenie programistyczne. Instalacja nowej funkcji może trochę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Tworzenie projektu i wybieranie architektury docelowej

Następnie utwórz projekt:

1. Na pasku menu programu Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu,** aby wyświetlić okno **Utwórz nowy projekt.**

   ![Tworzenie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Znajdź i wybierz **pustą aplikację (Universal Windows)**. Upewnij się, że wybierzesz wersję C# tego typu projektu (w przeciwieństwie do języka Visual Basic).

1. Wybierz **przycisk Dalej,** aby wyświetlić ekran **Konfigurowanie nowego projektu.**

   ![Konfigurowanie nowego projektu — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. W **nazwie**projektu `helloworld`wprowadź .

1. W **obszarze Lokalizacja**przejdź do folderu , aby zapisać projekt, w którym chcesz go zapisać.

1. Wybierz **pozycję Utwórz,** aby przejść do okna **Nowy projekt platformy uniwersalnej systemu Windows.**

   ![Nowe okno dialogowe Projektu platformy systemu Uniwersalnego systemu Windows — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. W **wersji minimalnej** (drugie pole rozwijane) wybierz **windows 10 Fall Creators Update (10.0; Kompilacja 16299),** co jest minimalnym wymaganiem dla SDK mowy.

1. W **wersji docelowej** (pierwsze pole listy rozwijanej) wybierz wartość identyczną lub nowszą niż wartość w **wersji minimalnej**.

1. Kliknij przycisk **OK**. Powrót do ide programu Visual Studio, z nowym projektem utworzony i widoczne w **eksploratorze rozwiązań** okienka.

   ![projekt helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Teraz wybierz architekturę platformy docelowej. Na pasku narzędzi programu Visual Studio znajdź pole listy rozwijanej **Platformy rozwiązań.** (Jeśli go nie widzisz, wybierz polecenie **Wyświetl** > **standard pasków** > narzędzi, aby wyświetlić pasek narzędzi zawierający platformy **rozwiązań).****Standard** Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu rozwijany. 64-bitowy system Windows może również uruchamiać aplikacje 32-bitowe, więc możesz wybrać **x86,** jeśli wolisz.

> [!NOTE]
> Zestaw Speech SDK obsługuje wyłącznie procesory zgodne z technologią Intel. Procesory ARM nie są obecnie obsługiwane.

### <a name="set-up-audio-capture"></a>Konfigurowanie przechwytywania dźwięku

Następnie pozwól projektowi na przechwytywanie danych wejściowych audio:

1. W **Eksploratorze rozwiązań**kliknij dwukrotnie **pozycję Package.appxmanifest,** aby otworzyć manifest aplikacji pakietu.

1. Wybierz kartę **Capabilities** (Możliwości).

   ![Karta Możliwości, manifest aplikacji pakiet — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Wybierz pole funkcji **Mikrofon.**

1. Na pasku menu wybierz **file** > **save package.appxmanifest,** aby zapisać zmiany.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Na koniec zainstaluj [pakiet NuGet zestawu SDK mowy](https://aka.ms/csspeech/nuget)i odwołaj się do zestawu SDK mowy w projekcie:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania,** aby przejść do okna **NuGet - Solution.**

1. Wybierz pozycję **Przeglądaj**.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. W **źródle pakietu**wybierz **nuget.org**.

1. W polu **Wyszukiwanie** `Microsoft.CognitiveServices.Speech`wprowadź , a następnie wybierz ten pakiet po wyświetlenie go w wynikach wyszukiwania.

   ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. W okienku stanu pakietu obok wyników wyszukiwania wybierz projekt **helloworld.**

1. Wybierz pozycję **Zainstaluj**.

1. W oknie **dialogowym Podgląd zmian** wybierz przycisk **OK**.

1. W oknie dialogowym **Akceptacja licencji** wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Rozpocznie się instalacja pakietu, a **Output** po zakończeniu instalacji w okienku `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`Wyjście zostanie wyświetlony komunikat podobny do następującego tekstu: .
