---
title: Porównanie usługi Azure IoT Hub i Azure Event Hubs | Dokumentacja firmy Microsoft
description: Porównanie usług IoT Hub i Event Hubs Azure wyróżnianie różnice funkcjonalne i przypadków użycia. Porównanie obejmuje obsługiwanych protokołów, monitorowania, zarządzania urządzeniami i przekazywania plików.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 20bb0cb6982bcbea6b18989099322cfd3389b0b0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819649"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Łączenie urządzeń IoT na platformie Azure: Usługa IoT Hub i Event Hubs

System Azure oferuje usługi specjalnie opracowanych dla różnego typu łączności i komunikacji w celu łatwiejszego nawiązania połączenia danych do możliwości chmury. Usługa Azure IoT Hub i Azure Event Hubs są usługi w chmurze, które mogą pozyskiwać dużych ilości danych i przetwarzania lub przechowywania tych danych, aby uzyskać wgląd w prowadzoną działalność. Te dwie usługi są podobne, że obsługują one zarówno pozyskiwania danych z małymi opóźnieniami i dużą niezawodność, ale są one przeznaczone do różnych celów. Usługi IoT Hub została opracowana specjalnie, aby spełnić unikatowe wymagania łączenie urządzeń IoT na dużą skalę, do chmury platformy Azure, natomiast usługa Event Hubs została zaprojektowana do obsługi dużych ilości danych przesyłania strumieniowego. Dlatego firma Microsoft zaleca, aby połączyć urządzenia IoT na platformie Azure przy użyciu usługi Azure IoT Hub

Usługa Azure IoT Hub jest przez bramę chmury, łączące urządzenia IoT na potrzeby zbierania danych na uzyskiwaniu wglądu w dane biznesowe i automatyzacji. Ponadto usługa IoT Hub zawiera funkcje, które wzbogacanie relacji między urządzeniami a z systemami zaplecza. Komunikacja dwukierunkowa, które możliwości oznacza, że podczas odbierania danych z urządzeń można również wysyłać polecenia i zasad do urządzeń, na przykład do aktualizacji właściwości lub wywołują akcje z zakresu zarządzania urządzeniami.  To połączenie chmury do urządzeń obsługuje także ważny czynnik dostarczania rozwiązań inteligentnych w chmurze na urządzenia brzegowe za pomocą usługi Azure IoT Edge. Unikatowa tożsamość urządzenia poziom udostępniane przez usługi IoT Hub ułatwia lepszego zabezpieczenia swojego rozwiązania IoT przed potencjalnymi atakami. 

[Usługa Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) są dane big data, przesyłania strumieniowego usługi Azure. Jest ona przeznaczona scenariusze o dużej przepływności danych przesyłania strumieniowego której klienci mogą wysyłać miliardów żądań dziennie. Usługa Event Hubs używa partycjonowanego modelu odbiorców do skalowania w poziomie strumienia i jest zintegrowana z danych big data i analizy usług platformy Azure, w tym usługi Databricks, usługi Stream Analytics, Azure Data Lake Store i HDInsight. Za pomocą funkcji, takich jak funkcja przechwytywania usługi Event Hubs i automatyczne rozszerzanie ta usługa jest przeznaczona do obsługi danych big data, aplikacji i rozwiązań. Ponadto usługi IoT Hub wykorzystuje usługę Event Hubs dla ścieżki przepływu danych telemetrycznych, więc rozwiązanie IoT jest również korzysta z ogromne możliwości usługi Event Hubs.

Aby podsumować, gdy oba rozwiązania są przeznaczone do wprowadzania danych na dużą skalę, tylko IoT Hub udostępnia zaawansowanych IoT specyficznych funkcji, które są przeznaczone dla Ciebie w celu zmaksymalizowania wartości biznesowej technologii łączenie urządzeń IoT w chmurze platformy Azure.  Jeśli właśnie rozpoczyna swoją podróż IoT, począwszy od usługi IoT Hub w celu obsługi różnych scenariuszy pozyskiwania danych zagwarantuje mieć natychmiastowy dostęp do możliwości IoT w pełni funkcjonalne, gdy Twoje potrzeby biznesowe i techniczne wymagają.

Poniższa tabela zawiera szczegółowe informacje o jak dwie warstwy usługi IoT Hub wypadają w porównaniu do usługi Event Hubs podczas oceniania ich możliwości IoT. Aby uzyskać więcej informacji na temat warstw standardowa i podstawowa usługi IoT Hub, zobacz [jak wybrać właściwą warstwę usługi IoT Hub](iot-hub-scaling.md).

| Możliwości IoT | W warstwie standardowa usługi IoT Hub | Warstwa podstawowa usługi IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Komunikaty z urządzenia do chmury | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |
| Protokoły: Protokół HTTPS, AMQP, AMQP, przez protokół webSockets | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |
| Protokoły: MQTT, MQTT przez protokół webSockets | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Tożsamość na urządzenie | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Przekazywanie plików z urządzeń | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Usługa Device Provisioning | ![Zaznacz][checkmark] | ![Zaznacz][checkmark] |  |
| Obsługa komunikatów między chmurą a urządzeniem | ![Zaznacz][checkmark] |  |  |
| Bliźniacza reprezentacja urządzenia i zarządzania urządzeniami | ![Zaznacz][checkmark] |  |  |
| Strumienie urządzenia (wersja zapoznawcza) | ![Zaznacz][checkmark] |  |  |
| IoT Edge | ![Zaznacz][checkmark] |  |  |

Nawet jeśli tylko przypadek użycia jest pozyskiwanie danych urządzenia do chmury, zdecydowanie zaleca się za pomocą usługi IoT Hub, ponieważ umożliwia to usługa, która jest przeznaczona dla łączności między urządzeniami IoT. 

### <a name="next-steps"></a>Kolejne kroki

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
