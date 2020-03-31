---
title: Poradnik logistyki IoT Connected | Dokumenty firmy Microsoft
description: Samouczek szablonu aplikacji logistycznej connected dla usługi IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d7050cfdae2c726a3a5bd44b4399792a400572a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025524"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Samouczek: Wdrażanie i przechodzenie przez szablon połączonej aplikacji logistycznej



W tym samouczku pokazano, jak rozpocząć wdrażanie szablonu **aplikacji połączonej logistyki IoT** Central. Dowiesz się, jak wdrożyć szablon, co jest zawarte po wyjęciu z pudełka i co możesz zrobić dalej.

W tym samouczku dowiesz się, jak,

* tworzenie połączonej aplikacji logistycznej
* przejść przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne

* Do wdrożenia tej aplikacji nie są wymagane żadne szczególne wymagania wstępne
* Zalecane, aby mieć subskrypcję platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-connected-logistics-application-template"></a>Tworzenie szablonu aplikacji logistycznej połączonej

Aplikację można tworzyć, wykonując następujące kroki

1. Przejdź do witryny menedżera aplikacji usługi Azure IoT Central. Wybierz **pozycję Buduj** na lewym pasku nawigacyjnym, a następnie kliknij kartę **Handel detaliczny.**

    > [!div class="mx-imgBorder"]
    > ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Wybierz **pozycję Utwórz aplikację** w obszarze Aplikacja **Połączonej Logistyki**

3. **Utwórz aplikację** otworzy nowy formularz aplikacji i wypełni żądane szczegóły, jak pokazano poniżej.
   * **Nazwa aplikacji:** możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   * **URL:** możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikalny adres URL w pamięć. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego bezpłatnego planu cenowego i wybrać opcję konwersji na standardowy plan cenowy w dowolnym momencie przed wygaśnięciem bezpłatnego szlaku.
   * **Informacje rozliczeniowe:** Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do inicjowania obsługi administracyjnej zasobów.
   * **Utwórz**: Wybierz utwórz u dołu strony, aby wdrożyć aplikację.

    > [!div class="mx-imgBorder"]
    > ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Informacje rozliczeniowe Connected Logistics](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Przejdź przez aplikację 

## <a name="dashboard"></a>Pulpit nawigacyjny

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest portalem skoncentrowanym na połączonym operatorze logistycznym. Northwind Trader jest fikcyjnym dostawcą logistycznym zarządzającym flotą ładunków w oceanie i na lądzie. Na tym pulpicie nawigacyjnym zostaną wyświetlone dwie różne bramy zapewniające dane telemetryczne dotyczące wysyłek wraz ze skojarzonymi poleceniami, zadaniami i akcjami, które można wykonać. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do prezentacji krytycznych działań operacyjnych urządzeń logistycznych.
Pulpit nawigacyjny jest logicznie podzielony na dwie różne operacje zarządzania urządzeniami bramy, 
   * Trasa logistyczna dla przesyłki samochodowej i szczegóły lokalizacji przesyłki oceanicznej jest istotnym elementem dla całego transportu multimodalnego
   * Wyświetlanie stanu bramy & odpowiednich informacji 

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Można łatwo śledzić całkowitą liczbę bram, aktywnych i nieznanych tagów.
   * Można wykonywać operacje zarządzania urządzeniami, takie jak aktualizacja oprogramowania układowego, wyłączanie czujnika, włączanie czujnika, próg aktualizacji czujnika, okresy aktualizacji danych telemetrycznych & aktualizowanie umów serwisowych urządzeń.
   * Wyświetlanie zużycia baterii urządzenia

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia

Kliknij kartę Szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest skonstruowany wokół dwóch różnych interfejsów **Telemetria bramy & właściwości** i **poleceń bramy**

**Właściwość & danych telemetrycznych bramy** — ten interfejs reprezentuje wszystkie dane telemetryczne związane z czujnikami, lokalizacją i informacjami o urządzeniu, a także możliwości właściwości bliźniaczej reprezentacji urządzenia, takie jak progi czujników & interwały aktualizacji.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości poleceń bramy

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguł, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Reguły te są skonfigurowane do powiadomień e-mail do operatorów w celu dalszego dochodzenia.
 
**Alert kradzieży bramy:** Ta reguła jest wyzwalana, gdy czujniki w trakcie podróży wykrywają nieoczekiwane światło. Operatorzy muszą zostać powiadomieni jak najszybciej w celu zbadania potencjalnej kradzieży.
 
**Brama nieodpowiadające:** Ta reguła wyzwoli się, jeśli brama nie zgłosi się do chmury przez dłuższy czas. Brama może nie odpowiadać z powodu trybu niskiego poziomu naładowania baterii, utraty łączności, kondycji urządzenia.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę Zadania, aby wyświetlić pięć różnych zadań, które istnieją jako część tego szablonu aplikacji:

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Funkcji zadań można używać do wykonywania operacji w całej rozwiązaniach. W tym miejscu zadania są za pomocą poleceń urządzenia i podwójnej funkcji do wykonywania zadań, takich jak wyłączenie określonych czujników we wszystkich bramach lub modyfikowanie progu czujnika w zależności od trybu wysyłki i trasy. 
   * Jest to standardowa operacja wyłączania czujników wstrząsów podczas transportu oceanicznego w celu oszczędzania baterii lub obniżenia progu temperatury podczas transportu w łańcuchu chłodniim. 
 
   * Zadania umożliwiają operacje w całym systemie, takie jak aktualizowanie oprogramowania układowego na bramkach lub aktualizowanie umowy serwisowej w celu utrzymania bieżących czynności konserwacyjnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń szablon aplikacji, odwiedzając**ustawienia aplikacji** **administracyjnej** > i kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji połączonej logistyki](./architecture-connected-logistics-pnp.md)
* Dowiedz się więcej o innych [szablonach detalicznych IoT Central](./overview-iot-central-retail-pnp.md)
* Dowiedz się więcej o [przeglądzie IoT Central](../core/overview-iot-central.md)
