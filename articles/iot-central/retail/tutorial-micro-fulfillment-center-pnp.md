---
title: Samouczek szablonu aplikacji centrum Micro-zrealizować | Microsoft Docs
description: Samouczek szablonu aplikacji Centrum programu Micro-zrealizować dla IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027790"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Samouczek: wdrażanie i przechodzenie przez szablon aplikacji Centrum programu Micro-zrealizować

W tym samouczku użyjemy szablonu aplikacji Centrum programu Azure IoT Central ***Micro-zrealizować*** , aby zobaczyć, jak utworzyć rozwiązanie detaliczne. Dowiesz się, jak wdrożyć szablon MFC, co jest dołączone do pola i co warto zrobić dalej.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Tworzenie aplikacji detalicznej za pomocą szablonu centrum programu Azure IoT Central **Micro-zrealizować**
> * Przechodzenie przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć tę serię samouczków, potrzebne są:
* Subskrypcja platformy Azure. Opcjonalnie możesz użyć bezpłatnej 7-dniowej wersji próbnej. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Tworzenie aplikacji 
W tej sekcji utworzysz nową aplikację usługi Azure IoT Central na podstawie szablonu. Ta aplikacja zostanie użyta w całej serii samouczków, aby skompilować kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .
1. Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. w przeciwnym razie zaloguj się przy pomocy konto Microsoft:

   ![Określanie konta organizacji](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**.

1. Wybierz pozycję **sprzedaż detaliczna**.  Na stronie detalicznej są wyświetlane kilka szablonów aplikacji sieci Web.

Aby utworzyć nową aplikację centrum o wysokiej realizacji, która korzysta z funkcji w wersji zapoznawczej:  
1. Wybierz szablon aplikacja **centrum mikrorealizacji** . Ten szablon zawiera szablony urządzeń dla wszystkich urządzeń używanych w samouczku. Szablon zawiera również pulpit nawigacyjny operatora służący do monitorowania warunków w centrum realizacji oraz warunków dla przewoźników zautomatyzowanych. 

    > [!div class="mx-imgBorder"]
    > Typ aplikacji ![Micro-zrealizować](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Opcjonalnie możesz wybrać przyjazną **nazwę aplikacji**.  Szablon aplikacji jest oparty na fikcyjnej firmie Northwind Traders. 

    > [!NOTE]
    > Jeśli używasz przyjaznej **nazwy aplikacji**, nadal musisz użyć unikatowej wartości dla **adresu URL**aplikacji.

1. Jeśli masz subskrypcję platformy Azure, wprowadź swój *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../preview/quick-deploy-iot-central.md).

1. Wybierz pozycję **Utwórz**.

> [!div class="mx-imgBorder"]
> ![utworzyć aplikację Micro-zrealizować](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

### <a name="dashboard"></a>Pulpit nawigacyjny 

Po pomyślnym wdrożeniu szablonu aplikacji należy najpierw obsłużyć **pulpit nawigacyjny centrum danych Northwind Traders**. Firma Northwind to fikcyjny sprzedawca detaliczny, który ma centrum mikrorealizacji zarządzane w tej IoT Central aplikacji. Na tym pulpicie nawigacyjnym operatora zostaną wyświetlone informacje i dane telemetryczne dotyczące urządzeń w tym szablonie wraz z zestawem poleceń, zadań i akcji, które można wykonać. Pulpit nawigacyjny dzieli się logicznie na dwie sekcje (w lewo i w prawo). Po lewej stronie można monitorować warunki środowiska w ramach struktury realizacji i po prawej stronie, a także monitorować kondycję przewoźnika roboticowego w ramach tej funkcji.  

Na pulpicie nawigacyjnym możesz:
   * Zobacz dane telemetryczne urządzenia, takie jak Liczba pobrań, Liczba przetworzonych zamówień i właściwości, takie jak stan systemu struktury itp.  
   * Zapoznaj się z **planem pomieszczeń** i lokalizacją przewoźników zautomatyzowanych w ramach struktury realizacji.
   * Wyzwalaj polecenia, takie jak resetowanie systemu kontroli, aktualizowanie oprogramowania układowego operatora, ponowne konfigurowanie sieci itp.

> [!div class="mx-imgBorder"]
> Pulpit nawigacyjny centrum ![Micro-zrealizować](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Zapoznaj się z przykładem pulpitu nawigacyjnego, który może wykorzystać operator do monitorowania warunków w centrum realizacji. 
   * Monitoruj kondycję ładunków uruchomionych na urządzeniu bramy w centrum realizacji.    

> [!div class="mx-imgBorder"]
> Pulpit nawigacyjny centrum ![Micro-zrealizować](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia
Po kliknięciu karty szablony urządzeń zobaczysz, że istnieją dwa różne typy urządzeń, które są częścią szablonu: 
   * **Przewoźnik zautomatyzowany**: ten szablon urządzenia reprezentuje definicję działającego operatora zautomatyzowanego, który został wdrożony w strukturze realizacji i wykonuje odpowiednie operacje związane z magazynowaniem i pobieraniem. Jeśli klikniesz szablon, zobaczysz, że robot wysyła dane urządzenia, takie jak temperatura, położenie osi i właściwości, takie jak stan nośnej, itp. 
   * **Monitorowanie warunków struktury**: ten szablon urządzenia reprezentuje kolekcję urządzeń, która pozwala monitorować warunek środowiska oraz urządzenie bramy obsługujące różne obciążenia brzegowe umożliwiające zarządzanie centrum realizacji. Urządzenie wysyła dane telemetryczne, takie jak temperatura, Liczba pobrań, Liczba zamówień itp., oprócz stanu i kondycji obciążeń obliczeniowych uruchomionych w danym środowisku. 

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png) szablonów urządzeń centrum realizacji

Jeśli klikniesz kartę grupy urządzeń, zobaczysz również, że te szablony urządzeń automatycznie mają utworzone grupy urządzeń.

## <a name="rules"></a>Reguły
Podczas przechodzenia do karty reguły zostanie wyświetlona Przykładowa reguła, która istnieje w szablonie aplikacji, aby monitorować warunki temperatury dla operatora zautomatyzowanego. Za pomocą tej reguły można wysyłać alerty do operatora, jeśli konkretna robota w obiekcie jest przegrzana i musi zostać przełączona w tryb offline w celu obsługi. 

Skorzystaj z przykładowej reguły jako inspiracji, aby zdefiniować reguły, które są bardziej odpowiednie dla funkcji firmy.

   - **Zbyt grzany przez przewoźnika Robotic**: Ta reguła zostanie wyzwolona, jeśli w danym okresie przez przewoźnika Robotic osiągnie próg temperatury. 

> [!div class="mx-imgBorder"]
> reguły centrum ![Micro realizacji](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając pozycję **administracja** > **Ustawienia aplikacji** , a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png) oczyszczania aplikacji centrum z mikrorealizacji

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [architekturze rozwiązania Micro-zrealizować centrum](./architecture-micro-fulfillment-center-pnp.md)
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail-pnp.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../preview/overview-iot-central.md)