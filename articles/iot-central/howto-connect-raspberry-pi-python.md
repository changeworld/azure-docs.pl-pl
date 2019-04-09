---
title: Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central (Python) | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia jak połączyć z urządzeniem Raspberry Pi do usługi Azure IoT Central aplikacji przy użyciu języka Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 6ac16651e2d49dd903ff994b18a8f571bd92fbf6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272363"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

W tym artykule opisano jak Deweloper urządzenia do łączenia z urządzeniem Raspberry Pi z aplikacją Microsoft Azure IoT Central, przy użyciu języka programowania Python.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące składniki:

* Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Urządzenie Raspberry Pi, systemem operacyjnym Raspbian. Urządzenia Raspberry Pi musi mieć możliwość łączenia się z Internetem. Aby uzyskać więcej informacji, zobacz [konfigurowania urządzenia Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Przykładowy Devkits** aplikacji

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce:

- Dane telemetryczne, obejmuje następujące pomiary zbieranych urządzenia:
  - Wilgotność
  - Temperatura
  - Ciśnienie
  - Magnetometrów (X, Y, Z)
  - Accelerometer (X, Y, Z)
  - Żyroskop (X, Y, Z)
- Ustawienia
  - Napięcie
  - Bieżący
  - Wentylator szybkości
  - Przełącz Podczerwieni.
- Właściwości
  - Zdechną liczba właściwości urządzenia
  - Właściwość chmury lokalizacji

Aby uzyskać szczegółowe konfiguracji szablonu urządzenia, zobacz [Szczegóły szablonu Raspberry Pi urządzenia](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **Raspberry Pi** szablon urządzenia. Zwróć uwagę na urządzeniu szczegóły połączenia (**identyfikator zakresu**, **identyfikator urządzenia**, i **klucz podstawowy**). Aby uzyskać więcej informacji, zobacz [dodać rzeczywistego urządzenia do aplikacji usługi Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Konfigurowanie urządzenia Raspberry Pi

Poniżej opisano sposób pobierania i konfigurowanie przykładowej aplikacji Python z usługi GitHub. Ta przykładowa aplikacja:

* Wysyła dane telemetryczne i wartości właściwości do usługi Azure IoT Central.
* Odnosi się do ustawienia zmian wprowadzonych w usłudze Azure IoT Central.

Aby skonfigurować urządzenie, [postępuj zgodnie z instrukcjami krok po kroku w serwisie GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Gdy urządzenie jest skonfigurowane, rozpoczyna się urządzenie, wysyłanie pomiarów dotyczących prawdziwych danych telemetrycznych do usługi Azure IoT Central.
1. W aplikacji usługi Azure IoT Central można zobaczyć, jak kod uruchomiony na urządzenia Raspberry Pi współdziała z aplikacją:

    * Na **pomiarów** strony dla Twojego rzeczywistego urządzenia można wyświetlić danych telemetrycznych wysyłanych z urządzenia Raspberry Pi.
    * Na **ustawienia** strony, można zmienić ustawienia urządzenia Raspberry Pi, takie jak szybkość napięcia i wychodzącą. Gdy urządzenia Raspberry Pi potwierdza zmianę, to ustawienie jest wyświetlany jako **synchronizowane**.

## <a name="raspberry-pi-device-template-details"></a>Szczegóły szablonu w usłudze raspberry Pi urządzenia

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

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
| IR           | activateIR | ON      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Type            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak połączyć z urządzeniem Raspberry Pi z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

* [Łączenie z ogólnego klienta aplikacji Node.js usługi Azure IoT Central](howto-connect-nodejs.md)
