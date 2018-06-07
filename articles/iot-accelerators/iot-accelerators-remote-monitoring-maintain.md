---
title: Rozwiązywanie problemów z urządzeń w rozwiązaniu monitorowania zdalnego - Azure | Dokumentacja firmy Microsoft
description: Ten samouczek pokazuje, jak rozwiązywanie problemów i Korygowanie problemów z urządzeniami w zdalnym rozwiązanie monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 9a620d91238393ba0bde89f521f790b58ab35baf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628076"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Rozwiązywanie problemów i Korygowanie problemów z urządzeniami

Ten samouczek przedstawia sposób użycia **konserwacji** strony w rozwiązaniu Rozwiązywanie problemów i Korygowanie problemów z urządzeniami. Aby dodać tych funkcji, samouczku scenariusza aplikacji Contoso IoT.

Firma Contoso jest testowania nowego **prototypu** urządzenie w polu. Jako operatora firmy Contoso, zauważysz podczas testowania, który **prototypu** urządzenia jest nieoczekiwanie wyzwalania alertu temperatury, na pulpicie nawigacyjnym. Musi teraz Zbadaj zachowanie tym błędny **prototypu** urządzenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Użyj **konserwacji** strony w celu zbadania alertu
> * Wywołanie metody urządzenia skorygowanie problemu

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, należy wdrożone wystąpienie rozwiązanie monitorowania zdalnego w ramach subskrypcji platformy Azure.

Jeśli jeszcze tego nie wdrożono rozwiązanie monitorowania zdalnego jeszcze, należy wykonać [wdrażanie akcelerator rozwiązań monitorowania zdalnego](iot-accelerators-remote-monitoring-deploy.md) samouczka.

## <a name="use-the-maintenance-dashboard"></a>Pulpit nawigacyjny obsługi

Na **pulpitu nawigacyjnego** strony można zauważyć, że ma nieoczekiwany temperatury alertów pochodzących z regułą skojarzoną z **prototypu** urządzeń:

![Alerty wyświetlane na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

Aby zbadać problem, wybierz **Eksploruj Alert** opcję obok alertu:

![Eksploruj alert z poziomu pulpitu nawigacyjnego](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

Przedstawia widok szczegółów alertu:

* Kiedy alert został uruchomiony
* Informacje o stanie o urządzeniach skojarzonych z tym alertem
* Dane telemetryczne z urządzeń skojarzonych z tym alertem

![Szczegóły alertu](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

Aby potwierdzić alertu, wybierz **alertów wystąpień** i wybierz polecenie **potwierdzenia**. Ta akcja umożliwia innych operatorom alertu jak już wspomniano, a następnie pracy.

![Potwierdzić alerty](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

Po potwierdzeniu alertu, stan wystąpienia zmienia się na **Acknowledged**.

Na liście widać **prototypu** odpowiedzialny za wyzwalania alertu temperatury urządzenia:

![Utwórz listę urządzeń, co powoduje alertu](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Rozwiązania problemu

Do rozwiązania problemu z **prototypu** urządzenia, należy wywołać **DecreaseTemperature** metody na urządzeniu.

Do działania na urządzeniu, wybierz go na liście urządzeń, a następnie wybierz pozycję **zadania**. **Prototypu** model urządzenia określa metody musi obsługiwać urządzenia:

![Wyświetl metody, które obsługuje urządzenia](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

Wybierz **DecreaseTemperature** i ustaw nazwę zadania **DecreaseTemperature**. Następnie wybierz pozycję **Zastosuj**:

![Utwórz zadanie, aby zmniejszyć temperatury](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

Aby śledzić stan zadania na **konserwacji** wybierz pozycję **zadania**. Użyj **zadania** Wyświetl, aby śledzić wszystkie zadania i wywołuje metodę w rozwiązaniu:

![Monitor zadania, aby zmniejszyć temperatury](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

Aby wyświetlić szczegóły określonego zadania lub wywołanie metody, wybierz go na liście w **zadania** widoku:

![Wyświetlanie szczegółów zadania](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Użyj **konserwacji** strony w celu zbadania alertu
> * Wywołanie metody urządzenia skorygowanie problemu

Po zapoznaniu się Rozwiązywanie problemów dotyczących urządzenia, sugerowane następnym krokiem jest Dowiedz się, jak [przetestowany z urządzeniami symulowane](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->