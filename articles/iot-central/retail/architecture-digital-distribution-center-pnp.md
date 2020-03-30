---
title: Architektura Centrum Dystrybucji Cyfrowej IoT | Dokumenty firmy Microsoft
description: Architektura szablonu aplikacji Digital Distribution Center dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 359aaf7bbde5501716f3d8c4229b2b90918fd5de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020985"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architektura szablonu aplikacji centrum dystrybucji cyfrowej IoT Central



Partnerzy i klienci mogą korzystać z szablonu aplikacji & następujących wskazówek, aby opracować kompleksowe rozwiązania **cyfrowego centrum dystrybucji.**

> [!div class="mx-imgBorder"]
> ![centrum dystrybucji cyfrowej](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Zestaw czujników IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i zagregowane szczegółowe informacje do usługi IoT Central
3. Dane są kierowane do żądanej usługi platformy Azure w celu manipulacji
4. Usługi platformy Azure, takie jak ASA lub Usługi Azure Functions, mogą służyć do formatowania strumieni danych i wysyłania ich do żądanych kont magazynu 
5. Przetwarzane dane są przechowywane w magazynie na gorąco dla akcji w czasie zbliżonym do rzeczywistego lub chłodni, aby uzyskać dodatkowe ulepszenia wglądu, które są oparte na analizie uczenia maszynowego lub wsadowej. 
6. Aplikacje logiki mogą być używane do zasilania różnych przepływów pracy biznesowych w aplikacjach biznesowych użytkowników końcowych

## <a name="details"></a>Szczegóły
W poniższej sekcji przedstawiono każdą część architektury koncepcyjnej

## <a name="video-cameras"></a>Kamery wideo 
Kamery wideo są podstawowymi czujnikami w tym połączonym cyfrowo ekosystemie korporacyjnym. Postępy w uczeniu maszynowym i sztucznej inteligencji, które umożliwiają przekształcanie wideo w uporządkowane dane i przetwarzanie ich na urządzeniach brzegowych przed wysłaniem do chmury. Możemy użyć kamer IP do przechwytywania obrazów, kompresji ich w aparacie, a następnie wysyłania skompresowanych danych na podstawie danych obliczeniowych do analizy wideo w potoku lub używać kamer wizyjnych GigE do przechwytywania obrazów na czujniku, a następnie wysyłania tych obrazów bezpośrednio do usługi Azure IoT Edge , który następnie kompresuje przed przetworzeniem w potoku analizy wideo. 

## <a name="azure-iot-edge-gateway"></a>Brama usługi Azure IoT Edge
"Kamery jako czujniki" i obciążenia brzegowe są zarządzane lokalnie przez usługę Azure IoT Edge, a strumień kamery jest przetwarzany przez potok analizy. Potok przetwarzania analizy wideo w usłudze Azure IoT Edge przynosi wiele korzyści, w tym skrócony czas reakcji, zużycie niskiej przepustowości, co powoduje małe opóźnienia w szybkim przetwarzaniu danych. Tylko najważniejsze metadane, szczegółowe informacje lub akcje są wysyłane do chmury w celu podjęcia dalszych działań lub badań. 

## <a name="device-management-with-iot-central"></a>Zarządzanie urządzeniami za pomocą usługi IoT Central 
Usługa Azure IoT Central to platforma programowa, która upraszcza urządzenia IoT & łączność, konfigurację i zarządzanie bramą usługi Azure IoT Edge. Platforma znacznie zmniejsza obciążenie i koszty zarządzania urządzeniami IoT, operacji i związanych z nimi zmian. Klienci & partnerzy mogą tworzyć kompleksowe rozwiązania dla przedsiębiorstw, aby uzyskać cyfrową pętlę sprzężenia zwrotnego w centrach dystrybucyjnych.

## <a name="business-insights-and-actions-using-data-egress"></a>Usługa Business Insights i akcje wykorzystujące wyjście danych 
Platforma IoT Central zapewnia zaawansowane opcje rozszerzalności za pośrednictwem ciągłego eksportowania danych (CDE) i interfejsów API. Szczegółowe informacje biznesowe oparte na przetwarzaniu danych telemetrycznych lub nieprzetworzonych danych telemetrycznych są zazwyczaj eksportowane do preferowanej aplikacji biznesowej. Można to osiągnąć za pomocą elementu webhook, usługi Service Bus, centrum zdarzeń lub magazynu obiektów blob w celu tworzenia, uczenia i wdrażania modeli uczenia maszynowego i dalszego wzbogacania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak wdrożyć [szablon centrum dystrybucji cyfrowej](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Dowiedz się więcej o [szablonach detalicznych IoT Central](./overview-iot-central-retail-pnp.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [przeglądem IoT Central](../core/overview-iot-central.md)
