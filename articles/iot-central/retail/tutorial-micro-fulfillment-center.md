---
title: Mikro-realizacji centrum szablonu aplikacji tutorial | Dokumenty firmy Microsoft
description: Samouczek dotyczący szablonu aplikacji centrum mikrorealizacji dla usługi Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 74deb4253a21445e21f7ef04f53f3bfe3f1fe0d0
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000539"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Samouczek: Wdrażanie i przechodzenie przez szablon aplikacji centrum mikrorealizacji

W tym samouczku używasz szablonu aplikacji centrum mikrorealizacji usługi Azure IoT Central do tworzenia rozwiązania sieci sprzedaży detalicznej. Dowiesz się, jak wdrożyć szablon, co jest w nim zawarte i co warto zrobić dalej.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć tę serię samouczków, potrzebujesz subskrypcji platformy Azure. Opcjonalnie możesz skorzystać z bezpłatnego 7-dniowego okresu próbnego. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji platformy Azure.](https://aka.ms/createazuresubscription)

## <a name="create-an-application"></a>Tworzenie aplikacji 
W tej sekcji utworzysz nową aplikację Azure IoT Central na podstawie szablonu. Użyjesz tej aplikacji w całej serii samouczków, aby utworzyć kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny [menedżera aplikacji Usługi Azure IoT Central.](https://aka.ms/iotcentral)
1. Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się przy użyciu konta Microsoft:

   ![Zrzut ekranu przedstawiający okno dialogowe logowania do konta Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**.

1. Wybierz **pozycję Handel detaliczny**.  Na stronie sieci sprzedaży jest wyświetlanych kilka szablonów aplikacji sieci sprzedaży.

Aby utworzyć nową aplikację centrum mikrorealizacji, która korzysta z funkcji w wersji zapoznawczej:  
1. Wybierz szablon aplikacji **Centrum mikrorealizacji.** Ten szablon zawiera szablony urządzeń dla wszystkich urządzeń używanych w samouczku. Szablon zapewnia również pulpit nawigacyjny operatora do monitorowania warunków w centrum realizacji, a także warunków dla operatorów robotów. 

    ![Zrzut ekranu przedstawiający stronę aplikacji IoT Central usługi Azure IoT](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Opcjonalnie wybierz przyjazną **nazwę aplikacji**. Szablon aplikacji jest oparty na fikcyjnej firmie Northwind Traders. 

    >[!NOTE]
    >Jeśli używasz przyjaznej nazwy aplikacji, nadal musisz użyć unikatowej wartości dla adresu URL aplikacji.

1. Jeśli masz subskrypcję platformy Azure, wprowadź swój katalog, subskrypcję platformy Azure i region. Jeśli nie masz subskrypcji, możesz włączyć 7-dniową bezpłatną wersję próbną i wypełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Tworzenie szybkiego startu aplikacji.](../preview/quick-deploy-iot-central.md)

1. Wybierz **pozycję Utwórz**.

    ![Zrzut ekranu przedstawiający stronę Nowa aplikacja Usługi Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Przejdź przez aplikację 

Po pomyślnym wdrożeniu szablonu aplikacji zostanie wyświetlony **pulpit nawigacyjny centrum mikrorealizacji Northwind Traders**. Northwind Traders to fikcyjny sprzedawca detaliczny, który ma centrum mikrorealizacji zarządzane w tej aplikacji Azure IoT Central. Na tym pulpicie nawigacyjnym operatora są widoczne informacje i dane telemetryczne dotyczące urządzeń w tym szablonie, a także zestaw poleceń, zadań i akcji, które można podjąć. Pulpit nawigacyjny jest logicznie podzielony na dwie sekcje. Po lewej stronie można monitorować warunki środowiskowe w strukturze realizacji, a po prawej można monitorować stan zdrowia robota przewoźnika w obiekcie.  

Na pulpicie nawigacyjnym można:
   * Zobacz dane telemetryczne urządzenia, takie jak liczba pobranych, liczba przetworzonych zamówień i właściwości, takie jak stan systemu struktury.  
   * Zobacz plan kondygnacji i lokalizację nośników robotów w strukturze realizacji.
   * Polecenia wyzwalania, takie jak resetowanie systemu sterowania, aktualizowanie oprogramowania układowego operatora i ponowne konfigurowanie sieci.

     ![Zrzut ekranu przedstawiający panel centrum mikrorealizacji Northwind Traders](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Zobacz przykład pulpitu nawigacyjnego, którego operator może używać do monitorowania warunków w centrum realizacji. 
   * Monitoruj kondycję ładunków uruchomionych na urządzeniu bramy w centrum realizacji.    

     ![Zrzut ekranu przedstawiający panel centrum mikrorealizacji Northwind Traders](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia
Jeśli wybierzesz kartę Szablony urządzeń, zobaczysz, że istnieją dwa różne typy urządzeń, które są częścią szablonu: 
   * **Robotic Carrier:** Ten szablon urządzenia reprezentuje definicję działającego przewoźnika robota, który został wdrożony w strukturze realizacji i wykonuje odpowiednie operacje przechowywania i pobierania. Jeśli wybierzesz szablon, zobaczysz, że robot wysyła dane urządzenia, takie jak temperatura i położenie osi, oraz właściwości, takie jak stan nośnika robota. 
   * **Monitorowanie stanu struktury:** Ten szablon urządzenia reprezentuje kolekcję urządzeń, która umożliwia monitorowanie stanu środowiska, a także urządzenie bramy obsługujące różne obciążenia brzegowe w celu zasilania centrum realizacji zamówień. Urządzenie wysyła dane telemetryczne, takie jak temperatura, liczba typów i liczba zamówień. Wysyła również informacje o stanie i kondycji obciążeń obliczeniowych działających w twoim środowisku. 

     ![Szablony urządzeń Centrum mikrorealizacji](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Jeśli wybierzesz kartę Grupy urządzeń, zobaczysz również, że te szablony urządzeń są automatycznie tworzone dla nich grupami urządzeń.

## <a name="rules"></a>Reguły
Na **reguły** kartę, zobaczysz przykładową regułę, która istnieje w szablonie aplikacji do monitorowania warunków temperatury dla przewoźnika robota. Ta reguła może zostać użyta do ostrzeżenia operatora, jeśli określony robot w zakładzie jest przegrzany i musi zostać przesuń do trybu offline w celu obsługi serwisowej. 

Użyj przykładowej reguły jako inspiracji, aby zdefiniować reguły, które są bardziej odpowiednie dla funkcji biznesowych.

![Zrzut ekranu przedstawiający kartę Reguły](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń szablon aplikacji. Przejdź do ustawień aplikacji **Delete** **administracyjnej** > **Application settings**i wybierz pozycję Usuń .

![Zrzut ekranu przedstawiający stronę Ustawienia ustawień centrum mikrorealizacji](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [architekturze rozwiązań mikrorealizacji.](./architecture-micro-fulfillment-center.md)
* Dowiedz się więcej o innych [szablonach sieci sprzedaży usługi Azure IoT Central](./overview-iot-central-retail.md).
* Przeczytaj [omówienie usługi Azure IoT Central](../preview/overview-iot-central.md).
