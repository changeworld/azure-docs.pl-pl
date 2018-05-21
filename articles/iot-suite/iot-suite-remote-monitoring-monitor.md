---
title: Zaawansowane monitorowanie zdalnego rozwiązanie monitorowania - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób monitorowania urządzeń ze zdalnego pulpitu nawigacyjnego monitorowania rozwiązania.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e6bf1962115c4ee9212ae73eb98f664efa3b6a2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Wykonaj zaawansowanego monitorowania przy użyciu zdalnego rozwiązanie monitorowania

Ten samouczek pokazuje możliwości zdalnego pulpitu nawigacyjnego monitorowania. Aby dodać tych funkcji, samouczku scenariusza aplikacji Contoso IoT.

W tym samouczku dwa symulowanego urządzenia ciężarówka Contoso służy do Dowiedz się, jak monitorować urządzenia z poziomu pulpitu nawigacyjnego akceleratora rozwiązań. Jako operatora firmy Contoso należy monitorować lokalizacji i zachowania użytkownika pojazdów w polu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Widok telemetrii w czasie rzeczywistym
> * Wyświetl szczegóły urządzenia
> * Wyświetl alerty z urządzeń
> * Wyświetlanie systemu wskaźników KPI

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, należy wdrożone wystąpienie zdalnego rozwiązanie monitorowania w ramach subskrypcji platformy Azure.

Jeśli jeszcze tego nie wdrożono rozwiązanie monitorowania zdalnego jeszcze, należy wykonać [wdrożyć zdalnego monitorowania akcelerator rozwiązań](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) samouczka.

## <a name="choose-the-devices-to-display"></a>Wybierz urządzenia do wyświetlenia

Aby wybrać, które urządzenia są wyświetlane na **pulpitu nawigacyjnego** Użyj filtrów. Aby wyświetlić tylko **ciężarówka** urządzenia, wybierz wbudowane **pojazdów** filtr w listy rozwijanej filtru:

![Filtr dla pojazdów na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Po zastosowaniu filtru tylko te urządzenia, które spełniają warunki filtru wyświetlania mapy na **pulpitu nawigacyjnego** strony:

![Wyświetl pojazdów na mapie](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

Filtr określa również urządzeń, które widać w **Telemetrii** wykresu:

![Wyświetla dane telemetryczne ciężarówka na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Aby utworzyć, edytować i usunąć filtry, wybierz **Zarządzanie filtrami**.

## <a name="view-real-time-telemetry"></a>Widok telemetrii w czasie rzeczywistym

Akcelerator rozwiązań zawiera dane szczegółowe dane telemetryczne w czasie rzeczywistym na wykresie na **pulpitu nawigacyjnego** strony. Wykres telemetrii pokazuje informacje o telemetrii dla urządzeń wybranych przez bieżący filtr:

![Ciężarówka telemetrii kreślenia](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Wybierz wartości telemetrii, aby wyświetlić, wybierz typ telemetrii w górnej części wykresu:

![Ciężarówka telemetrii kreślenia](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Użyj mapy

Mapy Wyświetla informacje o pojazdów symulowane, wybrana przez bieżący filtr. Można powiększanie i przesuwanie mapę, aby wyświetlić lokalizacje mniej lub bardziej szczegółowo. Ikony urządzeń na mapie wskazuje wszelkie **alerty** lub **ostrzeżenia** działają urządzenia. Podsumowanie liczby **alerty** i **ostrzeżenia** Wyświetla z lewej strony mapy.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Wyświetl alerty z urządzeń

Mapy wyróżnia urządzeń w bieżący filtr z **alerty** i **ostrzeżenia**. **Alerty** panelu Wyświetla szczegółowe informacje o najnowszych alertów z urządzeń:

![Wyświetl alerty systemu na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Można użyć **pulpitu nawigacyjnego** filtr, aby dopasować przedział czasu dla ostatnich alertów. Domyślnie panelu wyświetlane alerty z ostatniej godziny:

![Filtrowanie alertów według czasu](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Wyświetlanie systemu wskaźników KPI

**Pulpitu nawigacyjnego** zostanie wyświetlona strona system wskaźników KPI:

![Pulpit nawigacyjny wskaźników KPI](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Można użyć **pulpitu nawigacyjnego** filtr, aby dopasować przedział czasu dla agregacji kluczowego wskaźnika wydajności. Domyślnie panelu Wyświetla zagregowane w ciągu ostatniej godziny kluczowych wskaźników wydajności.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia **pulpitu nawigacyjnego** strony do filtrowania i monitorować pojazdów symulowane, udostępniane w rozwiązaniu monitorowania zdalnego:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Widok telemetrii w czasie rzeczywistym
> * Wyświetl szczegóły urządzenia
> * Wyświetl alerty z urządzeń
> * Wyświetlanie systemu wskaźników KPI

Teraz, kiedy znasz jak monitorować urządzenia, Sugerowane następne kroki są Aby dowiedzieć się, jak:

* [Wykrywanie problemów przy użyciu reguły progu](../iot-accelerators/iot-accelerators-remote-monitoring-automate.md).
* [Konfigurowanie urządzeń oraz zarządzanie nimi](./iot-suite-remote-monitoring-manage.md).
* [Rozwiązywanie problemów i Korygowanie problemów z urządzeniami](./iot-suite-remote-monitoring-maintain.md).
* [Testowanie rozwiązania z urządzeniami symulowane](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->