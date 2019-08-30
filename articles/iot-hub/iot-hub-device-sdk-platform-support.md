---
title: Obsługa platformy zestawów SDK urządzeń Azure IoT | Microsoft Docs
description: Pojęcia — lista platform obsługiwanych przez zestawy SDK urządzeń usługi Azure IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: robinsh
ms.openlocfilehash: 9a64c6e476910ab4fe983fa949680f05fdded3ae
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161861"
---
# <a name="azure-iot-sdks-platform-support"></a>Obsługa platform zestawów SDK usługi Azure IoT

[Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md) to zestaw bibliotek umożliwiających współdziałanie z IoT Hub i usługą Device Provisioning z szeroką obsługą języków i platform. Zestawy SDK działają na większości popularnych platform, a deweloperzy mogą przenieść zestaw C SDK do określonej platformy, postępując zgodnie ze [wskazówkami dotyczącymi przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Firma Microsoft obsługuje różne systemy operacyjne/platformy/struktury i można je rozszerzyć przy użyciu zestawu Azure IoT C SDK. Niektóre z nich są obsługiwane oficjalnie przez zespół, pogrupowane w warstwy, które reprezentują poziom oczekiwanych przez użytkowników pomocy technicznej. W *pełni obsługiwane platformy* to firma Microsoft:

- Ciągle kompiluje i uruchamia kompleksowe testy względem wzorców i LTS obsługiwanych wersji.  Aby zapewnić pokrycie testów w różnych wersjach, przeważnie przetestujemy się do najnowszej wersji LTS i najbardziej popularnej wersji.  Inne wersje tej samej platformy mogą być obsługiwane przez zgodność wersji platformy.
- Zapewnia wskazówki dotyczące instalacji lub pakiety, jeśli ma to zastosowanie.
- W pełni obsługuje platformy w serwisie GitHub.

Ponadto lista partnerów ma już port SDK języka C na większej liczbie platform i utrzymuje warstwę abstrakcji platformy (PAL). [Wykaz urządzeń z certyfikatem platformy Azure dla IoT](https://catalog.azureiotsolutions.com/) również zawiera listę platform systemu operacyjnego, względem których przetestowano różne zestawy SDK. Zestawy SDK są również regularnie kompilowane na tych platformach z ograniczoną testowaniem i wsparciem:

* MBED2
* Arduino
* Windows CE 2013 (zaniechanie w październiku 2018)
* .NET Standard 1,3 z platformą .NET Core 2,1 i .NET Framework 4,7
* Xamarin iOS, Android, platformy UWP

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są kilka platform.

### <a name="c-sdk"></a>ZESTAW SDK JĘZYKA C

| OS                  | Przekroj | Compiler             | Biblioteka TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | OpenSSL – 1.0.2 g |
| Ubuntu 18.04 LTS    | X64  | w zatoce 7,3              | WolfSSL – 1,13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0. X          | OpenSSL – 1.1.0 g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Natywna OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Rozciągnij Debian 9    | x64  | w zatoce 7,3              | OpenSSL – 1.1.0 f  |

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

| OS                  | Przekroj | Compiler   | Biblioteka TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x64  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x86  | Python 3,5 | OpenSSL     |
| Windows Server 2016 | x64  | Python 3,5 | OpenSSL     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | OpenSSL     |
| Ubuntu 18.04 LTS    | x86  | Python 3,4 | OpenSSL     |
| MacOS wysoka firma Sierra   | x64  | Python 2.7 | OpenSSL     |

### <a name="net-sdk"></a>Zestaw SDK .NET

| OS                  | Przekroj | Architektura            | Standardowa (Standard)          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET framework 4.7   | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | ND               |

### <a name="nodejs-sdk"></a>Zestaw SDK dla platformy Node.js

| OS                                           | Przekroj | Wersja węzła    |
|----------------------------------------------|------|-----------------|
| Ubuntu 16,04 LTS (przy użyciu obrazu platformy Docker w węźle 6) | X64  | LTS i bieżące |
| Windows Server 2016                          | X64  | LTS i bieżące |

### <a name="java-sdk"></a>Zestaw SDK Java

| OS                  | Przekroj | Wersja języka Java |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Interfejs API systemu Android 28 | X64  | Java 8       |
| Rzeczy dla systemu Android | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Platformy obsługiwane przez partnerów

Klienci mogą rozbudować nasze wsparcie dla platformy, przechodząc do zestawu SDK usługi Azure IoT C, a w przypadku tworzenia warstwy abstrakcji platformy (PAL) zestawu SDK. Firma Microsoft współpracuje z partnerami, aby zapewnić rozszerzoną pomoc techniczną. Lista partnerów ma zaportować zestaw SDK języka C na więcej platform i utrzymywać PAL.

| Partner             | Urządzenia                            | Łącze                     | Pomoc techniczna |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem Qualcomm MDM9206 LTE IoT     | [Zestaw SDK usługi Qualcomm LTE for IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST MICROELECTRONICS | Seria STM32L4 <br/> Seria STM32F4 <br/>  Seria STM32F7 <br/>  STM32L4 Discovery Kit dla węzła IoT    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Pomoc techniczna](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [Wtyczka usługi Azure IoT dla SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum E2E TI](https://e2e.ti.com) <br/> [Forum usługi TI E2E dla CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E Forum for MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Następne kroki

* [Zestawy SDK urządzeń i usługi](iot-hub-devguide-sdks.md)
* [Wskazówki dotyczące przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
