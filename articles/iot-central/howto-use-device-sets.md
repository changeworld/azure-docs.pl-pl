---
title: Użyj urządzenia ustawia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Uprawnienia operatora sposób używania urządzenia ustawia się w aplikacji usługi Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: e1e7b91e0808b9e23e653acd43b95f24a46c7d27
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503212"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Za pomocą zestawów urządzeń w aplikacji usługi Azure IoT Central

W tym artykule opisano, jak to zrobić, jako operator używanie urządzenia ustawia w aplikacji usługi Azure IoT Central.

Zestaw urządzeń znajduje się lista urządzeń, które są zgrupowane razem, ponieważ spełniają pewne określone kryteria. Urządzenia, ustawia pomocy zarządzania, wizualizowanie i analizowanie urządzeń na dużą skalę za pomocą grupowania urządzeń na mniejsze, logiczne grupy. Na przykład można utworzyć zestawu, aby wyświetlić listę wszystkich urządzeń klimatyzacyjne w Seattle, aby włączyć technika odnaleźć urządzenia, dla których są one odpowiedzialne urządzenia. W tym artykule pokazano, jak utworzyć i skonfigurować zestawy urządzeń.

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

Po utworzeniu zestawu urządzeń, można skonfigurować jej **pulpit nawigacyjny**. **Pulpit nawigacyjny** jest strona główna, w którym umieszcza się obrazy i łącza. Można również dodać siatki, które listy urządzeń w zestawie urządzeń.

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz zestaw Twojego urządzenia.

1. Wybierz kartę **Pulpit nawigacyjny** .

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
    1. Wybierz **OK**.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz siatki](media/howto-use-device-sets/image9.png)

    1. Przeciągnij i upuść na siatkę, aby umieścić na **pulpit nawigacyjny**.

        > [!NOTE]
        > Można dodać wiele obrazów, łączy i siatki.
  
    1. Wybierz pozycję **Done** (Gotowe).

Aby dowiedzieć się więcej o sposobie używania fragmentacji w usłudze Azure IoT Central, zobacz [Użyj Kafelki pulpitu nawigacyjnego](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Skonfiguruj mapę lokalizacji na pulpicie nawigacyjnym zestawy urządzeń

Można dodać mapy w celu wizualizowania lokalizacji urządzenia w zestawie urządzeń.

Aby dodać mapę, aby urządzenie ustawia pulpitu nawigacyjnego, należy skonfigurować pomiar lokalizacji lub właściwość location w szablonie urządzenia. Aby dowiedzieć się więcej, zobacz [tworzenie miary lokalizacji](howto-set-up-template.md) lub [utworzyć właściwość lokalizacji](howto-set-up-template.md).

1. Na urządzeniu z systemem ustaw **pulpit nawigacyjny**, wybierz opcję **mapy** z biblioteki.
2. Dodaj tytuł, a następnie wybierz lokalizację pomiar lub właściwości, które zostały wcześniej skonfigurowane.
3. Wybierz **Zapisz** i Kafelek mapy Wyświetla ostatnie lokalizacje znanych urządzeń w zestawie urządzeń.
4. Operator operator przegląda pulpit nawigacyjny zestawy urządzeń, widzi wszystkie Kafelki, które zostały skonfigurowane, w tym mapę lokalizacji.

Można zmienić rozmiar kafelka mapy na pulpicie nawigacyjnym. Wybieranie numeru pin na mapie Wyświetla informacje o urządzeniu, nazwę i lokalizację. Wybierz okno podręczne, aby przejść do strony właściwości urządzenia.

## <a name="configure-the-list-for-your-device-set"></a>Konfiguruj listę zestawu urządzeń

Po utworzeniu zestawu urządzeń, można skonfigurować **listy**. **Listy** pokazuje wszystkie urządzenia na urządzeniu ustawione w tabeli z kolumnami, możesz wybrać.

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz **listy** kartę.

1. Wybierz **opcje kolumny**.

    ![Opcje kolumny](media/howto-use-device-sets/image11.png)

1. Wybierz kolumny do wyświetlenia, wybierając kolumnę, którą chcesz wyświetlić i wybierając strzałkę w prawo, aby go zaznaczyć.

    ![Wybierz kolumnę](media/howto-use-device-sets/image12.png)

1. Wybierz **OK**.

## <a name="analytics"></a>Analiza

Analiza w zestawy urządzeń jest taka sama jak na karcie analiza główne menu nawigacji po lewej stronie. Temat można znaleźć więcej informacji na temat analizy w artykule na [sposób tworzenia analizy](howto-use-device-sets.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak użyć zestawów urządzenia w aplikacji usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak utworzyć zasady telemetrii](howto-create-telemetry-rules.md)
