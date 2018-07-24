---
title: Konstruktor Raspberry Pi z aplikacją usługi Azure IoT Central (Python) | Dokumentacja firmy Microsoft
description: Jako deweloper w urządzeniu jak połączyć z urządzeniem Raspberry Pi do usługi Azure IoT Central aplikacji przy użyciu języka Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a3d6ad9f2f442481908bc02252fcc8ab1a74419e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205592"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

W tym artykule opisano jak Deweloper urządzenia do łączenia z urządzeniem Raspberry Pi z aplikacją Microsoft Azure IoT Central, przy użyciu języka programowania Python.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [utworzyć aplikację usługi Azure IoT centralnej](howto-create-application.md).
* Urządzenie Raspberry Pi, systemem operacyjnym Raspbian. Potrzebujesz monitora, klawiatury i myszy podłączone do urządzenia Raspberry Pi można uzyskiwać dostęp do środowiska graficznego interfejsu użytkownika. Urządzenia Raspberry Pi musi mieć możliwość [połączenie z Internetem](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Opcjonalnie [Hat sensie](https://www.raspberrypi.org/products/sense-hat/) tablicy dodatku dla urządzenia Raspberry Pi. Ta tablica zbiera dane telemetryczne z różnych czujników do wysłania do aplikacji usługi Azure IoT Central. Jeśli nie masz **Hat sensie** tablicy, możesz zamiast tego użyć emulatora.

## <a name="sample-devkits-application"></a>**Przykładowy Devkits** aplikacji

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce: 

- Dane telemetryczne, zawierającą pomiarów urządzenia **wilgotności**, **temperatury**, **wykorzystanie**, **Magnometer** (mierzoną wzdłuż X Y, osi Z), **Accelorometer** (mierzoną wzdłuż X, Y osi Z) i **Żyroskop** (mierzoną wzdłuż X, Y osi Z).
- Ustawień przedstawiający **napięcia**, **bieżącego**,**szybkość wentylator** i **IR** przełącznika.
- Właściwości zawierający właściwości urządzenia **zdechną numer** i **lokalizacji** właściwość w chmurze.


Można znaleźć szczegółowe informacje o konfiguracji szablonu urządzenia [Szczegóły szablonu Raspberry PI urządzenia](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **Raspberry Pi** szablon urządzenia i zanotować parametry połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodać rzeczywistego urządzenia do aplikacji usługi Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurowanie urządzenia Raspberry Pi

Poniżej opisano sposób pobierania i konfigurowanie przykładowej aplikacji Python z usługi GitHub. Ta przykładowa aplikacja:

* Wysyła dane telemetryczne i wartości właściwości do usługi Azure IoT Central.
* Odnosi się do ustawienia zmian wprowadzonych w usłudze Azure IoT Central.

> [!NOTE]
> Aby uzyskać więcej informacji na temat przykładowych Raspberry Pi w języku Python, zobacz [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) pliku w usłudze GitHub.

1. Użyj przeglądarki sieci Web w programie desktop Raspberry Pi, aby przejść do [wersje oprogramowania układowego w usłudze Azure IoT Central](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) strony.

1. Pobierz plik zip, który zawiera najnowsze oprogramowanie układowe, do folderu głównego na urządzenia Raspberry Pi. Nazwa pliku wygląda `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Aby rozpakować plik oprogramowania układowego, należy użyć **Menedżera plików** na pulpicie urządzenia Raspberry Pi. Kliknij prawym przyciskiem myszy plik zip, a następnie wybierz **wyodrębnić tutaj**. Ta operacja tworzy folder o nazwie `RaspberryPi-IoTCentral-X.X.X` w folderze głównym.

1. Jeśli nie masz **Hat sensie** tablicy dołączone do urządzenia Raspberry Pi, należy włączyć w emulatorze:
    1. W **Menedżera plików**w `RaspberryPi-IoTCentral-X.X.X` folderu, kliknij prawym przyciskiem myszy **config.iot** pliku, a następnie wybierz **edytora tekstów**.
    1. Zmień wiersz `"simulateSenseHat": false,` do `"simulateSenseHat": true,`.
    1. Zapisz zmiany i zamknąć **edytora tekstów**.

1. Rozpocznij **terminalu** sesji i użyj `cd` polecenie, aby przejść do folderu utworzonego w poprzednim kroku.

1. Aby rozpocząć uruchamianie aplikacji przykładowej, wpisz `./start.sh` w **terminalu** okna. Jeśli używasz **emulatora HAT sensie**, wyświetla jego graficznego interfejsu użytkownika. Aby zmienić wartości danych telemetrycznych wysyłanych do usługi Azure IoT Central aplikacji, można użyć graficznego interfejsu użytkownika.

1. **Terminalu** okno zostanie wyświetlony komunikat, który wygląda jak `Device information being served at http://192.168.0.60:8080`. Adres URL może różnić się w danym środowisku. Skopiuj adres URL i przejdź do strony konfiguracji przy użyciu przeglądarki sieci Web:

    ![Konfigurowanie urządzenia](media/howto-connect-raspberry-pi-python/configure.png)

1. Wprowadź parametry połączenia urządzenia należy zanotować podczas dodawania rzeczywistego urządzenia do aplikacji usługi Azure IoT Central. Następnie wybierz **Konfigurowanie urządzenia**. Zostanie wyświetlony komunikat **skonfigurować urządzenie, urządzenie powinien zostać uruchomiony, wysyłanie danych do usługi Azure IoT Central chwilowo**.

1. W aplikacji usługi Azure IoT Central zobaczysz, jak kod uruchomiony na urządzenia Raspberry Pi współdziała z aplikacją:

    * Na **pomiarów** strony dla Twojego rzeczywistego urządzenia można wyświetlić danych telemetrycznych wysyłanych z urządzenia Raspberry Pi. Jeśli używasz **emulatora HAT sensie**, można zmodyfikować wartości danych telemetrycznych w interfejsie GUI na urządzenia Raspberry Pi.
    * Na **właściwości** stronie widać wartości zgłaszanych **zdechną numer** właściwości.
    * Na **ustawienia** strony, można zmienić różnych ustawień na urządzenia Raspberry Pi, takie jak szybkość napięcia i wychodzącą. Gdy urządzenia Raspberry Pi potwierdza zmianę, to ustawienie jest wyświetlany jako **synchronizowane** w usłudze Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Szczegóły szablonu w usłudze raspberry PI urządzenia

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| Temp           | C     | -40     | 120     | 0              |
| pressure       | hPa pakietu    | 260     | 1260    | 0              |
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

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napięcie      | setVoltage | V | 0              | 0       | 240     | 0       |
| Bieżący      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| Nazwa wyświetlana | Nazwa pola | W tekście | Wyłącz tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| ŚRODOWISKO IR           | activateIR | ON      | WYŁ.      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć z urządzeniem Raspberry Pi z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

* [Łączenie z ogólnego klienta aplikacji Node.js usługi Azure IoT Central](howto-connect-nodejs.md)
