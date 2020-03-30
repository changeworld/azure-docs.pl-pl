---
title: Omówienie przemysłowego IoT platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie przemysłowego IoT. Wyjaśnia podłączoną fabrykę, łączność fabryczną i komponenty bezpieczeństwa w IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73828129"
---
# <a name="what-is-industrial-iot-iiot"></a>Co to jest przemysłowy IoT (IIoT)

IIoT to przemysłowy internet rzeczy. IIoT zwiększa wydajność przemysłową dzięki zastosowaniu Internetu ioT w przemyśle wytwórczym. 

## <a name="improve-industrial-efficiencies"></a>Poprawa wydajności przemysłowej

Zwiększ wydajność operacyjną i rentowność dzięki podłączonemu akceleratorowi rozwiązań fabrycznych. Podłączaj i monitoruj swoje urządzenia przemysłowe i urządzenia w chmurze — w tym maszyny już działające w fabryce. Analizuj dane IoT, aby uzyskać szczegółowe informacje, które pomogą Ci zwiększyć wydajność całej hali produkcyjnej.

Zmniejsz czasochłonny proces uzyskiwania dostępu do fabrycznych maszyn podłogowych dzięki OPC Twin i skup swój czas na tworzeniu rozwiązań IIoT. Usprawnij zarządzanie certyfikatami i integrację zasobów przemysłowych z OPC Vault i poczuj się pewnie, że łączność z zasobami jest zabezpieczona. Te mikrousługi zapewniają interfejs API podobny do restu na podstawie [składników IoT przemysłowych platformy Azure.](https://github.com/Azure/azure-iiot-opc-ua) Interfejs API usługi zapewnia kontrolę nad funkcją modułu brzegowego. 

![Omówienie przemysłowego Internetu rzeczy](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Aby uzyskać więcej informacji na temat usług Azure Industrial IoT, zobacz [repozytorium](https://github.com/Azure/azure-iiot-services)GitHub .
Jeśli nie znasz sposobu działania modułów usługi Azure IoT Edge, zacznij od następujących artykułów:
- [Azure IoT Edge — informacje](../iot-edge/about-iot-edge.md)
- [Moduły usługi Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Połączona fabryka

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) to implementacja architektury referencyjnej Azure Industrial IoT firmy Microsoft, którą można dostosować do określonych wymagań biznesowych. Pełny kod rozwiązania jest open-source i jest dostępny w repozytorium GitHub akceleratora rozwiązań Connected Factory. Można go używać jako punktu wyjścia dla produktu komercyjnego i wdrożyć wstępnie wbudowane rozwiązanie w subskrypcji platformy Azure w ciągu kilku minut. 

## <a name="factory-floor-connectivity"></a>Łączność z podłogą fabryczną

OPC Twin to komponent IIoT, który automatyzuje wykrywanie i rejestrację urządzeń oraz oferuje zdalne sterowanie urządzeniami przemysłowymi za pośrednictwem interfejsów API REST. OPC Twin, używa usługi Azure IoT Edge i Usługi IoT Hub do łączenia chmury i sieci fabrycznej. OPC Twin umożliwia deweloperom IIoT skupienie się na tworzeniu aplikacji IIoT bez obawy, jak bezpiecznie uzyskać dostęp do maszyn lokalnych.

## <a name="security"></a>Zabezpieczenia

OPC Vault to implementacja programu OPC UA Global Discovery Server (GDS), która umożliwia konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatów dla serwerów OPC UA i aplikacji klienckich w chmurze. OPC Vault upraszcza wdrażanie i utrzymywanie bezpiecznej łączności zasobów w przestrzeni przemysłowej. Automatyzując zarządzanie certyfikatami, OPC Vault zwalnia operatorów fabrycznych z ręcznych i złożonych procesów związanych z łącznością i zarządzaniem certyfikatami.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy miałeś wprowadzenie do przemysłowego IoT i jego komponentów, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Co to jest bliźniacza reprezentacja OPC](overview-opc-twin.md)
