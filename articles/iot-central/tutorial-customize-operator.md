---
title: Dostosowywanie widoków operatora w usłudze Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz dostosować widoki operatora w aplikacji usługi Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 7fc42b24090b5fc68176fea2c7b993643ea24200
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747276"
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
> * Skonfiguruj domyślny pulpit nawigacyjny aplikacji
> * Domyślny pulpit nawigacyjny aplikacji jako operator w wersji zapoznawczej

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać dwa poprzednie samouczki:

* [Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central](tutorial-define-device-type.md).
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego urządzenia

Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. W ramach samouczka [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md) dodano wykres liniowy i inne informacje do pulpitu nawigacyjnego **Połączony klimatyzator**.

1. Aby zmodyfikować szablon urządzenia **Połączony klimatyzator**, wybierz pozycję **Szablony urządzeń** w menu nawigacji po lewej stronie:

    ![Strona Szablony urządzeń](media/tutorial-customize-operator/devicetemplates.png)

2. Aby dostosować pulpit nawigacyjny urządzenia, wybierz opcję **połączone klimatyzacyjne (1.0.0)** urządzenia szablon został utworzony w [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md) samouczka.

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

    ![Dodawanie wskaźnika KPI](media/tutorial-customize-operator/addkpi.png)

5. Wybierz pozycję **Zapisz**. Teraz na pulpicie nawigacyjnym można zobaczyć kafelek wskaźnika KPI:

    ![Kafelek wskaźnika KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Aby przenieść kafelek lub zmienić jego rozmiar w ramach pulpitu nawigacyjnego, przesuń wskaźnik myszy nad kafelek. Kafelek można przeciągnąć do nowej lokalizacji lub zmienić jego rozmiar.

## <a name="configure-your-settings-layout"></a>Konfigurowanie układu ustawień

Jako konstruktor możesz także skonfigurować widok operatora dla ustawień urządzenia. Operator używa karty ustawień urządzenia do konfigurowania urządzenia. Na przykład operator może użyć karty ustawień w celu ustawienia temperatury docelowej dla podłączonego klimatyzatora.

1. Aby zmodyfikować układ ustawień dla połączonego klimatyzatora, wybierz kartę **Ustawienia**.

2. Kafelki ustawień można przenosić, a ich rozmiar można zmieniać:

    ![Edytowanie układu ustawień](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurowanie układu właściwości

Poza pulpitem nawigacyjnym i ustawieniami możesz także skonfigurować widok operatora dla właściwości urządzenia. Operator używa karty właściwości urządzenia do zarządzania metadanymi urządzenia. Na przykład operator może użyć karty właściwości do wyświetlenia numeru seryjnego urządzenia lub zaktualizowania danych kontaktowych producenta.

1. Aby zmodyfikować układ właściwości dla połączonego klimatyzatora, wybierz kartę **Właściwości**.

2. Pola właściwości można przenieść, a także można zmienić ich rozmiar:

    ![Edytowanie układu właściwości](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Urządzenia w wersji zapoznawczej

Możesz użyć strony **Szablony urządzeń**, aby dostosować karty Pulpit nawigacyjny, Ustawienia i Właściwości dla operatora. Możesz użyć strony narzędzia **Device Explorer**, aby wyświetlić szablon urządzenia i go użyć.

1. Aby wyświetlić szablon połączonego klimatyzatora i użyć go jako operator, przejdź do strony **Device Explorer** i wybierz symulowane urządzenie, które zostało wygenerowane przez usługę IoT Central na podstawie szablonu:

    ![Wyświetlanie i używanie szablonu urządzenia](media/tutorial-customize-operator/usetemplate.png)

2. Aby zaktualizować lokalizację tego urządzenia, wybierz pozycję **Właściwości** i edytuj wartość na kafelku Lokalizacja. Następnie wybierz pozycję **Zapisz**:

    ![Edytowanie wartości właściwości](media/tutorial-customize-operator/editproperty.png)

3. Aby wysłać ustawienie do połączonego klimatyzatora, wybierz pozycję **Ustawienia**, zmień wartość ustawienia na kafelku i wybierz polecenie **Aktualizuj**:

    ![Wysyłanie ustawienia do urządzenia](media/tutorial-customize-operator/sendsetting.png)

    Gdy urządzenie potwierdzi nową wartość ustawienia, zostanie ono wyświetlone na kafelku jako **zsynchronizowane**.

4. Jako operator możesz wyświetlić pulpit nawigacyjny urządzenia skonfigurowany przez konstruktora:

    ![Widok operatora dla pulpitu nawigacyjnego urządzenia](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Skonfiguruj domyślny pulpit nawigacyjny

Jeśli konstruktor lub operator loguje się do aplikacji usługi Azure IoT Central, zobaczy pulpit nawigacyjny aplikacji. Jako Konstruktor można skonfigurować zawartość domyślny pulpit nawigacyjny, aby uwzględnić zawartość najbardziej przydatne i istotne dla operatora.

> [!NOTE]
> Użytkownicy mogą również tworzyć własne osobistych pulpitów nawigacyjnych i wybierz jedną z nich domyślnie.

1. Aby dostosować domyślny pulpit nawigacyjny aplikacji, przejdź do **pulpit nawigacyjny** strony i wybierz **Edytuj** w górnym rogu strony. Biblioteki obiektów, które można dodać do pulpitu nawigacyjnego zostanie wyświetlony panel.

    ![Strona pulpitu nawigacyjnego](media/tutorial-customize-operator/builderhome.png)

2. Aby dostosować pulpit nawigacyjny, należy dodać Kafelki z **biblioteki**. Wybierz pozycję **Link** i dodaj szczegóły witryny internetowej organizacji. Następnie wybierz polecenie **Zapisz**:

    ![Dodaj łącze do pulpitu nawigacyjnego](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Istnieje także możliwość dodania linków do stron w aplikacji usługi Azure IoT Central. Na przykład możesz dodać link do pulpitu nawigacyjnego urządzenia lub strony ustawień.

3. Opcjonalnie wybierz opcję **obraz** i przekazania obrazu do wyświetlenia na pulpicie nawigacyjnym. Obraz może mieć adres URL, do którego możesz przejść po jego wybraniu:

    ![Dodawanie obrazu do pulpitu nawigacyjnego](media/tutorial-customize-operator/addimage.png)

    Aby dowiedzieć się więcej, zobacz [Jak przygotować obrazy i przekazać je aplikacji usługi Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Podgląd pulpitu nawigacyjnego

Aby pulpit nawigacyjny aplikacji jako operator w wersji zapoznawczej, wybierz pozycję **gotowe** w górnym rogu strony.

![Przełączanie trybu projektowania](media/tutorial-customize-operator/operatorviewhome.png)

Możesz wybrać Kafelki linkiem i obrazem, aby przejść do adresów URL, Ustaw jako Konstruktor.

## <a name="next-steps"></a>Kolejne kroki

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