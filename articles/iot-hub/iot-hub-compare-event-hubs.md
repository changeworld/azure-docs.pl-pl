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
ms.openlocfilehash: b86132b42aef981e6218b27e271e6db645d14071
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Łączenie urządzenia IoT Azure: Centrum IoT i usługi Event Hubs

Platforma Azure udostępnia usługi opracowane specjalnie dla różnych typów połączeń i komunikacji, aby połączyć dane mocy chmury. Zarówno Centrum IoT Azure i usługi Azure Event Hubs są usługi w chmurze, które mogą pozyskiwania dużych ilości danych i przetwarzania lub przechowywania tych danych biznesowych. Te dwie usługi są podobne obydwie obsługuje wprowadzanie danych z małymi opóźnieniami i wysoką niezawodnością, ale są one przeznaczone do różnych celów. Centrum IoT została opracowana specjalnie do unikatowe wymagania dotyczące łączenia z urządzeń IoT, na dużą skalę, do chmury Azure, podczas gdy usługa Event Hubs zostało zaprojektowane na potrzeby danych big data przesyłania strumieniowego. Dlatego firma Microsoft zaleca używanie Centrum IoT Azure z usługą urządzenia IoT Azure

Centrum IoT Azure jest bramy chmury, która łączy z urządzeń IoT do zbierania danych do dysku biznesowych i automatyzacji. Ponadto Centrum IoT zawiera funkcje, które wzbogacić relacji między urządzeniami i systemy zaplecza. Komunikacja dwukierunkowa, który możliwości oznacza, że podczas odbierania danych z urządzeń, możesz również wysłać polecenia i zasady do urządzenia, na przykład do aktualizacji właściwości lub wywołanie akcji związanych z zarządzaniem urządzeniami.  Łączność tej chmury do urządzenia obsługuje również ważne możliwości dostarczania analizy chmury do urządzenia brzegowe Azure IoT krawędzi. Unikatową tożsamość urządzenia poziomie udostępniane przez Centrum IoT pomaga lepszego zabezpieczenia rozwiązania IoT przed potencjalnymi atakami. 

[Usługa Azure Event Hubs] [ Azure Event Hubs] jest danych big data przesyłania strumieniowego usługi Azure. Jest on przeznaczony dla danych scenariusze przesyłania strumieniowego wysokiej przepływności, gdzie klienci mogą wysłać miliardów żądań dziennie. Usługa Event Hubs używa partycjonowanego modelu odbiorców do skalowania w poziomie strumienia i zintegrowane usługi analytics platformy Azure, w tym Databricks, Stream Analytics ADLS i HDInsight i danych big data. Dzięki takim funkcjom jak przechwytywanie centra zdarzeń i zwiększyć Auto ta usługa jest przeznaczona do obsługi danych big data aplikacji i rozwiązań. Ponadto Centrum IoT wykorzystuje centra zdarzeń dla ścieżki przepływu danych telemetrycznych, więc rozwiązania IoT również korzysta z zainstalowanymi możliwości usługi Event hubs.

Krótko mówiąc, gdy oba rozwiązania są przeznaczone dla wprowadzanie danych w bardzo dużej skali, tylko Centrum IoT zapewnia zaawansowanych IoT specyficznych funkcji zaprojektowanych można zmaksymalizować wartość biznesowa łączenie urządzenia IoT w chmurze platformy Azure.  Jeśli właśnie rozpoczyna podróży IoT, począwszy od centrum IoT w celu obsługi różnych scenariuszy wprowadzanie danych zagwarantuje mieć natychmiastowy dostęp do funkcji IoT kompletne, po wymagają potrzeb biznesowych i technicznych.

Poniższa tabela zawiera szczegółowe informacje o dwóch warstw Centrum IoT porównanie do usługi Event Hubs podczas oceniania ich możliwości IoT. Aby uzyskać więcej informacji na temat warstwy standardowa i podstawowa z Centrum IoT, zobacz [Wybieranie prawo warstwy Centrum IoT][lnk-scaling].

| Możliwość IoT | Centrum IoT warstwy standardowa | Centrum IoT warstwa podstawowa | Event Hubs |
| --- | --- | --- | --- |
| Urządzenia do chmury do obsługi komunikatów | ![Zaznacz][1] | ![Zaznacz][1] | ![Zaznacz][1] |
| Protokoły: AMQP HTTPS, protokołu AMQP, przez protokół webSockets | ![Zaznacz][1] | ![Zaznacz][1] | ![Zaznacz][1] |
| Protokoły: MQTT, MQTT przez protokół webSockets | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Tożsamość na urządzenie | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Przekazywanie plików z urządzeń | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Usługa Device Provisioning | ![Zaznacz][1] | ![Zaznacz][1] |  |
| Obsługa komunikatów między chmurą a urządzeniem | ![Zaznacz][1] |  |  |
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
