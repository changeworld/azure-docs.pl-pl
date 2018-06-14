---
title: Azure Service Bus dzienników diagnostycznych | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania dzienników diagnostycznych dla usługi Service Bus na platformie Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 4ce724adc9ca167634be9a0b7137b6a3d54211bf
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29122185"
---
# <a name="service-bus-diagnostic-logs"></a>Dzienniki diagnostyczne usługi Service Bus

Dla usługi Azure Service Bus, można wyświetlić dwa typy dzienników:
* **[Dzienniki aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Dzienniki te zawierają informacje o operacji wykonywanych w zadaniu. Dzienniki są zawsze włączone.
* **[Dzienniki diagnostyczne](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Można skonfigurować dziennika diagnostycznego bardziej rozbudowane informacje o wszystko, co się stanie w ramach danego zadania. Dzienniki diagnostyczne obejmują działania, od czasu utworzenia zadania do momentu usunięcia zadania, w tym aktualizacje i działania, które są wykonywane, gdy zadanie jest uruchomione.

## <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1.  W [portalu Azure](https://portal.azure.com)w obszarze **monitorowanie i zarządzanie**, kliknij przycisk **dzienników diagnostycznych**.

    ![Nawigacja bloku do dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image1.png)

2. Kliknij zasób, który chcesz monitorować.  

3.  Kliknij przycisk **Włącz diagnostykę**.

    ![Włączanie dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image2.png)

4.  Aby uzyskać **stan**, kliknij przycisk **na**.

    ![Zmień stan dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image3.png)

5.  Ustaw element docelowy archiwum, które mają; na przykład konto magazynu, Centrum zdarzeń lub Analiza dzienników Azure.

6.  Zapisz nowe ustawienia diagnostyki.

Nowe ustawienia zaczęły obowiązywać w ciągu około 10 minut. Po wykonaniu tej dzienników pojawia się w celu archiwizacji skonfigurowane, na **dzienników diagnostycznych** bloku.

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki, zobacz [omówienie Azure dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Dzienniki diagnostyczne schematu

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis ma pól ciągów, które używają formatu opisane w poniższej sekcji.

## <a name="operational-logs-schema"></a>Schemat operacyjne dzienniki

Loguje **OperationalLogs** kategorii przechwytywania, co się dzieje podczas operacji usługi Service Bus. W szczególności te dzienniki przechwytywania typ operacji, łącznie z tworzeniem kolejki, zasoby używane i stan operacji.

Dziennik operacyjny JSON ciągi zawierać elementy wymienione w poniższej tabeli:

Name (Nazwa) | Opis
------- | -------
Identyfikator działania | Wewnętrzny identyfikator używany do śledzenia
EventName | Nazwa operacji           
resourceId | Identyfikator zasobu usługi Azure Resource Manager
SubscriptionId | Identyfikator subskrypcji
EventTimeString | Czas operacji
EventProperties | Właściwości operacji
Stan | Stan operacji
Obiekt wywołujący | Obiekt wywołujący operacji (Azure portalu lub zarządzania klienta)
category | OperationalLogs

Poniżej przedstawiono przykładowy dziennik operacyjny ciągu JSON:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz poniższe łącza, aby dowiedzieć się więcej na temat magistrali usług:

* [Wprowadzenie do usługi Service Bus](service-bus-messaging-overview.md)
* [Rozpoczynanie pracy z usługą Service Bus](service-bus-dotnet-get-started-with-queues.md)
