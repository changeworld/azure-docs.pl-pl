---
title: Dostosowywanie widoków operatora w usłudze Azure IoT Central | Microsoft Docs
description: Jako konstruktor możesz dostosować widoki operatora w aplikacji usługi Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 60e76a1a1d48aaafa8b9aa6b581e8634b1e47836
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956957"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Samouczek: Dostosowywanie widoku operatora usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym samouczku przedstawiono, jak konstruktor może dostosować widok operatora aplikacji. Po wprowadzeniu zmian w aplikacji jako konstruktor możesz wyświetlić podgląd widoku operatora w aplikacji usługi Microsoft Azure IoT Central.

W tym samouczku aplikacja jest dostosowywana pod kątem wyświetlania odpowiednich informacji o połączonym klimatyzatorze dla operatora. Twoje dostosowania umożliwiają operatorowi zarządzanie klimatyzatorami połączonymi z aplikacją.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie pulpitu nawigacyjnego urządzenia
> * Konfigurowanie układu ustawień urządzenia
> * Konfigurowanie układu właściwości urządzenia
> * Wyświetlanie podglądu urządzenia jako operator
> * Konfigurowanie domyślnego pulpitu nawigacyjnego aplikacji
> * Podgląd domyślnego pulpitu nawigacyjnego aplikacji jako operatora

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać dwa poprzednie samouczki:

* [Definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central](tutorial-define-device-type.md).
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego urządzenia

Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. W ramach samouczka [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md) dodano wykres liniowy i inne informacje do pulpitu nawigacyjnego **Połączony klimatyzator**.

1. Aby edytować szablon urządzenia **związanego z warunkiem kondycjonowania** , wybierz pozycję **Szablony urządzeń** w okienku po lewej stronie:

    ![Strona Szablony urządzeń](media/tutorial-customize-operator/devicetemplates.png)

2. Aby dostosować pulpit nawigacyjny urządzenia, wybierz urządzenie szablonu urządzenia **1.0.0 (Application Conditioner)** utworzone w samouczku [Definiowanie nowego urządzenia w aplikacji](tutorial-define-device-type.md) .

3. Aby edytować pulpit nawigacyjny, wybierz kartę **Pulpit nawigacyjny**.

4. Aby dodać kafelek kluczowego wskaźnika wydajności (KPI) do pulpitu nawigacyjnego, wybierz pozycję **Kluczowy wskaźnik wydajności**:

    Aby zdefiniować wskaźnik KPI, użyj informacji w poniższej tabeli:

    | Ustawienie     | Wartość |
    | ----------- | ----- |
    | Nazwa        | Temperatura maksymalna |
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

## <a name="preview-the-device"></a>Podgląd urządzenia

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

## <a name="configure-the-default-dashboard"></a>Konfigurowanie domyślnego pulpitu nawigacyjnego

Gdy Konstruktor lub operator loguje się do aplikacji IoT Central platformy Azure, zobaczy pulpit nawigacyjny aplikacji. Jako Konstruktor można skonfigurować zawartość domyślnego pulpitu nawigacyjnego, aby zawierał najbardziej przydatną i odpowiednią zawartość dla operatora.

> [!NOTE]
> Użytkownicy mogą również tworzyć własne osobiste pulpity nawigacyjne i wybierać jedną z nich jako domyślną.

1. Aby dostosować domyślny pulpit nawigacyjny aplikacji, przejdź do strony **pulpit nawigacyjny** i wybierz pozycję **Edytuj** w prawym górnym rogu strony. Zostanie wyświetlony panel z biblioteką obiektów, które można dodać do pulpitu nawigacyjnego.

    ![Strona pulpitu nawigacyjnego](media/tutorial-customize-operator/builderhome.png)

2. Aby dostosować pulpit nawigacyjny, Dodaj kafelki z **biblioteki**. Wybierz pozycję **Link** i dodaj szczegóły witryny internetowej organizacji. Następnie wybierz polecenie **Zapisz**:

    ![Dodawanie linku do pulpitu nawigacyjnego](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Istnieje także możliwość dodania linków do stron w aplikacji usługi Azure IoT Central. Na przykład możesz dodać link do pulpitu nawigacyjnego urządzenia lub strony ustawień.

3. Opcjonalnie wybierz pozycję **obraz** i Przekaż obraz, który ma być wyświetlany na pulpicie nawigacyjnym. Obraz może zawierać adres URL, do którego należy przejść po jego wybraniu:

    ![Dodawanie obrazu do pulpitu nawigacyjnego](media/tutorial-customize-operator/addimage.png)

    Aby dowiedzieć się więcej, zobacz [Jak przygotować obrazy i przekazać je aplikacji usługi Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Podgląd pulpitu nawigacyjnego

Aby wyświetlić podgląd pulpitu nawigacyjnego aplikacji jako operatora, wybierz pozycję **gotowe** w prawym górnym rogu strony.

![Przełączanie trybu projektowania](media/tutorial-customize-operator/operatorviewhome.png)

Możesz wybrać link i kafelki obrazu, aby przejść do adresów URL ustawionych jako Konstruktor.

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