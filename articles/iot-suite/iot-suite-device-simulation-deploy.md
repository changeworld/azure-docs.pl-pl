---
title: Wdrażanie rozwiązania symulacji urządzenia - Azure | Dokumentacja firmy Microsoft
description: Ten samouczek pokazuje, jak zainicjować rozwiązania symulacji urządzenia z azureiotsuite.com.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 52890c51b06777a2d74fcf143bee4e5e8919ba75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Wdrażanie rozwiązania symulacji urządzenia IoT Azure

Ten samouczek pokazuje, jak zainicjować rozwiązania symulacji urządzenia. Możesz wdrożyć rozwiązanie z azureiotsuite.com.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie rozwiązania symulacji urządzenia
> * Wdrażanie rozwiązania symulacji urządzenia
> * Zaloguj się do rozwiązania symulacji urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka jest potrzebna aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Przed przystąpieniem do wdrażania rozwiązania do subskrypcji platformy Azure, musisz wybrać niektóre opcje konfiguracji:

1. Zaloguj się do [azureiotsuite.com](https://www.azureiotsuite.com) poświadczenia konta przy użyciu platformy Azure i kliknij przycisk **+** do tworzenia nowego rozwiązania:

    ![Utwórz nowe rozwiązanie](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. Kliknij przycisk **wybierz** na **symulacji urządzenia** Kafelek:

    ![Wybierz symulacji urządzenia](media/iot-suite-device-simulation-deploy/select.png)

1. Na **rozwiązania utworzyć symulacji urządzenia** wprowadź **Nazwa rozwiązania** rozwiązania do symulacji urządzenia.

1. W polach **Subskrypcja** i **Region** wybierz wartości, których chcesz użyć do aprowizacji rozwiązania.

1. Określ, czy nowe Centrum IoT wdrożone z rozwiązania do symulacji urządzenia. Jeśli zaznaczysz to pole nowego centrum IoT jest wdrażany w ramach subskrypcji. Niezależnie od wyboru zawsze można wskazać symulacji w dowolnym Centrum IoT.

1. Kliknij pozycję **Utwórz rozwiązanie**, aby rozpocząć proces aprowizowania. Ten proces zwykle trwa kilka minut do uruchomienia:

    ![Szczegóły rozwiązania symulacji urządzenia](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Zaloguj się do rozwiązania

Po zakończeniu procesu inicjowania obsługi administracyjnej można logowania się do rozwiązania do symulacji urządzenia.

1. Na **elastycznie rozwiązań** kliknij przycisk **uruchamianie** w obszarze nowe rozwiązania symulacji urządzenia:

    ![Wybierz nowe rozwiązanie](media/iot-suite-device-simulation-deploy/newsolution.png)

1. Można wyświetlić informacje dotyczące rozwiązania symulacji urządzenia w panelu, który jest wyświetlany. Wybierz **pulpit nawigacyjny rozwiązania** nawiązać połączenia z rozwiązania do symulacji urządzenia.

    > [!NOTE]
    > Po zakończeniu pracy z nim, możesz usunąć rozwiązania do symulacji urządzenia z tego panelu.

    ![Panel rozwiązania](media/iot-suite-device-simulation-deploy/properties.png)

1. Pulpit nawigacyjny rozwiązania symulacji urządzenia wyświetla w przeglądarce.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie rozwiązania
> * Wdrażanie rozwiązania
> * Zaloguj się do rozwiązania

Teraz, gdy wdrożono rozwiązanie symulacji urządzenia, następnym krokiem jest [Poznaj możliwości rozwiązania symulacji urządzenia](./iot-suite-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
