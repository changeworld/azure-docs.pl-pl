---
title: Wdrażanie zdalne rozwiązanie monitorowania - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób udostępnienia zdalnego monitorowania akcelerator rozwiązań z azureiotsuite.com.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 30d0b0b0250563cf09a50e8165ad9fb1047fd570
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Wdrażanie zdalne akcelerator rozwiązań monitorowania

W tym samouczku przedstawiono sposób udostępnienia zdalnego monitorowania akcelerator rozwiązań. Możesz wdrożyć rozwiązanie z azureiotsuite.com. Można także wdrożyć rozwiązanie przy użyciu interfejsu wiersza polecenia, aby dowiedzieć się więcej o tej opcji, zobacz [wdrażanie akcelerator rozwiązań, w wierszu polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj akcelerator rozwiązań
> * Wdrażanie akcelerator rozwiązań
> * Zaloguj się do akcelerator rozwiązań

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka jest potrzebna aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Wdrażanie akcelerator rozwiązań

Przed wdrożeniem akcelerator rozwiązań do subskrypcji platformy Azure, musisz wybrać niektóre opcje konfiguracji:

1. Zaloguj się do [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) przy użyciu poświadczeń konta platformy Azure.

1. Kliknij przycisk **Spróbuj teraz** na **monitorowania zdalnego** kafelka.

    ![Wybierz monitorowania zdalnego](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. Na **utworzyć monitorowania zdalnego rozwiązania** wprowadź **Nazwa rozwiązania** dla zdalnego monitorowania akcelerator rozwiązań.

1. Wybierz **podstawowe** lub **standardowe** wdrożenia. Jeśli wdrażasz Dowiedz się, jak to działa rozwiązania lub uruchomić pokaz, wybierz **podstawowe** opcję, aby zminimalizować koszty.

1. Wybierz **Java** lub **.NET** jako język. Wszystkie mikrousług są dostępne jako implementacje Java lub .NET.

1. Przegląd **szczegóły rozwiązania** panelu, aby uzyskać więcej informacji o dostępnych opcjach konfiguracji.

1. W polach **Subskrypcja** i **Region** wybierz wartości, których chcesz użyć do aprowizacji rozwiązania.

1. Kliknij pozycję **Utwórz rozwiązanie**, aby rozpocząć proces aprowizowania. Ten proces zwykle trwa kilka minut do uruchomienia:

    ![Szczegóły rozwiązania monitorowanie zdalnego](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz [co robić, jeśli wdrożenie nie powiedzie się](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) w repozytorium GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Zaloguj się do akcelerator rozwiązań

Po zakończeniu procesu inicjowania obsługi administracyjnej można logowania się do zdalnego monitorowania akcelerator rozwiązań.

1. Na **elastycznie rozwiązań** wybierz nowe zdalnego rozwiązanie monitorowania:

    ![Wybierz nowe rozwiązanie](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. Można wyświetlić informacje dotyczące rozwiązania monitorowania zdalnego w panelu, który pojawi się. Wybierz **pulpit nawigacyjny rozwiązania** do nawiązania połączenia zdalnego rozwiązanie monitorowania.

    > [!NOTE]
    > Rozwiązanie monitorowania zdalnego można usunąć z tego panelu po zakończeniu pracy z nim.

    ![Panel rozwiązania](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. Zdalny pulpit nawigacyjny monitorowania rozwiązania wyświetla w przeglądarce.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj akcelerator rozwiązań
> * Wdrażanie akcelerator rozwiązań
> * Zaloguj się do akcelerator rozwiązań

Teraz, gdy wdrożono rozwiązanie monitorowania zdalnego następnym krokiem jest [Poznaj możliwości pulpit nawigacyjny rozwiązania](./../iot-suite/iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->