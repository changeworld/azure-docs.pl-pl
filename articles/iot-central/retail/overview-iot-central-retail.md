---
title: Tworzenie rozwiązań dla handlu detalicznego za pomocą usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się więcej o używaniu szablonów aplikacji Azure IoT Central do tworzenia połączonych logów, cyfrowego centrum dystrybucji, analizy w sklepie, monitorowania stanu, realizacji transakcji, inteligentnego zarządzania zapasami i rozwiązań detalicznych.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 9427b007bc047e2f01db2dad02e06bf0ab0f5dea
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000616"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Tworzenie rozwiązań dla handlu detalicznego za pomocą usługi Azure IoT Central



Azure IoT Central to platforma aplikacji IoT, która zmniejsza obciążenie i koszty związane z opracowywaniem, zarządzaniem i utrzymywaniem rozwiązań IoT klasy korporacyjnej. Wybór kompilacji za pomocą usługi Azure IoT Central daje możliwość skoncentrowania czasu, pieniędzy i energii na przekształcaniu firmy za pomocą danych IoT, a nie tylko na utrzymywaniu i aktualizowaniu złożonej i stale rozwijającej się infrastruktury IoT.

W tym artykule opisano kilka szablonów aplikacji IoT Central specyficznych dla sieci sprzedaży. Jako konstruktor rozwiązań możesz używać tych szablonów do tworzenia rozwiązań IoT, które optymalizują łańcuchy dostaw, poprawiają środowisko w sklepie dla klientów i usprawniają zapasy.

> [!div class="mx-imgBorder"]
> ![Omówienie usługi Azure IoT Retail](./media/overview-iot-central-retail/retail-app-templates.png)

W poniższych sekcjach opisano możliwości tych szablonów aplikacji:

## <a name="connected-logistics"></a>Połączona logistyka

Oczekuje się, że w 2020 r. globalne wydatki na logistykę wyniosą 10,6 bln USD. Większość tych wydatków stanowi transport towarów, a dostawcy usług żeglugowych znajdują się pod silną presją konkurencyjną i presją.

Za pomocą czujników IoT można zbierać i monitorować warunki otoczenia, takie jak temperatura, wilgotność, nachylenie, wstrząsy, światło i lokalizacja przesyłki. Dane telemetryczne zebrane z czujników i urządzeń IoT można łączyć z innymi źródłami danych, takimi jak informacje o pogodzie i ruchu drogowym w chmurowych systemach analizy biznesowej.

Korzyści płynące z połączonego rozwiązania logistycznego obejmują:

* Monitorowanie przesyłek z śledzeniem i śledzeniem w czasie rzeczywistym. 
* Integralność przesyłki z monitorowaniem stanu otoczenia w czasie rzeczywistym.
* Zabezpieczenie przed kradzieżą, utratą lub uszkodzeniem przesyłek.
* Geo-ogrodzenia, optymalizacja tras, zarządzanie flotą i analiza pojazdów.
* Prognozowanie przewidywalnego wyjazdu i przybycia przesyłek.

Poniższe zrzuty ekranu pokazują gotowy pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny można w pełni dostosować, aby spełnić określone wymagania dotyczące rozwiązań:

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny połączonej logistyki](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Aby dowiedzieć się więcej, zobacz [Wdrażanie i zapoznaj się z samouczkiem szablonu połączonej aplikacji logistycznej.](./tutorial-iot-central-connected-logistics.md)

## <a name="digital-distribution-center"></a>Cyfrowe centrum dystrybucji

W miarę jak producenci i sprzedawcy detaliczni zasiedli na całym świecie, ich łańcuchy dostaw rozgałęziają się i stają się bardziej złożone. Konsumenci oczekują teraz, że duży wybór produktów będzie dostępny, a towary te dotrą w ciągu jednego lub dwóch dni od zakupu. Centra dystrybucji muszą dostosować się do tych trendów, jednocześnie pokonując istniejące nieefektywności. 

Obecnie poleganie na pracy ręcznej oznacza, że zbieranie i pakowanie stanowi 55-65% kosztów centrum dystrybucji. Ręczne pobieranie i pakowanie jest również zazwyczaj wolniejsze niż zautomatyzowane systemy, a szybko zmieniające się potrzeby kadrowe jeszcze bardziej utrudniają zaspokojenie wielkości wysyłki. Te sezonowe wahania skutkuje dużą rotacją pracowników i zwiększają prawdopodobieństwo wystąpienia kosztownych błędów.

Rozwiązania oparte na kamerach z obsługą IoT mogą przynieść korzyści transformacyjne, umożliwiając cyfrową pętlę sprzężenia zwrotnego. Dane z całego centrum dystrybucji prowadzą do użytecznych spostrzeżeń, które z kolei przekładają się na lepsze dane.

Zalety cyfrowego centrum dystrybucji obejmują:

* Kamery monitorują towary podczas ich przybycia i przemieszczają się przez system przenośników.
* Automatyczna identyfikacja wadliwych towarów.
* Efektywne śledzenie zamówień.
* Niższe koszty, większa produktywność i zoptymalizowane zużycie.

Poniższy zrzut ekranu przedstawia gotowy pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny można w pełni dostosować, aby spełnić określone wymagania dotyczące rozwiązań: 

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny centrum dystrybucji cyfrowej](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Aby dowiedzieć się więcej, zobacz [Wdrażanie i zapoznaj się z samouczkiem szablonu aplikacji centrum dystrybucji cyfrowej.](./tutorial-iot-central-digital-distribution-center.md)

## <a name="in-store-analytics---condition-monitoring"></a>Analityka w sklepie - monitorowanie stanu

Dla wielu sprzedawców detalicznych warunki środowiskowe w ich sklepach są kluczowym wyróżnikiem od ich konkurentów. Detaliści chcą utrzymać przyjemne warunki w swoich sklepach z korzyścią dla swoich klientów.  

Jako konstruktor rozwiązań możesz użyć szablonu aplikacji monitorowania stanu analizy W sklepie IoT Central do tworzenia kompleksowego rozwiązania. Szablon aplikacji umożliwia cyfrowe łączenie się z sklepem detalicznym i monitorowanie ich przy użyciu różnych rodzajów urządzeń czujników. Te urządzenia czujników generują dane telemetryczne, które można przekształcić w informacje biznesowe, pomagając sprzedawcy detalicznemu obniżyć koszty operacyjne i stworzyć wspaniałe środowisko dla swoich klientów.

Użyj szablonu aplikacji, aby:

* Podłącz różne czujniki IoT do wystąpienia aplikacji IoT Central.
* Monitoruj kondycję sieci czujników i zarządzaj nią, a także wszystkimi urządzeniami bramy w środowisku.
* Tworzenie reguł niestandardowych wokół warunków środowiskowych w sklepie, aby wyzwolić alerty dla menedżerów sklepów.
* Przekształć warunki środowiskowe w sklepie w szczegółowe informacje, których zespół sklepu detalicznego może wykorzystać do poprawy jakości obsługi klienta.
* Eksportuj zagregowane informacje o istniejących lub nowych aplikacjach biznesowych, aby dostarczać przydatnych i terminowych informacji pracownikom sieci sprzedaży.

Szablon aplikacji zawiera zestaw szablonów urządzeń i używa zestawu symulowanych urządzeń do wypełniania pulpitu nawigacyjnego. 

Poniższy zrzut ekranu przedstawia gotowy pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny można w pełni dostosować, aby spełnić określone wymagania dotyczące rozwiązań: 

> [!div class="mx-imgBorder"]
> ![Monitorowanie stanu analizy w sklepie](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Aby dowiedzieć się więcej, zobacz Tworzenie aplikacji analitycznej w sklepie w samouczku [Azure IoT Central.](./tutorial-in-store-analytics-create-app.md)

## <a name="in-store-analytics---checkout"></a>Analityka w sklepie - kasa

Dla niektórych sprzedawców detalicznych, doświadczenie kasowe w ich sklepach jest kluczowym wyróżnikiem od ich konkurentów. Sprzedawcy detaliczni chcą zapewnić bezproblemowe zakupy w swoich sklepach, aby zachęcić klientów do powrotu.  

Jako konstruktor rozwiązań możesz użyć szablonu aplikacji do realizacji transakcji w sklepie IoT Central w sklepie, aby utworzyć rozwiązanie, które dostarcza pracownikom sklepu szczegółowe informacje z całej strefy realizacji transakcji w sklepie. Na przykład czujniki mogą dostarczać informacji o długości kolejek i średnim czasie oczekiwania dla każdego pasa realizacji transakcji.

Użyj szablonu aplikacji, aby:

* Podłącz różne czujniki IoT do wystąpienia aplikacji IoT Central.
* Monitoruj kondycję sieci czujników i zarządzaj nią, a także wszystkimi urządzeniami bramy w środowisku.
* Tworzenie niestandardowych reguł wokół warunku wyewidencjonowania w sklepie, aby wyzwolić alerty dla pracowników sieci sprzedaży.
* Przekształć warunki realizacji transakcji w sklepie w szczegółowe informacje, których zespół sklepu detalicznego może użyć do poprawy jakości obsługi klienta.
* Eksportuj zagregowane informacje o istniejących lub nowych aplikacjach biznesowych, aby dostarczać przydatnych i terminowych informacji pracownikom sieci sprzedaży.

Szablon aplikacji zawiera zestaw szablonów urządzeń i używa zestawu symulowanych urządzeń do wypełniania pulpitu nawigacyjnego danymi o obłożeniu pasa ruchu. 

Poniższy zrzut ekranu przedstawia gotowy pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny można w pełni dostosować, aby spełnić określone wymagania dotyczące rozwiązań: 

> [!div class="mx-imgBorder"]
> ![Kasa analityczna w sklepie](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Aby dowiedzieć się więcej, zobacz Tworzenie aplikacji analitycznej w sklepie w samouczku [Azure IoT Central.](./tutorial-in-store-analytics-create-app.md)

## <a name="smart-inventory-management"></a>Inteligentne zarządzanie magazynem

Zapasy to zapas towarów, które posiada detalista. Zarządzanie zapasami ma kluczowe znaczenie dla zapewnienia, że odpowiedni produkt znajduje się we właściwym miejscu i czasie. Detalista musi zrównoważyć koszty przechowywania zbyt dużej ilości zapasów z kosztami braku wystarczającej ilości zapasów, aby zaspokoić popyt.

Dane IoT generowane za pomocą znaczników, sygnalizatorów i kamer do identyfikacji radiowej (RFID) zapewniają możliwości usprawnienia procesów zarządzania zapasami. Dane telemetryczne zebrane z czujników i urządzeń IoT można łączyć z innymi źródłami danych, takimi jak informacje o pogodzie i ruchu drogowym w chmurowych systemach analizy biznesowej.

Korzyści płynące z inteligentnego zarządzania zapasami obejmują:

* Zmniejszenie ryzyka braku zapasów i zapewnienie pożądanego poziomu obsługi klienta. 
* Dogłębna analiza i wgląd w dokładność zapasów w czasie zbliżonym do rzeczywistego.
* Narzędzia ułatwiające podjęcie decyzji o odpowiedniej ilości zapasów do przechowywania w celu realizacji zamówień klientów.

Ten szablon aplikacji koncentruje się na łączności urządzenia oraz konfiguracji i zarządzaniu urządzeniami czytnika RFID i Bluetooth o niskiej energii (BLE).

Poniższy zrzut ekranu przedstawia gotowy pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny można w pełni dostosować, aby spełnić określone wymagania dotyczące rozwiązań:

> [!div class="mx-imgBorder"]
> ![Panel zarządzania inteligentnymi zapasami](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Aby dowiedzieć się więcej, zobacz [Wdrażanie i przechodzinie przez samouczek szablonu aplikacji do zarządzania inteligentnymi zapasami.](./tutorial-iot-central-smart-inventory-management.md)

## <a name="micro-fulfillment-center"></a>Centrum mikrorealizacji

W coraz bardziej konkurencyjnym krajobrazie detalicznym sprzedawcy detaliczni stale znajdują się pod presją, aby wypełnić lukę między popytem a realizacją. Nowy trend, który pojawił się w celu zaspokojenia rosnącego popytu konsumentów jest do domu zapasów w pobliżu klientów końcowych i sklepów, które odwiedzają.

Szablon aplikacji Centrum mikrorealizacji IoT Central umożliwia konstruktorom rozwiązań monitorowanie i zarządzanie wszystkimi aspektami ich w pełni zautomatyzowanych centrów realizacji zamówień. Szablon zawiera zestaw symulowanych czujników monitorowania stanu i nośników robotów w celu przyspieszenia procesu opracowywania rozwiązań. Te urządzenia czujnikowe przechwytują znaczące sygnały, które można przekształcić w informacje biznesowe, umożliwiając sprzedawcom detalicznym obniżenie kosztów operacyjnych i tworzenie środowisk dla swoich klientów.

Szablon aplikacji umożliwia: 

- Bezproblemowe łączenie różnych rodzajów czujników IoT, takich jak roboty lub czujniki monitorowania stanu, z wystąpieniem aplikacji IoT Central.
- Monitoruj kondycję sieci czujników i wszystkich urządzeń bramy w środowisku i zarządzaj nią.
- Tworzenie reguł niestandardowych wokół warunków środowiskowych w centrum realizacji, aby wyzwolić odpowiednie alerty.
- Przekształć warunki środowiskowe w centrum realizacji zamówień w informacje, które mogą być wykorzystane przez zespół magazynu detalicznego.
- Eksportuj zagregowane informacje o istniejących lub nowych aplikacjach biznesowych z korzyścią dla pracowników handlu detalicznego.

Poniższy zrzut ekranu przedstawia gotowy pulpit nawigacyjny w szablonie aplikacji. Pulpit nawigacyjny można w pełni dostosować, aby spełnić określone wymagania dotyczące rozwiązań:

> [!div class="mx-imgBorder"]
> ![Centrum mikrorealizacji](./media/overview-iot-central-retail/MFC-Dashboard.png)

Aby dowiedzieć się więcej, zobacz [Wdrażanie i przejdź przez samouczek szablonu aplikacji centrum mikrorealizacji.](./tutorial-micro-fulfillment-center.md)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć tworzenie rozwiązania dla handlu detalicznego:

* Wprowadzenie do [tworzenia aplikacji analitycznej w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app.md) samouczka, który przeprowadzi Cię przez sposób tworzenia rozwiązania za pomocą jednego z szablonów aplikacji analitycznych w sklepie.
* [Wdrażanie i przechodzenie przez szablon połączonej aplikacji logistycznej](./tutorial-iot-central-connected-logistics.md).
* [Wdrażanie i przechodzenie przez szablon aplikacji centrum dystrybucji cyfrowej](./tutorial-iot-central-digital-distribution-center.md).
* [Wdrażanie i przechodzenie przez szablon aplikacji do zarządzania inteligentnymi zapasami](./tutorial-iot-central-smart-inventory-management.md).
* [Wdrażanie i przechodzenie przez szablon aplikacji centrum mikrorealizacji](./tutorial-micro-fulfillment-center.md).
* Dowiedz się więcej o IoT Central w [przeglądzie IoT Central](../preview/overview-iot-central.md).
