---
title: Rozwiązania platformy Azure dla Internetu rzeczy (IoT Edge) | Microsoft Docs
description: Przegląd przykładowej architektury rozwiązania IoT i jej relacji z urządzeniami, usługą Azure IoT Hub, zestawami SDK urządzeń Azure IoT, zestawami SDK usługi Azure IoT i innymi usługami Azure.
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: bd59e740803f8f0e6f5f542805d615772efba913
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630344"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Kolejne kroki

Azure IoT Edge to usługa platformy Azure, która umożliwia wykonywanie analiz i przetwarzanie danych na urządzeniach brzegowych. Korzystając z usługi IoT Edge, możesz zwiększać możliwości urządzeń za pomocą kodu umieszczonego w kontenerze obejmującego już używaną logikę pobieraną bezpośrednio z usług platformy Azure lub kodu specyficznego dla rozwiązania. Dzięki niej urządzenia uzyskują następujące możliwości:

* Działanie jako urządzenia bramy (agregowanie i przetwarzanie danych z wielu urządzeń liścia).
* Wykrywanie anomalii i reagowanie na zmiany w środowisku bez potrzeby czekania na instrukcje z chmury.
* Minimalizowanie kosztu przepustowości i magazynu przez wstępne przetwarzanie danych i wysyłanie wyników. 

Usługa IoT Edge obejmuje również interfejs chmurowy, który umożliwia zdalne zarządzanie urządzeniami. Bez konieczności uzyskiwania fizycznego dostępu do urządzeń można wdrażać kod, monitorować stan i aktualizować go. Zdalnie można zarządzać pojedynczymi urządzeniami lub tworzyć wdrożenia, które mają wpływ na duże, zdefiniowane przez Ciebie zestawy urządzeń. Aby uzyskać więcej informacji, zobacz [Understand IoT Edge deployments for single devices or at scale (Informacje o wdrożeniach usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę)][lnk-deployment].

Aby dowiedzieć się więcej o składnikach, które umożliwiają korzystanie z usługi IoT Edge, zobacz [How Azure IoT Edge works (Jak działa usługa Azure IoT Edge)][lnk-overview].

Jeśli pierwszy raz używasz usługi Azure IoT Hub, możesz rozpocząć od artykułu [Omówienie usługi Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
