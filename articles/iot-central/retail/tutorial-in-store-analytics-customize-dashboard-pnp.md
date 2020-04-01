---
title: Samouczek — dostosowywanie pulpitu nawigacyjnego operatora w usłudze Azure IoT Central
description: W tym samouczku pokazano, jak dostosować pulpit nawigacyjny operatora w aplikacji IoT Central i zarządzać urządzeniami.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: fea3440dd7c017b85d6fffa2ec0fe5d26cd98b97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022175"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Samouczek: dostosowywanie pulpitu nawigacyjnego operatora i zarządzanie urządzeniami w usłudze Azure IoT Central


W tym samouczku jako konstruktor dowiesz się, jak dostosować pulpit nawigacyjny operatora w aplikacji analitycznej usługi Azure IoT Central w sklepie. Operatorzy aplikacji mogą używać dostosowanego pulpitu nawigacyjnego do uruchamiania aplikacji i zarządzania podłączonymi urządzeniami.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Zmienianie nazwy pulpitu nawigacyjnego
> * Dostosowywanie kafelków obrazów na pulpicie nawigacyjnym
> * Rozmieszczanie kafelków w celu zmodyfikowania układu
> * Dodawanie kafelków telemetrycznych do warunków wyświetlania
> * Dodawanie kafelków właściwości w celu wyświetlenia szczegółów urządzenia
> * Dodawanie kafelków poleceń w celu uruchamiania poleceń

## <a name="prerequisites"></a>Wymagania wstępne

Konstruktor powinien ukończyć samouczek, aby utworzyć aplikację analizy usługi Azure IoT Central w sklepie i dodać urządzenia:

* [Tworzenie aplikacji analitycznej w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) (wymagane)

## <a name="change-the-dashboard-name"></a>Zmienianie nazwy pulpitu nawigacyjnego
Aby dostosować pulpit nawigacyjny operatora, należy edytować domyślny pulpit nawigacyjny w aplikacji. Ponadto można utworzyć dodatkowe nowe pulpity nawigacyjne. Pierwszym krokiem, aby dostosować pulpit nawigacyjny w aplikacji jest zmiana nazwy.

1. Przejdź do witryny [menedżera aplikacji usługi Azure IoT Central.](https://aka.ms/iotcentral)

1. Otwórz aplikację monitorowania stanu utworzoną w samouczku [Tworzenie aplikacji analitycznej w sklepie w usłudze Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

1. Wybierz **pozycję Edytuj** na pasku narzędzi pulpitu nawigacyjnego. W trybie edycji można dostosować wygląd, układ i zawartość pulpitu nawigacyjnego.

    ![Pulpit nawigacyjny edycji usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Opcjonalnie ukryj lewe okienko. Ukrycie lewego okienka zapewnia większy obszar roboczy do edycji pulpitu nawigacyjnego.

1. Wprowadź przyjazną nazwę pulpitu nawigacyjnego w **nazwie pulpitu nawigacyjnego.** W tym samouczku użyto fikcyjnej firmy o nazwie Contoso, a przykładową nazwą pulpitu nawigacyjnego jest *pulpit nawigacyjny contoso*. 

1. Wybierz **pozycję Zapisz**. Zmiany są zapisywane na pulpicie nawigacyjnym, a tryb edycji jest wyłączony.

    ![Nazwa pulpitu nawigacyjnego zmiany usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Dostosowywanie kafelków obrazów na pulpicie nawigacyjnym
Pulpit nawigacyjny aplikacji usługi Azure IoT Central składa się z co najmniej jednego kafelka. Kafelek jest prostokątnym kontenerem do wyświetlania zawartości na pulpicie nawigacyjnym. Różne typy zawartości są kojarzone z kafelkami, a kafelki przeciąganie, upuszczanie i rozmiary w celu dostosowania układu pulpitu nawigacyjnego. Istnieje kilka typów kafelków do wyświetlania zawartości. Kafelki obrazów zawierają obrazy i można dodać adres URL, który umożliwia użytkownikom klikanie obrazu. Kafelki etykiet wyświetlają zwykły tekst. Kafelki znaczników zawierają sformatowaną zawartość i umożliwiają ustawienie obrazu, adresu URL, tytułu i kodu znaczników renderowanego w formacie HTML. Dane telemetryczne, właściwości lub kafelki poleceń wyświetlają dane specyficzne dla urządzenia. 

W tej sekcji dowiesz się, jak dostosować kafelki obrazów na pulpicie nawigacyjnym.

Aby dostosować kafelek obrazu, na który znajduje się obraz marki na pulpicie nawigacyjnym:

1. Wybierz **pozycję Edytuj** na pasku narzędzi pulpitu nawigacyjnego. 

1. Wybierz **pozycję Konfiguruj** na kafelku obrazu, na który jest wyświetlany obraz marki Northwind. 

    ![Obraz marki edycji usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. Zmień **tytuł**. Tytuł pojawia się, gdy użytkownik najedzie kursorem na obraz.

1. Wybierz **opcję Obraz**. Zostanie otwarte okno dialogowe umożliwiające przesłanie obrazu niestandardowego. 

1. Opcjonalnie określ adres URL obrazu.

1. Wybierz **pozycję Aktualizuj konfigurację**. Przycisk **Aktualizuj konfigurację** zapisuje zmiany na pulpicie nawigacyjnym i pozostawia włączony tryb edycji.

    ![Obraz marki zapisywania konta Usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Opcjonalnie wybierz pozycję **Konfiguruj** na kafelku **zatytułowanym Dokumentacja**i określ adres URL zawartości pomocy technicznej. 

Aby dostosować kafelek obrazu, który wyświetla mapę stref czujników w sklepie:

1. Wybierz **pozycję Konfiguruj** na kafelku obrazu, na który wyświetlana jest domyślna mapa strefy sklepu. 

1. Wybierz **pozycję Obraz**i użyj okna dialogowego, aby przesłać niestandardowy obraz mapy strefy sklepu. 

1. Wybierz **pozycję Aktualizuj konfigurację**.

    ![Mapa magazynu zapisu usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    Przykładowa mapa sklepu Contoso przedstawia cztery strefy: dwie strefy kasy, strefę odzieży i higieny osobistej oraz strefę artykułów spożywczych i delikatesów. W tym samouczku skojarzysz czujniki z tymi strefami, aby zapewnić dane telemetryczne.

    ![Strefy sklepu Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Wybierz **pozycję Zapisz**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Rozmieszczanie kafelków w celu zmodyfikowania układu
Kluczowym krokiem w dostosowywaniu pulpitu nawigacyjnego jest zmiana rozmieszczenia kafelków w celu utworzenia użytecznego widoku. Operatorzy aplikacji używają pulpitu nawigacyjnego do wizualizacji danych telemetrycznych urządzenia, zarządzania urządzeniami i monitorowania warunków w magazynie. Usługa Azure IoT Central upraszcza zadanie konstruktora aplikacji podczas tworzenia pulpitu nawigacyjnego. Tryb edycji pulpitu nawigacyjnego umożliwia szybkie dodawanie, przenoszenie, zmiana rozmiaru i usuwanie kafelków. Szablon aplikacji **analizy w sklepie — wyewidencjonowanie** upraszcza również zadanie tworzenia pulpitu nawigacyjnego. Zapewnia działający układ deski rozdzielczej, z podłączonymi czujnikami i kafelkami, które wyświetlają liczbę linii realizacji transakcji i warunki środowiskowe.

W tej sekcji można zmienić kolejność pulpitu nawigacyjnego w szablonie aplikacji **analizy w sklepie — wyewidencjonowywać,** aby utworzyć układ niestandardowy.

Aby usunąć kafelki, których nie zamierzasz używać w aplikacji:

1. Wybierz **pozycję Edytuj** na pasku narzędzi pulpitu nawigacyjnego. 

1. Wybierz **X Usuń,** aby usunąć następujące kafelki: **Powrót do wszystkich stref,** **Odwiedź pulpit nawigacyjny sklepu,** **Czas oczekiwania**i wszystkie trzy kafelki skojarzone z **wyewidencjonowanymi 3**. Pulpit nawigacyjny sklepu Contoso nie używa tych kafelków. 

    ![Kafelki usuwania usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Przewiń, aby wyświetlić pozostałe kafelki pulpitu nawigacyjnego.

1. Wybierz **X Usuń,** aby usunąć następujące kafelki: **Strefa wyewidencjonowania rozgrzewania,** **Strefa kasy schładzającej,** **Ustawienia czujnika obłożenia,** **Ustawienia czujnika termostatu**i **warunki otoczenia.** 

   ![Usługa Azure IoT Central usuwa pozostałe kafelki](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Wybierz **pozycję Zapisz**. Usunięcie nieużywanych kafelków zwalnia miejsce na stronie edycji i upraszcza widok pulpitu nawigacyjnego dla operatorów.

1. Wyświetlanie zmian na pulpicie nawigacyjnym.

   ![Usługa Azure IoT Central po usunięciu kafelków](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Po usunięciu nieużywanych kafelków zmień rozmieszczenie pozostałych kafelków, aby utworzyć zorganizowany układ. Nowy układ zawiera miejsce na kafelki dodawanych w późniejszym kroku.

Aby zmienić rozmieszczenie pozostałych kafelków:

1. Wybierz pozycję **Edit** (Edytuj).

1. Wybierz kafelek **oprogramowania układowego obłożenia** i przeciągnij go na prawo od kafelka baterii **obłożenia.**

1. Wybierz **płytkę oprogramowania układowego termostatu** i przeciągnij ją w prawo od płytki baterii **termostatu.**

1. Wybierz **pozycję Zapisz**.

1. Wyświetlanie zmian układu. 

    ![Kafelki baterii oprogramowania układowego Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Dodawanie kafelków telemetrycznych do warunków wyświetlania
Po dostosowaniu układu pulpitu nawigacyjnego można dodać kafelki do pokazania danych telemetrycznych. Aby utworzyć kafelek telemetrii, wybierz szablon urządzenia i wystąpienie urządzenia, a następnie wybierz dane telemetryczne specyficzne dla urządzenia do wyświetlenia na kafelku. Szablon aplikacji **analizy w sklepie — wyewidencjonowanie** zawiera kilka kafelków telemetrycznych na pulpicie nawigacyjnym. Cztery kafelki w dwóch strefach kasy wyświetlają dane telemetryczne z symulowanego czujnika obłożenia. Kafelek **Ruch osób** pokazuje liczby w dwóch strefach wyewidencjonowyw. 

W tej sekcji można dodać dwa kafelki telemetrii, aby wyświetlić dane telemetryczne środowiska z czujników RuuviTag dodane w [tworzenie aplikacji analitycznej w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) samouczka. 

Aby dodać płytki do wyświetlania danych środowiskowych z czujników RuuviTag:

1. Wybierz pozycję **Edit** (Edytuj).

1. Wybierz `RuuviTag` pozycję na liście **Szablon urządzenia.** 

1. Wybierz **wystąpienie urządzenia** jednego z dwóch czujników RuuviTag. W przykładowym magazynie Contoso wybierz, `Zone 1 Ruuvi` aby utworzyć kafelek telemetrii dla strefy 1. 

1. Zaznacz `Relative humidity` `temperature` i na liście **Telemetria.** Są to elementy telemetryczne, które są wyświetlane dla każdej strefy na kafelku.

1. Wybierz **opcję Połącz**. 

    ![Usługa Azure IoT Central dodaje kafelek RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Pojawi się nowy kafelek, który wyświetla połączone dane telemetryczne wilgotności i temperatury dla wybranego czujnika. 

1. Wybierz **pozycję Konfiguruj** na nowym kafelku dla czujnika RuuviTag. 

1. Zmień **tytuł** na *środowisko strefy 1*. 

1. Wybierz **pozycję Aktualizuj konfigurację**.

1. Powtórz poprzednie kroki, aby utworzyć kafelek dla drugiego wystąpienia czujnika. Ustaw **środowisko Title** na *Strefę 2,* a następnie wybierz pozycję **Aktualizuj konfigurację.**

1. Przeciągnij kafelek o nazwie **Środowisko strefy 2** poniżej kafelka **oprogramowania układowego termostatu.** 

1. Przeciągnij kafelek o nazwie **Środowisko strefy 1** poniżej kafelka **Ruch osób.** 

1. Wybierz **pozycję Zapisz**. Pulpit nawigacyjny wyświetla dane telemetryczne strefy w dwóch nowych kafelkach.

    ![Azure IoT Central wszystkie kafelki RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Aby edytować kafelek **Ruch osób,** aby wyświetlić dane telemetryczne tylko dla dwóch stref wyewidencjonowywalenia:

1. Wybierz pozycję **Edit** (Edytuj). 

1. Wybierz **pozycję Konfiguruj** na kafelku **Ruch osób.**

1. W **telemetrii** wybierz **liczbę 1**, **zliczanie 2**i **liczbę 3**. 

1. Wybierz **pozycję Aktualizuj konfigurację**. Czyści istniejącą konfigurację na kafelku. 

1. Wybierz **pozycję Konfiguruj** ponownie na kafelku **Ruch osób.**

1. W **obszarze Telemetria** wybierz **liczbę 1**i **liczbę 2**. 

1. Wybierz **pozycję Aktualizuj konfigurację**. 

1. Wybierz **pozycję Zapisz**.  Zaktualizowany pulpit nawigacyjny wyświetla tylko liczbę dwóch stref realizacji transakcji, które są oparte na symulowanym czujniku obłożenia.

    ![Użytkownicy usługi Azure IoT Central ruchu dwa pasy ruchu](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Dodawanie kafelków właściwości w celu wyświetlenia szczegółów urządzenia
Operatorzy aplikacji używają pulpitu nawigacyjnego do zarządzania urządzeniami i monitorowania stanu. Dodaj kafelek dla każdego RuuviTag, aby umożliwić operatorom wyświetlanie wersji oprogramowania. 

Aby dodać kafelek właściwości dla każdego RuuviTag:

1. Wybierz pozycję **Edit** (Edytuj).

1. Wybierz `RuuviTag` pozycję na liście **Szablon urządzenia.** 

1. Wybierz **wystąpienie urządzenia** jednego z dwóch czujników RuuviTag. W przykładowym magazynie Contoso wybierz, `Zone 1 Ruuvi` aby utworzyć kafelek telemetrii dla strefy 1. 

1. Wybierz **właściwości > wersji oprogramowania**.

1. Wybierz **opcję Połącz**. 

1. Wybierz **pozycję Konfiguruj** na nowo utworzonym kafelku o **nazwie Wersja oprogramowania**. 

1. Zmień **tytuł** na *wersję oprogramowania Ruuvi 1*.

1. Wybierz **pozycję Aktualizuj konfigurację**. 

1. Przeciągnij kafelek o nazwie **Ruuv 1 wersja oprogramowania** poniżej **kafelka środowiska Strefa 1.**

1. Powtórz poprzednie kroki, aby utworzyć kafelek właściwości wersji oprogramowania dla drugiego RuuviTag. 

1. Wybierz **pozycję Zapisz**.  

    ![Kafelki właściwości Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Dodawanie kafelków poleceń w celu uruchamiania poleceń
Operatorzy aplikacji używają również pulpitu nawigacyjnego do zarządzania urządzeniami, uruchamiając polecenia. Do pulpitu nawigacyjnego można dodać kafelki poleceń, które będą wykonywać wstępnie zdefiniowane polecenia na urządzeniu. W tej sekcji należy dodać kafelek poleceń, aby umożliwić operatorom ponowne uruchomienie bramy Rigado. 

Aby dodać kafelek polecenia w celu ponownego uruchomienia bramy:

1. Wybierz pozycję **Edit** (Edytuj). 

1. Wybierz `C500` pozycję na liście **Szablon urządzenia.** Jest to szablon bramy Rigado C500. 

1. Wybierz wystąpienie bramy w **wystąpieniu urządzenia**.

1. Wybierz **polecenie > uruchom ponownie i** przeciągnij go na pulpit nawigacyjny obok mapy sklepu. 

1. Wybierz **pozycję Zapisz**. 

1. Wyświetl ukończony pulpit nawigacyjny contoso. 

    ![Pełne dostosowywanie pulpitu nawigacyjnego usługi Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Opcjonalnie wybierz kafelek **Uruchom ponownie,** aby uruchomić polecenie ponownego uruchomienia na bramie.

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Zmienianie nazwy pulpitu nawigacyjnego
* Dostosowywanie kafelków obrazów na pulpicie nawigacyjnym
* Rozmieszczanie kafelków w celu zmodyfikowania układu
* Dodawanie kafelków telemetrycznych do warunków wyświetlania
* Dodawanie kafelków właściwości w celu wyświetlenia szczegółów urządzenia
* Dodawanie kafelków poleceń w celu uruchamiania poleceń

Teraz, gdy dostosowano pulpit nawigacyjny w aplikacji analitycznej usługi Azure IoT Central w sklepie, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Eksportowanie danych i wizualizowanie szczegółowych informacji](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
