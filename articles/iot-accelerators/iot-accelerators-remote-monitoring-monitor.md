---
title: Samouczek dotyczący monitorowania urządzeń IoT z poziomu rozwiązania platformy Azure — Azure | Microsoft Docs
description: W tym samouczku pokazano, jak monitorować urządzenia IoT przy użyciu akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d6d850fa8f896809318be77529e10abddaf6ea9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61452438"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Samouczek: Monitorowanie urządzeń IoT

W tym samouczku użyjesz akceleratora rozwiązania do monitorowania zdalnego w celu monitorowania połączonych urządzeń IoT. Użyjesz pulpitu nawigacyjnego rozwiązania, aby wyświetlać dane telemetryczne, informacje na temat urządzenia, alerty i wskaźniki KPI.

Ten samouczek korzysta z dwóch urządzeń symulowanych: ciężarówek. Wysyłają one dane telemetryczne obejmujące lokalizację, szybkość i temperaturę ładunku. Ciężarówki są zarządzane przez organizację Contoso i są połączone z akceleratorem rozwiązania do monitorowania zdalnego. Jako operator firmy Contoso musisz monitorować lokalizację i działanie jednej z ciężarówek (truck-02) w terenie.

W tym samouczku zostaną wykonane następujące czynności:

>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Wyświetlanie danych telemetrycznych w czasie rzeczywistym
> * Wyświetlanie szczegółów urządzenia
> * Wyświetlanie alertów z urządzeń
> * Wyświetlanie wskaźników KPI

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Wybieranie urządzenia do wyświetlenia

Aby wybrać połączone urządzenia, które mają być wyświetlane na stronie **Pulpit nawigacyjny**, użyj filtrów. Aby wyświetlić tylko urządzenia typu **Truck** (Ciężarówka), wybierz wbudowany filtr **Trucks** na liście rozwijanej filtrów:

[![Filtrowanie ciężarówek na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Po zastosowaniu filtrowania na mapie oraz w panelu telemetrii są wyświetlane tylko te urządzenia, które odpowiadają warunkom filtrowania. Widać, że do akceleratora rozwiązania są podłączone dwie ciężarówki, w tym ciężarówka truck-02:

[![Tylko ciężarówki są wyświetlane na mapie](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Aby tworzyć, edytować i usuwać filtry, kliknij pozycję **Zarządzaj grupami urządzeń**.

## <a name="view-real-time-telemetry"></a>Wyświetlanie danych telemetrycznych w czasie rzeczywistym

Akcelerator rozwiązań pokazuje dane telemetryczne w czasie rzeczywistym na wykresie na stronie **Pulpit nawigacyjny**. W górnej części wykresu z danymi telemetrycznymi pokazane są dostępne typy telemetrii dla urządzeń wybranych według bieżącego filtru, w tym dla urządzenia truck-02. Domyślnie na wykresie jest wyświetlana szerokość geograficzna ciężarówek. Ciężarówka truck-02 wydaje się nie poruszać:

[![Typy danych telemetrycznych dotyczących ciężarówek](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Aby wyświetlić dane telemetryczne dotyczące temperatury dla ciężarówek, kliknij pozycję **Temperature**: Widać, jak zmieniała się temperatura ciężarówki truck-02 w ciągu ostatniej godziny:

[![Wykres danych telemetrycznych dotyczących temperatury ciężarówek](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Wyświetlanie mapy

Na mapie wyświetlane są informacje dotyczące symulowanych ciężarówek wybranych przez bieżący filtr. Można powiększać i pomniejszać mapę, aby pokazać lokalizacje bardziej lub mniej szczegółowo. Kolor ikony urządzenia na mapie wskazuje, czy są aktywne jakiekolwiek **alerty** (ciemnoniebieski) lub **ostrzeżenia** (czerwony) dotyczące urządzenia. Podsumowanie liczby **alertów** i **ostrzeżeń** jest wyświetlane po lewej stronie mapy.

Aby wyświetlić szczegóły ciężarówki truck-02, powiększ lub pomniejsz mapę w celu zlokalizowania ciężarówki, a następnie wybierz ją na mapie. Następnie kliknij etykietę urządzenia, aby otworzyć panel **Szczegóły urządzenia**. Szczegóły urządzenia obejmują:

* Ostatnie wartości danych telemetrycznych
* Metody obsługiwane przez urządzenie
* Właściwości urządzenia

[![Wyświetlanie szczegółów urządzenia na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Wyświetlanie alertów

Na panelu **Alerty** wyświetlane są szczegółowe informacje dotyczące najnowszych alertów z urządzeń. Alerty z ciężarówki truck-02 wskazują na wyższą niż normalnie temperaturę ładunku:

[![Wyświetlanie alertów urządzenia na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Możesz użyć filtrów, aby dopasować zakres czasowy dla ostatnich alertów. Domyślne na panelu wyświetlane są alerty z ostatniej godziny.

## <a name="view-the-system-kpis"></a>Wyświetlanie wskaźników KPI

Na stronie **Pulpit nawigacyjny** wyświetlane są wskaźniki KPI systemu obliczane przez akcelerator rozwiązań na panelu **Analiza**:

[![Wskaźniki KPI na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Na pulpicie nawigacyjnym pokazane są trzy wskaźniki KPI dla alertów wybranych zgodnie z bieżącymi filtrami urządzeń i czasu:

* Liczba aktywnych alertów dotyczących reguł, które wywołały najwięcej alertów.
* Proporcje alertów według typu urządzenia.
* Procent alertów krytycznych.

W przypadku ciężarówki truck-02 wszystkie alerty są ostrzeżeniami o wyższej niż normalnie temperaturze ładunku.

Te same filtry, które wyznaczają zakres czasowy dla alertów i określają, które urządzenia są wyświetlane, wyznaczają sposób agregowania wskaźników KPI. Domyślnie na panelu wyświetlane są zagregowane wskaźniki KPI z ostatniej godziny.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono, jak korzystać ze strony **Pulpit nawigacyjny** w akceleratorze rozwiązania do monitorowania zdalnego, aby filtrować i monitorować symulowane ciężarówki. Aby dowiedzieć się, w jaki sposób korzystać z akceleratora rozwiązań w celu wykrywania problemów z połączonymi urządzeniami, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Wykrywanie problemów z urządzeniami połączonymi z rozwiązaniem do monitorowania](iot-accelerators-remote-monitoring-automate.md)