---
title: Centrum IoT Azure — wprowadzenie połączenie urządzenia IoT z chmurą | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i Podłącz urządzenia IoT i starter Kit do Centrum IoT Azure. Urządzenia można wysyłać dane telemetryczne Centrum IoT i Centrum IoT można monitorować i zarządzania urządzeniami.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
keywords: Samouczek Centrum Azure iot
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dafb8aca34a5a41f45f76d526aa3b8f3b1b792c4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Centrum IoT Azure Rozpoczynanie pracy z rzeczywistych urządzeń

Azure IoT Hub i zestawy SDK urządzenia Azure IoT służy do tworzenia rozwiązań Internetu rzeczy (IoT):

* Centrum IoT Azure jest w pełni zarządzana usługa w chmurze, która bezpieczny sposób łączy, monitoruje i zarządza urządzeniami IoT. Zestawy SDK urządzenia IoT Azure umożliwia wdrożenie urządzenia IoT.
* Użyj bramy IoT w bardziej złożonych scenariuszach IoT. Na przykład, gdzie należy wziąć pod uwagę czynników, takich jak starszych urządzeń, kosztów przepustowości, zasady zabezpieczeń i prywatności lub przetwarzania danych krawędzi. W tych scenariuszach użyj [Azure IoT krawędzi](https://docs.microsoft.com/azure/iot-edge/) do zaimplementowania bramy, która łączy się z Centrum IoT z urządzeń.

## <a name="what-the-how-to-articles-cover"></a>Obejmuje artykuły

Te artykuły przedstawiono Azure IoT Hub i zestawy SDK urządzenia. Artykuły opisano typowe scenariusze IoT, aby zademonstrować możliwości Centrum IoT. Artykułów przedstawiono również sposób łączenia Centrum IoT z innymi usługami platformy Azure i narzędzia umożliwiające tworzenie bardziej zaawansowanych rozwiązania IoT. W tych artykułów należy użyć rzeczywistego urządzenia IoT.

## <a name="set-up-your-device"></a>Konfigurowanie urządzenia

Podłącz urządzenia IoT lub bramy z Centrum IoT Azure:

| Urządzenia IoT                       | Język programowania |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Zestaw deweloperski IoT                       | [Arduino w VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| ESP8266 HUZZAH Adafruit piór  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 operacją deweloperów       | [Arduino][Th_Ard]              |
| M0 Adafruit piór              | [Arduino][M0_Ard]              |
| Symulator urządzeń online         | [Pi malinowe (Node.js)][Ol_Sim] |

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
