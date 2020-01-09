---
title: Dodawanie kafelków do pulpitu nawigacyjnego | Microsoft Docs
description: Jako Konstruktor można dowiedzieć się, jak skonfigurować domyślny pulpit nawigacyjny aplikacji platformy Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b13349ae4293f6377429e9dc72b6c2cb43f92348
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435128"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego aplikacji

**Pulpit nawigacyjny** to strona, która ładuje się, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdą do adresu URL aplikacji. Jeśli aplikacja została utworzona przy użyciu jednego z **szablonów aplikacji**, aplikacja będzie mieć wstępnie zdefiniowany pulpit nawigacyjny do uruchomienia. Jeśli aplikacja została utworzona za pomocą szablonu aplikacji ze **starszej wersji** , pulpit nawigacyjny będzie pusty do uruchomienia.

> [!NOTE]
> Oprócz domyślnego pulpitu nawigacyjnego aplikacji użytkownicy mogą [tworzyć wiele pulpitów nawigacyjnych](howto-create-personal-dashboards.md) . Te pulpity nawigacyjne mogą być osobiste tylko dla użytkownika lub udostępniane wszystkim użytkownikom aplikacji. 

## <a name="add-tiles"></a>Dodaj kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie **niestandardowego szablonu aplikacji** . Aby dostosować domyślny pulpit nawigacyjny dla aplikacji, wybierz pozycję **Edytuj** w lewym górnym rogu strony.

> [!div class="mx-imgBorder"]
> ![pulpit nawigacyjny dla aplikacji na podstawie szablonu "Przykładowa contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Wybranie pozycji **Edytuj** powoduje otwarcie panelu Biblioteka pulpitu nawigacyjnego. Biblioteka zawiera elementy pierwotne kafelków i pulpitów nawigacyjnych, których można użyć do dostosowania pulpitu nawigacyjnego.

> [!div class="mx-imgBorder"]
> ![](media/howto-add-tiles-to-your-dashboard/dashboard-library.png) Biblioteka pulpitu nawigacyjnego

Na przykład można dodać kafelek **telemetrii** dla bieżącej temperatury urządzenia. W tym celu:
1. Wybierz **szablon urządzenia**
1. Wybierz **wystąpienie urządzenia** dla urządzenia, które chcesz zobaczyć na kafelku pulpitu nawigacyjnego. Zostanie wyświetlona lista właściwości urządzenia, które mogą być używane na kafelku.
1. Aby utworzyć kafelek na pulpicie nawigacyjnym, kliknij pozycję **temperatura** i przeciągnij ją do obszaru pulpit nawigacyjny. Możesz również kliknąć pole wyboru obok pozycji **temperatura** , a następnie kliknąć przycisk **Połącz**. Poniższy zrzut ekranu przedstawia Wybieranie szablonu urządzenia i wystąpienia urządzenia, a następnie Tworzenie kafelka telemetrii na pulpicie nawigacyjnym.
1. Wybierz pozycję **Zapisz** w lewym górnym rogu, aby zapisać kafelek na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> !["Konfiguruj szczegóły urządzenia", podając szczegóły dotyczące ustawień i właściwości](media/howto-add-tiles-to-your-dashboard/device-details.png)

Teraz po wyświetleniu domyślnego pulpitu nawigacyjnego aplikacji przez operatora zostanie wyświetlony nowy kafelek z **temperaturą** urządzenia. Każdy kafelek ma wstępnie wybrany wykres, wykres itp., który będzie wyświetlany podczas tworzenia kafelka. Jednak użytkownicy mogą edytować i zmieniać tę wizualizację. 

> [!div class="mx-imgBorder"]
> ![kartę "pulpit nawigacyjny" z wyświetlanymi ustawieniami i właściwościami kafelka](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)


## <a name="edit-tiles"></a>Edytuj kafelki

Aby edytować kafelek na pulpicie nawigacyjnym, najpierw kliknij pozycję **Edytuj** w lewym górnym rogu strony, co spowoduje otwarcie trybu edycji pulpitu nawigacyjnego i wszystkich jego kafelków. 

> [!div class="mx-imgBorder"]
> ![ekranu pulpitu nawigacyjnego z aktywnym trybem edycji dla wybranego kafelka](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Następnie kliknij ikonę **koła zębatego** w prawym górnym rogu kafelka, który chcesz edytować. Tutaj możesz edytować aspekty kafelka, w tym jego tytuł, wizualizację, agregację itp.

> [!div class="mx-imgBorder"]
> Lista rozwijana ![dla ustawień agregacji kafelków](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Wizualizację wykresu można także zmienić, klikając ikonę **linijki** na kafelku.

> [!div class="mx-imgBorder"]
> Lista rozwijana ![dla ustawień wizualizacji kafelka](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Typy kafelków

Poniższa tabela zawiera podsumowanie użycia kafelków w usłudze Azure IoT Central:
 
| Tile | Pulpit nawigacyjny | Opis
| ----------- | ------- | ------- |
| Zawartość | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki obsługiwane przez promocji są kafelkami, które wyświetlają tekst nagłówka i opisu. Możesz również użyć tego kafelka jako kafelka linku, aby umożliwić użytkownikowi nawigację do adresu URL związanego z Twoją aplikacją.|
| Obraz | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki obrazu wyświetlają obraz niestandardowy i można go kliknąć. Za pomocą kafelka obrazu Dodaj grafikę do pulpitu nawigacyjnego i opcjonalnie Zezwól użytkownikowi na przejście do adresu URL istotnego dla aplikacji.|
| Etykieta | Pulpity nawigacyjne aplikacji |Kafelki etykiet wyświetlają niestandardowy tekst na pulpicie nawigacyjnym. Możesz wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać odpowiednie informacje do pulpitu nawigacyjnego, takie opisy, szczegóły kontaktu lub pomoc.|
| Mapa | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki mapy wyświetlają lokalizację i stan urządzenia na mapie. Można na przykład wyświetlić, gdzie znajduje się urządzenie i czy jego wentylator jest włączony.|
| Wykres liniowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu liniowego przedstawiają wykres zagregowanej miary dla urządzenia w danym okresie czasu. Na przykład można wyświetlić wykres liniowy, który pokazuje średnią temperaturę i ciśnienie urządzenia w ciągu ostatniej godziny.|
| Wykres słupkowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu słupkowego przedstawiają wykres zagregowanych pomiarów dla urządzenia w danym okresie czasu. Na przykład można wyświetlić wykres słupkowy, który pokazuje średnią temperaturę i ciśnienie urządzenia w ciągu ostatniej godziny.|
| Wykres kołowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu kołowego przedstawiają wykres zagregowanych pomiarów dla urządzenia w danym okresie czasu.|
| Mapa cieplna | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki mapy cieplnej wyświetlają informacje o zestawie urządzeń reprezentowane jako kolory.|
| Historia zdarzeń | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki historii zdarzeń wyświetlają zdarzenia dotyczące urządzenia w określonym czasie. Na przykład można użyć go do wyświetlenia wszystkich zmian temperatury urządzenia w ciągu ostatniej godziny.|
| Historia stanu | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki historii stanu wyświetlają wartości pomiarów dla danego przedziału czasu. Na przykład można użyć go do wyświetlenia wartości temperatury dla urządzenia w ciągu ostatniej godziny.|
| Wskaźnik KPI | Pulpity nawigacyjne aplikacji i urządzeń | Kafelki KPI przedstawiają zagregowane dane telemetryczne lub pomiary zdarzeń przez okres. Można na przykład użyć jej do wyświetlenia maksymalnej temperatury dla urządzenia w ciągu ostatniej godziny.|
| Ostatnia znana wartość | Pulpity nawigacyjne aplikacji i urządzeń |Na kafelkach Ostatnia znana wartość jest wyświetlana najnowsza wartość dla danych telemetrycznych lub stanu. Na przykład można użyć tego kafelka, aby wyświetlić najnowsze pomiary temperatury, ciśnienia i wilgotności dla urządzenia.|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować pulpit nawigacyjny domyślnego aplikacji platformy Azure IoT Central, możesz [dowiedzieć się, jak przygotować i przekazać obrazy](howto-prepare-images.md).