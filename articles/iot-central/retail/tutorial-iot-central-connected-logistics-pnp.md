---
title: Samouczek dotyczący rozwiązanej logistyki IoT | Microsoft Docs
description: Samouczek dotyczący połączonego szablonu aplikacji logistycznej dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 6ddf0a5300caa5e202b16fc4f376056489cd9b61
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984294"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Samouczek: wdrażanie i przechodzenie przez połączony szablon aplikacji logistycznej

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak rozpocząć pracę, wdrażając IoT Central połączony szablon aplikacji **logistycznej** . Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co warto zrobić dalej.

W ramach tego samouczka nauczysz się, jak, 
* Tworzenie połączonej aplikacji logistycznej 
* Przechodzenie przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne
* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych
* Zalecane jest posiadanie subskrypcji platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-connected-logistics-application-template"></a>Tworzenie szablonu połączonej aplikacji logistycznej
Możesz utworzyć aplikację, wykonując następujące czynności
1. Przejdź do witryny sieci Web programu Azure IoT Central Application Manager. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **sprzedaż detaliczna** .

    > [!div class="mx-imgBorder"]
    > ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Wybierz pozycję **Utwórz aplikację** w ramach **połączonej aplikacji logistycznej**

3. **Utwórz aplikację** spowoduje otwarcie formularza nowej aplikacji i zapełnienie żądanych szczegółów, jak pokazano poniżej.
   * **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   * **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikatowy adres URL, który można dopamiętać. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego planu cenowego w wersji próbnej i wybrać konwersję do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem okresu bezpłatnego.
   * **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
   * **Utwórz** : wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

    > [!div class="mx-imgBorder"]
    > ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![informacje o rozliczeniu z rozliczeniami logistycznymi](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

## <a name="dashboard"></a>Pulpit nawigacyjny

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest podłączonym portalem ukierunkowanym na logistykę. Podmiot gospodarczy Northwind to fikcyjny dostawca logistyczny zarządzający flotą ładunku w oceanie i na lądzie. Na tym pulpicie nawigacyjnym zostaną wyświetlone dwie różne bramy dostarczające dane telemetryczne dotyczące wysyłek wraz z skojarzonymi poleceniami, zadaniami i akcjami, które można wykonywać. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do zaprezentowania krytycznych działań związanych z operacjami logistycznymi urządzenia.
Pulpit nawigacyjny jest logicznie podzielony między dwie różne operacje zarządzania urządzeniami bramy. 
   * Trasa logistyczna dla wysyłki i lokalizacji w układzie morskim jest istotnym elementem dla całego transportu wielomodalnego
   * Wyświetlanie stanu bramy & istotnych informacji 

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Możesz łatwo śledzić łączną liczbę bram, aktywnych i nieznanych tagów.
   * Można wykonywać operacje związane z zarządzaniem urządzeniami, takie jak oprogramowanie układowe aktualizacji, wyłączanie czujnika, Włączanie czujnika, próg czujnika aktualizacji, aktualizowanie interwałów telemetrii, & aktualizowanie umów usługi urządzenia.
   * Wyświetlanie zużycia baterii urządzenia

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia

Kliknij kartę szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest strukturalny wokół dwóch różnych poleceń **& właściwości** i **bramy** bramy między bramami interfejsów

**Właściwość & danych telemetrycznych bramy** — ten interfejs reprezentuje wszystkie dane telemetryczne związane z czujnikami, informacjami o lokalizacji i urządzeniu, a także możliwościami właściwości sieci bliźniaczych, takimi jak progi czujników & interwały aktualizacji.

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości polecenia bramy

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguły, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań.
 
**Alert dotyczący kradzieży bramy**: Ta reguła jest wyzwalana, gdy w trakcie podróży wystąpi nieoczekiwane wykrywanie światła. Operatory muszą zostać powiadomione JNW, aby zbadać potencjalne kradzieży.
 
**Brama nieodpowiadająca**: Ta reguła zostanie wyzwolona, jeśli Brama nie zgłosi do chmury przez długi czas. Brama może nie odpowiadać z powodu trybu niskiego stanu baterii, utraty łączności i kondycji urządzenia.

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Zadania
Wybierz kartę zadania, aby zobaczyć pięć różnych zadań, które istnieją w ramach tego szablonu aplikacji:

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Funkcja zadania służy do wykonywania operacji na całym rozwiązaniu. W tym miejscu zadania korzystają z poleceń urządzenia & bliźniaczym możliwością wykonywania zadań, takich jak wyłączenie określonych czujników dla wszystkich bram lub modyfikowanie progu czujnika w zależności od trybu wysyłki i trasy. 
   * Jest to standardowa operacja, która umożliwia wyłączenie czujników wstrząsów podczas wysyłki w oceanie w celu zachowania baterii lub obniżenia progu temperatury podczas transportowania zimnego łańcucha. 
 
   * Zadania umożliwiają wykonywanie operacji na poziomie systemu, takich jak aktualizowanie oprogramowania układowego na bramach lub aktualizowanie kontraktu usługi, aby zachować bieżące informacje o działaniach konserwacyjnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając pozycję **administracja** > **Ustawienia aplikacji** , a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji rozwiązanej logistyki](./architecture-connected-logistics-pnp.md)
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail-pnp.md)
* Dowiedz się więcej o [IoT Central przegląd](../preview/overview-iot-central.md)
