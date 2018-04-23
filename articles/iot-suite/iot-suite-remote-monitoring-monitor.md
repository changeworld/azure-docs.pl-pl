---
title: Zaawansowane monitorowanie zdalnego rozwiązanie monitorowania - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób monitorowania urządzeń ze zdalnego pulpitu nawigacyjnego monitorowania rozwiązania.
services: ''
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
ms.openlocfilehash: fe0d936b4ee0d7703222c86c00959869b99f7851
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Wykonaj zaawansowanego monitorowania przy użyciu zdalnego rozwiązanie monitorowania

Ten samouczek pokazuje możliwości zdalnego pulpitu nawigacyjnego monitorowania. Aby dodać tych funkcji, samouczku scenariusza aplikacji Contoso IoT.

W tym samouczku dwa symulowanego urządzenia ciężarówka Contoso służy do Dowiedz się, jak monitorować urządzenia z poziomu pulpitu nawigacyjnego wstępnie skonfigurowanych rozwiązań. Jako operatora firmy Contoso należy monitorować lokalizacji i zachowania użytkownika pojazdów w polu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Widok telemetrii w czasie rzeczywistym
> * Wyświetl szczegóły urządzenia
> * Alarmy widoku z urządzeń
> * Wyświetlanie systemu wskaźników KPI

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, należy wdrożone wystąpienie zdalnego rozwiązanie monitorowania w ramach subskrypcji platformy Azure.

Jeśli jeszcze tego nie wdrożono rozwiązanie monitorowania zdalnego jeszcze, należy wykonać [wdrożyć zdalnego wstępnie skonfigurowane rozwiązanie monitorowania](iot-suite-remote-monitoring-deploy.md) samouczka.

## <a name="choose-the-devices-to-display"></a>Wybierz urządzenia do wyświetlenia

Aby wybrać, które urządzenia są wyświetlane na **pulpitu nawigacyjnego** Użyj filtrów. Aby wyświetlić tylko **ciężarówka** urządzenia, wybierz wbudowane **pojazdów** filtr w listy rozwijanej filtru:

![Filtr dla pojazdów na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Po zastosowaniu filtru tylko te urządzenia, które spełniają warunki filtru wyświetlania mapy na **pulpitu nawigacyjnego** strony:

![Wyświetl pojazdów na mapie](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

Filtr określa również urządzeń, które widać w **Telemetrii** wykresu:

![Wyświetla dane telemetryczne ciężarówka na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Aby utworzyć, edytować i usunąć filtry, wybierz **Zarządzanie filtrami**.

## <a name="view-real-time-telemetry"></a>Widok telemetrii w czasie rzeczywistym

Wstępnie skonfigurowane rozwiązanie zawiera dane szczegółowe dane telemetryczne w czasie rzeczywistym na wykresie na **pulpitu nawigacyjnego** strony. Wykres telemetrii pokazuje informacje o telemetrii dla urządzeń wybranych przez bieżący filtr:

![Ciężarówka telemetrii kreślenia](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Wybierz wartości telemetrii, aby wyświetlić, wybierz typ telemetrii w górnej części wykresu:

![Ciężarówka telemetrii kreślenia](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

Aby wstrzymać wyświetlania danych telemetrycznych na żywo, wybierz **Flowing**. Aby ponownie włączyć wyświetlanie na żywo, wybierz **Wstrzymaj**:

![Wstrzymywanie i wznawianie wyświetlania telemetrii](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>Użyj mapy

Mapy Wyświetla informacje o pojazdów symulowane, wybrana przez bieżący filtr. Można powiększanie i przesuwanie mapę, aby wyświetlić lokalizacje mniej lub bardziej szczegółowo. Ikony urządzeń na mapie wskazuje wszelkie **alarmy** lub **ostrzeżenia** działają urządzenia. Podsumowanie liczby **alarmy** i **ostrzeżenia** Wyświetla z lewej strony mapy.

Aby wyświetlić szczegóły urządzenia, kadrować powiększenie mapy, aby zlokalizować urządzenia, a następnie kliknij urządzenie na mapie. Szczegóły obejmują:

* Ostatnie wartości telemetrii
* Metody urządzenie obsługuje
* Właściwości urządzenia

![Wyświetl szczegóły urządzenia na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>Alarmy widoku z urządzeń

Mapy wyróżnia urządzeń w bieżący filtr z **alarmy** i **ostrzeżenia**. **Alarmy systemu** panelu Wyświetla szczegółowe informacje o najnowszych alarmy z urządzeń:

![Alarmy systemu widoku na pulpicie nawigacyjnym](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Można użyć **alarmy systemu** filtr, aby dopasować przedział czasu dla ostatnich alarmy. Domyślnie panelu wyświetla alarmy z ostatniej godziny:

![Filtrowanie wg czasu alarmy](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Wyświetlanie systemu wskaźników KPI

**Pulpitu nawigacyjnego** zostanie wyświetlona strona system wskaźników KPI:

![Filtrowanie wg czasu alarmy](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Można użyć **kluczowy wskaźnik wydajności systemu** filtr, aby dopasować przedział czasu dla agregacji kluczowego wskaźnika wydajności. Domyślnie panelu Wyświetla zagregowane w ciągu ostatniej godziny kluczowych wskaźników wydajności.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia **pulpitu nawigacyjnego** strony do filtrowania i monitorować pojazdów symulowane, udostępniane w rozwiązaniu monitorowania zdalnego:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Widok telemetrii w czasie rzeczywistym
> * Wyświetl szczegóły urządzenia
> * Alarmy widoku z urządzeń
> * Wyświetlanie systemu wskaźników KPI

Teraz, kiedy znasz jak monitorować urządzenia, Sugerowane następne kroki są Aby dowiedzieć się, jak:

* [Wykrywanie problemów przy użyciu reguły progu](./iot-suite-remote-monitoring-automate.md).
* [Konfigurowanie urządzeń oraz zarządzanie nimi](./iot-suite-remote-monitoring-manage.md).
* [Rozwiązywanie problemów i Korygowanie problemów z urządzeniami](./iot-suite-remote-monitoring-maintain.md).
* [Testowanie rozwiązania z urządzeniami symulowane](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->