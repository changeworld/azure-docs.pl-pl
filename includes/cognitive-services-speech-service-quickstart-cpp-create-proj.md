---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: b5a24d83faef5a895317f162178f8bd588a1466d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620150"
---
1. Uruchom program Visual Studio 2017.

1. Upewnij się, że jest dostępny pakiet roboczy **Programowanie aplikacji klasycznych w języku C++**. Wybierz kolejno pozycje **Narzędzia** > **Pobierz narzędzia i funkcje** na pasku menu programu Visual Studio, aby otworzyć instalator programu Visual Studio. Jeśli ten pakiet roboczy jest już włączony, pomiń ten krok i przejdź do kolejnego.

    ![Zrzut ekranu karty Pakiety robocze programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    W przeciwnym razie zaznacz pole wyboru obok pozycji **Programowanie aplikacji klasycznych w języku C++**.

1. Upewnij się, że jest dostępny składnik **Menedżer pakietów NuGet**. Przejdź do karty **Poszczególne składniki** w oknie dialogowym instalatora programu Visual Studio i wybierz pozycję **Menedżer pakietów NuGet**, jeśli nie została jeszcze wybrana.

      ![Zrzut ekranu karty Poszczególne składniki programu Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Jeśli musisz włączyć pakiet roboczy języka C++ lub program NuGet, wybierz pozycję **Modyfikuj** (w prawym dolnym rogu okna dialogowego). Instalowanie nowej funkcji chwilę potrwa. Jeśli obie funkcje zostały już włączone, należy zamknąć okno dialogowe.

1. Utwórz nową aplikację konsolową dla systemu Windows Desktop, korzystając z zestawu narzędzi Visual C++. Najpierw wybierz w menu pozycje **Plik** > **Nowy** > **Projekt**. W oknie dialogowym **Nowy projekt** w lewym okienku rozwiń pozycje **Zainstalowane** > **Visual C++** > **Windows Desktop**. Następnie wybierz pozycję **Aplikacja konsolowa systemu Windows**. Jako nazwę projektu podaj *helloworld*.

    ![Zrzut ekranu przedstawiający okno dialogowe nowego projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Jeśli korzystasz z 64-bitowego systemu Windows, możesz przełączyć platformę kompilacji na `x64` za pomocą menu rozwijanego na pasku narzędzi programu Visual Studio. (64-bitowe wersje systemu Windows mogą obsługiwać aplikacje 32-bitowe, więc nie jest to wymagane.)

    ![Zrzut ekranu paska narzędzi programu Visual Studio z wyróżnioną opcją x64](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Zrzut ekranu Eksploratora rozwiązań z wyróżnioną opcją Zarządzaj pakietami NuGet rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. W prawym górnym rogu wybierz w polu **Źródło pakietu** wartość **nuget.org**. Wyszukaj pakiet `Microsoft.CognitiveServices.Speech` i zainstaluj go w projekcie **helloworld**.

    ![Zrzut ekranu okna dialogowego Zarządzaj pakietami dla rozwiązania](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.4.0`.

1. Zaakceptuj wyświetloną licencję, aby rozpocząć instalowanie pakietu NuGet.

    ![Zrzut ekranu okna dialogowego Akceptacja licencji](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Po zainstalowaniu pakietu w konsoli Menedżera pakietów zostanie wyświetlone potwierdzenie.
