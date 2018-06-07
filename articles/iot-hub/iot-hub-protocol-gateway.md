---
title: Brama protokołu IoT Azure | Dokumentacja firmy Microsoft
description: Jak używać brama protokołu Azure IoT rozszerzenie Centrum IoT, możliwości i obsługa protokołu, aby umożliwić urządzeniom do nawiązania połączenia z koncentratorem przy użyciu protokołów nie natywnie obsługiwane przez Centrum IoT.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 2c90ee899d0002d41ca21ed4a4927470ee53b2e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635308"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Obsługa protokołów dodatkowych Centrum IoT
Centrum IoT Azure natywnie obsługuje komunikację za pośrednictwem protokołów MQTT, AMQP i HTTPS. W niektórych przypadkach urządzenia lub bramy pola nie może być może użyć jednej z tych standardowych protokołów i wymagają protokołu dostosowania. W takich przypadkach można użyć niestandardowych bramy. Niestandardowe bramy umożliwia dostosowanie protokołu punkty końcowe Centrum IoT przez mostkowanie ruchu do i z Centrum IoT. Można użyć [brama protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) jako brama niestandardowych umożliwiające dostosowanie protokołu IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brama protokołu IoT Azure
Brama protokołu Azure IoT to platforma dostosowania protokołu, który jest przeznaczony dla wysokiej skali, komunikację dwukierunkową urządzenie z Centrum IoT. Brama protokołu jest przekazujące składnik, który akceptuje połączenia urządzenia za pośrednictwem określonego protokołu. Tworzy ono ruch do Centrum IoT za pośrednictwem protokołu AMQP 1.0. 

Brama protokołu na platformie Azure w taki sposób, skalowalnej można wdrożyć za pomocą usługi Azure Service Fabric, roli proces roboczy usług Azure Cloud Services lub maszyn wirtualnych systemu Windows. Ponadto bramy protokołu można wdrożyć w środowiskach lokalnych, takich jak pole bram.

Brama protokołu Azure IoT zawiera karty protokołu MQTT, która umożliwia dostosowanie zachowanie protokołu MQTT, jeśli to konieczne. Ponieważ Centrum IoT udostępnia wbudowaną obsługę protokołu v3.1.1 MQTT, tylko warto za pomocą karty protokołu MQTT, jeśli protokół dostosowań lub określonych wymagań dotyczących dodatkowe funkcje są wymagane.

Karta MQTT przedstawiono również model programowania do tworzenia protokołu kart dla innych protokołów. Ponadto model programowania brama protokołu Azure IoT umożliwia podłączenie niestandardowych składników przetwarzania specjalnych, takich jak niestandardowe uwierzytelnianie, przekształcenia wiadomości, kompresji i dekompresji lub szyfrowania i odszyfrowywania ruchu między urządzenia i Centrum IoT.

Elastyczność brama protokołu Azure IoT i implementacji MQTT są zawarte w projekcie oprogramowania typu open source. Użyj projekt open source, aby dodać obsługę różnych protokołów i protokołów lub dostosowania wdrożenia dla danego scenariusza. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat brama protokołu Azure IoT i sposobu użycia, a następnie wdrożyć go jako część rozwiązania IoT, zobacz:

* [Repozytorium brama protokołu IoT Azure w serwisie GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Przewodnik dewelopera bramy protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Aby dowiedzieć się więcej na temat planowania wdrożenia Centrum IoT, zobacz:

* [Porównaj z usługi Event Hubs][lnk-compare]
* [Skalowanie, wysokiej dostępności i odzyskiwania po awarii][lnk-scaling]
* [Przewodnik dewelopera Centrum IoT][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
