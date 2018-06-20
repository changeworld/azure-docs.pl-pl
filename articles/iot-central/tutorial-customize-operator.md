---
title: Dostosowywanie widoków operatora w usłudze Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz dostosować widoki operatora w aplikacji usługi Azure IoT Central.
author: sandeeppujar
ms.author: sadeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ddb6e6d7859227b8eec7f13b95fab06b333dacda
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235372"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Samouczek: Dostosowywanie widoku operatora usługi Azure IoT Central

W tym samouczku przedstawiono, jak konstruktor może dostosować widok operatora aplikacji. Po wprowadzeniu zmian w aplikacji jako konstruktor możesz wyświetlić podgląd widoku operatora w aplikacji usługi Microsoft Azure IoT Central.

W tym samouczku aplikacja jest dostosowywana pod kątem wyświetlania odpowiednich informacji o połączonym klimatyzatorze dla operatora. Twoje dostosowania umożliwiają operatorowi zarządzanie klimatyzatorami połączonymi z aplikacją.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie pulpitu nawigacyjnego urządzenia
> * Konfigurowanie układu ustawień urządzenia
> * Konfigurowanie układu właściwości urządzenia
> * Wyświetlanie podglądu urządzenia jako operator
> * Konfigurowanie domyślnej strony głównej
> * Wyświetlanie podglądu domyślnej strony głównej jako operator

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać dwa poprzednie samouczki:

* [Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central](tutorial-define-device-type.md).
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego urządzenia

Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. W ramach samouczka [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md) dodano wykres liniowy i inne informacje do pulpitu nawigacyjnego **Połączony klimatyzator-1**.

1. Aby zmodyfikować szablon urządzenia **Połączony klimatyzator**, wybierz pozycję **Eksplorator** w menu nawigacji po lewej stronie:

    ![Strona Eksplorator](media/tutorial-customize-operator/explorer.png)

2. Aby rozpocząć dostosowywanie pulpitu nawigacyjnego połączonego klimatyzatora, wybierz szablon urządzenia **Połączony klimatyzator (1.0.0)**. Wybierz urządzenie **Połączony klimatyzator-1** utworzone w ramach samouczka [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md):

    ![Wybieranie połączonego klimatyzatora](media/tutorial-customize-operator/selectdevice.png)

    Po wprowadzeniu zmiany dla urządzenia, takiego jak **Połączony klimatyzator-1**, możesz wprowadzić zmianę do bazowego szablonu. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej wersji szablonu urządzenia](howto-version-devicetemplate.md).

3. Aby zmodyfikować pulpit nawigacyjny, wybierz pozycję **Pulpit nawigacyjny**:

    ![Strona pulpitu nawigacyjnego szablonu urządzenia](media/tutorial-customize-operator/dashboard.png)

4. Aby dodać kafelek wskaźnika KPI do pulpitu nawigacyjnego, wybierz pozycję **Wskaźnik KPI**:

    ![Dodawanie wskaźnika KPI](media/tutorial-customize-operator/addkpi.png)

    Aby zdefiniować wskaźnik KPI, użyj informacji w poniższej tabeli:

    | Ustawienie     | Wartość |
    | ----------- | ----- |
    | Name (Nazwa)        | Temperatura maksymalna |
    | Miara | temperature |
    | Agregacja | Maksimum |
    | Przedział czasu  | Ostatni tydzień |

5. Wybierz pozycję **Zapisz**. Teraz na pulpicie nawigacyjnym można zobaczyć kafelek wskaźnika KPI:

    ![Kafelek wskaźnika KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Aby przenieść kafelek lub zmienić jego rozmiar w ramach pulpitu nawigacyjnego, przesuń wskaźnik myszy nad kafelek. Kafelek można przeciągnąć do nowej lokalizacji lub zmienić jego rozmiar:

    ![Edytowanie układu pulpitu nawigacyjnego](media/tutorial-customize-operator/dashboardlayout.png)

## <a name="configure-your-settings-layout"></a>Konfigurowanie układu ustawień

Jako konstruktor możesz także skonfigurować widok operatora dla ustawień urządzenia. Operator używa strony ustawień urządzenia do konfigurowania urządzenia. Na przykład operator może użyć strony ustawień do ustawienia temperatury docelowej dla lodówki.

1. Aby zmodyfikować układ ustawień dla połączonego klimatyzatora, wybierz pozycję **Ustawienia**:

    ![Strona Ustawienia](media/tutorial-customize-operator/settings.png)

2. Kafelki ustawień można przenosić, a ich rozmiar można zmieniać:

    ![Edytowanie układu ustawień](media/tutorial-customize-operator/settingslayout.png)

> [!NOTE]
> W **trybie projektowania** nie można edytować wartości ustawień.

## <a name="configure-your-properties-layout"></a>Konfigurowanie układu właściwości

Poza pulpitem nawigacyjnym i ustawieniami możesz także skonfigurować widok operatora dla właściwości urządzenia. Operator używa strony właściwości urządzenia do zarządzania metadanymi urządzenia. Na przykład operator może użyć strony właściwości do wyświetlenia numeru seryjnego urządzenia lub zaktualizowania danych kontaktowych producenta.

1. Aby zmodyfikować układ właściwości dla połączonego klimatyzatora, wybierz pozycję **Właściwości**:

    ![Strona właściwości](media/tutorial-customize-operator/properties.png)

2. Pola właściwości można przenieść, a także można zmienić ich rozmiar:

    ![Edytowanie układu właściwości](media/tutorial-customize-operator/propertieslayout.png)

> [!NOTE]
> W **trybie projektowania** nie można edytować wartości właściwości.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Wyświetlanie podglądu połączonego klimatyzatora jako operator

W **trybie projektowania** możesz dostosować pulpit nawigacyjny oraz strony ustawień i właściwości dla operatora. Po wyłączeniu **trybu projektowania** możesz wyświetlić aplikację jako operator.

1. Aby wyświetlić połączony klimatyzator jako operator, musisz wyłączyć **tryb projektowania**. Aby wyłączyć **tryb projektowania**, przełącz element **Tryb projektowania** w prawej górnej części strony.

2. Aby zaktualizować numer seryjny urządzenia, zmodyfikuj wartość na kafelku numeru seryjnego i wybierz polecenie **Zapisz**:

    ![Edytowanie wartości właściwości](media/tutorial-customize-operator/editproperty.png)

3. Aby wysłać ustawienie do połączonego klimatyzatora, wybierz pozycję **Ustawienia**, zmień wartość ustawienia na kafelku i wybierz polecenie **Aktualizuj**:

    ![Wysyłanie ustawienia do urządzenia](media/tutorial-customize-operator/sendsetting.png)

    Gdy urządzenie potwierdzi nową wartość ustawienia, zostanie ono wyświetlone na kafelku jako **zsynchronizowane**.

4. Jako operator możesz wyświetlić pulpit nawigacyjny urządzenia skonfigurowany przez konstruktora:

    ![Widok operatora dla pulpitu nawigacyjnego urządzenia](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurowanie domyślnej strony głównej

Gdy konstruktor lub operator zaloguje się do aplikacji usługi Azure IoT Central, widzi stronę główną. Jako konstruktor możesz skonfigurować zawartość strony głównej, umieszczając na niej zawartość najbardziej przydatną dla operatora.

1. Aby dostosować domyślną stronę główną, przejdź na stronę **Strona główna** i przełącz element **Tryb projektowania** w prawej górnej części strony. Po włączeniu **trybu projektowania** z prawej strony wysuwa się panel z listą obiektów, które można dodać do strony głównej.

    ![Strona Konstruktor aplikacji](media/tutorial-customize-operator/builderhome.png)

2. Aby dostosować stronę główną, dodaj kafelki z pola **Biblioteka**. Wybierz pozycję **Link** i dodaj szczegóły witryny internetowej organizacji. Następnie wybierz polecenie **Zapisz**:

    ![Dodawanie linku do strony głównej](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Istnieje także możliwość dodania linków do stron w aplikacji usługi Azure IoT Central. Na przykład możesz dodać link do pulpitu nawigacyjnego urządzenia lub strony ustawień.

3. Opcjonalnie możesz wybrać pozycję **Obraz** i przekazać obraz do wyświetlania na stronie głównej. Dla obrazu może być określony adres URL otwierany po kliknięciu obrazu:

    ![Dodawanie obrazu do strony głównej](media/tutorial-customize-operator/addimage.png)

    Aby dowiedzieć się więcej, zobacz [Jak przygotować obrazy i przekazać je aplikacji usługi Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Wyświetlanie podglądu domyślnej strony głównej jako operator

Aby wyświetlić podgląd strony głównej jako operator, wyłącz pozycję **Tryb projektowania** w prawej górnej części strony:

![Przełączanie trybu projektowania](media/tutorial-customize-operator/operatorviewhome.png)

Link i kafelki obrazu można kliknąć, aby przejść do adresów URL ustawionych przez konstruktora.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób dostosowywania widoku operatora aplikacji.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Konfigurowanie pulpitu nawigacyjnego urządzenia
> * Konfigurowanie układu ustawień urządzenia
> * Konfigurowanie układu właściwości urządzenia
> * Wyświetlanie podglądu urządzenia jako operator
> * Konfigurowanie domyślnej strony głównej
> * Wyświetlanie podglądu domyślnej strony głównej jako operator

Teraz, gdy znasz już sposób dostosowywania widoku operatora dla aplikacji, możesz wykonać kolejne sugerowane kroki:

* [Monitorowanie urządzeń (jako operator)](tutorial-monitor-devices.md)
* [Dodawanie nowego urządzenie do aplikacji (jako operator i deweloper urządzeń)](tutorial-add-device.md)
