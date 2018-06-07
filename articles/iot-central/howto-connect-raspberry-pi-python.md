---
title: Konstruktor Pi malina do aplikacji Azure IoT centralnej (Python) | Dokumentacja firmy Microsoft
description: Deweloper urządzenia jak nawiązać Pi malina Azure IoT centralnej aplikację przy użyciu języka Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: e9c2d18a518bd5c98fcc35efdb0dff36970a49b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629069"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Pi malina nawiązać połączenia z aplikacji Azure IoT centralnej (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

W tym artykule opisano sposób Deweloper urządzenia nawiązać Pi malina aplikację Microsoft Azure IoT centralnej przy użyciu języka programowania Python.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja Azure IoT centralnej utworzone na podstawie **Devkits próbki** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji centralnej Azure IoT](howto-create-application.md).
* Pi malina urządzenie z systemem operacyjnym Raspbian. Należy monitora, klawiatury i myszy podłączone do sieci Pi malina można uzyskać dostępu do środowiska graficznego interfejsu użytkownika. Pi malina musi mieć możliwość [połączenie z Internetem](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Opcjonalnie [Hat znaczeniu](https://www.raspberrypi.org/products/sense-hat/) tablicy dodatek malina pi. Ta tablica zbiera dane telemetryczne z różnych czujników, aby wysłać do aplikacji Azure IoT centralnej. Jeśli nie masz **Hat znaczeniu** tablicy, możesz zamiast tego użyć emulatora.

Aplikacji utworzone na podstawie **Devkits próbki** szablon aplikacji obejmuje **Pi malina** szablonu urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

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

W aplikacji Azure IoT centralnej, Dodaj rzeczywistego urządzenia z **Pi malina** szablon urządzenia i zanotować ciąg połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodawania rzeczywistego urządzenia do aplikacji Azure IoT centralnej](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>Skonfiguruj malinowe Pi

W poniższych krokach opisano, jak pobrać i skonfigurować przykładowej aplikacji Python z witryny GitHub. Ta przykładowa aplikacja:

* Wysyła dane telemetryczne i wartości właściwości do Azure IoT centralnej.
* Reaguje na ustawienie zmiany wprowadzone w Azure IoT centralnej.

> [!NOTE]
> Aby uzyskać więcej informacji dotyczących przykładu malina Pi Python, zobacz [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) pliku w witrynie GitHub.

1. Korzystanie z przeglądarki sieci Web na pulpicie Pi malina można przejść do [wersje oprogramowania układowego Azure IoT centralnej](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) strony.

1. Pobierz plik zip, który zawiera najnowsze oprogramowania układowego do folderu macierzystego na malina Pi. Nazwa pliku wygląda jak `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Aby rozpakować plik oprogramowania układowego, należy użyć **Menedżera plików** na pulpicie malina Pi. Kliknij prawym przyciskiem myszy plik zip, a następnie wybierz pozycję **wyodrębnić tutaj**. Ta operacja tworzy folder o nazwie `RaspberryPi-IoTCentral-X.X.X` w folderze macierzystego.

1. Jeśli nie masz **Hat znaczeniu** tablicy dołączony do sieci Pi malina należy włączyć emulator:
    1. W **Menedżera plików**w `RaspberryPi-IoTCentral-X.X.X` folderu, kliknij prawym przyciskiem myszy **config.iot** plik i wybierz polecenie **Edytor tekstu**.
    1. Zmień wiersz `"simulateSenseHat": false,` do `"simulateSenseHat": true,`.
    1. Zapisz zmiany i Zamknij **Edytor tekstu**.

1. Uruchom **terminali** sesji i użyj `cd` polecenie, aby przejść do folderu utworzonego w poprzednim kroku.

1. Aby uruchomić przykładową aplikację uruchomiony, wpisz `./start.sh` w **Terminal** okna. Jeśli używasz **emulatora HAT znaczeniu**, jego Wyświetla graficzny interfejs użytkownika. Aby zmienić wartości danych telemetrycznych wysłanych do aplikacji Azure IoT centralnej, można użyć graficznego interfejsu użytkownika.

1. **Terminali** komunikat, który wygląda jak wyświetlany w oknie `Device information being served at http://192.168.0.60:8080`. Adres URL może się różnić w danym środowisku. Skopiuj adres URL i można przejść do strony konfiguracji przy użyciu przeglądarki sieci Web:

    ![Konfigurowanie urządzenia](media/howto-connect-raspberry-pi-python/configure.png)

1. Wprowadź ciąg połączenia urządzenia należy zanotować po dodaniu prawdziwe urządzenia do aplikacji Azure IoT centralnej. Następnie wybierz pozycję **Konfigurowanie urządzenia**. Zostanie wyświetlony komunikat z **urządzenia skonfigurowana, urządzenia powinien rozpocząć wysyłanie danych do usługi Azure IoT centralnej na chwilę**.

1. W aplikacji Azure IoT centralnej Zobacz, jak uruchomiony Pi malina kod współdziała z aplikacją:

    * Na **pomiary** strony dla rzeczywistego urządzenia, można wyświetlić telemetrycznych wysłanych z malina Pi. Jeśli używasz **emulatora HAT znaczeniu**, można zmodyfikować wartości telemetrii w interfejsie GUI na malina Pi.
    * Na **właściwości** strony, można zobaczyć wartość opisane **Die numer** właściwości.
    * Na **ustawienia** strony, można zmienić różnych ustawień na Pi malina, takich jak napięcia i wentylator szybkości. Gdy Pi malina potwierdza zmianę ustawienia pokazuje jako **zsynchronizowane** w Azure IoT centralnej.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób nawiązywania Pi malina Azure IoT centralnej aplikacji, w tym miejscu są Sugerowane następne kroki:

* [Połącz ogólnego aplikacji klienckiej Node.js do centralnej IoT Azure](howto-connect-nodejs.md)