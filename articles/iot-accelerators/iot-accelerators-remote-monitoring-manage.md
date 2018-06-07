---
title: Zarządzanie urządzeniami w zdalnym rozwiązanie monitorowania - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób zarządzania urządzenia podłączone do zdalnego rozwiązanie monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 0f177c3a8746f801e52cdac6cb2189e9cc28e1e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627283"
---
# <a name="manage-and-configure-your-devices"></a>Konfigurowanie urządzeń oraz zarządzanie nimi

Ten samouczek Pokazuje urządzenie możliwości zarządzania, monitorowania zdalnego rozwiązania. Aby dodać tych funkcji, samouczku scenariusza aplikacji Contoso IoT.

Firma Contoso ma uporządkowane nowe maszyny do rozwiń jedno ze swoich urządzeń do zwiększenia danych wyjściowych. Podczas oczekiwania dla nowych maszyn mają być dostarczane chcesz uruchomić symulacji, aby sprawdzić zachowanie rozwiązania. Jako operator chcesz zarządzania i skonfigurować urządzenia w rozwiązaniu monitorowania zdalnego.

Oferuje rozszerzalny sposób zarządzania i konfigurowanie urządzeń, to rozwiązanie monitorowanie zdalnego korzysta z Centrum IoT funkcji, takich jak [zadania](../iot-hub/iot-hub-devguide-jobs.md) i [bezpośrednie metody](../iot-hub/iot-hub-devguide-direct-methods.md). Aby dowiedzieć się, jak implementuje metody dewelopera urządzenia na urządzeniu fizycznym, zobacz [dostosować akcelerator rozwiązań monitorowania zdalnego](iot-accelerators-remote-monitoring-customize.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Zapewnienie symulowane urządzenie.
> * Przetestuj symulowane urządzenie.
> * Wywołanie metody urządzenia z rozwiązania.
> * Zmień konfigurację urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, należy wdrożone wystąpienie rozwiązanie monitorowania zdalnego w ramach subskrypcji platformy Azure.

Jeśli jeszcze tego nie wdrożono rozwiązanie monitorowania zdalnego jeszcze, należy wykonać [wdrażanie akcelerator rozwiązań monitorowania zdalnego](iot-accelerators-remote-monitoring-deploy.md) samouczka.

## <a name="add-a-simulated-device"></a>Dodaj symulowane urządzenie

Przejdź do **urządzeń** strony w rozwiązaniu, a następnie wybierz pozycję **+ nowe urządzenie**. W **nowe urządzenie** panelu, wybierz polecenie **symulowane**:

![Zainicjuj obsługę symulowane urządzenie](./media/iot-accelerators-remote-monitoring-manage/devicesprovision.png)

Pozostaw liczbę urządzeń do świadczenia ustawioną **1**. Wybierz **błędny aparat** urządzenia modelu, a następnie wybierz pozycję **Zastosuj** Aby utworzyć symulowane urządzenie:

![Zainicjuj obsługę aparatu symulowane urządzenie](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine.png)

Aby dowiedzieć się, jak udostępnić *fizycznych* urządzenia, zobacz [Podłącz urządzenie do akcelerator rozwiązań monitorowania zdalnego](iot-accelerators-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Testowanie symulowane urządzenie

Aby wyświetlić szczegóły nowego symulowane urządzenie, wybierz go z listy urządzeń na **urządzeń** strony. Wyświetla informacje o urządzeniu **szczegółów urządzenia** panelu:

![Nowe urządzenie symulowane aparat widoku](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew.png)

W **szczegółów urządzenia**, Sprawdź nowe urządzenie wysyła dane telemetryczne. Aby wyświetlić strumieni różne dane telemetryczne z urządzenia, wybierz nazwę telemetrii takich jak **wibrację**:

![Wybierz strumień telemetrii, aby wyświetlić](./media/iot-accelerators-remote-monitoring-manage/devicesvibration.png)

**Szczegółów urządzenia** panelu zawiera inne informacje o urządzeniu, takie jak wartości tagów, obsługuje metod i właściwości zgłoszonych przez urządzenia.

Aby wyświetlić szczegółowe diagnostyki, przewiń w dół do widoku **diagnostyki**.

## <a name="act-on-a-device"></a>Działania na urządzeniu

Do działania na co najmniej jedno urządzenie, wybierz je z listy urządzeń, a następnie wybierz pozycję **zadania**. **Aparat** model urządzenia określa trzy metody musi obsługiwać urządzenia:

![Metody aparatu](./media/iot-accelerators-remote-monitoring-manage/devicesmethods.png)

Wybierz **FillTank**, ustaw nazwę zadania **FillEngineTank**, a następnie wybierz pozycję **Zastosuj**:

![Zaplanuj metodę ponownego uruchomienia](./media/iot-accelerators-remote-monitoring-manage/devicesrestartengine.png)

Aby śledzić stan zadania na **konserwacji** wybierz pozycję **zadania**:

![Monitor zadania harmonogramów](./media/iot-accelerators-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Metody w innych urządzeń

Poznaj typy różnych symulowane urządzenie, zobacz, czy inne typy urządzeń obsługuje różne metody. W przypadku wdrożenia z urządzeń fizycznych model urządzenia określa metody, które powinny obsługiwać urządzenia. Zazwyczaj developer urządzenia jest odpowiedzialny za tworzenie kodu, który sprawia, że urządzenie działa w odpowiedzi na wywołanie metody.

Aby zaplanować metody do uruchamiania na wielu urządzeniach, można wybrać wiele urządzeń na liście na **urządzeń** strony. **Zadania** panel zawiera typy metody wspólne dla wszystkich wybranych urządzeń.

## <a name="reconfigure-a-device"></a>Ponownie skonfiguruj urządzenie

Aby zmienić konfigurację urządzenia, wybierz ją na liście urządzeń **urządzeń** strony, a następnie wybierz pozycję **zadania**, a następnie wybierz pozycję **ponownie skonfigurować**. Panelu zadań wyświetlane dla wybranego urządzenia, które można zmienić wartości właściwości:

![Ponownie skonfiguruj urządzenie](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure.png)

Aby wprowadzić zmiany, Dodaj nazwę zadania, zaktualizować wartości właściwości i wybierz **Zastosuj**:

![Zaktualizuj wartość właściwości urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical.png)

Aby śledzić stan zadania na **konserwacji** wybierz pozycję **zadania**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono należy jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Zapewnienie symulowane urządzenie.
> * Przetestuj symulowane urządzenie.
> * Wywołanie metody urządzenia z rozwiązania.
> * Zmień konfigurację urządzenia.

Teraz, kiedy znasz sposobu zarządzania urządzeniami, Sugerowane następne kroki są Aby dowiedzieć się, jak:

* [Rozwiązywanie problemów i Korygowanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md).
* [Testowanie rozwiązania z urządzeniami symulowane](iot-accelerators-remote-monitoring-test.md).
* [Podłącz urządzenie do akcelerator rozwiązań monitorowania zdalnego](iot-accelerators-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->