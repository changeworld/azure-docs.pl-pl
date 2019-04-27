---
title: Dzienniki diagnostyczne platformy Azure Service Bus | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne usługi Service Bus na platformie Azure.
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
ms.openlocfilehash: 7d4cb8e55c5d1561c09cf85122550a66e3671f17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714141"
---
# <a name="service-bus-diagnostic-logs"></a>Dzienniki diagnostyczne usługi Service Bus

Możesz wyświetlić dwa typy dzienników dla usługi Azure Service Bus:
* **[Dzienniki aktywności](../azure-monitor/platform/activity-logs-overview.md)**. Te dzienniki zawierają informacje dotyczące operacji wykonywanych na zadania. Dzienniki są zawsze włączone.
* **[Dzienniki diagnostyczne](../azure-monitor/platform/diagnostic-logs-overview.md)**. Można skonfigurować dzienniki diagnostyczne na potrzeby bardziej rozbudowane informacje o wszystko, co się stanie, w ramach danego zadania. Dzienniki diagnostyczne czynności tytułowa, od czasu utworzenia zadania do momentu usunięcia zadania, w tym aktualizacje i działań, które występują, gdy zadanie jest uruchomione.

## <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1.  W [witryny Azure portal](https://portal.azure.com)w obszarze **monitorowanie + zarządzanie**, kliknij przycisk **dzienniki diagnostyczne**.

    ![Nawigacja w bloku do dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image1.png)

2. Kliknij zasób, który chcesz monitorować.  

3.  Kliknij pozycję **Włącz diagnostykę**.

    ![Włączanie dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image2.png)

4.  Aby uzyskać **stan**, kliknij przycisk **na**.

    ![Zmień stan dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image3.png)

5.  Ustaw docelowy archiwum, który ma; na przykład konto magazynu, Centrum zdarzeń lub usługi Azure Monitor dzienników.

6.  Zapisz nowe ustawienia diagnostyki.

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Po tym dzienników pojawia się w skonfigurowanym archiwizacji lokalizacji docelowej na **dzienniki diagnostyczne** bloku.

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [Przegląd dzienniki diagnostyczne platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Dzienniki diagnostyczne schematu

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis ma pola ciągów, w formacie opisane w poniższej sekcji.

## <a name="operational-logs-schema"></a>Dzienniki operacyjne schematu

Loguje się **OperationalLogs** kategorii przechwytywania, co się dzieje podczas operacji usługi Service Bus. W szczególności te dzienniki przechwytywania typ operacji, w tym tworzenie kolejek, zasoby używane i stan operacji.

Dziennik operacyjny ciągów JSON obejmują elementy wymienione w poniższej tabeli:

Name (Nazwa) | Opis
------- | -------
Identyfikator działania | Wewnętrzny identyfikator, używane do śledzenia
EventName | Nazwa operacji           
resourceId | Identyfikator zasobu usługi Azure Resource Manager
SubscriptionId | Identyfikator subskrypcji
EventTimeString | Czas operacji
EventProperties | Właściwości operacji
Stan | Stan operacji
Obiekt wywołujący | Obiekt wywołujący operacji (klient usługi Azure portal lub zarządzania)
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

Zobacz poniższe linki, aby dowiedzieć się więcej na temat usługi Service Bus:

* [Wprowadzenie do usługi Service Bus](service-bus-messaging-overview.md)
* [Rozpoczynanie pracy z usługą Service Bus](service-bus-dotnet-get-started-with-queues.md)
