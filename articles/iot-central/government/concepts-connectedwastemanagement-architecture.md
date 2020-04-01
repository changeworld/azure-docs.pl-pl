---
title: Architektura referencyjna dla rozwiązania do gospodarowania odpadami połączonymi z usługą Azure IoT Central| Dokumenty firmy Microsoft
description: Poznaj pojęcia dotyczące rozwiązania do gospodarowania odpadami połączonego utworzonego za pomocą usługi Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016089"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Architektura referencyjna połączonego zarządzania odpadami 



Połączone rozwiązanie do zarządzania odpadami można skompilować przy użyciu **szablonu aplikacji Azure IoT Central** jako aplikacji IoT startowego. Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej wysokiego poziomu na temat tworzenia rozwiązania end-to-end. 

![Architektura gospodarowania odpadami połączonym](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Pojęcia:

1. Urządzenia i łączność  
1. IoT Central 
2. Rozszerzalność i integracja
3. Aplikacje biznesowe

Przyjrzyjmy się kluczowym komponentom, które zazwyczaj odgrywają rolę w rozwiązaniu do monitorowania zużycia wody.

## <a name="devices-and-connectivity"></a>Urządzenia i łączność 
Urządzenia używane w środowiskach otwartych, takich jak pojemniki na odpady, mogą być podłączone za pośrednictwem sieci o dużym posie: LPWAN za pośrednictwem operatora sieci innej firmy. W przypadku urządzeń tego typu można użyć [mostka urządzeń centrali Usługi Azure IoT,](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) aby wysłać dane urządzenia do aplikacji IoT w usłudze Azure IoT Central. Alternatywnie może być bram urządzeń, które są zdolne do adresów IP i można połączyć się bezpośrednio z IoT Central.

## <a name="iot-central"></a>IoT Central 
Usługa Azure IoT Central to platforma aplikacji IoT, która umożliwia szybkie uruchomienie rozwiązania IoT. Możesz oznaczyć, dostosować i zintegrować swoje rozwiązanie z usługami innych firm.
Po podłączeniu inteligentnych urządzeń wodnych do usługi IoT Central otrzymujesz polecenia i sterowanie urządzeniem, monitorowanie i ostrzeganie, interfejs użytkownika z wbudowanym funkcją RBAC, konfigurowalne pulpity nawigacyjne i opcje rozszerzalności. 

## <a name="extensibility-and-integrations"></a>Rozszerzalność i integracja 
Aplikację IoT można rozszerzyć w UIŚ Central i opcjonalnie:
* przekształcaj i integruj dane IoT w celu zaawansowanej analizy, na przykład szkoleniowych modeli uczenia maszynowego, poprzez ciągły eksport danych z aplikacji IoT Central. 
* automatyzowanie przepływów pracy w innych systemach, wyzwalając akcje przy użyciu usługi Microsoft Flow lub elementów webhook z aplikacji IoT Central
* programowo uzyskać dostęp do aplikacji IoT w UIOT Central za pośrednictwem interfejsów API IoT Central.

## <a name="business-applications"></a>Aplikacje biznesowe 
Dane IoT mogą być używane do zasilania różnych aplikacji biznesowych w zamówiach odpadów. Aby dowiedzieć się, jak połączyć aplikację do zarządzania odpadami połączonymi z IoT Central z usługami terenowymi, wykonaj samouczek dotyczący [integracji z usługami terenowymi Dynamics 365](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć aplikację IoT Central do [zarządzania odpadami](./tutorial-connected-waste-management.md)
* Dowiedz się więcej o [szablonach instytucji rządowych IoT](./overview-iot-central-government.md)
* Aby dowiedzieć się więcej o UIOT Central, zobacz [Omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

