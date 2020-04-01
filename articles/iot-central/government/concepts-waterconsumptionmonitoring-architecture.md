---
title: Architektura referencyjna dla rozwiązania do monitorowania zużycia wody zbudowanego za pomocą usługi Azure IoT Central| Dokumenty firmy Microsoft
description: Poznaj pojęcia dotyczące rozwiązania do monitorowania zużycia wody utworzonego w usłudze Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3a64ca5b0c2a092f895873e097ea6beb9a235a37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017704"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Architektura referencyjna monitorowania zużycia wody 



Rozwiązania do monitorowania zużycia wody można sbudować za pomocą **szablonu aplikacji Azure IoT Central** jako aplikacji IoT startowego. Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej wysokiego poziomu na temat tworzenia rozwiązania end-to-end. 

![Architektura monitorowania zużycia wody](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Pojęcia:

1. Urządzenia i łączność  
1. IoT Central 
2. Rozszerzalność i integracja
3. Aplikacje biznesowe

Przyjrzyjmy się kluczowym komponentom, które zazwyczaj odgrywają rolę w rozwiązaniu do monitorowania zużycia wody.

## <a name="devices-and-connectivity"></a>Urządzenia i łączność 
Ta sekcja odnosi się do urządzeń wykorzystywanych do inteligentnych rozwiązań wodnych, takich jak monitorowanie jakości wody lub monitorowanie zużycia wody, zazwyczaj jako inteligentne urządzenia wodne. Inteligentne urządzenia wodne mogą być przepływomierzami, monitorami jakości wody, inteligentnymi zaworami, detektorami wycieków i tak dalej.

Urządzenia stosowane w inteligentnych rozwiązaniach wodnych będą zazwyczaj podłączane za pośrednictwem sieci o dużym poczcie (LPWAN) za pośrednictwem zewnętrznego operatora sieci. W przypadku urządzeń tego typu można użyć [mostka urządzeń centrali Usługi Azure IoT,](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) aby wysłać dane urządzenia do aplikacji IoT w usłudze Azure IoT Central. Alternatywnie może być bram urządzeń, które są zdolne do adresów IP i można połączyć się bezpośrednio z IoT Central.

## <a name="iot-central"></a>IoT Central 
Usługa Azure IoT Central to platforma aplikacji IoT, która umożliwia szybkie uruchomienie rozwiązania IoT. Możesz oznaczyć, dostosować i zintegrować swoje rozwiązanie z usługami innych firm.
Po podłączeniu inteligentnych urządzeń wodnych do usługi IoT Central otrzymujesz polecenia i sterowanie urządzeniem, monitorowanie i ostrzeganie, interfejs użytkownika z wbudowanym funkcją RBAC, konfigurowalne pulpity nawigacyjne i opcje rozszerzalności. 


## <a name="extensibility-and-integrations"></a>Rozszerzalność i integracja 
Aplikację IoT można rozszerzyć w UIŚ Central i opcjonalnie:
* przekształcanie i integrowanie danych IoT w celu zaawansowanej analizy, na przykład szkolenia modeli uczenia maszynowego, poprzez ciągły eksport danych z aplikacji IoT Central
* automatyzowanie przepływów pracy w innych systemach, wyzwalając akcje przy użyciu usługi Microsoft Flow lub elementów webhook z aplikacji IoT Central
* programowo uzyskać dostęp do aplikacji IoT w UIOT Central za pośrednictwem interfejsów API IoT Central

## <a name="business-applications"></a>Aplikacje biznesowe 
Dane IoT mogą być używane do zasilania różnego rodzaju aplikacji biznesowych w przedsiębiorstwie wodociągowym. Aby dowiedzieć się, jak połączyć aplikację do monitorowania zużycia wody IoT z usługami terenowymi, wykonaj samouczek dotyczący [integracji z usługami terenowymi Dynamics 365](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć aplikację IoT Central [zużycia wody](./tutorial-water-consumption-monitoring.md)
* Dowiedz się więcej o [szablonach instytucji rządowych IoT](./overview-iot-central-government.md)
* Aby dowiedzieć się więcej o UIOT Central, zobacz [Omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
