---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 55920b6c147626f68f51b9e0479949330c71a748
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "38756430"
---
> [!div class="op_single_selector"]
> * [C w systemie Windows](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [C w systemie Linux](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Omówienie scenariusza
W tym scenariuszu utworzysz urządzenie, które wysyła następujące dane telemetryczne do [wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][lnk-what-are-preconfig-solutions]:

* Temperatura zewnętrzna
* Temperatura wewnętrzna
* Wilgotność

Dla uproszczenia kod na urządzeniu generuje przykładowe wartości, ale zachęcamy do rozszerzenia przykładu przez podłączenie rzeczywistych czujników do urządzenia i wysyłanie rzeczywistych danych telemetrycznych.

Urządzenie potrafi także odpowiadać na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania i wartości żądanych właściwości ustawione na pulpicie nawigacyjnym rozwiązania.

Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk-free-trial].

## <a name="before-you-start"></a>Przed rozpoczęciem
Przed przystąpieniem do pisania jakiegokolwiek kodu dla urządzenia musisz przeprowadzić aprowizację wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego i nowego niestandardowego urządzenia w ramach tego rozwiązania.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprowizowanie wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego
Urządzenie, które utworzysz w ramach tego samouczka, będzie wysyłać dane do wystąpienia wstępnie skonfigurowanego rozwiązania do [monitorowania zdalnego][lnk-remote-monitoring]. Jeśli jeszcze nie przeprowadzono aprowizacji wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego na Twoim koncie platformy Azure, wykonaj poniższe kroki:

1. Na <https://www.azureiotsolutions.com/> kliknij **+** do tworzenia rozwiązań.
2. Kliknij pozycję **Wybierz** w panelu **Monitorowanie zdalne**, aby utworzyć rozwiązanie.
3. Na stronie **Tworzenie rozwiązania do monitorowania zdalnego** w polu **Nazwa rozwiązania** wprowadź wybraną nazwę rozwiązania, w polu **Region** wybierz region, w którym chcesz przeprowadzić wdrożenie, a następnie wybierz subskrypcję platformy Azure, której chcesz użyć. Następnie kliknij pozycję **Utwórz rozwiązanie**.
4. Zaczekaj na ukończenie procesu aprowizowania.

> [!WARNING]
> Wstępnie skonfigurowane rozwiązania korzystają z płatnych usług platformy Azure. Pamiętaj o usunięciu wstępnie skonfigurowanego rozwiązania ze swojej subskrypcji po zakończeniu pracy z nim, aby uniknąć wszelkich zbędnych opłat. Można całkowicie usunąć wstępnie skonfigurowane rozwiązanie z subskrypcji, odwiedzając <https://www.azureiotsolutions.com/> strony.
> 
> 

Po zakończeniu procesu aprowizowania rozwiązania do monitorowania zdalnego kliknij pozycję **Uruchom**, aby otworzyć pulpit nawigacyjny rozwiązania w przeglądarce.

![Pulpit nawigacyjny rozwiązania][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Aprowizowanie urządzenia w ramach rozwiązania do monitorowania zdalnego
> [!NOTE]
> Jeśli już przeprowadzono aprowizację urządzenia w ramach rozwiązania, możesz pominąć ten krok. Podczas tworzenia aplikacji klienckiej musisz znać poświadczenia urządzenia.
> 
> 

Aby urządzenie mogło nawiązać połączenie ze wstępnie skonfigurowanym rozwiązaniem, musi ono zidentyfikować się względem usługi IoT Hub za pomocą prawidłowych poświadczeń. Poświadczenia urządzenia możesz pobrać z pulpitu nawigacyjnego rozwiązania. W dalszej części tego samouczka podasz te poświadczenia urządzenia w Twojej aplikacji klienckiej.

Aby dodać urządzenie do swojego rozwiązania do monitorowania zdalnego, wykonaj poniższe kroki na pulpicie nawigacyjnym rozwiązania:

1. W lewym dolnym rogu pulpitu nawigacyjnego kliknij pozycję **Dodaj urządzenie**.
   
   ![Dodawanie urządzenia][1]
2. W panelu **Urządzenie niestandardowe** kliknij pozycję **Dodaj nowe**.
   
   ![Dodawanie urządzenia niestandardowego][2]
3. Wybierz pozycję **Pozwól mi zdefiniować własny identyfikator urządzenia**. Wprowadź identyfikator urządzenia, na przykład **moje_urządzenie**, kliknij pozycję **Sprawdź identyfikator**, aby sprawdzić, czy dana nazwa nie jest już używana, a następnie kliknij pozycję **Utwórz**, aby przeprowadzić aprowizację urządzenia.
   
   ![Dodawanie identyfikatora urządzenia][3]
4. Zanotuj poświadczenia urządzenia (identyfikator urządzenia, nazwę hosta usługi IoT Hub i klucz urządzenia). Twoja aplikacja kliencka potrzebuje tych wartości, aby mogła nawiązać połączenie z rozwiązaniem do monitorowania zdalnego. Następnie kliknij przycisk **Gotowe**.
   
    ![Wyświetlanie poświadczeń urządzenia][4]
5. Wybierz urządzenie na liście urządzeń na pulpicie nawigacyjnym rozwiązania. Następnie w panelu **Szczegóły urządzenia** kliknij pozycję **Włącz urządzenie**. Stan Twojego urządzenia to teraz **Uruchomione**. Rozwiązanie do monitorowania zdalnego może teraz odbierać dane telemetryczne z Twojego urządzenia i wywoływać metody na tym urządzeniu.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/