---
title: Samouczek Centrum Dystrybucji Cyfrowej IoT | Dokumenty firmy Microsoft
description: Samouczek szablonu aplikacji centrum dystrybucji cyfrowej dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: cb40252460fe62deb94ce495bb41d38c326f10e2
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000588"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Samouczek: Wdrażanie i przechodzenie przez szablon aplikacji centrum dystrybucji cyfrowej



W tym samouczku pokazano, jak rozpocząć wdrażanie szablonu aplikacji **centrum dystrybucji cyfrowej** IoT Central. Dowiesz się, jak wdrożyć szablon, co jest zawarte po wyjęciu z pudełka i co możesz zrobić dalej.

W tym samouczku dowiesz się, jak, 
* Tworzenie aplikacji cyfrowego centrum dystrybucji 
* Przejdź przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne
* Do wdrożenia tej aplikacji nie są wymagane żadne szczególne wymagania wstępne
* Zalecane, aby mieć subskrypcję platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-digital-distribution-center-application-template"></a>Tworzenie szablonu aplikacji centrum dystrybucji cyfrowej

Aplikację można tworzyć, wykonując następujące kroki

1. Przejdź do witryny menedżera aplikacji usługi Azure IoT Central. Wybierz **pozycję Buduj** na lewym pasku nawigacyjnym, a następnie kliknij kartę **Handel detaliczny.**

    > [!div class="mx-imgBorder"]
    > ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Wybierz kartę **Handel detaliczny** i wybierz pozycję **Utwórz aplikację** w aplikacji **cyfrowego centrum dystrybucji**

3. **Utwórz aplikację** otworzy nowy formularz aplikacji i wypełni żądane szczegóły, jak pokazano poniżej.
   **Nazwa aplikacji:** możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   **URL:** możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikalny adres URL w pamięć. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego bezpłatnego planu cenowego i wybrać opcję konwersji na standardowy plan cenowy w dowolnym momencie przed wygaśnięciem bezpłatnego szlaku.
   **Informacje rozliczeniowe:** Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do inicjowania obsługi administracyjnej zasobów.
   **Utwórz**: Wybierz utwórz u dołu strony, aby wdrożyć aplikację.

    > [!div class="mx-imgBorder"]
    > ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![Informacje rozliczeniowe dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Przechodzenie przez pulpit nawigacyjny aplikacji 

Po pomyślnym wdrożeniu szablonu aplikacji domyślnym pulpitem nawigacyjnym jest portal skoncentrowany na centrum dystrybucji. Northwind Trader jest fikcyjnym dostawcą rozwiązań dystrybucyjnych zarządzających systemami przenośników. 

Na tym pulpicie nawigacyjnym zobaczysz jedną bramę i jedną kamerę działającą jako urządzenie IoT. Brama udostępnia dane telemetryczne dotyczące pakietów, takich jak prawidłowe, nieprawidłowe, niezidentyfikowane i rozmiar wraz z skojarzonymi właściwościami bliźniaczej reprezentacji urządzenia. Wszystkie polecenia podrzędne są wykonywane na urządzeniach IoT, takich jak kamera. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do prezentacji działania urządzenia centrum dystrybucji o krytycznym znaczeniu.

Pulpit nawigacyjny jest logicznie zorganizowany, aby wyświetlić możliwości zarządzania urządzeniami bramy IoT platformy Azure i urządzenia IoT.  
   * Można wykonywać zadania & sterowania poleceniem bramy
   * Zarządzaj wszystkimi kamerami, które są częścią rozwiązania. 

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Szablon urządzenia

Kliknij kartę Szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest skonstruowany wokół dwóch różnych interfejsów **Camera** i **Digital Distribution Gateway**

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** - Ten interfejs organizuje wszystkie funkcje poleceń specyficznych dla kamery 

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Cyfrowa brama dystrybucji** — ten interfejs reprezentuje wszystkie dane telemetryczne pochodzące z kamery, właściwości bliźniaczych urządzeń zdefiniowanych w chmurze i informacje o bramie.

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Polecenia bramy
Ten interfejs organizuje wszystkie możliwości poleceń bramy

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguł, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Reguły te są skonfigurowane do powiadomień e-mail do operatorów w celu dalszego dochodzenia.

 **Zbyt wiele nieprawidłowych pakietów alert** — ta reguła jest wyzwalana, gdy aparat wykryje dużą liczbę nieprawidłowych pakietów przepływających przez system przenośnika.
 
**Duży pakiet** — ta reguła zostanie uruchomiona, jeśli aparat wykryje ogromny pakiet, który nie może być sprawdzony pod kątem jakości. 

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę zadania, aby wyświetlić pięć różnych zadań, które istnieją jako część tego szablonu aplikacji: Można wykorzystać funkcje zadań do wykonywania operacji w całej rozwiązania. W tym miejscu zadania centrum dystrybucji cyfrowej używają poleceń urządzenia & podwójnej funkcji do wykonywania zadań, takich jak
   * kalibracji kamery przed rozpoczęciem wykrywania opakowania 
   * okresowe aktualizowanie oprogramowania układowego aparatu
   * modyfikowanie interwału telemetrii w celu zarządzania przekazywaniem danych

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń szablon aplikacji, odwiedzając**ustawienia aplikacji** **administracyjnej** > i kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![Centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji cyfrowego centrum dystrybucji](./architecture-digital-distribution-center.md) o architekturze cyfrowego centrum dystrybucji
* Dowiedz się więcej o innych [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [przeglądem IoT Central](../core/overview-iot-central.md)
