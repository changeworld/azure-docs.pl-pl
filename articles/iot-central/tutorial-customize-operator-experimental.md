---
title: Dostosowywanie widoków operatora w usłudze Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz dostosować widoki operatora w aplikacji usługi Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 267a619fe32a8d4af0ee9cc8a5001d7a321c3098
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765157"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Samouczek: Dostosowywanie widoków operatora usługi Azure IoT Central (nowy projekt interfejsu użytkownika)

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

* [Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="configure-your-device-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego urządzenia

Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. W ramach samouczka [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) dodano wykres liniowy i inne informacje do pulpitu nawigacyjnego **Połączony klimatyzator**.

1. Aby zmodyfikować szablon urządzenia **Połączony klimatyzator**, wybierz pozycję **Szablony urządzeń** w menu nawigacji po lewej stronie:

    ![Strona Szablony urządzeń](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. Aby dostosować pulpit nawigacyjny urządzenia, kliknij przycisk szablonu urządzenia **Połączony klimatyzator (1.0.0)** utworzonego w samouczku [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

3. Aby edytować pulpit nawigacyjny, wybierz kartę **Pulpit nawigacyjny**.

4. Aby dodać kafelek kluczowego wskaźnika wydajności (KPI) do pulpitu nawigacyjnego, wybierz pozycję **Kluczowy wskaźnik wydajności**:

    Aby zdefiniować wskaźnik KPI, użyj informacji w poniższej tabeli:

    | Ustawienie     | Wartość |
    | ----------- | ----- |
    | Name (Nazwa)        | Temperatura maksymalna |
    | Przedział czasu  | Ostatni tydzień |
    | Typ miary | Telemetria |
    | Miara | temperature |
    | Agregacja | Maksimum |
    | Widoczność  | Enabled (Włączony) |

    ![Dodawanie wskaźnika KPI](media/tutorial-customize-operator-experimental/addkpi.png)

5. Kliknij pozycję **Zapisz**. Teraz na pulpicie nawigacyjnym można zobaczyć kafelek wskaźnika KPI:

    ![Kafelek wskaźnika KPI](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. Aby przenieść kafelek lub zmienić jego rozmiar w ramach pulpitu nawigacyjnego, przesuń wskaźnik myszy nad kafelek. Kafelek można przeciągnąć do nowej lokalizacji lub zmienić jego rozmiar.

## <a name="configure-your-settings-layout"></a>Konfigurowanie układu ustawień

Jako konstruktor możesz także skonfigurować widok operatora dla ustawień urządzenia. Operator używa karty ustawień urządzenia do konfigurowania urządzenia. Na przykład operator może użyć karty ustawień w celu ustawienia temperatury docelowej dla podłączonego klimatyzatora.

1. Aby zmodyfikować układ ustawień dla połączonego klimatyzatora, wybierz kartę **Ustawienia**.

2. Kafelki ustawień można przenosić, a ich rozmiar można zmieniać:

    ![Edytowanie układu ustawień](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurowanie układu właściwości

Poza pulpitem nawigacyjnym i ustawieniami możesz także skonfigurować widok operatora dla właściwości urządzenia. Operator używa karty właściwości urządzenia do zarządzania metadanymi urządzenia. Na przykład operator może użyć karty właściwości do wyświetlenia numeru seryjnego urządzenia lub zaktualizowania danych kontaktowych producenta.

1. Aby zmodyfikować układ właściwości dla połączonego klimatyzatora, wybierz kartę **Właściwości**.

2. Pola właściwości można przenieść, a także można zmienić ich rozmiar:

    ![Edytowanie układu właściwości](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Wyświetlanie podglądu połączonego klimatyzatora jako operator

Możesz użyć strony **Szablony urządzeń**, aby dostosować karty Pulpit nawigacyjny, Ustawienia i Właściwości dla operatora. Możesz użyć strony narzędzia **Device Explorer**, aby wyświetlić szablon urządzenia i go użyć.

1. Aby wyświetlić szablon połączonego klimatyzatora i użyć go jako operator, przejdź do strony **Device Explorer** i wybierz symulowane urządzenie, które zostało wygenerowane przez usługę IoT Central na podstawie szablonu:

    ![Wyświetlanie i używanie szablonu urządzenia](media/tutorial-customize-operator-experimental/usetemplate.png)

2. Aby zaktualizować lokalizację tego urządzenia, wybierz pozycję **Właściwości** i edytuj wartość na kafelku Lokalizacja. Następnie kliknij przycisk **Zapisz**:

    ![Edytowanie wartości właściwości](media/tutorial-customize-operator-experimental/editproperty.png)

3. Aby wysłać ustawienie do połączonego klimatyzatora, wybierz pozycję **Ustawienia**, zmień wartość ustawienia na kafelku i wybierz polecenie **Aktualizuj**:

    ![Wysyłanie ustawienia do urządzenia](media/tutorial-customize-operator-experimental/sendsetting.png)

    Gdy urządzenie potwierdzi nową wartość ustawienia, zostanie ono wyświetlone na kafelku jako **zsynchronizowane**.

4. Jako operator możesz wyświetlić pulpit nawigacyjny urządzenia skonfigurowany przez konstruktora:

    ![Widok operatora dla pulpitu nawigacyjnego urządzenia](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurowanie domyślnej strony głównej

Gdy konstruktor lub operator zaloguje się do aplikacji usługi Azure IoT Central, widzi stronę główną. Jako konstruktor możesz skonfigurować zawartość strony głównej, umieszczając na niej zawartość najbardziej przydatną dla operatora.

1. Aby dostosować domyślną stronę główną, przejdź na stronę **Strona główna** i wybierz pozycję **Edytuj** w prawej górnej części strony. Z lewej strony wysunie się panel z listą obiektów, które można dodać do **strony głównej**:

    ![Strona Konstruktor aplikacji](media/tutorial-customize-operator-experimental/builderhome.png)

2. Aby dostosować **stronę główną**, dodaj kafelki z pola **Biblioteka**. Wybierz pozycję **Link** i dodaj szczegóły witryny internetowej organizacji. Następnie kliknij przycisk **Zapisz**:

    ![Dodawanie linku do strony głównej](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > Istnieje także możliwość dodania linków do stron w aplikacji usługi Azure IoT Central. Na przykład możesz dodać link do pulpitu nawigacyjnego urządzenia lub strony ustawień.

3. Opcjonalnie możesz wybrać pozycję **Obraz** i przekazać obraz do wyświetlania na stronie głównej. Dla obrazu może być określony adres URL otwierany po kliknięciu obrazu:

    ![Dodawanie obrazu do strony głównej](media/tutorial-customize-operator-experimental/addimage.png)

    Aby dowiedzieć się więcej, zobacz [Jak przygotować obrazy i przekazać je aplikacji usługi Azure IoT Central](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="preview-the-default-home-page-as-an-operator"></a>Wyświetlanie podglądu domyślnej strony głównej jako operator

Aby wyświetlić podgląd strony głównej jako operator, kliknij pozycję **Gotowe** w prawej górnej części strony:

![Przełączanie trybu projektowania](media/tutorial-customize-operator-experimental/operatorviewhome.png)

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

* [Monitorowanie urządzeń (jako operator)](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Dodawanie nowego urządzenie do aplikacji (jako operator i deweloper urządzeń)](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
