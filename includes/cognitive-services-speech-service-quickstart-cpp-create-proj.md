---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 1287eb5de1391f40c4409d5d10522d8979ae4207
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383847"
---
Aby utworzyć projekt programu Visual Studio dla środowiska deweloperskiego języka C++, należy skonfigurować opcje rozwoju programu Visual Studio, utworzyć projekt, wybrać architekturę docelową i zainstalować zestaw SDK mowy.

### <a name="set-up-visual-studio-development-options"></a>Konfigurowanie opcji tworzenia programu Visual Studio

Aby rozpocząć, upewnij się, że poprawnie skonfigurowane w programie Visual Studio dla środowiska dewelopera pulpitu W++:To start, make sure you'refigured correctly in Visual Studio for C++ desktop development:

1. Otwórz program Visual Studio 2019, aby wyświetlić okno **Start.**

   ![Okno Start — Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Wybierz **kontynuuj bez kodu,** aby przejść do środowiska IDE programu Visual Studio.

1. Na pasku menu programu Visual Studio wybierz pozycję **Narzędzia** > **Pobierz narzędzia i funkcje,** aby otworzyć Instalator programu Visual Studio i wyświetlić okno dialogowe **Modyfikowanie.**

   ![Karta Obciążenia, okno dialogowe Modyfikowanie, Instalator programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Na karcie **Obciążenia** w obszarze **Windows**znajdź programowanie pulpitu z obciążeniem **języka C++.** Jeśli pole wyboru obok tego obciążenia nie jest jeszcze zaznaczone, zaznacz je.

1. Na karcie **Poszczególne składniki** znajdź pole wyboru **Menedżer pakietów Nuget.** Jeśli pole wyboru nie jest jeszcze zaznaczone, zaznacz je.

1. Zaznacz przycisk w rogu oznaczony jako **Zamknij** lub **Modyfikuj**. (Nazwa przycisku różni się w zależności od tego, czy wybrano funkcje do instalacji). Jeśli wybierzesz **opcję Modyfikuj,** rozpocznie się instalacja, co może chwilę potrwać.

1. Zamknij Instalator programu Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Tworzenie projektu i wybieranie architektury docelowej

Następnie utwórz projekt:

1. Na pasku menu programu Visual Studio wybierz pozycję **Plik** > **nowego** > **projektu,** aby wyświetlić okno **Utwórz nowy projekt.**

   ![Tworzenie nowego projektu, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Znajdź i wybierz **aplikację konsoli**. Upewnij się, że wybierzesz wersję C++ tego typu projektu (w przeciwieństwie do języka C# lub Visual Basic).

1. Wybierz **przycisk Dalej,** aby wyświetlić ekran **Konfigurowanie nowego projektu.**

   ![Konfigurowanie nowego projektu, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. W **nazwie**projektu `helloworld`wprowadź .

1. W **obszarze Lokalizacja**przejdź do folderu , aby zapisać projekt, w którym chcesz go zapisać.

Teraz wybierz architekturę platformy docelowej. Na pasku narzędzi programu Visual Studio znajdź pole listy rozwijanej **Platformy rozwiązań.** (Jeśli go nie widzisz, wybierz polecenie **Wyświetl** > **standard pasków** > narzędzi, aby wyświetlić pasek narzędzi zawierający platformy **rozwiązań).****Standard** Jeśli korzystasz z 64-bitowego systemu Windows, wybierz **x64** w polu rozwijany. 64-bitowy system Windows może również uruchamiać aplikacje 32-bitowe, więc możesz wybrać **x86,** jeśli wolisz.

### <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Na koniec zainstaluj [pakiet NuGet zestawu SDK mowy](https://aka.ms/csspeech/nuget)i odwołaj się do zestawu SDK mowy w projekcie:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania,** aby przejść do okna **Nuget - Solution.**

1. Wybierz pozycję **Przeglądaj**.

   ![NuGet — karta Rozwiązanie, visual studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. W **źródle pakietu**wybierz **nuget.org**.

1. W polu **Wyszukiwanie** `Microsoft.CognitiveServices.Speech`wprowadź , a następnie wybierz ten pakiet po wyświetlenie go w wynikach wyszukiwania.

   ![Instalacja pakietu Microsoft.CognitiveServices.Speech C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. W okienku stanu pakietu obok wyników wyszukiwania wybierz projekt **helloworld.**

1. Wybierz pozycję **Zainstaluj**.

1. W oknie **dialogowym Podgląd zmian** wybierz przycisk **OK**.

1. W oknie dialogowym **Akceptacja licencji** wyświetl licencję, a następnie wybierz pozycję **Akceptuję**. Rozpocznie się instalacja pakietu, a **Output** po zakończeniu instalacji w okienku `Successfully installed 'Microsoft.CognitiveServices.Speech 1.10.0' to helloworld`Wyjście zostanie wyświetlony komunikat podobny do następującego tekstu: .
