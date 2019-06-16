---
title: Podłącz urządzenie Mxchip z aplikacją usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia Dowiedz się, jak połączyć urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 130ca6bc946d44d80cddba5486d405bfb15523cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235880"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzenia podłączenia urządzenia zestawu deweloperskiego IoT Mxchip (Mxchip) do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

1. Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Urządzenie Mxchip. Aby zakupić urządzenia Mxchip, odwiedź stronę [zestawu deweloperskiego IoT Mxchip](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Przykładowa aplikacja Devkits

Aplikacja utworzone na podstawie **Devkits przykładowe** szablon aplikacji zawiera **zestawu deweloperskiego** szablon urządzenia, który definiuje następujące właściwości urządzenia:

- Pomiary telemetrii dla **wilgotności**, **temperatury**, **wykorzystanie**, **magnetometrów** (mierzoną wzdłuż X, Y osi Z), **Przyspieszeniomierza** (mierzoną wzdłuż X, Y osi Z), i **Żyroskop** (mierzoną wzdłuż X, Y osi Z).
- Stan miary dla **stan urządzenia**.
- Miary zdarzeń dla **użycia B przycisku**.
- Ustawienia dla **napięcia**, **bieżącego**, **szybkość wentylator**i **IR** przełącznika.
- Właściwości urządzenia **zdechną numer** i **lokalizacji urządzenia**, czyli właściwości location.
- Właściwość w chmurze **wyprodukowany w**.
- Polecenia **Echo** i **odliczania**. Po odebraniu rzeczywistego urządzenia **Echo** polecenie pokazuje wartość wysłane na ekranie urządzenia. Po odebraniu rzeczywistego urządzenia **odliczania** polecenia cykle LED przy użyciu wzorca i urządzenie wysyła odliczania wartości z powrotem do IoT Central.

Aby uzyskać szczegółowe informacje o konfiguracji, zobacz [Szczegóły szablonu zestawu deweloperskiego urządzenia](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

### <a name="get-your-device-connection-details"></a>Uczyń swoje urządzenie szczegóły połączenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **zestawu deweloperskiego** szablon urządzenia i zanotować szczegóły połączenia urządzenia: **Zakres Identyfikatora, identyfikator urządzenia i klucz podstawowy**:

1. Dodaj **rzeczywistego urządzenia** Device Explorer, zaznacz **+ nowy > rzeczywistych** dodać rzeczywistego urządzenia.

    * Wprowadzić małymi literami **identyfikator urządzenia**, lub użyj sugerowanego **identyfikator urządzenia**.
    * Wprowadź **nazwy urządzenia**, lub użyć sugerowanej nazwy

    ![Dodaj urządzenie](media/howto-connect-devkit/add-device.png)

1. Aby uzyskać szczegóły połączenia urządzenia **identyfikator zakresu**, **identyfikator urządzenia**, i **klucza podstawowego**, wybierz opcję **Connect** na stronie urządzenia.

    ![Szczegóły połączenia](media/howto-connect-devkit/device-connect.png)

1. Zanotuj szczegóły połączenia. Teraz możesz tymczasowo odłączony od Internetu, podczas przygotowywania urządzenia Mxchip w następnym kroku.

### <a name="prepare-the-devkit-device"></a>Przygotuj urządzenie Mxchip

Jeśli wcześniej używano urządzenia i chcesz ponownie skonfigurować go do korzystania z innej sieci Wi-Fi, parametry połączenia lub pomiaru danych telemetrycznych, naciśnij klawisz, zarówno **A** i **B** przycisków w tym samym czasie. Jeśli to nie zadziała, naciśnij klawisz **resetowania** przycisk, a następnie spróbuj ponownie.

#### <a name="to-prepare-the-devkit-device"></a>Aby przygotować urządzenie Mxchip

1. Pobierz najnowszy wstępnie skompilowanych usługi Azure IoT Central oprogramowania układowego dla zestawu deweloperskiego z [zwalnia](https://aka.ms/iotcentral-docs-MXChip-releases) strony w witrynie GitHub.
1. Podłącz urządzenie Mxchip do komputera deweloperskiego za pomocą kabla USB. W Windows na dysku mapowane na pamięć na urządzeniu Mxchip zostanie otwarte okno Eksploratora plików. Na przykład dysk może być wywoływana **az3166 usługi (D:)** .
1. Przeciągnij **iotCentral.bin** pliku na okno dysku. Po zakończeniu kopiowania urządzenie uruchamia się ponownie z nowym oprogramowaniem układowym.

1. Po ponownym uruchomieniu urządzenia Mxchip, wyświetli się następujący ekran:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Jeśli na ekranie są wyświetlane inaczej, zresetować urządzenie i naciśnij klawisz **A** i **B** przyciski na urządzeniu, w tym samym czasie, aby ponownie uruchomić urządzenie.

1. Urządzenie jest teraz w trybie punktu dostępu. Można połączyć z tym punktem dostępu do sieci Wi-Fi, z komputera lub urządzenia przenośnego.

1. Na komputerze telefonie lub tablecie połączyć do nazwy sieci Wi-Fi, które są wyświetlane na ekranie urządzenia. Po nawiązaniu połączenia z tą siecią, nie masz dostępu do Internetu. Ten stan jest oczekiwana i są tylko połączenia z tą siecią, przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę internetową i przejdź do [ http://192.168.0.1/start ](http://192.168.0.1/start). Następujące wyświetla stronę sieci web:

    ![Strona konfiguracji urządzenia](media/howto-connect-devkit/configpage.png)

    Na stronie sieci web wprowadź:
    - Nazwa sieci Wi-Fi
    - Twoje hasło dostępu do sieci Wi-Fi
    - Kod PIN wyświetlane na ekranie urządzenia
    - Szczegóły połączenia **identyfikator zakresu**, **identyfikator urządzenia**, i **klucz podstawowy** urządzenia (powinien został już zapisany zgodnie z krokami)
    - Wybierz wszystkie dostępne dane telemetryczne miary

1. Po wybraniu **Konfigurowanie urządzenia**, zostanie wyświetlona następująca strona:

    ![Urządzenia skonfigurowane](media/howto-connect-devkit/deviceconfigured.png)

1. Naciśnij klawisz **resetowania** przycisk na urządzeniu.

## <a name="view-the-telemetry"></a>Widok danych telemetrycznych

Po ponownym uruchomieniu urządzenia Mxchip pokazuje ekran na urządzeniu:

* Liczba komunikaty telemetryczne wysyłane.
* Liczba błędów.
* Liczba żądanych właściwości odebranych i liczbę zgłaszanych właściwości wysyłanych z.

> [!NOTE]
> Jeśli urządzenie znajduje się w pętli, gdy próbuje połączyć, sprawdź, czy urządzenie jest **zablokowane** w IoT Central i **odblokowanie** urządzenia, dzięki czemu można połączyć ją do aplikacji.

Potrząśnij urządzeniem, aby wysłać zgłaszanej właściwości. Urządzenie wysyła losową liczbę jako **zdechną numer** właściwości urządzenia.

Wyświetlanie danych telemetrycznych pomiarów i wartości zgłaszanych właściwości i skonfiguruj ustawienia w usłudze Azure IoT Central:

1. Użyj **Device Explorer** można przejść do **pomiarów** strona rzeczywistego urządzenia zestawu deweloperskiego został dodany:

    ![Przejdź do rzeczywistego urządzenia](media/howto-connect-devkit/realdevicenew.png)

1. Na **pomiarów** strony, możesz zobaczyć dane telemetryczne pochodzące z urządzeń zestawu deweloperskiego:

    ![Wyświetlanie telemetrii z rzeczywistego urządzenia](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na **właściwości** strony, można wyświetlić ostatni numer struktury i lokalizacji urządzenia zgłoszona przez urządzenie:

    ![Wyświetl właściwości urządzenia](media/howto-connect-devkit/devicepropertynew.png)

1. Na **ustawienia** strony, możesz zaktualizować ustawienia na urządzeniu zestawu deweloperskiego:

    ![Wyświetl ustawienia urządzenia](media/howto-connect-devkit/devicesettingsnew.png)

1. Na **polecenia** strony, można wywołać **Echo** i **odliczania** poleceń:

    ![Wywołanie polecenia](media/howto-connect-devkit/devicecommands.png)

1. Na **pulpit nawigacyjny** stronie widać, lokalizacja, mapy

    ![Wyświetl pulpit nawigacyjny z urządzenia](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Jeśli chcesz eksplorować i modyfikować kod urządzenia, możesz ją pobrać z witryny GitHub. Jeśli zamierzasz zmodyfikować kod, należy wykonać te instrukcje, aby [przygotowywanie środowiska deweloperskiego](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) pulpitu w systemie operacyjnym.

Aby pobrać kod źródłowy, uruchom następujące polecenie na komputerze stacjonarnym:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Poprzednie polecenie pobiera kod źródłowy w folderze o nazwie `iot-central-firmware`.

> [!NOTE]
> Jeśli **git** nie jest zainstalowany w środowisku deweloperskim, możesz ją pobrać z [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Przeglądanie kodu

Użyj programu Visual Studio Code, aby otworzyć `MXCHIP/mxchip_advanced` folderze `iot-central-firmware` folderu:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Aby zobaczyć, jak dane telemetryczne są wysyłane do usługi Azure IoT Central aplikacji, otwórz **telemetry.cpp** w pliku `src` folderu:

- Funkcja `TelemetryController::buildTelemetryPayload` tworzy ładunek danych telemetrycznych w formacie JSON przy użyciu danych z czujników na urządzeniu.

- Funkcja `TelemetryController::sendTelemetryPayload` wywołania `sendTelemetry` w **AzureIOTClient.cpp** wysyłać ładunek JSON usługi IoT Hub używane przez aplikację usługi Azure IoT Central.

Aby zobaczyć, jak wartości właściwości są zgłaszane do usługi Azure IoT Central aplikacji, otwórz **telemetry.cpp** w pliku `src` folderu:

- Funkcja `TelemetryController::loop` wysyła **lokalizacji** zgłaszane właściwości co 30 sekund. Używa ona `sendReportedProperty` działa w programach **AzureIOTClient.cpp** pliku źródłowego.

- Funkcja `TelemetryController::loop` wysyła **dieNumber** zgłaszane właściwości, gdy wykryje przyspieszeniomierza urządzenia, naciśnij dwukrotnie. Używa ona `sendReportedProperty` działa w programach **AzureIOTClient.cpp** pliku źródłowego.

Aby zobaczyć, jak urządzenie odpowiada na polecenia wywoływane z poziomu aplikacji IoT Central, otwórz **registeredMethodHandlers.cpp** w pliku `src` folderu:

- **DmEcho** funkcji jest obsługa **echo** polecenia. Pokazuje **displayedValue** zgłoszone w ładunku na ekranie urządzenia.

- **DmCountdown** funkcji jest obsługa **odliczania** polecenia. Zmienia kolor LED urządzenia i używa zgłaszanych właściwości do odesłania wartość odliczania w dół do aplikacji IoT Central. Zgłaszane właściwości ma taką samą nazwę jak polecenie. Funkcja używa `sendReportedProperty` działa w programach **AzureIOTClient.cpp** pliku źródłowego.

Kod w **AzureIOTClient.cpp** plik źródłowy używa funkcji z [Microsoft Azure IoT SDK i bibliotek dla języka C](https://github.com/Azure/azure-iot-sdk-c) do interakcji z usługą IoT Hub.

Aby uzyskać informacje o tym, jak można zmodyfikować, kompilacji i Przekaż przykładowy kod do Twojego urządzenia, zobacz **readme.md** w pliku `MXCHIP/mxchip_advanced` folderu.

## <a name="mxchip-device-template-details"></a>Szczegóły szablonu zestawu deweloperskiego urządzenia

Aplikacja utworzone na podstawie szablonu aplikacji przykładowej Devkits zawiera szablon zestawu deweloperskiego urządzenia o następującej charakterystyce:

### <a name="measurements"></a>Miary

#### <a name="telemetry"></a>Telemetria

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| Temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Stany 
| Name (Nazwa)          | `Display name`   | NORMALNY | UWAGA | ZAGROŻENIA | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stan urządzenia   | Zielony  | Orange  | Czerwony    | 

#### <a name="events"></a>Events 
| Name (Nazwa)             | `Display name`      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Naciśnięty przycisk B  | 

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| `Display name` | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napięcie      | setVoltage | V | 0              | 0       | 240     | 0       |
| bieżący      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| `Display name` | Nazwa pola | W tekście | Wyłącz tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | `Display name` | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Właściwości urządzenia | Lokalizacji urządzenia   | location  | location    |
| Text            | Produkowane w     | manufacturedIn   | ND       |

### <a name="commands"></a>Polecenia

| `Display name` | Nazwa pola | Zwracany typ | Nazwa wyświetlana pola wejściowego | Nazwa pola wejściowego | Typ pola wejściowego |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo       | tekst        | wartość, aby wyświetlić         | displayedValue   | tekst             |
| Odliczanie    | Odliczanie  | numer      | Są liczone od               | countFrom        | numer           |

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz Łączenie zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central, sugerowane następnym krokiem jest Dowiedz się, jak [Konfigurowanie szablonu niestandardowego urządzenia](howto-set-up-template.md) dla urządzenia IoT.
