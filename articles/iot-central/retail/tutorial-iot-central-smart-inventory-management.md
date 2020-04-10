---
title: Samouczek zarządzania inteligentnymi zapasami IoT | Dokumenty firmy Microsoft
description: Samouczek szablonu aplikacji do zarządzania inteligentnymi zapasami dla usługi IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 430f477422e040e0e7e28fd69a1cbc18bce7d656
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000546"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Samouczek: Wdrażanie i przechodzenie przez szablon aplikacji do zarządzania inteligentnymi zapasami



W tym samouczku pokazano, jak rozpocząć wdrażanie szablonu aplikacji do **zarządzania inteligentnymi zapasami** usługi IoT Central. Dowiesz się, jak wdrożyć szablon, co jest zawarte po wyjęciu z pudełka i co możesz zrobić dalej.

W tym samouczku dowiesz się, jak, 
* tworzenie inteligentnej aplikacji do zarządzania zapasami 
* przejść przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne

* Do wdrożenia tej aplikacji nie są wymagane żadne szczególne wymagania wstępne
* Zalecane, aby mieć subskrypcję platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-smart-inventory-management-application-template"></a>Tworzenie szablonu aplikacji do zarządzania inteligentnymi zapasami

Aplikację można tworzyć, wykonując następujące kroki

1. Przejdź do witryny menedżera aplikacji usługi Azure IoT Central. Wybierz **pozycję Buduj** na lewym pasku nawigacyjnym, a następnie kliknij kartę **Handel detaliczny.**

    > [!div class="mx-imgBorder"]
    > ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Wybierz kartę **Handel detaliczny** i wybierz pozycję **Utwórz aplikację** w obszarze Zarządzanie **inteligentnymi zapasami**

3. **Utwórz aplikację** otworzy nowy formularz aplikacji i wypełni żądane szczegóły, jak pokazano poniżej.
   **Nazwa aplikacji:** możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   **URL:** możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikalny adres URL w pamięć. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego bezpłatnego planu cenowego i wybrać opcję konwersji na standardowy plan cenowy w dowolnym momencie przed wygaśnięciem bezpłatnego szlaku.
   **Informacje rozliczeniowe:** Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do inicjowania obsługi administracyjnej zasobów.
   **Utwórz**: Wybierz utwórz u dołu strony, aby wdrożyć aplikację.

    > [!div class="mx-imgBorder"]
    > ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Informacje rozliczeniowe dotyczące inteligentnego zarządzania zapasami](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Przejdź przez aplikację 

### <a name="dashboard"></a>Pulpit nawigacyjny 

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest portalem skoncentrowanym na inteligentnym module zarządzania zapasami. Northwind Trader jest fikcyjnym dostawcą inteligentnych zapasów zarządzających magazynem z niskim zużyciem energii Bluetooth (BLE) i sklepem detalicznym z identyfikacją radiową (RFID). Na tym pulpicie nawigacyjnym zostaną wyświetlone dwie różne bramy zapewniające dane telemetryczne dotyczące zapasów wraz ze skojarzonymi poleceniami, zadaniami i akcjami, które można wykonać. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do prezentacji krytycznej aktywności operacyjnej urządzenia do zarządzania inteligentnymi zapasami.
Pulpit nawigacyjny jest logicznie podzielony na dwie różne operacje zarządzania urządzeniami bramy, 
   * Magazyn jest wdrażany ze stałą bramą BLE & znacznikami BLE na paletach w celu śledzenia & śledzenia zapasów w większym obiekcie
   * Sklep detaliczny jest realizowany ze stałą bramą RFID & znacznikami RFID na poziomie pojedynczego towaru w celu śledzenia i śledzenia zapasów w sklepie
   * Wyświetlanie lokalizacji bramy, stanu & powiązanych szczegółów 

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Można łatwo śledzić całkowitą liczbę bram, aktywnych i nieznanych tagów.
   * Można wykonywać operacje zarządzania urządzeniami, takie jak aktualizacja oprogramowania układowego, wyłączanie czujnika, włączanie czujnika, próg czujnika aktualizacji, aktualizowanie interwałów telemetrycznych & aktualizowanie umów serwisowych urządzeń
   * Urządzenia bramy mogą wykonywać zarządzanie zapasami na żądanie za pomocą pełnego lub przyrostowego skanowania.

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia
Kliknij kartę Szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest skonstruowany wokół dwóch różnych interfejsów **Telemetria bramy & właściwości** i **poleceń bramy**

**Właściwość & danych telemetrycznych bramy** — ten interfejs reprezentuje wszystkie dane telemetryczne związane z czujnikami, lokalizacją, informacjami o urządzeniu i możliwościami właściwości bliźniaczej reprezentacji urządzenia, takimi jak progi bramy i interwały aktualizacji.

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości poleceń bramy

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguł, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Reguły te są skonfigurowane do powiadomień e-mail do operatorów w celu dalszego dochodzenia.

**Brama w trybie offline:** Ta reguła zostanie wyzwolona, jeśli brama nie zgłosi się do chmury przez dłuższy czas. Brama może nie odpowiadać z powodu trybu niskiego poziomu naładowania baterii, utraty łączności, kondycji urządzenia.

**Nieznane tagi:** Śledzenie wszystkich znaczników RFID & BLE skojarzonych z zasobem ma kluczowe znaczenie. Jeśli brama wykrywa zbyt wiele nieznanych tagów, jest to wskazanie problemów synchronizacji z aplikacjami pozyskiwania znaczników.

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę zadania, aby wyświetlić pięć różnych zadań, które istnieją jako część tego szablonu aplikacji: Można użyć funkcji zadań do wykonywania operacji w całej rozwiązania. W tym miejscu zadania zarządzania zapasami używają poleceń urządzenia i możliwości bliźniaczej do wykonywania zadań, takich jak
   * wyłączanie czytników we wszystkich bramkach
   * modyfikowanie progu telemetrii między 
   * skanowanie zapasów na żądanie w całym rozwiązaniu.

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń szablon aplikacji, odwiedzając**ustawienia aplikacji** **administracyjnej** > i kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji inteligentnego zarządzania zapasami inteligentnych zapasów](./architecture-smart-inventory-management.md)
* Dowiedz się więcej o innych [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [przeglądem IoT Central](../core/overview-iot-central.md)
