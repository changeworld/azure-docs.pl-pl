---
title: Koncepcje dotyczące architektury w usłudze Azure IoT Central — energia | Microsoft Docs
description: W tym artykule przedstawiono najważniejsze pojęcia związane z architekturą szablonu aplikacji energetycznej usługi Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8272764e967fccfbfa88d54e688f9d7aaf4e0917
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027683"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central — architektura aplikacji licznika inteligentnego

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten artykuł zawiera omówienie architektury szablonu aplikacji do monitorowania mierników inteligentnych. Na poniższym diagramie przedstawiono powszechnie używaną architekturę dla inteligentnego miernika aplikacji na platformie Azure przy użyciu platformy IoT Central.

[!div class="mx-imgBorder"]
![architektury mierników inteligentnych](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Niniejsza architektura zawiera następujące składniki. Niektóre rozwiązania mogą nie wymagać każdego składnika wymienionego w tym miejscu.

## <a name="smart-meters-and-connectivity"></a>Inteligentne liczniki i łączność 

Inteligentny licznik jest jednym z najważniejszych urządzeń we wszystkich zasobach energii. Rejestruje i przekazuje dane dotyczące zużycia energii do narzędzi do monitorowania i innych przypadków użycia, takich jak rozliczenia i reagowanie na żądania. Na podstawie typu licznika może połączyć się z IoT Central za pośrednictwem bram lub za pośrednictwem innych urządzeń lub systemów pośrednich, takich jak urządzenia brzegowe i systemy końcowe. Kompiluj IoT Central mostek urządzeń, aby połączyć urządzenia, które nie mogą być połączone bezpośrednio. IoT Central mostka urządzenia to rozwiązanie Open Source, w [tym miejscu](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)można znaleźć pełne szczegóły. 


## <a name="iot-central-platform"></a>IoT Central platformę

Azure IoT Central to platforma, która upraszcza tworzenie rozwiązania IoT oraz zmniejsza obciążenie i koszty związane z zarządzaniem IoT, operacjami i programowaniem. Dzięki IoT Central można łatwo łączyć i monitorować zasoby Internet rzeczy (IoT) oraz zarządzać nimi na dużą skalę. Po połączeniu inteligentnych mierników do IoT Central szablon aplikacji będzie używać wbudowanych funkcji, takich jak modele urządzeń, polecenia i pulpity nawigacyjne. Szablon aplikacji korzysta również z IoT Central magazynu dla scenariuszy ścieżki ciepłej, takich jak monitorowanie danych, analiza, reguły i wizualizacja w czasie rzeczywistym. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Opcje rozszerzalności do skompilowania przy użyciu IoT Central
Platforma IoT Central udostępnia dwie opcje rozszerzalności: ciągły eksport danych (CDE) i interfejsy API. Klienci i partnerzy mogą wybrać jedną z tych opcji, aby dostosować swoje rozwiązania do konkretnych potrzeb. Na przykład jeden z naszych partnerów skonfigurował CDE z Azure Data Lake Storage (ADLS). Korzystają one z ADLS do długoterminowego przechowywania danych i innych scenariuszy magazynu zimnej ścieżki, takich jak przetwarzanie wsadowe, Inspekcja i raportowanie. 

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy już znasz architekturę, [Utwórz za darmo aplikację z miernikiem inteligentnym](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Aby dowiedzieć się więcej na temat IoT Central, zobacz [omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)
