---
title: Porównanie Centrum IoT Azure do usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Porównanie usług Centrum IoT i Azure centra zdarzeń wyróżnianie różnice funkcjonalne i przypadki użycia. Porównanie zawiera obsługiwanych protokołów, zarządzanie urządzeniami, monitorowania i przekazywania plików.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Porównanie Centrum IoT Azure i usługi Azure Event Hubs

Zarówno Centrum IoT Azure i usługi Azure Event Hubs są usługi w chmurze, które mogą pozyskiwania dużych ilości danych i przetwarzania lub przechowywania tych danych biznesowych. Te dwie usługi są podobne, że obsługuje obie przetwarzania danych zdarzenia i dane telemetryczne z małymi opóźnieniami i wysoką niezawodnością. Jednak tylko Centrum IoT został opracowany z określonymi możliwościami potrzebnych do obsługi na dużą skalę internet rzeczy scenariuszy. 

Centrum IoT Azure jest brama chmury, który jest podłączany urządzeń i zbiera dane dotyczące biznesowych i automatyzacji. Go łatwo strumienia danych w chmurze i Zarządzaj swoimi urządzeniami na dużą skalę. Ważne różnicą między centrum IoT i innych usług wprowadzanie danych jest, że Centrum IoT zawiera funkcje, które wzbogacić relacji między urządzeniami i systemy zaplecza. Komunikacja dwukierunkowa możliwości oznacza podczas odbierania danych z urządzeń, można również wysyłane wiadomości z powrotem do urządzenia, aby zaktualizować właściwości lub wywoływania akcji. Tożsamości na poziomie urządzeń pomaga w zabezpieczeniu systemu. Rozproszonego przetwarzania logiki usługi chmury przenosi na krawędzi urządzenia.

[Usługa Azure Event Hubs] [ Azure Event Hubs] jest usługą wprowadzanie zdarzeń, która może przetwarzania i przechowywania dużych ilości danych telemetrycznych. Centra zdarzeń zaprojektowano pod kątem wprowadzanie zdarzeń w bardzo dużej skali, zarówno w kontekście między centrum danych i centrum danych wewnątrz scenariuszy, ale nie zapewnia rozbudowane możliwości specyficznych dla IoT, które są dostępne z Centrum IoT. Z tego względu nie zaleca się centra zdarzeń z rozwiązania IoT. 

Poniższa tabela zawiera szczegółowe informacje o dwóch warstw Centrum IoT porównanie do usługi Event Hubs podczas oceniania ich możliwości IoT. Aby uzyskać więcej informacji na temat warstwy standardowa i podstawowa z Centrum IoT, zobacz [Wybieranie prawo warstwy Centrum IoT][lnk-scaling].

| Możliwość IoT | Centrum IoT warstwy standardowa | Centrum IoT warstwa podstawowa | Event Hubs |
| --- | --- | --- | --- |
| Urządzenia do chmury do obsługi komunikatów | ![Zaznacz][1] | ![Zaznacz][1] | ![Zaznacz][1] |
| Protokoły: AMQP HTTPS, protokołu AMQP, przez protokół websockets | ![Zaznacz][1] | ![Zaznacz][1] | ![Zaznacz][1] |
| Protokoły: MQTT, MQTT przez protokół websockets | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Tożsamość na urządzenie | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Przekazywanie plików z urządzeń | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Usługa Device Provisioning | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Obsługa wiadomości chmury do urządzenia | ![Zaznacz][1] |  |  |
| Dwie urządzeń i zarządzanie urządzeniami | ![Zaznacz][1] |  |  |
| IoT Edge | ![Zaznacz][1] |  |  |

Nawet jeśli tylko przypadek użycia jest wprowadzanie danych urządzenia do chmury, zdecydowanie zaleca się przy użyciu Centrum IoT, ponieważ zapewnia to usługa, która jest przeznaczona dla łączności urządzenia IoT. 

### <a name="next-steps"></a>Kolejne kroki

Aby dokładniej analizować możliwości Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png