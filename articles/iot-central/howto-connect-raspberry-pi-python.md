---
title: Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central (Python) | Dokumentacja firmy Microsoft
description: Jako deweloper w urządzeniu jak połączyć z urządzeniem Raspberry Pi do usługi Azure IoT Central aplikacji przy użyciu języka Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9f39832b50ed983e7d8a0bfc0a06366870717fa3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051989"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

W tym artykule opisano jak Deweloper urządzenia do łączenia z urządzeniem Raspberry Pi z aplikacją Microsoft Azure IoT Central, przy użyciu języka programowania Python.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące składniki:

* Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Urządzenie Raspberry Pi, systemem operacyjnym Raspbian. Potrzebujesz monitora, klawiatury i myszy podłączone do urządzenia Raspberry Pi można uzyskiwać dostęp do środowiska graficznego interfejsu użytkownika. Urządzenia Raspberry Pi musi mieć możliwość [połączenie z Internetem](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Opcjonalnie [Hat sensie](https://www.raspberrypi.org/products/sense-hat/) tablicy dodatku dla urządzenia Raspberry Pi. Ta tablica zbiera dane telemetryczne z różnych czujników do wysłania do aplikacji usługi Azure IoT Central. Jeśli nie masz **Hat sensie** tablicy, możesz zamiast tego użyć emulatora (dostępne jako część obrazu urządzenia Raspberry Pi).

## <a name="sample-devkits-application"></a>**Przykładowy Devkits** aplikacji

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce: 

- Dane telemetryczne, obejmuje następujące pomiary zbieranych urządzenia:
    - Wilgotność
    - Temperatura
    - Ciśnienie
    - Magnetometrów (X, Y, Z)
    - Przyspieszeniomierza (X, Y, Z)
    - Żyroskop (X, Y, Z)
- Ustawienia
    - Napięcie
    - Bieżące
    - Wentylator szybkości
    - Przełącz Podczerwieni.
- Właściwości
    - Zdechną liczba właściwości urządzenia
    - Właściwość chmury lokalizacji

Pełne szczegóły konfiguracji szablonu urządzeń można znaleźć [Szczegóły szablonu Raspberry PI urządzenia](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **Raspberry Pi** urządzenia szablonu i Śledź szczegółów połączenia urządzeń (**klucza podstawowego identyfikator zakresu, identyfikator urządzenia**). Aby uzyskać więcej informacji, zobacz [dodać rzeczywistego urządzenia do aplikacji usługi Azure IoT Central](tutorial-add-device.md).


### <a name="configure-the-raspberry-pi"></a>Konfigurowanie urządzenia Raspberry Pi

Poniżej opisano sposób pobierania i konfigurowanie przykładowej aplikacji Python z usługi GitHub. Ta przykładowa aplikacja:

* Wysyła dane telemetryczne i wartości właściwości do usługi Azure IoT Central.
* Odnosi się do ustawienia zmian wprowadzonych w usłudze Azure IoT Central.

Aby skonfigurować urządzenie, [postępuj zgodnie z instrukcjami krok po kroku w witrynie GitHub.](https://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> Aby uzyskać więcej informacji na temat przykładowych Raspberry Pi w języku Python, zobacz [README](https://aka.ms/iotcentral-docs-Raspi-releases) pliku w usłudze GitHub.


1. Po skonfigurowaniu urządzenia urządzenia na początek chwilowo wysyłania danych do usługi Azure IoT Central.
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
| Bieżące      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| Nazwa wyświetlana | Nazwa pola | W tekście | Wyłącz tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć z urządzeniem Raspberry Pi z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

* [Łączenie z ogólnego klienta aplikacji Node.js usługi Azure IoT Central](howto-connect-nodejs.md)
