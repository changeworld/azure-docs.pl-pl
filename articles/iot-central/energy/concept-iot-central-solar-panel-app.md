---
title: Koncepcje architektoniczne w usłudze Azure IoT Central — energia | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono kluczowe pojęcia dotyczące architektury usługi Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77018010"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central — architektura aplikacji paneli słonecznych




Ten artykuł zawiera omówienie architektury szablonu aplikacji monitorowania panelu słonecznego. Poniższy diagram przedstawia powszechnie używaną architekturę aplikacji panelu słonecznego na platformie Azure przy użyciu platformy IoT Central.

> [!div class="mx-imgBorder"]
> ![architektura inteligentnych liczników](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Niniejsza architektura zawiera następujące składniki. Niektóre aplikacje mogą nie wymagać każdego wymienionego składnika.

## <a name="solar-panels-and-connectivity"></a>Panele słoneczne i łączność 

Panele słoneczne są jednym ze znaczących źródeł energii odnawialnej. W zależności od typu i konfiguracji panelu słonecznego można go podłączyć za pomocą bram lub innych urządzeń pośrednich i systemów zastrzeżonych. Może być konieczne zbudowanie mostka urządzeń IoT Central w celu połączenia urządzeń, których nie można podłączyć bezpośrednio. Mostek na urządzenia IoT Central jest rozwiązaniem typu open source, a szczegółowe informacje można znaleźć [tutaj.](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) 



## <a name="iot-central-platform"></a>Platforma IoT Central
Usługa Azure IoT Central to platforma, która upraszcza tworzenie rozwiązania IoT i pomaga zmniejszyć obciążenie i koszty zarządzania IoT, operacji i rozwoju. Dzięki IoT Central można łatwo łączyć, monitorować i zarządzać zasobami Internetu rzeczy (IoT) na dużą skalę. Po podłączeniu paneli słonecznych do usługi IoT Central szablon aplikacji korzysta z wbudowanych funkcji, takich jak modele urządzeń, polecenia i pulpity nawigacyjne. Szablon aplikacji używa również magazynu IoT Central do scenariuszy ścieżek o ciepłej, takich jak monitorowanie danych licznika w czasie rzeczywistym, analiza, reguły i wizualizacja.


## <a name="extensibility-options-to-build-with-iot-central"></a>Opcje rozszerzalności do tworzenia za pomocą IoT Central
Platforma IoT Central udostępnia dwie opcje rozszerzalności: ciągły eksport danych (CDE) i interfejsy API. Klienci i partnerzy mogą wybierać między tymi opcjami w celu dostosowania swoich rozwiązań do konkretnych potrzeb. Na przykład jeden z naszych partnerów skonfigurowany CDE z usługi Azure Data Lake Storage (ADLS). Używają usługi ADLS do długoterminowego przechowywania danych i innych scenariuszy przechowywania ścieżek chłodniczych, takich jak przetwarzanie wsadowe, inspekcja i raportowanie. 

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy dowiedziałeś się o architekturze, [utwórz aplikację panelu słonecznego za darmo](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Aby dowiedzieć się więcej o UIOT Central, zobacz [Omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)