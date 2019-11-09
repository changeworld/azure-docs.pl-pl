---
title: Samouczek dotyczący zarządzania magazynem w usłudze IoT Smart | Microsoft Docs
description: Samouczek szablonu aplikacji do zarządzania magazynem inteligentnym dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d72636265ff3ac654faba91d1420b502b35d3192
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888997"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Samouczek: wdrażanie i przechodzenie przez szablon aplikacji do zarządzania magazynem inteligentnym

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak rozpocząć pracę, wdrażając szablon IoT Central aplikacji do **zarządzania magazynem inteligentnym** . Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co warto zrobić dalej.

W ramach tego samouczka nauczysz się, jak, 
* Tworzenie aplikacji do zarządzania magazynem inteligentnym 
* Przechodzenie przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne
* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych
* Zalecane jest posiadanie subskrypcji platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-smart-inventory-management-application-template"></a>Utwórz szablon aplikacji do zarządzania magazynem inteligentnym

Możesz utworzyć aplikację, wykonując następujące czynności
1. Przejdź do witryny sieci Web programu Azure IoT Central Application Manager. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **sprzedaż detaliczna** .

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Wybierz kartę **sprzedaż detaliczna** i wybierz pozycję **Utwórz aplikację** w obszarze * * Zarządzanie magazynem inteligentnym * *

3. **Utwórz aplikację** spowoduje otwarcie formularza nowej aplikacji i zapełnienie żądanych szczegółów, jak pokazano poniżej.
   **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikatowy adres URL, który można dopamiętać. Następnie zalecane jest ustawienie domyślne, jeśli masz już subskrypcję platformy Azure lub możesz zacząć od siedmiu dni bezpłatnej wersji próbnej, a następnie wybrać opcję konwersji płatnej zgodnie z rzeczywistym użyciem w dowolnym momencie przed wygaśnięciem.
   **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
   **Utwórz**: wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

### <a name="dashboard"></a>Pulpit nawigacyjny 
Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest portalem ukierunkowanym przez operatora zarządzania magazynem inteligentnym. Podmiot gospodarczy Northwind jest fikcyjnym dostawcą magazynu inteligentnego, zarządzanym magazynem z technologią Bluetooth Low Energy (beli) i sklepem detalicznym z identyfikacją częstotliwości radiowych (RFID). Na tym pulpicie nawigacyjnym zostaną wyświetlone dwie różne bramy dostarczające dane telemetryczne dotyczące spisu wraz z skojarzonymi poleceniami, zadaniami i akcjami, które można wykonywać. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do zaprezentowania krytycznego działania urządzenia do zarządzania magazynem inteligentnym.
Pulpit nawigacyjny jest logicznie podzielony między dwie różne operacje zarządzania urządzeniami bramy. 
   * Magazyn jest wdrażany ze stałą bramą do obsługi spisu & znacznikiami na paletach na potrzeby śledzenia & śledzenia śladów w większej pojemności
   * Sklep detaliczny jest implementowany ze stałą bramą RFID & znaczniki RFID na indywidualnym poziomie elementu, aby śledzić i śledzić zapasy w punkcie sprzedaży sklepu
   * Wyświetlanie informacji o lokalizacji bramy, & stanu 

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Możesz łatwo śledzić łączną liczbę bram, aktywnych i nieznanych tagów.
   * Można wykonywać operacje związane z zarządzaniem urządzeniami, takie jak oprogramowanie układowe aktualizacji, wyłączenie czujnika, włączenie czujnika, próg czujnika aktualizacji, aktualizowanie interwałów telemetrii & aktualizowanie kontraktów usługi urządzenia
   * Urządzenia bramy mogą wykonywać zarządzanie spisem na żądanie przy użyciu pełnego lub przyrostowego skanowania.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia
Kliknij kartę szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest strukturalny wokół dwóch różnych poleceń **& właściwości** i **bramy** bramy między bramami interfejsów

**Właściwość & danych telemetrycznych bramy** — ten interfejs reprezentuje wszystkie dane telemetryczne związane z czujnikami, lokalizacją, informacjami o urządzeniu, a także możliwości właściwości sieci bliźniaczych, takich jak progi bramy & interwały aktualizacji.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości polecenia bramy

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguły, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań.

**Brama w trybie offline**: Ta reguła zostanie wyzwolona, jeśli Brama nie zgłosi do chmury przez długi czas. Brama może nie odpowiadać z powodu trybu niskiego stanu baterii, utraty łączności i kondycji urządzenia.

**Nieznane znaczniki**: mają kluczowe znaczenie dla śledzenia każdego znacznika RFID & znaczników związanych z elementem zawartości. Jeśli Brama wykrywa zbyt wiele nieznanych tagów, jest to wskaźnik wyzwań związanych z synchronizacją z aplikacjami, które są źródłem znaczników.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Zadania
Wybierz kartę zadania, aby zobaczyć pięć różnych zadań, które istnieją w ramach tego szablonu aplikacji: można wykorzystać funkcję zadań do wykonywania operacji na całym rozwiązaniu. W tym miejscu zadania zarządzania spisem używają poleceń urządzenia & bliźniaczym możliwością wykonywania zadań takich jak,
   * Wyłączanie czytników na całej bramie
   * Modyfikowanie progu telemetrii między 
   * wykonaj skanowanie spisu na żądanie w całym rozwiązaniu.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając pozycję **administracja** > **Ustawienia aplikacji** , a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji zarządzania magazynem inteligentnym](./architecture-smart-inventory-management-pnp.md) zarządzania magazynem inteligentnym
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail-pnp.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../preview/overview-iot-central.md)
