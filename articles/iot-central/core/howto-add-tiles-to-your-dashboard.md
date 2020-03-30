---
title: Dodawanie kafelków do pulpitu nawigacyjnego | Dokumenty firmy Microsoft
description: Jako konstruktor dowiedz się, jak skonfigurować domyślny pulpit nawigacyjny aplikacji Usługi Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 49b41715d95a5f210e6e70faf09aa016d1478728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158783"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego aplikacji

Pulpit **nawigacyjny** to strona, która ładuje się, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdź do adresu URL aplikacji. Jeśli aplikacja została utworzona z jednego z **szablonów aplikacji,** aplikacja będzie miała wstępnie zdefiniowany pulpit nawigacyjny do uruchomienia. Jeśli aplikacja została utworzona z szablonu **aplikacji Starsza wersja** aplikacji, pulpit nawigacyjny będzie pusty do uruchomienia.

> [!NOTE]
> Użytkownicy mogą [tworzyć wiele pulpitów nawigacyjnych](howto-create-personal-dashboards.md) oprócz domyślnego pulpitu nawigacyjnego aplikacji. Te pulpity nawigacyjne mogą być osobiste tylko dla użytkownika lub udostępniane wszystkim użytkownikom aplikacji. 

## <a name="add-tiles"></a>Dodaj kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie szablonu **aplikacji niestandardowej.** Aby dostosować domyślny pulpit nawigacyjny aplikacji, wybierz pozycję **Edytuj** w lewym górnym rogu strony.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny dla aplikacji opartych na szablonie "Przykładowy contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Wybranie opcji **Edytuj** powoduje otwarcie panelu biblioteki pulpitu nawigacyjnego. Biblioteka zawiera kafelki i elementy pierwotne pulpitu nawigacyjnego, których można użyć do dostosowania pulpitu nawigacyjnego.

> [!div class="mx-imgBorder"]
> ![Biblioteka pulpitu nawigacyjnego](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Na przykład można dodać kafelek **Telemetrii** dla bieżącej temperatury urządzenia. W tym celu:
1. Wybieranie **szablonu urządzenia**
1. Wybierz **wystąpienie urządzenia** dla urządzenia, które chcesz wyświetlić na kafelku pulpitu nawigacyjnego. Następnie zostanie wyświetlona lista właściwości urządzenia, które mogą być używane na kafelku.
1. Aby utworzyć kafelek na pulpicie nawigacyjnym, kliknij **pozycję Temperatura** i przeciągnij go do obszaru pulpitu nawigacyjnego. Możesz również kliknąć pole wyboru obok **pozycji Temperatura** i kliknąć pozycję **Połącz**. Poniższy zrzut ekranu przedstawia wybranie szablonu urządzenia i wystąpienia urządzenia, a następnie utworzenie kafelka telemetrii temperatury na pulpicie nawigacyjnym.
1. Wybierz **pozycję Zapisz** w lewym górnym rogu, aby zapisać kafelek na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> ![Formularz "Konfigurowanie szczegółów urządzenia" ze szczegółami ustawień i właściwości](media/howto-add-tiles-to-your-dashboard/device-details.png)

Teraz, gdy operator wyświetla domyślny pulpit nawigacyjny aplikacji, zobaczy nowy kafelek z **temperaturą** dla urządzenia. Każdy kafelek ma wstępnie wybrany wykres, wykres itp., który będzie wyświetlany podczas tworzenia kafelka. Użytkownicy mogą jednak edytować i zmieniać tę wizualizację. 

> [!div class="mx-imgBorder"]
> ![Karta "Pulpit nawigacyjny" z wyświetlonymi ustawieniami i właściwościami kafelka](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Edytowanie kafelków

Aby edytować kafelek na pulpicie nawigacyjnym, najpierw kliknij pozycję **Edytuj** w lewym górnym rogu strony, który otworzy tryb edycji pulpitu nawigacyjnego i wszystkich jego kafelków. 

> [!div class="mx-imgBorder"]
> ![Ekran pulpitu nawigacyjnego z włączonym trybem edycji dla wybranego kafelka](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Następnie kliknij ikonę **Koła zębatego** w prawym górnym rogu kafelka, który chcesz edytować. Tutaj można edytować aspekty kafelka, w tym jego tytuł, jego wizualizację, agregację itp.

> [!div class="mx-imgBorder"]
> ![Rozwijane ustawienia agregacji kafelków](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Wizualizację wykresu można również zmienić, klikając ikonę **Linijka** na kafelku.

> [!div class="mx-imgBorder"]
> ![Rozwijane ustawienia wizualizacji kafelków](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Typy kafelków

W poniższej tabeli podsumowano użycie kafelków w usłudze Azure IoT Central:
 
| Kafelek | Pulpit nawigacyjny | Opis
| ----------- | ------- | ------- |
| Zawartość | Pulpity nawigacyjne zestawów aplikacji i urządzeń |Obsługiwane kafelki z funkcją Markdown to klikalne kafelki, które wyświetlają tekst nagłówka i opisu. Ten kafelek można również użyć jako kafelka łącza, aby umożliwić użytkownikowi przechodzenie do adresu URL związanego z aplikacją.|
| Image (Obraz) | Pulpity nawigacyjne zestawów aplikacji i urządzeń |Kafelki obrazów wyświetlają obraz niestandardowy i można je kliknąć. Użyj kafelka obrazu, aby dodać grafikę do pulpitu nawigacyjnego i opcjonalnie umożliwić użytkownikowi przejście do adresu URL odpowiedniego dla aplikacji.|
| Label | Pulpity nawigacyjne aplikacji |Kafelki etykiet wyświetlają niestandardowy tekst na pulpicie nawigacyjnym. Można wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać odpowiednie informacje do pulpitu nawigacyjnego, takie jak opisy, dane kontaktowe lub pomoc.|
| Mapa | Pulpity nawigacyjne zestawów aplikacji i urządzeń |Kafelki mapy wyświetlają lokalizację i stan urządzenia na mapie. Można na przykład wyświetlić, gdzie znajduje się urządzenie i czy jego wentylator jest włączony.|
| Wykres liniowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu liniowego wyświetlają wykres pomiaru zagregowanego dla urządzenia przez pewien okres czasu. Można na przykład wyświetlić wykres liniowy przedstawiający średnią temperaturę i ciśnienie urządzenia w ciągu ostatniej godziny.|
| Wykres słupkowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu słupkowego wyświetlają wykres pomiarów zagregowanych dla urządzenia przez pewien okres czasu. Można na przykład wyświetlić wykres słupkowy przedstawiający średnią temperaturę i ciśnienie urządzenia w ciągu ostatniej godziny.|
| Wykres kołowy | Pulpity nawigacyjne zestawów aplikacji i urządzeń |Kafelki wykresu kołowego wyświetlają wykres łącznych pomiarów dla urządzenia przez pewien okres czasu.|
| Mapa cieplna | Pulpity nawigacyjne zestawów aplikacji i urządzeń |Kafelki Mapy ciepła wyświetlają informacje o zestawie urządzeń, reprezentowane jako kolory.|
| Historia zdarzeń | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki Historia zdarzeń wyświetlają zdarzenia dla urządzenia w okresie czasu. Można go na przykład użyć do wyświetlenia wszystkich zmian temperatury dla urządzenia w ciągu ostatniej godziny.|
| Historia stanu | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki historii stanu wyświetlają wartości pomiarów dla okresu. Można go na przykład użyć do wyświetlenia wartości temperatury urządzenia w ciągu ostatniej godziny.|
| Wskaźnik KPI | Pulpity nawigacyjne aplikacji i urządzeń | Kafelki kluczowych wskaźników wydajności wyświetlają agregację danych telemetrycznych lub pomiar zdarzeń dla okresu. Można go na przykład użyć do pokazania maksymalnej temperatury osiągniętej dla urządzenia w ciągu ostatniej godziny.|
| Ostatnia znana wartość | Pulpity nawigacyjne aplikacji i urządzeń |Ostatnie znane kafelki wartości wyświetlają najnowszą wartość pomiaru danych telemetrycznych lub stanu. Na przykład można użyć tego kafelka do wyświetlania najnowszych pomiarów temperatury, ciśnienia i wilgotności dla urządzenia.|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować domyślny pulpit nawigacyjny aplikacji Usługi Azure IoT Central, możesz [dowiedzieć się, jak utworzyć osobisty pulpit nawigacyjny](howto-create-personal-dashboards.md).
