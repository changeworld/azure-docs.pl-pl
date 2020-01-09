---
title: Łączenie Raspberry Pi z aplikacją platformy Azure IoT Central (Python) | Microsoft Docs
description: Jako deweloper urządzenia dołączymy Raspberry Pi do aplikacji platformy Azure IoT Central przy użyciu języka Python.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3daa567a916bd0abeb407028c7d06bd1f2bd464b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454106"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Łączenie Raspberry Pi z aplikacją platformy Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano sposób, w jaki deweloper urządzenia nawiązuje połączenie Raspberry Pi z aplikacją IoT Central Microsoft Azure przy użyciu języka programowania w języku Python.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące składniki:

* Aplikacja IoT Central platformy Azure utworzona na podstawie szablonu aplikacji **starszej** aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Urządzenie Raspberry Pi działające w systemie operacyjnym raspbian. Raspberry Pi musi mieć możliwość nawiązania połączenia z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Aby dowiedzieć się więcej o konfigurowaniu i łączeniu się z urządzeniem Raspberry Pi, odwiedź stronę wprowadzenie do [Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

W aplikacji IoT Central platformy Azure Dodaj nowy szablon urządzenia **Raspberry Pi** o następujących cechach:

- Dane telemetryczne, które obejmują następujące pomiary zebrane przez urządzenie:
  - Wilgotność
  - Temperatura
  - Ciśnienie
  - Magnetometer (X, Y, Z)
  - Przyspieszeniomierz (X, Y, Z)
  - Żyroskop (X, Y, Z)
- Ustawienia
  - Zakres
  - Bieżący
  - Szybkość wentylatorów
  - Przełącznik IR.
- Właściwości
  - Właściwość urządzenia numeru wartości
  - Właściwość chmury lokalizacji

1. Wybierz pozycję **+ Nowy** z szablonów urządzeń ![szablon urządzenia](media/howto-connect-raspberry-pi-python/adddevicetemplate.png)
   

2. Wybierz pozycję **Raspberry Pi** i Utwórz szablon urządzenia Raspberry Pi ![Dodaj szablon urządzenia](media/howto-connect-raspberry-pi-python/newdevicetemplate.png)

Aby uzyskać szczegółowe informacje na temat konfiguracji szablonu urządzenia, zobacz [szczegóły szablonu urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie z szablonu urządzenia **Raspberry Pi** . Zanotuj szczegóły połączenia urządzenia (**Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**). Aby uzyskać więcej informacji, zobacz [Dodawanie rzeczywistego urządzenia do aplikacji IoT Central platformy Azure](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Skonfiguruj Raspberry Pi

W poniższych krokach opisano sposób pobierania i konfigurowania przykładowej aplikacji w języku Python z usługi GitHub. Ta przykładowa aplikacja:

* Wysyła wartości telemetryczne i właściwości do usługi Azure IoT Central.
* Reaguje na zmianę ustawień wprowadzonych w usłudze Azure IoT Central.

1. Połącz się ze środowiskiem powłoki w Raspberry Pi, z pulpitu Raspberry Pi lub zdalnie przy użyciu protokołu SSH.

1. Uruchom następujące polecenie, aby zainstalować klienta programu IoT Central Python:

    ```bash
    pip install iotc
    ```

1. Pobierz przykładowy kod w języku Python:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Edytuj pobrany plik `app.py` i Zastąp symbole zastępcze `DEVICE_ID`, `SCOPE_ID`i `PRIMARY/SECONDARY device KEY` wartościami połączeń, które zostały wcześniej wykonane. Zapisz zmiany.

    > [!TIP]
    > W powłoce na Raspberry Pi można użyć edytorów tekstu **nano** lub **VI** .

1. Użyj następującego polecenia, aby uruchomić przykład:

    ```bash
    python app.py
    ```

    Raspberry Pi zaczyna wysyłać pomiary danych telemetrycznych do usługi Azure IoT Central.

1. W aplikacji IoT Central platformy Azure można zobaczyć, jak kod działający w Raspberry Pi współdziała z aplikacją:

    * Na stronie **miary** rzeczywistego urządzenia można zobaczyć dane telemetryczne wysyłane z Raspberry Pi.
    * Na stronie **Właściwości** można wyświetlić właściwość " **Die numer** urządzenia".
    * Na stronie **Ustawienia** można zmienić ustawienia w Raspberry Pi, takie jak napięcie i szybkość wentylatorów. Gdy Raspberry Pi potwierdzi zmianę, ustawienie jest wyświetlane jako **zsynchronizowane**.

## <a name="raspberry-pi-device-template-details"></a>Szczegóły szablonu urządzenia Raspberry Pi

Aplikacja utworzona na podstawie **przykładowego** szablonu aplikacji Devkits zawiera szablon urządzenia **Raspberry Pi** o następujących cechach:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| przyspieszeniomierz | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Wartość początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Zakres      | setnapięci | Wolty | 0              | 0       | 240     | 0       |
| Bieżący      | SetCurrent | Amper  | 0              | 0       | 100     | 0       |
| Szybkość wentylatorów    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Przełącz ustawienia

| Nazwa wyświetlana | Nazwa pola | Na tekst | Off text | Wartość początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwość urządzenia | Numer struktury   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć Raspberry Pi z aplikacją Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie niestandardowego szablonu urządzenia](howto-set-up-template.md) dla własnego urządzenia IoT.
