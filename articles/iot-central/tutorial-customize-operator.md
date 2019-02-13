---
title: Dostosowywanie widoków operatora w usłudze Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz dostosować widoki operatora w aplikacji usługi Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65463b534988e0a721a1a5f816183f8dd8ebcaf
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657658"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Samouczek: Dostosowywanie widoków operatora usługi Azure IoT Central

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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

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

    Po przejściu do urządzenia, takiego jak **Połączony klimatyzator-1**, możesz wybrać pozycję **Edytuj szablon**, aby wprowadzić zmianę do szablonu bazowego. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej wersji szablonu urządzenia](howto-version-devicetemplate.md).

3. Aby edytować pulpit nawigacyjny, wybierz pozycję **Pulpit nawigacyjny**, a następnie wybierz przycisk **Edytuj szablon**:

    ![Strona pulpitu nawigacyjnego szablonu urządzenia](media/tutorial-customize-operator/dashboard.png)

4. Aby dodać kafelek kluczowego wskaźnika wydajności (KPI) do pulpitu nawigacyjnego, wybierz pozycję **Kluczowy wskaźnik wydajności**:

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

7. Kliknij przycisk **Gotowe** po zakończeniu wprowadzania zmian.

## <a name="configure-your-settings-layout"></a>Konfigurowanie układu ustawień

Jako konstruktor możesz także skonfigurować widok operatora dla ustawień urządzenia. Operator używa strony ustawień urządzenia do konfigurowania urządzenia. Na przykład operator może użyć strony ustawień w celu ustawienia temperatury docelowej dla podłączonego klimatyzatora.

1. Aby edytować układ ustawień dla połączonego klimatyzatora, wybierz pozycję **Ustawienia**, a następnie wybierz przycisk **Edytuj szablon**:

    ![Strona Ustawienia](media/tutorial-customize-operator/settings.png)

2. Kafelki ustawień można przenosić, a ich rozmiar można zmieniać:

    ![Edytowanie układu ustawień](media/tutorial-customize-operator/settingslayout.png)

3. Kliknij przycisk **Gotowe** po zakończeniu wprowadzania zmian.

> [!NOTE]
> W trybie **Edytowanie szablonu** nie można edytować wartości ustawień.

## <a name="configure-your-properties-layout"></a>Konfigurowanie układu właściwości

Poza pulpitem nawigacyjnym i ustawieniami możesz także skonfigurować widok operatora dla właściwości urządzenia. Operator używa strony właściwości urządzenia do zarządzania metadanymi urządzenia. Na przykład operator może użyć strony właściwości do wyświetlenia numeru seryjnego urządzenia lub zaktualizowania danych kontaktowych producenta.

1. Aby edytować układ właściwości dla połączonego klimatyzatora, wybierz pozycję **Właściwości**, a następnie wybierz przycisk **Edytuj szablon**:

    ![Strona właściwości](media/tutorial-customize-operator/properties.png)

2. Pola właściwości można przenieść, a także można zmienić ich rozmiar:

    ![Edytowanie układu właściwości](media/tutorial-customize-operator/propertieslayout.png)

3. Kliknij przycisk **Gotowe** po zakończeniu wprowadzania zmian.

> [!NOTE]
> W trybie **Edytowanie szablonu** nie można edytować wartości właściwości.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Wyświetlanie podglądu połączonego klimatyzatora jako operator

W trybie **Edytowanie szablonu** możesz dostosować pulpit nawigacyjny oraz strony ustawień i właściwości dla operatora. Jeśli nie pracujesz w trybie **Edytowanie szablonu**, możesz wyświetlić aplikację jako operator.

1. Aby wyświetlić podłączone urządzenie klimatyzatora jako operator, kliknij przycisk **Gotowe** w celu zatrzymania edycji szablonu i powrotu do widoku operatora urządzenia.

2. Aby zaktualizować lokalizację tego urządzenia, zmodyfikuj wartość na kafelku Lokalizacja, a następnie wybierz przycisk **Zapisz**:

    ![Edytowanie wartości właściwości](media/tutorial-customize-operator/editproperty.png)

3. Aby wysłać ustawienie do połączonego klimatyzatora, wybierz pozycję **Ustawienia**, zmień wartość ustawienia na kafelku i wybierz polecenie **Aktualizuj**:

    ![Wysyłanie ustawienia do urządzenia](media/tutorial-customize-operator/sendsetting.png)

    Gdy urządzenie potwierdzi nową wartość ustawienia, zostanie ono wyświetlone na kafelku jako **zsynchronizowane**.

4. Jako operator możesz wyświetlić pulpit nawigacyjny urządzenia skonfigurowany przez konstruktora:

    ![Widok operatora dla pulpitu nawigacyjnego urządzenia](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurowanie domyślnej strony głównej

Gdy konstruktor lub operator zaloguje się do aplikacji usługi Azure IoT Central, widzi stronę główną. Jako konstruktor możesz skonfigurować zawartość strony głównej, umieszczając na niej zawartość najbardziej przydatną dla operatora.

1. Aby dostosować domyślną stronę główną, przejdź na stronę **Strona główna** i wybierz pozycję **Edycja** w prawej górnej części strony. Po wybraniu pozycji **Edycja** z prawej strony wysuwa się panel z listą obiektów, które można dodać do strony głównej.

    ![Strona Konstruktor aplikacji](media/tutorial-customize-operator/builderhome.png)

2. Aby dostosować stronę główną, dodaj kafelki z pola **Biblioteka**. Wybierz pozycję **Link** i dodaj szczegóły witryny internetowej organizacji. Następnie wybierz polecenie **Zapisz**:

    ![Dodawanie linku do strony głównej](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Istnieje także możliwość dodania linków do stron w aplikacji usługi Azure IoT Central. Na przykład możesz dodać link do pulpitu nawigacyjnego urządzenia lub strony ustawień.

3. Opcjonalnie możesz wybrać pozycję **Obraz** i przekazać obraz do wyświetlania na stronie głównej. Dla obrazu może być określony adres URL otwierany po kliknięciu obrazu:

    ![Dodawanie obrazu do strony głównej](media/tutorial-customize-operator/addimage.png)

    Aby dowiedzieć się więcej, zobacz [Jak przygotować obrazy i przekazać je aplikacji usługi Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Wyświetlanie podglądu domyślnej strony głównej jako operator

Aby wyświetlić podgląd strony głównej jako operator i już jej nie edytować, wybierz pozycję **Gotowe** w prawej górnej części strony

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
