---
title: Zarządzanie urządzeniami w rozwiązaniu do zdalnego monitorowania opartym na platformie Azure | Microsoft Docs
description: W tym samouczku pokazano, w jaki sposób zarządzać urządzeniami połączonymi z akceleratorem rozwiązań do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/12/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63baf6397b2542311525bac740c50b5eacbd35cf
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097431"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Samouczek: konfigurowanie urządzeń połączonych z rozwiązaniem do monitorowania i zarządzanie nimi

W tym samouczku użyjesz akceleratora rozwiązań do zdalnego monitorowania, aby skonfigurować połączone urządzenia IoT i nimi zarządzać. Dodasz nowe urządzenie do akceleratora rozwiązań, skonfigurujesz je i zaktualizujesz jego oprogramowanie układowe.

Firma Contoso zamówiła nowe maszyny, aby rozwinąć jedną ze swoich placówek. Czekając na dostawę nowych maszyn, chcesz uruchomić symulację, aby przetestować działanie rozwiązania. Aby uruchomić symulację, dodasz nowy symulowany silnik do akceleratora rozwiązań do zdalnego monitorowania i sprawdzisz, czy symulowane urządzenie prawidłowo reaguje na działania i aktualizacje konfiguracji.

Aby zapewnić rozszerzalny sposób konfigurowania urządzeń i zarządzania nimi, akcelerator rozwiązań do zdalnego monitorowania korzysta z funkcji centrum IoT Hub, takich jak [zadania](../iot-hub/iot-hub-devguide-jobs.md) i [metody bezpośrednie](../iot-hub/iot-hub-devguide-direct-methods.md). W tym samouczku używane są urządzenia symulowane, ale deweloper urządzeń może wdrażać metody bezpośrednie na [urządzeniu fizycznym połączonym z akceleratorem rozwiązań do zdalnego monitorowania](iot-accelerators-connecting-devices.md).

W tym samouczku zostaną wykonane następujące czynności:

>[!div class="checklist"]
> * Aprowizowanie symulowanego urządzenia.
> * Testowanie symulowanego urządzenia.
> * Aktualizowanie oprogramowania układowego urządzenia.
> * Zmiana konfiguracji urządzenia.
> * Organizowanie urządzeń.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, należy wdrożyć wystąpienie akceleratora rozwiązań do zdalnego monitorowania w subskrypcji platformy Azure.

Jeśli jeszcze nie wdrożono akceleratora rozwiązań do zdalnego monitorowania, należy ukończyć przewodnik Szybki start [Deploy a cloud-based remote monitoring solution (Wdrażanie rozwiązania do zdalnego monitorowania opartego na chmurze)](quickstart-remote-monitoring-deploy.md).

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Przejdź do strony **Urządzenia** w rozwiązaniu, a następnie kliknij przycisk **+ Nowe urządzenie**:

[![Aprowizowanie urządzenia symulowanego](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Na panelu **Nowe urządzenie** wybierz pozycję **Symulowane**, pozostaw liczbę urządzeń do aprowizacji **1**, wybierz model urządzenia **Faulty Engine** (Wadliwy silnik), a następnie wybierz polecenie **Zastosuj**, aby utworzyć symulowane urządzenie:

[![Aprowizowanie symulowanego silnika](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testowanie symulowanego urządzenia

Aby sprawdzić, czy symulowane urządzenie wysyła dane telemetryczne i zgłasza wartości właściwości, wybierz je z listy urządzeń na stronie **Urządzenia**. Bieżące informacje o urządzeniu są wyświetlane na panelu **Szczegóły urządzenia**:

[![Wyświetlanie nowego symulowanego silnika](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

W polu **Szczegóły urządzenia** sprawdź, czy nowe urządzenie wysyła dane telemetryczne. Aby wyświetlić różne strumienie danych telemetrycznych o wibracjach, przesyłane z urządzenia, kliknij pozycję **Vibration** (Wibracje):

[![Wybieranie wyświetlanego strumienia danych telemetrycznych](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Panel **Szczegóły urządzenia** zawiera inne informacje dotyczące urządzenia, takie jak wartości tagów, obsługiwane metody oraz właściwości zgłaszane przez urządzenie.

Aby wyświetlić szczegółową diagnostykę, przewiń w dół, aby wyświetlić pozycję **Diagnostyka**:

[![Wyświetlanie diagnostyki urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-expanded.png#lightbox)

## <a name="act-on-a-device"></a>Wykonywanie akcji względem urządzenia

Aby sprawdzić, czy symulowany silnik prawidłowo reaguje na akcje zainicjowane przez akcelerator rozwiązań, uruchom metodę **FirmwareUpdate**. Aby uruchomić metodę na urządzeniu, wybierz je z listy urządzeń, a następnie kliknij przycisk **Zadania**. Możesz wybrać więcej niż jedno urządzenie, jeśli chcesz wykonać akcję na wielu urządzeniach. Na panelu **Zadania** wybierz pozycję **Uruchom metodę**. Model urządzenia **Engine** (Silnik) określa trzy metody: **FirmwareUpdate**, **FillTank** i **EmptyTank**:

[![Metody urządzenia Engine](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Wybierz pozycję **FirmwareUpdate**, ustaw nazwę zadania **UpdateEngineFirmware**, ustaw wersję oprogramowania układowego **2.0.0**, ustaw identyfikator URI oprogramowania układowego **http://contoso.com/engine.bin**, a następnie kliknij przycisk **Zastosuj**:

[![Planowanie metody aktualizacji oprogramowania układowego](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Aby śledzić stan zadania, kliknij pozycję **Wyświetl stan zadania**:

[![Monitorowanie zaplanowanego zadania aktualizacji oprogramowania układowego](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Po zakończeniu zadania wróć do strony **Urządzenia**. Jest wyświetlana nowa wersja oprogramowania układowego silnika.

Jeśli wybierzesz wiele urządzeń różnych typów na stronie **Urządzenia**, nadal możesz utworzyć zadanie, aby uruchomić metodę na tych urządzeniach. Na panelu **Zadania** wyświetlane są tylko metody wspólne dla wszystkich wybranych urządzeń.

## <a name="reconfigure-a-device"></a>Zmiana konfiguracji urządzenia

Aby przetestować aktualizowanie właściwości konfiguracji silnika, wybierz go z listy urządzeń na stronie **Urządzenia**. Kliknij przycisk **Zadania**, a następnie wybierz polecenie **Zmień konfigurację**. Na panelu Zadania wyświetlane są wartości właściwości, które można zaktualizować dla wybranego urządzenia:

[![Zmienianie konfiguracji urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Aby zaktualizować lokalizację silnika, ustaw nazwę zadania **UpdateEngineLocation**, ustaw wartość długości geograficznej **-122,15**, ustaw lokalizację **Factory 2** (Fabryka 2), ustaw wartość szerokości geograficznej **47,62** i kliknij pozycję **Zastosuj**:

[![Aktualizowanie wartości właściwości urządzenia](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Aby śledzić stan zadania, kliknij pozycję **Wyświetl stan zadania**:

[![Aktualizowanie wartości właściwości urządzenia](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Po zakończeniu zadania przejdź do strony **Pulpit nawigacyjny**. Urządzenie zostanie wyświetlone na mapie w nowej lokalizacji:

[![Wyświetlanie lokalizacji silnika](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizowanie urządzeń

Aby ułatwić operatorom organizowanie urządzeń i zarządzanie nimi, warto je otagować przy użyciu odpowiedniej nazwy zespołu. Firma Contoso ma dwa różne zespoły do wykonywania zadań serwisowych w terenie:

* Zespół Smart Vehicle zajmuje się ciężarówkami i urządzeniami do tworzenia prototypów.
* Zespół Smart Building zajmuje się chłodniami, podnośnikami i silnikami.

Aby wyświetlić wszystkie urządzenia, przejdź do strony **Urządzenia** i wybierz filtr **Wszystkie urządzenia**:

[Wyświetlanie wszystkich urządzeń![](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Dodawanie tagów

Zaznacz wszystkie urządzenia typu **Truck** (Ciężarówka) i **Prototyping** (Prototypy). Następnie kliknij pozycję **Zadania**:

[![Wybieranie pozycji Truck i Prototyping](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-expanded.png#lightbox)

Wybierz pozycję **Tag**, ustaw nazwę zadania **AddConnectedVehicleTag**, a następnie dodaj tag tekstowy o nazwie **FieldService** z wartością **ConnectedVehicle**. Następnie kliknij pozycję **Zastosuj**:

[![Dodawanie tagów do urządzeń Truck i Prototyping](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na stronie Urządzenia zaznacz wszystkie urządzenia **Chiller** (Chłodnia), **Elevator** (Podnośnik) i **Engine** (Silnik). Następnie kliknij pozycję **Zadania**:

[![Wybieranie urządzeń Chiller, Elevator i Engine](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-expanded.png#lightbox)

Wybierz pozycję **Tag**, ustaw nazwę zadania **AddSmartBuildingTag**, a następnie dodaj tag tekstowy o nazwie **FieldService** z wartością **SmartBuilding**. Następnie kliknij pozycję **Zastosuj**:

[![Dodawanie tagów do urządzeń Chiller, Elevator i Engine](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Tworzenie filtrów

Teraz możesz użyć wartości tagów do utworzenia filtrów. Na stronie **Urządzenia** kliknij opcję **Zarządzaj grupami urządzeń**:

[![Zarządzanie grupami urządzeń](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Utwórz filtr tekstowy, używając w warunku nazwy tagu **FieldService** i wartości **SmartBuilding**. Zapisz filtr jako **Smart Building**:

[![Tworzenie filtru Smart Building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Utwórz filtr tekstowy, używając w warunku nazwy tagu **FieldService** i wartości **ConnectedVehicle**. Zapisz filtr jako **Connected Vehicle**.

[![Tworzenie filtru Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Teraz operator firmy Contoso może wykonać zapytanie dotyczące urządzeń według zespołu operacyjnego:

[![Tworzenie filtru Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz przejść do kolejnego samouczka, akcelerator rozwiązań do zdalnego monitorowania powinien pozostać wdrożony. Aby zmniejszyć koszty działania akceleratora rozwiązań, gdy go nie używasz, możesz zatrzymać symulowane urządzenia w panelu Ustawienia:

[![Wstrzymywanie telemetrii](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-expanded.png#lightbox)

Możesz ponownie uruchomić urządzenia symulowane, gdy postanowisz rozpocząć kolejny samouczek.

Jeśli akcelerator rozwiązań nie jest już potrzebny, usuń go na stronie [Aprowizowane rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Usuwanie rozwiązania](media/iot-accelerators-remote-monitoring-manage/deletesolution.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, w jaki sposób skonfigurować urządzenia połączone z akceleratorem rozwiązań do zdalnego monitorowania i zarządzać nimi. Aby dowiedzieć się, w jaki sposób korzystać z akceleratora rozwiązań w celu identyfikowania i rozwiązywania problemów z urządzeniami połączonymi, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Use device alerts to identify and fix issues with devices connected to your monitoring solution (Korzystanie z alertów urządzenia do identyfikowania i rozwiązywania problemów z urządzeniami połączonymi z rozwiązaniem do monitorowania)](iot-accelerators-remote-monitoring-maintain.md)
