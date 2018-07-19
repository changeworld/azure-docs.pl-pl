---
title: Usługa Azure IoT Hub — wprowadzenie do łączenia urządzeń IoT w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z usługi IoT — tablice i pakiety startowe usługi Azure IoT Hub. Urządzenia można wysyłać danych telemetrycznych do Centrum IoT i Centrum IoT Hub można monitorować urządzenia oraz zarządzaj nimi.
author: dominicbetts
manager: timlt
keywords: Usługa Azure iot hub samouczek
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125196"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Usługa Azure IoT Hub Rozpoczynanie pracy z rzeczywistych urządzeń

Te artykuły zawierają wprowadzenie do usługi Azure IoT Hub i uruchamianie zestawów SDK urządzeń na rzeczywistych urządzeniach.

## <a name="set-up-your-device"></a>Konfigurowanie urządzenia

Łączenie urządzeń IoT lub bramy usługi Azure IoT Hub:

| Urządzenia IoT                       | Język programowania |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Mxchip IoT DevKit                       | [Arduino w VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Urządzenie Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Urządzenie Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Urządzenie Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Symulator urządzeń w trybie online         | [Urządzenie raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
