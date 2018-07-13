---
title: Monitorowanie urządzeń IoT z poziomu rozwiązania platformy Azure | Microsoft Docs
description: W tym samouczku pokazano, jak monitorować urządzenia IoT przy użyciu akceleratora rozwiązań do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097465"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Samouczek: monitorowanie urządzeń IoT

W tym samouczku użyjesz akceleratora rozwiązań do zdalnego monitorowania w celu monitorowania połączonych urządzeń IoT. Użyjesz pulpitu nawigacyjnego rozwiązania, aby wyświetlać dane telemetryczne, informacje na temat urządzenia, alerty i wskaźniki KPI.

Aby wprowadzić te funkcje monitorowania, w samouczku wykorzystano dwa urządzenia symulowane: ciężarówki. Ciężarówki są zarządzane przez organizację Contoso i są połączone z akceleratorem rozwiązań do zdalnego monitorowania. Jako operator firmy Contoso musisz monitorować lokalizację i działanie ciężarówek w terenie.

W tym samouczku zostaną wykonane następujące czynności:

>[!div class="checklist"]
> * Filtrowanie urządzeń na pulpicie nawigacyjnym
> * Wyświetlanie danych telemetrycznych w czasie rzeczywistym
> * Wyświetlanie szczegółów urządzenia
> * Wyświetlanie alertów z urządzeń
> * Wyświetlanie wskaźników KPI

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, należy wdrożyć wystąpienie akceleratora rozwiązań do zdalnego monitorowania w subskrypcji platformy Azure.

Jeśli jeszcze nie wdrożono akceleratora rozwiązań do zdalnego monitorowania, należy ukończyć przewodnik Szybki start [Deploy a cloud-based remote monitoring solution (Wdrażanie rozwiązania do zdalnego monitorowania opartego na chmurze)](quickstart-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Wybieranie urządzenia do wyświetlenia

Aby wybrać połączone urządzenia, które mają być wyświetlane na stronie **Pulpit nawigacyjny**, użyj filtrów. Aby wyświetlić tylko urządzenia typu **Truck** (Ciężarówka), wybierz wbudowany filtr **Trucks** na liście rozwijanej filtrów:

[![Filtrowanie ciężarówek na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Po zastosowaniu filtrowania na mapie na stronie **Pulpit nawigacyjny** są wyświetlane tylko te urządzenia, które odpowiadają warunkom filtrowania:

[![Tylko ciężarówki są wyświetlane na mapie](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Filtr określa również, które urządzenia są widoczne na wykresie **Telemetria**:

[![Dane telemetryczne ciężarówek są wyświetlane na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

Aby tworzyć, edytować i usuwać filtry, wybierz pozycję **Zarządzaj grupami urządzeń**.

## <a name="view-real-time-telemetry"></a>Wyświetlanie danych telemetrycznych w czasie rzeczywistym

Akcelerator rozwiązań pokazuje dane telemetryczne w czasie rzeczywistym na wykresie na stronie **Pulpit nawigacyjny**. W górnej części wykresu z danymi telemetrycznymi pokazane są dostępne typy telemetrii dla wybranych urządzeń według bieżącego filtra:

[![Typy danych telemetrycznych dotyczących ciężarówek](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Aby wyświetlić dane telemetryczne dotyczące temperatury, kliknij pozycję **Temperature**:

[![Wykres danych telemetrycznych dotyczących temperatury ciężarówek](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>Używanie mapy

Na mapie wyświetlane są informacje dotyczące symulowanych ciężarówek wybranych przez bieżący filtr. Można powiększać i pomniejszać mapę, aby pokazać lokalizacje bardziej lub mniej szczegółowo. Kolor ikony urządzenia na mapie wskazuje, czy są aktywne jakiekolwiek **alerty** lub **ostrzeżenia** dotyczące urządzenia. Podsumowanie liczby **alertów** i **ostrzeżeń** jest wyświetlane po lewej stronie mapy.

Aby wyświetlić szczegóły urządzenia, powiększ lub pomniejsz mapę w celu zlokalizowania urządzenia, a następnie wybierz urządzenie na mapie. Następnie kliknij etykietę urządzenia, aby otworzyć panel **Szczegóły urządzenia**. Szczegóły urządzenia obejmują:

* Ostatnie wartości danych telemetrycznych
* Metody obsługiwane przez urządzenie
* Właściwości urządzenia

[![Wyświetlanie szczegółów urządzenia na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Wyświetlanie alertów

Na panelu **Alerty** wyświetlane są szczegółowe informacje dotyczące najnowszych alertów z urządzeń:

[![Wyświetlanie alertów urządzenia na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Możesz użyć filtrów, aby dopasować zakres czasowy dla ostatnich alertów. Domyślne na panelu wyświetlane są alerty z ostatniej godziny:

[![Filtrowanie alertów według czasu](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>Wyświetlanie wskaźników KPI

Na stronie **Pulpit nawigacyjny** wyświetlane są wskaźniki KPI systemu obliczane przez akcelerator rozwiązań na panelu **Analiza**:

[![Wskaźniki KPI na pulpicie nawigacyjnym](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Na pulpicie nawigacyjnym pokazane są trzy wskaźniki KPI dla alertów wybranych zgodnie z bieżącymi filtrami urządzeń i czasu:

* Liczba aktywnych alertów dotyczących reguł, które wywołały najwięcej alertów.
* Proporcje alertów według typu urządzenia.
* Procent alertów krytycznych.

Te same filtry, które wyznaczają zakres czasowy dla alertów i określają, które urządzenia są wyświetlane, wyznaczają sposób agregowania wskaźników KPI. Domyślnie na panelu wyświetlane są zagregowane wskaźniki KPI z ostatniej godziny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz przejść do kolejnego samouczka, akcelerator rozwiązań do zdalnego monitorowania powinien pozostać wdrożony. Aby zmniejszyć koszty działania akceleratora rozwiązań, gdy go nie używasz, możesz zatrzymać symulowane urządzenia w panelu Ustawienia:

[![Wstrzymywanie telemetrii](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

Możesz ponownie uruchomić urządzenia symulowane, gdy postanowisz rozpocząć kolejny samouczek.

Jeśli akcelerator rozwiązań nie jest już potrzebny, usuń go na stronie [Aprowizowane rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Usuwanie rozwiązania](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono, jak korzystać ze strony **Pulpit nawigacyjny** w akceleratorze rozwiązań do zdalnego monitorowania, aby filtrować i monitorować symulowane ciężarówki. Aby dowiedzieć się, w jaki sposób korzystać z akceleratora rozwiązań w celu wykrywania problemów z połączonymi urządzeniami, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Wykrywanie problemów z urządzeniami połączonymi z rozwiązaniem do monitorowania](iot-accelerators-remote-monitoring-automate.md)