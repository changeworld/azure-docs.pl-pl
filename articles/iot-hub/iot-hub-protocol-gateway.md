---
title: Brama protokołu IoT platformy Azure | Dokumentacja firmy Microsoft
description: Jak rozszerzyć usługę IoT Hub, możliwości i obsługę protokołu, aby umożliwić urządzeniom połączyć się z Twojego Centrum przy użyciu protokołów nie obsługiwane natywnie przez usługę IoT Hub za pomocą bramy protokołu Azure IoT.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.openlocfilehash: 9dbb7905c2a0fed65ede610577e0fa11a1deef92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345400"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Obsługa dodatkowych protokołów IoT Hub

Usługa Azure IoT Hub natywnie obsługuje komunikację za pośrednictwem protokołów MQTT, AMQP i HTTPS. W niektórych przypadkach urządzenia lub bram działających w terenie może nie można użyć jednej z tych standardowych protokołów i wymagać dostosowania protokołu. W takich przypadkach można użyć niestandardowych bramy. Niestandardowe bramy umożliwia dostosowanie protokołu dla punktów końcowych usługi IoT Hub mostkowanie ruch do i z usługi IoT Hub. Możesz użyć [brama protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) jako brama niestandardowe umożliwiające dostosowanie protokołu dla Centrum IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brama protokołu IoT platformy Azure

Brama protokołu Azure IoT to architektura służąca do dostosowania protokołu, który zaprojektowano z myślą o dużej skali, dwukierunkowej komunikacji urządzenia z usługą IoT Hub. Brama protokołu jest przekazywane składnik, który akceptuje połączenia urządzenia za pośrednictwem określonego protokołu. Tworzy ono ruchu do usługi IoT Hub za pośrednictwem protokołu AMQP 1.0.

Brama protokołu w systemie Azure można wdrożyć w sposób o wysokim stopniu skalowalności za pomocą usługi Azure Service Fabric, role procesów roboczych usług Azure Cloud Services lub Windows Virtual Machines. Ponadto można wdrożyć w środowiskach lokalnych, takich jak bram działających w terenie bramy protokołu.

Brama protokołu Azure IoT obejmuje karty protokołu MQTT, które umożliwia dostosowywanie zachowania protokołu MQTT, jeśli to konieczne. Ponieważ usługa IoT Hub udostępnia wbudowaną obsługę protokołu MQTT 3.1.1, powinny być udostępniane wyłącznie za pomocą karty protokołu MQTT, jeśli wymagane są dostosowania protokołu lub określone wymagania dotyczące dodatkowych funkcji.

Karta MQTT przedstawia również model programowania do tworzenia protokołu adaptery dla innych protokołów. Ponadto model programowania bramy protokołu Azure IoT umożliwia podłączenie w składnikach niestandardowych dla przetwarzania specjalnych, takich jak uwierzytelnianie niestandardowe, przekształcenia wiadomości, kompresji i dekompresji lub operacji szyfrowania i odszyfrowywania ruchu między urządzenia i usługi IoT Hub.

Aby zapewnić elastyczność bramy protokołu Azure IoT i implementacji protokołu MQTT są dostarczane w projekt oprogramowania typu open source. Umożliwia obsługę różnych protokołów i protokołów projekt open source lub dostosowywanie wdrożenia dla danego scenariusza. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat bramy protokołu Azure IoT i sposobie używania i wdrożyć go jako część swojego rozwiązania IoT, zobacz:

* [Repozytorium brama protokołu IoT Azure w witrynie GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Przewodnik dla deweloperów bramy protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Aby dowiedzieć się więcej na temat planowania wdrożenia usługi IoT Hub, zobacz:

* [Porównanie z usługą Event Hubs](iot-hub-compare-event-hubs.md)

* [Skalowanie, wysoka dostępność i odzyskiwanie po awarii](iot-hub-scaling.md)

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)