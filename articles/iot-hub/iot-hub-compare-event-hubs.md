---
title: Porównanie usługi Azure IoT Hub i Azure Event Hubs | Dokumentacja firmy Microsoft
description: Porównanie usług IoT Hub i Event Hubs Azure wyróżnianie różnice funkcjonalne i przypadków użycia. Porównanie obejmuje obsługiwanych protokołów, monitorowania, zarządzania urządzeniami i przekazywania plików.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60735526"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Łączenie urządzeń IoT na platformie Azure: Usługa IoT Hub i Event Hubs

System Azure oferuje usługi specjalnie opracowanych dla różnego typu łączności i komunikacji w celu łatwiejszego nawiązania połączenia danych do możliwości chmury. Usługa Azure IoT Hub i Azure Event Hubs są usługi w chmurze, które mogą pozyskiwać dużych ilości danych i przetwarzania lub przechowywania tych danych, aby uzyskać wgląd w prowadzoną działalność. Te dwie usługi są podobne, że obsługują one zarówno pozyskiwania danych z małymi opóźnieniami i dużą niezawodność, ale są one przeznaczone do różnych celów. Usługa IoT Hub został opracowany, aby spełnić unikatowe wymagania nawiązywania połączenia z urządzeń IoT w chmurze platformy Azure a usługi Event Hubs została zaprojektowana do przesyłania strumieniowego danych big data. Firma Microsoft zaleca, aby połączyć urządzenia IoT na platformie Azure przy użyciu usługi Azure IoT Hub

Usługa Azure IoT Hub jest przez bramę chmury, łączącej urządzeń IoT w celu zbierania danych i szczegółowe informacje biznesowe i automatyzacji. Ponadto usługa IoT Hub zawiera funkcje, które wzbogacanie relacji między urządzeniami a z systemami zaplecza. Komunikacja dwukierunkowa możliwości oznaczać, że podczas odbierania danych z urządzeń, można także wysyłać polecenia, a zasady z powrotem do urządzenia. Na przykład użyć komunikatów z chmury do urządzeń do aktualizacji właściwości lub wywołują akcje z zakresu zarządzania urządzeniami. Komunikacja z chmury do urządzeń umożliwia także wysyłanie rozwiązań inteligentnych w chmurze na urządzenia brzegowe za pomocą usługi Azure IoT Edge. Unikatowa tożsamość urządzenia poziom udostępniane przez usługi IoT Hub ułatwia lepszego zabezpieczenia swojego rozwiązania IoT przed potencjalnymi atakami. 

[Usługa Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) są dane big data, przesyłania strumieniowego usługi Azure. Jest ona przeznaczona scenariusze o dużej przepływności danych przesyłania strumieniowego której klienci mogą wysyłać miliardów żądań dziennie. Usługa Event Hubs używa partycjonowanego modelu odbiorców do skalowania w poziomie strumienia i jest zintegrowana z danych big data i analizy usług platformy Azure, w tym usługi Databricks, usługi Stream Analytics, Azure Data Lake Store i HDInsight. Za pomocą funkcji, takich jak funkcja przechwytywania usługi Event Hubs i automatyczne rozszerzanie ta usługa jest przeznaczona do obsługi danych big data, aplikacji i rozwiązań. Ponadto usługi IoT Hub używa usługi Event Hubs do jego ścieżki przepływu danych telemetrycznych, dlatego rozwiązania IoT również korzyści płynące z ogromne możliwości usługi Event Hubs.

Aby podsumować, oba rozwiązania są przeznaczone do wprowadzania danych na dużą skalę. Tylko usługi IoT Hub udostępnia zaawansowanych IoT specyficznych funkcji, które są przeznaczone dla Ciebie w celu zmaksymalizowania wartości biznesowej technologii łączenie urządzeń IoT w chmurze platformy Azure.  Jeśli właśnie rozpoczyna swoją podróż IoT, począwszy od usługi IoT Hub w celu obsługi różnych scenariuszy pozyskiwania danych zagwarantuje mieć natychmiastowy dostęp do możliwości IoT w pełni funkcjonalne, gdy Twoje potrzeby biznesowe i techniczne wymagają.

Poniższa tabela zawiera szczegółowe informacje o jak dwie warstwy usługi IoT Hub wypadają w porównaniu do usługi Event Hubs podczas oceniania ich możliwości IoT. Aby uzyskać więcej informacji na temat warstw standardowa i podstawowa usługi IoT Hub, zobacz [jak wybrać właściwą warstwę usługi IoT Hub](iot-hub-scaling.md).

| Możliwości IoT | W warstwie standardowa usługi IoT Hub | Warstwa podstawowa usługi IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Komunikaty z urządzenia do chmury | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] |
| Protokoły: Protokół HTTPS, AMQP, AMQP, przez protokół webSockets | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] |
| Protokoły: MQTT, MQTT przez protokół webSockets | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] |  |
| Tożsamość na urządzenie | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] |  |
| Przekazywanie plików z urządzeń | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] |  |
| Usługa Device Provisioning | ![Sprawdź][checkmark] | ![Sprawdź][checkmark] |  |
| Obsługa komunikatów między chmurą a urządzeniem | ![Sprawdź][checkmark] |  |  |
| Bliźniacza reprezentacja urządzenia i zarządzania urządzeniami | ![Sprawdź][checkmark] |  |  |
| Strumienie urządzenia (wersja zapoznawcza) | ![Sprawdź][checkmark] |  |  |
| IoT Edge | ![Sprawdź][checkmark] |  |  |

Nawet jeśli tylko przypadek użycia jest pozyskiwanie danych urządzenia do chmury, zdecydowanie zaleca się za pomocą usługi IoT Hub, ponieważ umożliwia to usługa, która jest przeznaczona dla łączności między urządzeniami IoT. 

### <a name="next-steps"></a>Kolejne kroki

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
