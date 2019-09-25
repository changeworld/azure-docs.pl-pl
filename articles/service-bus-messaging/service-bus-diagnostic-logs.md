---
title: Azure Service Bus dzienników diagnostycznych | Microsoft Docs
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne dla Service Bus na platformie Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261806"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus dzienników diagnostycznych

Można wyświetlić dwa typy dzienników dla Azure Service Bus:
* **[Dzienniki aktywności](../azure-monitor/platform/activity-logs-overview.md)** . Te dzienniki zawierają informacje o operacjach wykonywanych w ramach zadania. Dzienniki są zawsze włączone.
* **[Dzienniki diagnostyczne](../azure-monitor/platform/resource-logs-overview.md)** . Dzienniki diagnostyczne można skonfigurować w celu uzyskania bardziej szczegółowych informacji na temat wszystkiego, co się dzieje w ramach zadania. Dzienniki diagnostyczne czynności tytułowa, od czasu utworzenia zadania do momentu usunięcia zadania, w tym aktualizacje i działań, które występują, gdy zadanie jest uruchomione.

## <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1.  W [witryny Azure portal](https://portal.azure.com)w obszarze **monitorowanie + zarządzanie**, kliknij przycisk **dzienniki diagnostyczne**.

    ![Nawigacja w bloku do dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image1.png)

2. Kliknij zasób, który chcesz monitorować.  

3.  Kliknij pozycję **Włącz diagnostykę**.

    ![Włączanie dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image2.png)

4.  Aby uzyskać **stan**, kliknij przycisk **na**.

    ![Zmień stan dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image3.png)

5.  Ustaw żądany cel Archiwum; na przykład konto magazynu, centrum zdarzeń lub dzienniki Azure Monitor.

6.  Zapisz nowe ustawienia diagnostyki.

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w bloku **dzienniki diagnostyczne** .

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [Przegląd dzienniki diagnostyczne platformy Azure](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Dzienniki diagnostyczne schematu

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższej sekcji.

## <a name="operational-logs-schema"></a>Dzienniki operacyjne schematu

Dzienniki w kategorii **OperationalLogs** przechwytują, co się dzieje podczas operacji Service Bus. W szczególności te dzienniki przechwytują typ operacji, w tym Tworzenie kolejki, używane zasoby i stan operacji.

Dziennik operacyjny ciągów JSON obejmują elementy wymienione w poniższej tabeli:

Name (Nazwa) | Opis
------- | -------
Identyfikator działania | Identyfikator wewnętrzny używany do śledzenia
EventName | Nazwa operacji           
resourceId | Identyfikator zasobu Azure Resource Manager
SubscriptionId | Identyfikator subskrypcji
EventTimeString | Czas operacji
EventProperties | Właściwości operacji
State | Stan operacji
Caller | Obiekt wywołujący operacji (Azure Portal lub klient zarządzania)
category | OperationalLogs

Oto przykład ciągu JSON dziennika operacyjnego:

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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus, zobacz następujące linki:

* [Wprowadzenie do Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do Service Bus](service-bus-dotnet-get-started-with-queues.md)
