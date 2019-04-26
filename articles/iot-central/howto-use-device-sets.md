---
title: Użyj urządzenia ustawia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Uprawnienia operatora sposób używania urządzenia ustawia się w aplikacji usługi Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: a28cf68eb449b563d93a139b830752748c448dd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517321"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Za pomocą zestawów urządzeń w aplikacji usługi Azure IoT Central

W tym artykule opisano, jak to zrobić, jako operator używanie urządzenia ustawia w aplikacji usługi Azure IoT Central.

Zestaw urządzeń znajduje się lista urządzeń, które są zgrupowane razem, ponieważ wszystkie spełniają pewne określone kryteria. Urządzenia, ustawia pomocy zarządzania, wizualizowanie i analizowanie urządzeń na dużą skalę za pomocą grupowania urządzeń na mniejsze, logiczne grupy. Na przykład utworzyć listę wszystkich urządzeń klimatyzacyjne w Seattle, aby umożliwić mu Seattle i Znajdź wszystkie urządzenia, dla których obsługi administracyjnej jest odpowiedzialna. W tym artykule pokazano, jak utworzyć i skonfigurować zestawy urządzeń.

## <a name="create-a-device-set"></a>Tworzenie zestawu urządzeń

Aby utworzyć zestaw urządzeń:

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz **+ nowe**.

    ![Nowy zestaw urządzeń](media/howto-use-device-sets/image1.png)

1. Zapewniają twoim zestawie urządzeń nazwę, która jest unikatowa w obrębie całej aplikacji. Można również dodać opis. Zestaw urządzeń może zawierać tylko urządzenia z szablonu pojedyncze urządzenie. Wybierz szablon urządzeń do użycia dla tego zestawu.

1. Utwórz zapytanie, aby zidentyfikować urządzenia dla urządzenia ustawiona przez wybranie właściwości, operator porównania i wartości. Możesz dodać wiele zapytań i urządzeń, które spełniają **wszystkich** kryteria są umieszczane w zestawie urządzeń. Zestaw urządzeń, tworzonych jest dostępny dla każdego, kto ma dostęp do aplikacji, dzięki czemu każdy wyświetlanie, modyfikowanie lub usuwanie zestawu urządzeń.

    ![Zapytanie zestawu urządzeń](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Zestaw urządzenia jest zapytanie dynamiczne. Za każdym razem, gdy możesz wyświetlić listę urządzeń, na liście mogą istnieć różnych urządzeń. Lista zależy od tego, które urządzenia obecnie spełniają kryteria zapytania.

1. Wybierz pozycję **Zapisz**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurowanie pulpitu nawigacyjnego dla zestawu urządzeń

Po utworzeniu zestawu urządzeń, można skonfigurować jej **pulpit nawigacyjny**. **Pulpit nawigacyjny** jest strona główna, w których można umieścić obrazy i łącza. Można również dodać siatki, które listy urządzeń w zestawie urządzeń.

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz zestaw Twojego urządzenia.

1. Wybierz kartę **Pulpit nawigacyjny**.

1. Wybierz pozycję **Edit** (Edytuj).

    ![Tryb projektowania na](media/howto-use-device-sets/image3.png)

1. Aby dowiedzieć się, jak dodawanie obrazu, zobacz [przygotowywania i przekazywania obrazów do usługi Azure IoT Central aplikacji](howto-prepare-images.md).

1. Dodaj Kafelek łącza:
    1. Wybierz **łącze** w okienku po prawej stronie.
    1. Nadaj link **tytuł**.
    1. Wybierz adres URL do otwarcia po wybraniu linku.
    1. Nadaj link opis, który pokazuje poniżej **tytuł**.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz łącze](media/howto-use-device-sets/image7.png)

    1. Umożliwia przenoszenie i zmiana rozmiaru kafelka łącze na **pulpit nawigacyjny**.

1. Dodać siatkę. Siatka jest spis urządzeń na urządzeniu, ustawić za pomocą wybrane kolumny.
    1. Wybierz **siatki** w okienku po prawej stronie.
    1. Nadaj swojej siatce **tytuł**.
    1. Wybierz kolumny do wyświetlenia, wybierając **Dodaj/Usuń**. W panelu, które się pojawi wybierz kolumny, które mają być pokazywane i kliknij strzałkę w prawo, aby go zaznaczyć.
    1. Wybierz pozycję **OK**.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz siatki](media/howto-use-device-sets/image9.png)

    1. Przeciągnij i upuść na siatkę, aby umieścić na **pulpit nawigacyjny**.

        > [!NOTE]
        > Można dodać wiele obrazów, łączy i siatki.
  
    1. Wybierz pozycję **Done** (Gotowe).

### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Konfigurowanie Mapa lokalizacji w urządzeniu ustawia pulpitu nawigacyjnego

Możesz dodać mapę lokalizacji, aby wizualizować lokalizacji urządzenia zestawy w mapie.

Aby dodać mapę lokalizacji na urządzeniu ustawia pulpit nawigacyjny, musi mieć właściwość skonfigurowanej lokalizacji w szablonie urządzenia, zobacz [utworzyć właściwość lokalizacji obsługiwane przez usługi Azure Maps](howto-set-up-template.md).

1. Na pulpicie nawigacyjnym z zestawu urządzeń wybierz mapę z biblioteki.
2. Nadaj tytuł, a następnie wybierz właściwość location, wcześniej skonfigurowane w ramach właściwości urządzenia.
3. Zapisz, aby zobaczyć mapę kafelka, wyświetlanie lokalizacji urządzenia w zestawie urządzeń.
4. Teraz gdy operator widoki urządzenie ustawia pulpitu nawigacyjnego, operator zobaczyć wszystkie Kafelki, które zostały skonfigurowane, włącznie z lokalizacją mapy w celu wizualizowania lokalizacji urządzenia w skrócie!

> [!NOTE]
> Można zmienić rozmiar mapy rozmiarowi żądaną. Wybieranie numeru pin na mapie Wyświetla informacje o urządzeniu, nazwę i lokalizację. Możesz wybrać okno podręczne, aby przejść do strony właściwości urządzenia.

## <a name="configure-the-list-for-your-device-set"></a>Konfiguruj listę zestawu urządzeń

Po utworzeniu zestawu urządzeń, można skonfigurować **listy**. **Listy** pokazuje wszystkie urządzenia na urządzeniu ustawione w tabeli z kolumnami, możesz wybrać.

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz **listy** kartę.

1. Wybierz **opcje kolumny**.

    ![Opcje kolumny](media/howto-use-device-sets/image11.png)

1. Wybierz kolumny do wyświetlenia, wybierając kolumnę, którą chcesz wyświetlić i wybierając strzałkę w prawo, aby go zaznaczyć.

    ![Wybierz kolumnę](media/howto-use-device-sets/image12.png)

1. Wybierz pozycję **OK**.

## <a name="analytics"></a>Analiza

Analiza w zestawy urządzeń jest taka sama jak na karcie analiza główne menu nawigacji po lewej stronie. Temat można znaleźć więcej informacji na temat analizy w artykule na [sposób tworzenia analizy](howto-use-device-sets.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak użyć zestawów urządzenia w aplikacji usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak utworzyć zasady telemetrii](howto-create-telemetry-rules.md)
