---
title: Samouczek centrum dystrybucji cyfrowej IoT | Microsoft Docs
description: Samouczek szablonu aplikacji Digital Distribution Center dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 20ed04efc1d10e419148cb4f6c75c3eab4ab40a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957916"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Samouczek: wdrażanie i przechodzenie przez szablon aplikacji centrum dystrybucji cyfrowej

W tym samouczku pokazano, jak rozpocząć pracę, wdrażając szablon aplikacji IoT Central **Digital Distribution Center** . Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co warto zrobić dalej.

## <a name="details"></a>Szczegóły

W ramach tego samouczka nauczysz się, jak, 
* Tworzenie aplikacji centrum dystrybucji cyfrowej 
* Przechodzenie przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne
* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych
* Zalecane jest posiadanie subskrypcji platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-digital-distribution-center-application-template"></a>Utwórz szablon aplikacji centrum dystrybucji cyfrowej

Możesz utworzyć aplikację, wykonując następujące czynności

1. Przejdź do witryny sieci Web programu Azure IoT Central Application Manager. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **sprzedaż detaliczna** .

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Wybierz kartę **sprzedaż detaliczna** , a następnie wybierz pozycję **Utwórz aplikację** w obszarze * * aplikacja centrum dystrybucji Digital * *

3. **Utwórz aplikację** spowoduje otwarcie formularza nowej aplikacji i zapełnienie żądanych szczegółów, jak pokazano poniżej.
   **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wpisać przyjazny unikatowy, zapamiętany adres URL. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. W przeciwnym razie możesz zacząć korzystać z siedmiu-dniowej bezpłatnej wersji próbnej i wybrać opcję konwersji płatnej zgodnie z rzeczywistym użyciem w dowolnym momencie przed wygaśnięciem końcowej wersji.
   **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
   **Utwórz**: wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

## <a name="dashboard"></a>Pulpit nawigacyjny 

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest portalem ukierunkowanym przez operatora centrum dystrybucji. Podmiot gospodarczy Northwind to fikcyjny dostawca rozwiązań centrum dystrybucji Zarządzający systemami przenośników. 

Na tym pulpicie nawigacyjnym zostanie wyświetlona jedna brama brzegowa i aparat działający jako urządzenie IoT. Brama udostępnia dane telemetryczne dotyczące pakietów, takich jak prawidłowy, nieprawidłowy, nieidentyfikowany i rozmiar wraz ze skojarzonymi z nią właściwościami z sznurka urządzenia. Wszystkie polecenia podrzędne są wykonywane na urządzeniach IoT, takich jak aparat. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do zaprezentowania krytycznego działania centrum dystrybucji.

Pulpit nawigacyjny jest logicznie zorganizowany do wyświetlania możliwości zarządzania urządzeniami w bramie Azure IoT Edge i urządzeniu IoT.  
   * Można wykonać polecenie graniczne & polecenia sterowania
   * Zarządzaj wszystkimi aparatami, które są częścią rozwiązania. 

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Szablon urządzenia

Kliknij kartę **Szablony urządzeń** , aby zobaczyć różne szablony urządzeń dla Azure IoT Edge & aparat. 

Szablon urządzenia bramy Azure IoT Edge reprezentuje plan, który definiuje cechy i zachowanie urządzenia bramy brzegowej. Niektóre składniki urządzenia brzegowego to,
   * Manifest wdrożenia
   * Relacja z urządzeniami podrzędnymi
   * Moduły oprogramowania
   * Właściwości modułów & polecenia 

Manifest wdrożenia zawiera listę modułów oprogramowania, które będą uruchamiane na urządzeniu Azure IoT Edge & module bliźniaczych reprezentacji skonfigurowany przy użyciu żądanych właściwości. Przy użyciu manifestu rozmieszczenia, który jest częścią szablonu urządzenia, Azure IoT Edge czas wykonywania wie, które moduły zainstalować i jak skonfigurować je do pracy ze sobą.

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

W tym szablonie urządzenia można wyświetlić modele możliwości urządzenia modułu są generowane na podstawie manifestu wdrożenia. 

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-devicetemplate2.png)

W tym miejscu możesz dodać relacje między urządzeniami podrzędnymi, właściwości chmury i tworzyć widoki, dostosowując możliwości modułów i urządzeń.

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-moduletemplate.png)

Kliknij kartę **urządzenie** i wybierz urządzenie **Ddcgateway** skojarzone z centrum dystrybucji cyfrowej Azure IoT Edge. W tym miejscu będą widoczne niestandardowe moduły brzegowe & kamerze podrzędnej, która jest częścią relacji urządzeń. Moduły IoT Edge są najmniejszą jednostką obliczeniową i mogą zawierać usługi platformy Azure (takie jak Azure Stream Analytics, moduły AI) lub kod specyficzny dla rozwiązania.

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-modules.png)

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-downstream.png)

## <a name="gateway-commands"></a>Polecenia bramy
Ten interfejs organizuje wszystkie możliwości polecenia bramy

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguły, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań.
 **Alert dotyczący zbyt wielu nieprawidłowych pakietów** — ta reguła jest wyzwalana, gdy aparat wykryje dużą liczbę nieprawidłowych pakietów przepływających przez system przenośnika.
 
**Duży pakiet** — ta reguła zostanie wyzwolona, jeśli aparat wykryje ogromny pakiet, którego nie można sprawdzić pod kątem jakości. 

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę zadania, aby zobaczyć pięć różnych zadań, które istnieją w ramach tego szablonu aplikacji: można wykorzystać funkcję zadań do wykonywania operacji na całym rozwiązaniu. W tym miejscu zadania centrum dystrybucji cyfrowej służą do wykonywania zadań, takich jak, & z użyciem technologii bliźniaczych.
   * Kalibrowanie aparatu przed zainicjowaniem wykrywania pakietu 
   * okresowe aktualizowanie oprogramowania układowego aparatu
   * Modyfikowanie interwału telemetrii w celu zarządzania przekazywaniem danych

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając pozycję **administracja** > **Ustawienia aplikacji** , a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji](./architecture-digital-distribution-center-pnp.md) rozwiązania centrum dystrybucji cyfrowej
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail-pnp.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../core/overview-iot-central-pnp.md)
