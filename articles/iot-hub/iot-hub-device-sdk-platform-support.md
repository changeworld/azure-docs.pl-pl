---
title: Obsługa platformy Zestaw SDK urządzeń Usługi Azure IoT | Dokumenty firmy Microsoft
description: Zestaw SDK urządzeń typu open source jest dostępny w usłudze GitHub w językach C, .NET (C#), Java, Node.js i Python, aby połączyć urządzenia z usługą Azure IoT Hub i usługą inicjowania obsługi administracyjnej urządzeń (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045121"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Obsługa platformy Zestaw SDK urządzeń Usługi Azure IoT

Firma Microsoft stara się stale rozszerzać zakres urządzeń obsługujących usługę Azure IoT Hub. Firma Microsoft publikuje zestawów SDK urządzeń typu open source w usłudze GitHub, aby ułatwić łączenie urządzeń z centrum Azure IoT Hub i usługą inicjowania obsługi administracyjnej urządzeń. SDK urządzeń są dostępne dla języków C, .NET (C#), Java, Node.js i Python. Firma Microsoft testuje każdy pakiet SDK, aby upewnić się, że działa on w obsługiwanych konfiguracjach wyszczególnionych dla niego w sekcji [Microsoft SDK i obsługa platformy urządzeń.](#microsoft-sdks-and-device-platform-support)

Oprócz zestawów SDK urządzeń firma Microsoft udostępnia kilka innych możliwości umożliwienia klientom i deweloperom łączenia urządzeń z usługą Azure IoT:

* Firma Microsoft współpracuje z kilkoma firmami partnerskimi, aby ułatwić im publikowanie zestawów deweloperskich opartych na zestawie Azure IoT C SDK dla ich platform sprzętowych.

* Firma Microsoft współpracuje z zaufanymi partnerami firmy Microsoft, aby zapewnić stale rozwijający się zestaw urządzeń, które zostały przetestowane i certyfikowane dla usługi Azure IoT. Aby uzyskać bieżącą listę tych urządzeń, zobacz [katalog urządzeń z certyfikatem platformy Azure dla IoT](https://catalog.azureiotsolutions.com/).

* Firma Microsoft udostępnia warstwę abstrakcji platformy (PAL) w sdk C urządzenia usługi Azure IoT Hub, który pomaga deweloperom łatwo przenieść zestaw SDK na ich platformę. Aby dowiedzieć się więcej, zobacz [wskazówki dotyczące przenoszenia SDK języka C](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Ten temat zawiera informacje o pakietach Microsoft SDK i konfiguracjach platformy, które obsługują, a także każdej z innych opcji wymienionych powyżej.

## <a name="microsoft-sdks-and-device-platform-support"></a>Pakiety SDK firmy Microsoft i obsługa platformy urządzeń

Firma Microsoft publikuje pakiety SDK typu open source w usłudze GitHub dla następujących języków: C, .NET (C#), Node.js, Java i Python. SDK i ich zależności są wymienione w tej sekcji. Pakiety SDK są obsługiwane na dowolnej platformie urządzenia, która spełnia te zależności.

Dla każdego z wymienionych pakietów SDK firma Microsoft:

* Stale tworzy i uruchamia testy end-to-end dla gałęzi głównej odpowiedniego SDK w usłudze GitHub na kilku popularnych platformach.  Aby zapewnić pokrycie testowe w różnych wersjach kompilatora, zazwyczaj testujemy pod względem najnowszej wersji LTS i najpopularniejszej wersji.

* Zawiera wskazówki dotyczące instalacji lub pakiety instalacyjne, jeśli dotyczy.

* W pełni obsługuje zestawy SDK w usłudze GitHub z kodem open source, ścieżką dla wkładu klientów i zaangażowaniem zespołu produktów w problemy z githubem.

### <a name="c-sdk"></a>C SDK

Zestaw [SDK urządzenia usługi Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c) jest testowany z następującymi konfiguracjami i obsługuje je.

| System operacyjny                  | Biblioteka TLS                  | Dodatkowe wymagania                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL lub BearSSL | Gniazda Berkeley</br></br>Interfejs przenośnego systemu operacyjnego (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emulowane w OSX 10.13.4                                                               |
| Rodzina systemów Windows 10   | Schannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [Zestaw deweloperski MXChip IoT](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| System operacyjny Azure Sphere     | Wilki                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL (Niedźwiedź)                      | [ESP32 lub ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Zestaw [SDK urządzenia usługi Azure IoT Hub Python](https://github.com/Azure/azure-iot-sdk-python) jest testowany z następującymi konfiguracjami i obsługuje je.

| System operacyjny                  | Kompilator                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.5 lub nowsze |
| MacOS Wysokie Sierra   | Python 2.7.*, 3.5 lub nowsze |
| Rodzina systemów Windows 10   | Python 2.7.*, 3.5 lub nowsze |

Tylko python w wersji 3.5.3 lub nowszej obsługuje asynchroniczne interfejsy API, zaleca się użycie wersji 3.7 lub nowszej.

### <a name="net-sdk"></a>Zestaw SDK .NET

Zestaw [SDK urządzenia usługi Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp) jest testowany z następującymi konfiguracjami i obsługuje je.

| System operacyjny                                   | Standardowa                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Jednostki SKU dla komputerów stacjonarnych i serwerów systemu Windows 10   | .NET Core 2.1, .NET Framework 4.5.1 lub .NET Framework 4.7 |

Zestaw .NET SDK może być również używany z systemem Windows IoT Core z [agentem urządzeń platformy Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) lub [niestandardową usługą NTService, która może używać rpc do komunikowania się z aplikacjami platformy uniwersalnej](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)systemu Windows.

### <a name="nodejs-sdk"></a>Zestaw SDK dla platformy Node.js

Zestaw [SDK urządzenia usługi Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) jest testowany z następującymi konfiguracjami i obsługuje je.

| System operacyjny                  | Wersja węzła    |
|---------------------|-----------------|
| Linux               | LTS i prąd |
| Rodzina systemów Windows 10   | LTS i prąd |

### <a name="java-sdk"></a>Zestaw SDK Java

Zestaw [SDK urządzenia Java usługi Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-java) jest testowany z następującymi konfiguracjami i obsługuje je.

| System operacyjny                     | Wersja środowiska Java |
|------------------------|--------------|
| Interfejs API systemu Android 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 rodzina x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Zestawy programistyczne obsługiwane przez partnerów

Firma Microsoft współpracuje z różnymi partnerami w celu zapewnienia zestawów programistów dla kilku architektur mikroprocesorowych. Ci partnerzy przenieśli zestaw SDK usługi Azure IoT C na swoją platformę. Partnerzy tworzą i utrzymują warstwę abstrakcji platformy (PAL) sdk. Firma Microsoft współpracuje z tymi partnerami w celu zapewnienia rozszerzonej pomocy technicznej.

| Partner             | Urządzenia                            | Link                     | Pomoc techniczna |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif ( Espressif )           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE dla IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| St Mikroelektronika | Seria STM32L4 <br/> Seria STM32F4 <br/>  Seria STM32F7 <br/>  Zestaw odnajdowania STM32L4 dla węzła IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [PLATFORMA FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Pomoc techniczna](https://www.st.com/content/st_com/en/support/support-home.html)
| Instrumenty texas   | Płyta startowa CC3220SF </br> Płyta startowa CC3220S </br> Płyta startowa CC3235SF </br> Program Startowy CC3235S </br> Klawiatura startowa MSP432E4 | [Wtyczka IoT platformy Azure dla simplelink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E Forum](https://e2e.ti.com) <br/> [TI E2E Forum dla CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E Forum dla MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Przenoszenie sdk C usługi IoT platformy Microsoft Azure

Jeśli twoja platforma urządzenia nie jest objęta jedną z poprzednich sekcji, możesz rozważyć przeniesienie sdk C usługi Azure IoT. Przenoszenie SDK C polega przede wszystkim na implementacji warstwy abstrakcji platformy (PAL) sdk. Pal definiuje prymitywy, które zapewniają klej między urządzeniem i funkcje wyższego poziomu w SDK. Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Partnerzy firmy Microsoft i certyfikowane urządzenia Azure IoT

Firma Microsoft współpracuje z wieloma partnerami w celu ciągłego rozszerzania wszechświata usługi Azure IoT o przetestowane i certyfikowane urządzenia usługi Azure IoT.

* Aby przeglądać urządzenia z certyfikatem Usługi Azure IoT, zobacz [Wykaz urządzeń certyfikowanych platformy Microsoft Azure dla IoT](https://catalog.azureiotsolutions.com/).

* Aby dowiedzieć się więcej o ekosystemie certyfikatu platformy Azure dla IoT, zobacz [Dołączanie do ekosystemu certyfikowanego dla IoT.](https://catalog.azureiotsolutions.com/register)

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Łączenie się z centrum IoT bez sdk

Jeśli nie możesz używać jednego z zestawów SDK urządzeń usługi IoT Hub, możesz połączyć się bezpośrednio z centrum IoT Hub przy użyciu [interfejsów API REST usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/) z dowolnej aplikacji zdolnej do wysyłania i odbierania żądań i odpowiedzi HTTPS.

## <a name="support-and-other-resources"></a>Wsparcie i inne zasoby

Jeśli wystąpią problemy podczas korzystania z zestawów SDK urządzeń IoT platformy Azure, istnieje kilka sposobów, aby uzyskać pomoc techniczną. Możesz wypróbować jeden z następujących kanałów:

**Zgłaszanie błędów** — błędy w skusie SDK urządzenia można zgłaszać na stronie problemów odpowiedniego projektu GitHub. Poprawki szybko docierają z projektu do aktualizacji produktów.

* [Problemy z zestawem SDK usługi Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemy z zestawem SDK usługi Azure IoT Hub (C#)](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemy z zestawem SDK java usługi Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemy z zestawem SDK węzła usługi Azure IoT Hub.js](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemy z zestawem SDK usługi Azure IoT Hub Python](https://github.com/Azure/azure-iot-sdk-python/issues)

**Zespół obsługi klienta firmy Microsoft** — użytkownicy, którzy mają plan pomocy [technicznej,](https://azure.microsoft.com/support/plans/) mogą zaangażować zespół działu obsługi klienta firmy Microsoft, tworząc nowe żądanie pomocy technicznej bezpośrednio z [witryny Azure portal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

**Żądania funkcji** — żądania funkcji Usługi Azure IoT są śledzone za pośrednictwem [strony Głos użytkownika](https://feedback.azure.com/forums/321918-azure-iot)produktu.

## <a name="next-steps"></a>Następne kroki

* [Zestawy SDK urządzeń i usługi](iot-hub-devguide-sdks.md)
* [Wskazówki dotyczące przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
