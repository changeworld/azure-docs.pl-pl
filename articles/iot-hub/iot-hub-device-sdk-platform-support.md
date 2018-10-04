---
title: Obsługa Platform zestawy SDK urządzeń Azure IoT | Dokumentacja firmy Microsoft
description: Pojęcia — lista platform obsługiwanych przez zestawy SDK urządzeń IoT platformy Azure
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: 078f6bcd7aa48f2a63118e6dfb625d9b2c69fbb3
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248027"
---
# <a name="azure-iot-sdks-platform-support"></a>Obsługa Platform do usługi Azure IoT SDK

[Azure IoT SDKs](iot-hub-devguide-sdks.md) to zestaw bibliotek do interakcji z usługi IoT Hub i Device Provisioning Service przy użyciu szerokiej języka i składniki obsługi platformy. Zestawy SDK na najbardziej popularnych platform, a deweloperzy mogą portu zestawu SDK języka C do określonej platformy, postępując zgodnie z [przenoszenie wskazówki](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Firma Microsoft obsługuje wiele różnych systemów operacyjnych/platform/platform i można rozszerzyć za pomocą zestawu SDK C usługi IoT platformy Azure. Niektóre są obsługiwane oficjalnie przez zespół, pogrupowane według warstwy, które reprezentują poziom pomocy technicznej, które użytkownicy mogą oczekiwać. *W pełni obsługiwane platformy* oznacza, że firma Microsoft:

    * Stale kompiluje i uruchamia testy end-to-end na głównej maszynie wirtualnej i wersje obsługiwane LTS.
    * Zawiera wskazówki dotyczące instalacji lub pakietów, jeśli ma to zastosowanie.
    * W pełni obsługuje platformy w witrynie GitHub.

Ponadto listy partnerów ma przenoszone naszego zestawu SDK języka C na wielu platformach i są one utrzymywanie warstwę abstrakcji platformy (PAL). [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/) również funkcje listę platform systemów operacyjnych różnych zestawów SDK zostały przetestowane przed. Zestawy SDK regularnie kompilacji na tych platformach, z ograniczoną testowania i obsługi:

* MBED2
* Arduino
* Windows CE 2013 (wycofana w października 2018 r.)
* .NET standard 1.3 przy użyciu platformy .NET Core 2.1 i .NET Framework 4.7
* Platformy Xamarin iOS, Android i platformy uniwersalnej systemu Windows
* Android przy użyciu języka Java

## <a name="supported-platforms"></a>Obsługiwane platformy

Istnieje kilka obsługiwane platformy.

### <a name="c-sdk"></a>ZESTAW SDK JĘZYKA C

| System operacyjny                  | Architektura | Kompilator             | Biblioteki protokołu TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | GCC 5.4.0            | openssl - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | GCC 7.3              | WolfSSL — 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl — 1.1.0g  |
| OSX 10.13.4         | x64  | Środowisko XCode 9.4.1          | Natywne OSX        |
| Windows Server 2016 | x64  | Program Visual Studio 14.0.X | Dostawca SChannel          |
| Windows Server 2016 | x86  | Program Visual Studio 14.0.X | Dostawca SChannel          |
| Debian 9 Stretch    | x64  | GCC 7.3              | Openssl — 1.1.0f  |

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

| System operacyjny                  | Architektura | Kompilator   | Biblioteki protokołu TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Środowisko Python 2.7 | biblioteki openssl     |
| Windows Server 2016 | x64  | Środowisko Python 2.7 | biblioteki openssl     |
| Windows Server 2016 | x86  | Język Python 3.5 | biblioteki openssl     |
| Windows Server 2016 | x64  | Język Python 3.5 | biblioteki openssl     |
| Ubuntu 18.04 LTS    | x86  | Środowisko Python 2.7 | biblioteki openssl     |
| Ubuntu 18.04 LTS    | x86  | Języka Python 3.4 | biblioteki openssl     |
| Wysoki z systemem MacOS Sierra   | x64  | Środowisko Python 2.7 | biblioteki openssl     |

### <a name="net-sdk"></a>Zestaw SDK .NET

| System operacyjny                  | Architektura | Framework            | Standardowa (Standard)          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET framework 4.7   | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET framework 4.5.1 | ND               |

### <a name="nodejs-sdk"></a>Zestaw SDK dla platformy Node.js

| System operacyjny                                           | Architektura | Wersja węzła |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (przy użyciu obrazu platformy docker węzła 6) | X64  | Węzeł 6       |
| Windows Server 2016                          | X64  | Węzeł 6       |

### <a name="java-sdk"></a>Zestaw SDK Java

| System operacyjny                  | Architektura | Wersja języka Java |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>Platformy obsługiwane przez partnerów

Klienci mogą rozszerzyć nasze wsparcie platformy przez przenoszenie C zestawu SDK Azure IoT, w szczególności, Tworzenie warstwy abstrakcji platformy (PAL) zestawu SDK. Firma Microsoft współpracuje z partnerami w celu zapewnienia wsparcia dodatkowego. Lista partnerów ma przenoszone zestawu SDK języka C na wielu platformach i utrzymywanie listy PAL.

| Partner             | Urządzenia                            | Link                     | Pomoc techniczna |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT modemu     | [LTE Qualcomm dla zestawu SDK usługi IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Seria STM32L4 <br/> Seria STM32F4 <br/>  Seria STM32F7 <br/>  Zestaw odnajdywania STM32L4 węzła IoT    | [X MODUŁU CHMURY](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X MODUŁÓW PLATFORMY AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P NUCLEO PLATFORMY AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP CLD PLATFORMY AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Pomoc techniczna](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | Aplikacja Launchpad CC3220SF <br/> Aplikacja Launchpad CC3220S <br/> Aplikacja Launchpad MSP432E4      | [Wtyczka usługi Azure IoT dla SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum TI E2E](https://e2e.ti.com) <br/> [Forum TI E2E CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Forum TI E2E MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Kolejne kroki

* [Zestawy SDK urządzeń i usługi](iot-hub-devguide-sdks.md)
* [Wskazówki dotyczące przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)