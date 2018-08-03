---
title: Przykłady usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Przykłady usługi Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: sethm
ms.openlocfilehash: 8e80587f7b3f6b0cb081fd963848c7aa3ab11e3e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431260"
---
# <a name="event-hubs-samples"></a>Przykłady usługi Event Hubs 
Przykłady usługi Event Hubs można znaleźć na [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Te przykłady przedstawiono najważniejsze funkcje w [usługi Azure Event Hubs](/azure/event-hubs/). W tym artykule kategoryzuje i opisuje dostępna, wraz z łączami do każdego przykładów.

## <a name="net-samples"></a>Przykłady dla platformy .NET

| Przykładowa nazwa | Opis | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Ten przykład pokazuje, jak napisać aplikację konsoli .NET Core wysyłającej zestaw zdarzeń do Centrum zdarzeń. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | W tym przykładzie pokazano, jak napisać aplikację konsoli .NET Core, która odbiera zestaw zdarzeń z Centrum zdarzeń przy użyciu biblioteki hosta procesora zdarzeń.  | 

## <a name="java-samples"></a>Przykłady w języku Java

| Przykładowa nazwa | Opis | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Ten przykład ilustruje sposób pozyskiwanie partii zdarzeń do Centrum zdarzeń. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Ten przykład ilustruje sposób pozyskiwanie zdarzeń do Centrum zdarzeń. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Ten przykład ilustruje różne opcje dostępne w usłudze Event Hubs do obsługi zdarzeń pochodzących. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | W tym przykładzie pokazano, jak odbierać zdarzenia z partycji Centrum zdarzeń przy użyciu określonego przesunięcia daty i godziny. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | W tym przykładzie pokazano, jak odbierać zdarzenia z partycji Centrum zdarzeń za pomocą przesunięcie określonych danych. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | W tym przykładzie pokazano sposób mogą odbierać z partycji Centrum zdarzeń za pomocą numeru sekwencji. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Ten przykład ilustruje sposób odbieranie zdarzeń z Centrum zdarzeń za pomocą hosta procesora zdarzeń, co zapewnia wybór automatycznych partycji i tryb failover między wieloma odbiornikami współbieżnych. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | W tym przykładzie pokazano, jak Centrum zdarzeń można automatycznie Skaluj w górę na wysokie obciążenie. Próbka będzie wysyłać zdarzenia z szybkością, które po prostu przekracza skonfigurowany stopień Centrum zdarzeń, powodując Centrum zdarzeń, aby skalować w górę. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Ten przykład umożliwia pomiar szybkości transferu danych przychodzących. | 

## <a name="next-steps"></a>Kolejne kroki
Możesz dowiedzieć się więcej o usłudze Event Hubs w następujących artykułach:

- [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Funkcje usługi Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)