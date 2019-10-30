---
title: Architektura referencyjna dla rozwiązania połączonego zarządzania odpadami utworzonego przy użyciu usługi Azure IoT Central | Microsoft Docs
description: Zapoznaj się z pojęciami dotyczącymi połączonego rozwiązania do zarządzania odpadami utworzonego przy użyciu usługi Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 806b853b7d3a6291b576c0dde6cdc0b88beec4eb
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027619"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Architektura referencyjna monitorowania połączonych odpadów 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Rozwiązanie połączonego zarządzania odpadami można skompilować przy użyciu **szablonu aplikacji IoT Central platformy Azure** jako startowej aplikacji IoT. Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej wysokiego poziomu dotyczące tworzenia kompleksowych rozwiązań. 

![Architektura zarządzania odpadami podłączonymi](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Pojęcia:

1. Urządzenia i łączność  
1. IoT Central 
2. Rozszerzalność i integracje
3. Aplikacje biznesowe

Przyjrzyjmy się kluczowym składnikom, które zwykle odgrywają część w rozwiązaniu do monitorowania zużycia wody.

## <a name="devices-and-connectivity"></a>Urządzenia i łączność 
Urządzenia używane w otwartych środowiskach, takich jak pojemniki, mogą być zwykle połączone za pośrednictwem sieci rozległej (LPWAN) z niską mocą za pośrednictwem operatora sieci innej firmy. W przypadku tych typów urządzeń można korzystać z [mostka urządzenia usługi azure IoT Central](https://docs.microsoft.com/azure/iot-central/howto-build-iotc-device-bridge) , aby wysyłać dane urządzenia do aplikacji IoT na platformie Azure IoT Central. Alternatywnie mogą istnieć bramy urządzeń z możliwością IP i mogą łączyć się bezpośrednio z IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central to platforma aplikacji IoT, która umożliwia szybkie rozpoczęcie pracy z rozwiązaniem IoT. Możesz Marka, dostosowywać i zintegrować rozwiązanie z usługami innych firm.
Po połączeniu urządzeń z urządzeniami inteligentnymi do IoT Central otrzymujesz polecenie i sterowanie, monitorowanie i alerty, interfejs użytkownika z wbudowaną funkcją RBAC, konfigurowalne pulpity nawigacyjne usługi Insights oraz opcje rozszerzalności. 

## <a name="extensibility-and-integrations"></a>Rozszerzalność i integracje 
Aplikację IoT można zwiększyć w IoT Central i opcjonalnie:
* Przekształcaj i Integruj dane IoT na potrzeby zaawansowanej analizy, na przykład szkolenia modeli uczenia maszynowego za pośrednictwem ciągłego eksportu danych z IoT Central aplikacji. 
* Automatyzowanie przepływów pracy w innych systemach przez wyzwalanie akcji za pośrednictwem Microsoft Flow lub elementów webhook z aplikacji IoT Central
* programowo dostęp do aplikacji IoT w IoT Central za poorednictwem IoT Central interfejsów API.

## <a name="business-applications"></a>Aplikacje biznesowe 
Dane IoT mogą służyć do zarządzania różnymi aplikacjami biznesowymi w ramach narzędzia do strat. Aby dowiedzieć się, jak połączyć IoT Central połączonej aplikacji do zarządzania odpadami z usługami pól, postępuj zgodnie z samouczkiem dotyczącym [integracji z usługami pól usługi Dynamics 365](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [tworzenia połączonej aplikacji IoT Central zarządzania odpadami](./tutorial-connected-waste-management.md)
* Dowiedz się więcej na temat [IoT Central szablonów dla instytucji rządowych](./overview-iot-central-government.md)
* Aby dowiedzieć się więcej na temat IoT Central, zobacz [omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/overview-iot-central)

