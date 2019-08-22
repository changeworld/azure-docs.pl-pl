---
title: Używanie zestawów urządzeń w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako operator, jak używać zestawów urządzeń w aplikacji IoT Central platformy Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 9576711c33979cef7e043c18ac3b56251dd8a806
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877341"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Korzystanie z zestawów urządzeń w aplikacji IoT Central platformy Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak za pomocą operatora korzystać z zestawów urządzeń w aplikacji IoT Central platformy Azure.

Zestaw urządzeń to lista urządzeń zgrupowanych ze sobą, ponieważ odpowiadają one określonym kryteriom. Zestawy urządzeń ułatwiają zarządzanie, wizualizowanie i analizowanie urządzeń w dużej skali przez grupowanie urządzeń w mniejsze, logiczne grupy. Na przykład możesz utworzyć zestaw urządzeń, aby wyświetlić listę wszystkich urządzeń z warunkiem klimatyzacyjnym w Seattle, aby umożliwić pracownikom znalezienie urządzeń, dla których są odpowiedzialni. W tym artykule opisano sposób tworzenia i konfigurowania zestawów urządzeń.

## <a name="create-a-device-set"></a>Tworzenie zestawu urządzeń

Aby utworzyć zestaw urządzeń:

1. Wybierz pozycję **Zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz pozycję **+ Nowy**.

    ![Nowy zestaw urządzeń](media/howto-use-device-sets/image1.png)

1. Nadaj urządzeniu nazwę, która jest unikatowa w całej aplikacji. Można również dodać opis. Zestaw urządzeń może zawierać tylko urządzenia z jednego szablonu urządzenia. Wybierz szablon urządzenia, który ma być używany dla tego zestawu.

1. Utwórz zapytanie, aby zidentyfikować urządzenia dla zestawu urządzeń, wybierając właściwość, operator porównania i wartość. Można dodać wiele zapytań i urządzeń, które spełniają **wszystkie** kryteria, są umieszczane w zestawie urządzeń. Utworzony zestaw urządzeń jest dostępny dla każdego, kto ma dostęp do aplikacji, więc każda osoba może wyświetlać, modyfikować lub usuwać zestaw urządzeń.

    ![Zapytanie dotyczące zestawu urządzeń](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Zestaw urządzeń jest kwerendą dynamiczną. Za każdym razem, gdy oglądasz listę urządzeń, na liście mogą znajdować się różne urządzenia. Lista zależy od tego, które urządzenia spełniają kryteria zapytania.

1. Wybierz polecenie **Zapisz**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurowanie pulpitu nawigacyjnego dla zestawu urządzeń

Po utworzeniu zestawu urządzeń można skonfigurować jego **pulpit nawigacyjny**. **Pulpit nawigacyjny** to Strona główna, w której umieszczasz obrazy i linki. Możesz również dodać siatki, które wyświetlają listę urządzeń w zestawie urządzeń.

1. Wybierz pozycję **Zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz zestaw urządzeń.

1. Wybierz kartę **Pulpit nawigacyjny** .

1. Wybierz pozycję **Edit** (Edytuj).

    ![Tryb projektowania na](media/howto-use-device-sets/image3.png)

1. Aby uzyskać informacje na temat dodawania obrazu, zobacz [Przygotowywanie i przekazywanie obrazów do aplikacji IoT Central platformy Azure](howto-prepare-images.md).

1. Dodawanie kafelka linku:
    1. Wybierz **łącze** w okienku po prawej stronie.
    1. Nadaj linkowi **tytuł**.
    1. Wybierz adres URL, który ma zostać otwarty w przypadku wybrania łącza.
    1. Nadaj linkowi opis widoczny pod tytułem.
    1. Wybierz polecenie **Zapisz**.

        ![Zapisz link](media/howto-use-device-sets/image7.png)

    1. Kafelek linku można przenieść i zmienić jego rozmiar na **pulpicie nawigacyjnym**.

1. Dodawanie siatki. Siatka to tabela urządzeń w zestawie urządzeń z wybranymi kolumnami.
    1. Wybierz pozycję **Siatka** w prawym okienku.
    1. Nadaj **tytuł**siatce.
    1. Wybierz kolumny, które mają być wyświetlane, wybierając pozycję **Dodaj/Usuń**. W panelu, który się pojawia, wybierz kolumnę, którą chcesz wyświetlić, a następnie wybierz strzałkę w prawo, aby ją zaznaczyć.
    1. Wybierz **OK**.
    1. Wybierz polecenie **Zapisz**.

        ![Zapisz siatkę](media/howto-use-device-sets/image9.png)

    1. Przeciągnij i upuść siatkę, aby ją umieścić na **pulpicie nawigacyjnym**.

        > [!NOTE]
        > Można dodać wiele obrazów, linków i siatek.
  
    1. Wybierz pozycję **Done** (Gotowe).

Aby dowiedzieć się więcej na temat używania kafelków w usłudze Azure IoT Central, zobacz [Używanie kafelków pulpitu nawigacyjnego](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Konfigurowanie mapy lokalizacji na pulpicie nawigacyjnym zestawów urządzeń

Można dodać mapę do wizualizacji lokalizacji urządzeń w zestawie urządzeń.

Aby dodać mapę do pulpitu nawigacyjnego zestawów urządzeń, musisz skonfigurować właściwość pomiaru lokalizacji lub lokalizacji w szablonie urządzenia. Aby dowiedzieć się więcej, zobacz [Tworzenie pomiaru lokalizacji](howto-set-up-template.md) lub [Tworzenie właściwości lokalizacji](howto-set-up-template.md).

1. Na **pulpicie nawigacyjnym**zestawu urządzeń wybierz pozycję **Mapuj** z biblioteki.
2. Dodaj tytuł i wybierz wcześniej skonfigurowaną miarę lokalizacji lub właściwość.
3. Wybierz pozycję **Zapisz** , a kafelek Mapa przedstawia ostatnie znane lokalizacje urządzeń w zestawie urządzeń.
4. Gdy operator wyświetla pulpit nawigacyjny zestawów urządzeń, jest on widoczny dla wszystkich skonfigurowanych kafelków, w tym mapy lokalizacji.

Możesz zmienić rozmiar kafelka mapy na pulpicie nawigacyjnym. Wybranie numeru PIN na mapie spowoduje wyświetlenie informacji o urządzeniu, nazwy i lokalizacji. Wybierz wyskakujące okienko, aby przejść do strony właściwości urządzenia.

## <a name="configure-the-list-for-your-device-set"></a>Skonfiguruj listę dla zestawu urządzeń

Po utworzeniu zestawu urządzeń można skonfigurować **listę**. **Lista** zawiera wszystkie urządzenia z zestawu urządzeń w tabeli z wybranymi kolumnami.

1. Wybierz pozycję **Zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz kartę **Lista** .

1. Wybierz **Opcje kolumn**.

    ![Opcje kolumn](media/howto-use-device-sets/image11.png)

1. Wybierz kolumny, które mają być wyświetlane, zaznaczając kolumnę, którą chcesz wyświetlić, i wybierając strzałkę w prawo, aby ją zaznaczyć.

    ![Wybierz kolumnę](media/howto-use-device-sets/image12.png)

1. Wybierz **OK**.

## <a name="analytics"></a>Analiza

Analiza w zestawach urządzeń jest taka sama jak główna Karta analityczna w menu nawigacji po lewej stronie. Więcej informacji na temat analiz można znaleźć w artykule na temat [tworzenia analiz](howto-use-device-sets.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać zestawów urządzeń w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetrii](howto-create-telemetry-rules.md)
