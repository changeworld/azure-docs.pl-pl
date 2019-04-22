---
title: Przykłady dla usługi Azure Event Hubs — | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę przykładów dla usługi Azure Event Hubs, które są w witrynie GitHub.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 1c1198733fb56303d328ee97152442d25dbe945a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682399"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Repozytoria Git z przykładami dla usługi Azure Event Hubs 
Przykłady usługi Event Hubs można znaleźć na [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Te przykłady pokazują kluczowe funkcje [usługi Azure Event Hubs](/azure/event-hubs/). W tym artykule kategoryzuje i opisuje dostępna, wraz z łączami do każdego przykładów.

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

## <a name="python-samples"></a>Przykłady w języku Python
Można znaleźć przykłady kodu Python dla usługi Azure Event Hubs w [koncentratory python, azure event w-](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) repozytorium GitHub.

## <a name="nodejs-samples"></a>Przykłady dla platformy Node.js
Możesz znaleźć przykłady dla platformy Node.js dla usługi Azure Event Hubs w [azure sdk dla js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) repozytorium GitHub.

## <a name="go-samples"></a>Przykłady kodu języka go
Możesz znaleźć przykładów kodu Go dla usługi Azure Event Hubs w [centra pracy, azure zdarzeń w-](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) repozytorium GitHub.

## <a name="azure-cli-samples"></a>Przykłady interfejsu wiersza polecenia platformy Azure
Możesz znaleźć przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Event Hubs w [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) repozytorium GitHub.

## <a name="azure-powershell-samples"></a>Przykłady programu Azure PowerShell
Możesz znaleźć przykładów programu Azure PowerShell dla usługi Azure Event Hubs w [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) repozytorium GitHub.
 
## <a name="apache-kafka-samples"></a>Przykłady dla platformy Apache Kafka
Możesz znaleźć przykłady dla usługi Event Hubs dla platformy Apache Kafka funkcji [azure — zdarzeń hubs dla — platformy kafka](https://github.com/Azure/azure-event-hubs-for-kafka) repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki
Możesz dowiedzieć się więcej o usłudze Event Hubs w następujących artykułach:

- [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Funkcje usługi Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)