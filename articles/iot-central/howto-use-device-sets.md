---
title: Ustawia Użyj urządzenia w aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Jako operatora jak używać urządzenia ustawia w aplikacji Azure IoT centralnej.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: ef1fa64a276926a35dbf98646317bfe29200bb22
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261682"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Użyj zestawy urządzeń w aplikacji Azure IoT centralnej

W tym artykule opisano, jak to zrobić, jako operator do korzystania z urządzenia ustawia w aplikacji Microsoft Azure IoT centralnej.

Zestaw urządzenia jest listę urządzeń, które są zgrupowane razem, ponieważ wszystkie spełniają pewne określone kryteria. Urządzenie ustawia pozwalają na zarządzanie, zwizualizować i analizować urządzeń na dużą skalę, grupując urządzeń w mniejszych grup logicznych. Na przykład utworzyć listę wszystkich urządzeń klimatyzator w Seattle, aby umożliwić mu Seattle i Znajdź wszystkie urządzenia, dla których jest odpowiedzialna. W tym artykule przedstawiono sposób tworzenia i konfigurowania zestawy urządzeń.

## <a name="create-a-device-set"></a>Tworzenie zestawu urządzeń

Aby utworzyć zestaw urządzenia:

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Kliknij pozycję **+ Nowy**.

    ![Nowy zestaw urządzeń](media/howto-use-device-sets/image1.png)

1. Nadaj nazwę, która jest unikatowa w całej aplikacji zestawu urządzeń. Można również dodać opis. Zestaw urządzenia może zawierać tylko urządzenia z szablonu pojedyncze urządzenie. Wybierz szablon urządzenia, które ma być używany dla tego zestawu.

1. Utwórz zapytanie, aby zidentyfikować urządzenia urządzenia określone przez wybranie właściwości, operator porównania i wartości. Można dodać wiele zapytań i urządzeń, które spełniają **wszystkie** kryteria są umieszczane w zestawie urządzenia. Zestaw urządzenia, tworzonych jest dostępna dla każdego, kto ma dostęp do aplikacji, więc każda osoba, która można wyświetlić, zmodyfikować lub usunąć grupę urządzeń.

    ![Zapytanie zestawu urządzeń](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Zestaw urządzenia jest zapytaniem dynamicznych. Za każdym razem, możesz wyświetlić listę urządzeń, na liście mogą istnieć różnych urządzeń. Lista zależy od tego, które urządzenia obecnie spełniają kryteria zapytania.

1. Wybierz pozycję **Zapisz**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurowanie pulpitu nawigacyjnego dla zestawu urządzeń

Po utworzeniu zestawu urządzeń, można skonfigurować jego **pulpitu nawigacyjnego**. **Pulpitu nawigacyjnego** jest strony głównej, gdzie możesz umieścić obrazy i łącza. Można również dodać siatki, które listy urządzeń w zestawie urządzenia.

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz **pulpitu nawigacyjnego** kartę.

1. Włącz **trybu projektowania**.

    ![Tryb projektowania](media/howto-use-device-sets/image3.png)

1. Informacje o dodawaniu obrazu, zobacz [przygotowanie i przekazywanie obrazów do aplikacji Azure IoT centralnej](howto-prepare-images.md).

1. Dodaj kafelka łącza:
    1. Wybierz **łącze** w okienku po prawej stronie.

        ![Wybierz link](media/howto-use-device-sets/image6.png)

    1. Nadaj łącze **tytuł**.
    1. Wybierz adres URL do otwarcia po kliknięciu łącza.
    1. Wprowadź łącze opis pokazujący poniżej **tytuł**.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz łącze](media/howto-use-device-sets/image7.png)

    1. Można przenieść i zmień rozmiar kafelka łącza na **pulpitu nawigacyjnego**.

1. Dodaj siatki. Siatka jest spis urządzeń w urządzeniu ustawiony za pomocą wybrane kolumny.
    1. Wybierz **siatki** w okienku po prawej stronie.

        ![Wybierz siatki](media/howto-use-device-sets/image8.png)

    1. Nadaj siatka **tytuł**.
    1. Wybierz kolumny, które można wyświetlić, wybierając przycisk Ustawienia. W panelu, które pojawia się wybierz kolumny, które mają być wyświetlane i wybierz strzałkę w prawo, aby go wybrać.
    1. Wybierz pozycję **OK**.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz siatki](media/howto-use-device-sets/image9.png)

    1. Przeciągnij i upuść siatki umieścić je na **pulpitu nawigacyjnego**.

    > [!NOTE]
    > Można dodać wiele obrazów, łączy i siatki.
  
    1. Wyłącz **tryb projektowania**.

    ![Trybu projektowania wyłączone](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Konfigurowanie Mapa lokalizacji w urządzeniu ustawia pulpitu nawigacyjnego 
Można dodać mapy lokalizacji w celu wizualizowania lokalizacji urządzenia ustawia na mapie. 

Aby można było dodać Mapa lokalizacji urządzeniu ustawia pulpitu nawigacyjnego musi mieć właściwość skonfigurowanej lokalizacji w szablonie urządzenia, zobacz [utworzyć właściwość lokalizacji obsługiwane przez usługę Azure mapy](howto-set-up-template.md).


1. Na pulpicie nawigacyjnym zestawy urządzenia wybierz mapę z biblioteki. 

    ![Urządzenie ustawia mapy pulpitu nawigacyjnego](media/howto-use-device-sets/LocationMaps1.png)


2. Nadaj tytuł, a następnie wybierz właściwość lokalizacji wcześniej skonfigurowanych jako część programu właściwości urządzenia.

    ![Konfigurowanie mapowania pulpitu nawigacyjnego](media/howto-use-device-sets/LocationMaps2.png)

3. Zapisz i zobaczą mapy kafelka wyświetlanie lokalizacji z urządzeń w zestawie urządzenia.

    ![Zapisywanie map pulpitu nawigacyjnego](media/howto-use-device-sets/LocationMaps3.png)


5. Teraz, gdy operator widoków pulpitu nawigacyjnego zestawy urządzenia, może on zobaczyć wszystkie Kafelki zostały skonfigurowane, włącznie z lokalizacją mapy w celu wizualizowania lokalizacji urządzenia jeden rzut oka!

    ![Widok pulpitu nawigacyjnego mapy — Operator](media/howto-use-device-sets/LocationMaps4.png)

    Będzie można zmienić rozmiar mapy w celu żądane rozmiary.




## <a name="configure-the-list-for-your-device-set"></a>Konfigurowanie listy dla zestawu urządzeń

Po utworzeniu zestawu urządzeń, można skonfigurować **listy**. **Listy** są wyświetlane wszystkie urządzenia w urządzeniu ustawione w tabeli z kolumnami, możesz wybrać.

1. Wybierz **zestawy urządzeń** w menu nawigacji po lewej stronie.

1. Wybierz **listy** kartę.

1. Wybierz **opcje kolumny**.

    ![Opcje kolumny](media/howto-use-device-sets/image11.png)

1. Wybierz kolumny, które mają być wyświetlane wybierając kolumnę, którą chcesz wyświetlić, a następnie wybierając strzałkę w prawo, aby go wybrać.

    ![Wybierz kolumnę](media/howto-use-device-sets/image12.png)

1. Wybierz pozycję **OK**.

## <a name="analytics"></a>Analiza

Analityka w zestawach urządzenia jest taka sama jak karta głównego analityka w menu nawigacji po lewej stronie. Użytkownik może dowiedzieć się więcej o analityka w artykule na [tworzenie analytics](howto-create-analytics.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu używania zestawów urządzeń w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Jak utworzyć zasady telemetrii](howto-create-telemetry-rules.md)
