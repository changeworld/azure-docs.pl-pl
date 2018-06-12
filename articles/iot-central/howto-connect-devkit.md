---
title: Podłącz urządzenie zestaw deweloperski do aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Deweloper urządzenia jak Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Azure IoT centralnej.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d7b92359e8875c281fd460f1f5307a7941c11c1f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261580"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Azure IoT centralnej

W tym artykule opisano sposób Deweloper urządzenia połączenia z urządzeniem zestaw deweloperski IoT MXChip (zestaw deweloperski) do aplikacji Microsoft Azure IoT centralnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja Azure IoT centralnej utworzone na podstawie **Devkits próbki** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji centralnej Azure IoT](howto-create-application.md).
1. Urządzenie zestaw deweloperski. Aby zakupić urządzenia zestaw deweloperski, odwiedź stronę [zestaw deweloperski IoT MXChip](http://mxchip.com/az3166).

Aplikacji utworzone na podstawie **Devkits próbki** szablon aplikacji obejmuje **MXChip** szablonu urządzenia o następującej charakterystyce:

### <a name="measurements"></a>Miary

#### <a name="telemetry"></a>Telemetria 

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| wilgotność       | %      | 0       | 100     | 0              |
| Temp           | C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | grupą zarządzania     | -2000   | 2000    | 0              |
| accelerometerY | grupą zarządzania     | -2000   | 2000    | 0              |
| accelerometerZ | grupą zarządzania     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Stany 

| Name (Nazwa)          | Nazwa wyświetlana   | NORMALNY | UWAGA | ZAGROŻENIA | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stan urządzenia   | Zielony  | Orange  | Czerwony    | 

#### <a name="events"></a>Zdarzenia 

| Name (Nazwa)             | Nazwa wyświetlana      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Naciśnięty przycisk B  | 



### <a name="settings"></a>Ustawienia

Ustawienia numeryczne

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napięcia      | setVoltage | V | 0              | 0       | 240     | 0       |
| Bieżący      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| Nazwa wyświetlana | Nazwa pola | W tekście | Wyłączanie tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR.           | activateIR | ON      | WYŁ.      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Die numer   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |


### <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT centralnej, Dodaj rzeczywistego urządzenia z **MXChip** szablon urządzenia i zanotować ciąg połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodawania rzeczywistego urządzenia do aplikacji Azure IoT centralnej](tutorial-add-device.md).

## <a name="prepare-the-devkit-device"></a>Przygotuj urządzenie zestaw deweloperski

> [!TIP]
> Dla urządzenia zestaw deweloperski wskazówki rozwiązywania problemów, zobacz [wprowadzenie zestaw deweloperski IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/).

Aby przygotować urządzenie zestaw deweloperski:

1. Pobierz najnowsze wbudowanych Azure IoT centralnej oprogramowania układowego dla MXChip z [zwalnia](https://github.com/Azure/iot-central-firmware/releases) strony w witrynie GitHub. Filename pobierania na stronie wersjach wygląda jak `AZ3166-IoT-Central-X.X.X.bin`.

1. Podłącz urządzenie zestaw deweloperski na komputerze deweloperskim za pomocą kabla USB. W systemie Windows otwarcie okna Eksploratora plików na dysku mapowane na pamięć na urządzeniu zestaw deweloperski. Na przykład dysk może wywołać **AZ3166 (D:)**.

1. Przeciągnij **iotCentral.bin** pliku na dysku okna. Po zakończeniu kopiowania nowego oprogramowania układowego ponowne uruchomienie urządzenia.

1. Po ponownym uruchomieniu urządzenia zestaw deweloperski, wyświetli się następujący ekran:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Na ekranie są wyświetlane inaczej, naciśnij klawisz **zresetować** przycisk na urządzeniu. 

1. Urządzenie jest teraz w trybie zasięg punktu dostępu. Ten punkt dostępu sieci Wi-Fi można nawiązać z komputera lub urządzenia przenośnego.

1. Na komputerze telefon lub tablet połączyć się z nazwy sieci Wi-Fi wyświetlone na ekranie urządzenia. Podczas łączenia z tą siecią, nie masz dostępu do Internetu. Ten stan jest oczekiwany, a tylko połączenia z tą siecią przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę sieci web i przejdź do [ http://192.168.0.1/start ](http://192.168.0.1/start). Następujące Wyświetla strony sieci web:

    ![Na stronie konfiguracji urządzenia](media/howto-connect-devkit/configpage.png)

    Na stronie sieci web: 
    - Dodaj nazwę sieci Wi-Fi 
    - hasła sieci Wi-Fi 
    - Kod PIN wyświetlany na urządzeniu LCD 
    - Parametry połączenia urządzenia. 
      Można znaleźć parametrów połączenia @ `https://apps.iotcentral.com`  ->  `Device Explorer`  ->  `Device`  ->  `Select or Create a new Real Device`  ->  `Connect this device` (w prawym górnym narożniku) 
    - Wybierz wszystkie dostępne dane telemetryczne pomiary! 

1. Po wybraniu **Konfigurowanie urządzenia**, zobaczysz tę stronę:

    ![Urządzenia skonfigurowane](media/howto-connect-devkit/deviceconfigured.png)

1. Naciśnij klawisz **zresetować** przycisk na urządzeniu.

> [!NOTE]
> Aby zmienić konfigurację urządzenia może korzystać z innej sieci Wi-Fi, parametry połączenia lub pomiaru telemetrii, naciśnij jednocześnie **A** i **B** przyciski na tablicy jednocześnie. Jeśli ta funkcja nie działa, naciśnij klawisz **zresetować** przycisk, a następnie spróbuj ponownie. 

## <a name="view-the-telemetry"></a>Widok telemetrii

Po ponownym uruchomieniu urządzenia zestaw deweloperski, pokazuje ekranu na urządzeniu:

* Liczba wiadomości telemetrii.
* Liczba błędów.
* Liczba żądanej właściwości odebranych i liczba zgłoszonych właściwości wysyłane.

Potrząśnij urządzenia przyrost liczby właściwości zgłoszone wysyłane. Urządzenie wysyła liczbę losową jako **Die numer** właściwości urządzenia.

Możesz wyświetlić dane telemetryczne pomiarów i wartości właściwości zgłoszony i skonfigurować ustawienia w Azure IoT centralnej:

1. Użyj **Explorer urządzenia** można przejść do **pomiary** strony rzeczywistego urządzenia MXChip dodany:

    ![Przejdź do rzeczywistego urządzenia.](media/howto-connect-devkit/realdevice.png)

1. Na **pomiary** strony widać telemetrii pochodzące z urządzenia MXChip:

    ![Dane telemetryczne wyświetleń z rzeczywistego urządzenia.](media/howto-connect-devkit/realtelemetry.png)

1. Na **właściwości** strony, można wyświetlić ostatni numer struktury zgłoszonych przez urządzenia:

    ![Wyświetl właściwości urządzenia](media/howto-connect-devkit/deviceproperties.png)

1. Na **ustawienia** strony, należy zaktualizować ustawienia na urządzeniu MXChip:

    ![Wyświetl ustawienia urządzenia](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>Pobieranie kodu źródłowego

Jeśli chcesz Eksploruj i zmodyfikuj kod urządzenia, można go pobrać z witryny GitHub. Jeśli zamierzasz zmodyfikować kod, należy wykonać te instrukcje, aby [przygotować środowisko projektowe](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) pulpitu systemu operacyjnego.

Aby pobrać kodu źródłowego, uruchom następujące polecenie na komputerze pulpitu:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Poprzednie polecenie pobiera kod źródłowy do folderu o nazwie `iot-central-firmware`. 

> [!NOTE]
> Jeśli **git** nie jest zainstalowany w środowisku projektowania, możesz pobrać go z [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Przeglądanie kodu

Użyj Visual Studio Code, która została zainstalowana podczas przygotowywania środowiska deweloperskiego, aby otworzyć `AZ3166` folderu w `iot-central-firmware` folderu: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Aby zobaczyć, jak dane telemetryczne są wysyłane do aplikacji Azure IoT centralna, otwórz **main_telemetry.cpp** plików w folderze źródłowym.

Funkcja `buildTelemetryPayload` tworzy ładunek danych telemetrycznych JSON przy użyciu danych z czujników na urządzeniu.

Funkcja `sendTelemetryPayload` wywołania `sendTelemetry` w **iotHubClient.cpp** do wysyłania ładunek JSON do Centrum IoT Azure IoT centralnej aplikacja używa.

Aby zobaczyć sposób zgłaszania wartości właściwości do aplikacji Azure IoT centralna, otwórz **main_telemetry.cpp** plików w folderze źródłowym.

Funkcja `telemetryLoop` wysyła **doubleTap** podać właściwość wykrycie przyspieszeniomierza naciśnij dwa razy. Używa `sendReportedProperty` działać w **iotHubClient.cpp** pliku źródłowego.

Kod w **iotHubClient.cpp** plik źródłowy używa funkcji z [ zestawów SDK programu Microsoft Azure IoT i bibliotek C](https://github.com/Azure/azure-iot-sdk-c) wchodzić w interakcje z Centrum IoT.

Aby uzyskać informacje na temat modyfikowania, kompilacji i przekazać przykładowy kod do Twojego urządzenia, zobacz **readme.md** w pliku `AZ3166` folderu.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób podłączania urządzeń zestaw deweloperski do aplikacji Azure IoT centralnej, Oto Sugerowane następne kroki:

* [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)