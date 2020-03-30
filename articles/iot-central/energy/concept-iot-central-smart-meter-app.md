---
title: Koncepcje architektoniczne w usłudze Azure IoT Central — energia | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono kluczowe pojęcia dotyczące architektury szablonu aplikacji energetycznej Usługi Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024317"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central — architektura aplikacji inteligentnych liczników



Ten artykuł zawiera omówienie architektury szablonu aplikacji do monitorowania inteligentnych liczników. Poniższy diagram przedstawia powszechnie używaną architekturę aplikacji inteligentnych liczników na platformie Azure przy użyciu platformy IoT Central.

> [!div class="mx-imgBorder"]
> ![architektura inteligentnych liczników](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Niniejsza architektura zawiera następujące składniki. Niektóre rozwiązania mogą nie wymagać każdego składnika wymienionego tutaj.

## <a name="smart-meters-and-connectivity"></a>Inteligentne liczniki i łączność 

Inteligentny licznik jest jednym z najważniejszych urządzeń spośród wszystkich zasobów energetycznych. Rejestruje i przekazuje dane dotyczące zużycia energii do narzędzi do monitorowania i innych przypadków użycia, takich jak rozliczenia i odpowiedzi na żądanie. W zależności od typu miernika może łączyć się z IoT Central za pomocą bram lub innych urządzeń pośrednich lub systemów, takich urządzeń brzegowych i systemów head-end. Tworzenie mostka urządzenia IoT Central w celu łączenia urządzeń, których nie można podłączyć bezpośrednio. Mostek na urządzenia IoT Central jest rozwiązaniem typu open source, a szczegółowe informacje można znaleźć [tutaj.](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) 


## <a name="iot-central-platform"></a>Platforma IoT Central

Usługa Azure IoT Central to platforma, która upraszcza tworzenie rozwiązania IoT i pomaga zmniejszyć obciążenie i koszty zarządzania IoT, operacji i rozwoju. Dzięki IoT Central można łatwo łączyć, monitorować i zarządzać zasobami Internetu rzeczy (IoT) na dużą skalę. Po podłączeniu inteligentnych liczników do usługi IoT Central szablon aplikacji korzysta z wbudowanych funkcji, takich jak modele urządzeń, polecenia i pulpity nawigacyjne. Szablon aplikacji używa również magazynu IoT Central do scenariuszy ścieżek o ciepłej, takich jak monitorowanie danych licznika w czasie rzeczywistym, analiza, reguły i wizualizacja. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Opcje rozszerzalności do tworzenia za pomocą IoT Central
Platforma IoT Central udostępnia dwie opcje rozszerzalności: ciągły eksport danych (CDE) i interfejsy API. Klienci i partnerzy mogą wybierać między tymi opcjami w celu dostosowania swoich rozwiązań do konkretnych potrzeb. Na przykład jeden z naszych partnerów skonfigurowany CDE z usługi Azure Data Lake Storage (ADLS). Używają usługi ADLS do długoterminowego przechowywania danych i innych scenariuszy przechowywania ścieżek chłodniczych, takich jak przetwarzanie wsadowe, inspekcja i raportowanie. 

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy dowiedziałeś się o architekturze, [utwórz aplikację inteligentnych liczników za darmo](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Aby dowiedzieć się więcej o UIOT Central, zobacz [Omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)
