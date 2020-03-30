---
title: Konfigurowanie dzienników diagnostycznych — Usługa Azure Event Hub | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować dzienniki aktywności i dzienniki diagnostyczne dla centrów zdarzeń na platformie Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162314"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurowanie dzienników diagnostycznych na potrzeby centrum zdarzeń platformy Azure

Można wyświetlić dwa typy dzienników dla usługi Azure Event Hubs:

* **[Dzienniki aktywności:](../azure-monitor/platform/platform-logs-overview.md)** Te dzienniki mają informacje o operacjach wykonywanych w zadaniu. Dzienniki są zawsze włączone.
* **[Dzienniki diagnostyczne:](../azure-monitor/platform/platform-logs-overview.md)** Można skonfigurować dzienniki diagnostyczne dla bogatszego widoku wszystkiego, co dzieje się z zadaniem. Dzienniki diagnostyczne obejmują działania od momentu utworzenia zadania do momentu usunięcia zadania, w tym aktualizacje i działania występujące podczas uruchamiania zadania.

## <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  W [witrynie Azure portal](https://portal.azure.com)w obszarze **Monitorowanie + Zarządzanie**kliknij pozycję **Dzienniki diagnostyczne**.

    ![Nawigacja po okienku do dzienników diagnostycznych](./media/event-hubs-diagnostic-logs/image1.png)

2.  Kliknij zasób, który chcesz monitorować.

3.  Kliknij pozycję **Włącz diagnostykę**.

    ![Włączanie dzienników diagnostycznych](./media/event-hubs-diagnostic-logs/image2.png)

4.  Aby **uzyskać status,** kliknij przycisk **Włącz**.

    ![Zmienianie stanu dzienników diagnostycznych](./media/event-hubs-diagnostic-logs/image3.png)

5.  Ustaw odpowiedni cel archiwum; na przykład konto magazynu, centrum zdarzeń lub dzienniki usługi Azure Monitor.

6.  Zapisz nowe ustawienia diagnostyczne.

Nowe ustawienia zajmą się efektem około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym celu archiwizacji w okienku **Dzienniki diagnostyki.**

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki, zobacz [omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Kategorie dzienników diagnostycznych

Usługa Event Hubs przechwytuje dzienniki diagnostyczne dla dwóch kategorii:

* **Dzienniki archiwum:** dzienniki związane z archiwum Event Hubs, w szczególności dzienniki związane z błędami archiwum.
* **Dzienniki operacyjne:** informacje o tym, co dzieje się podczas operacji centrum zdarzeń, w szczególności typu operacji, w tym tworzenia centrum zdarzeń, używanych zasobów i stanu operacji.

## <a name="diagnostic-logs-schema"></a>Schemat dzienników diagnostycznych

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższych sekcjach.

### <a name="archive-logs-schema"></a>Schemat dzienników archiwum

Ciągi JSON dziennika archiwum zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | -------
Nazwa_zadania | Opis zadania, które nie powiodło się.
Activityid | Identyfikator wewnętrzny, używany do śledzenia.
identyfikator śledzenia | Identyfikator wewnętrzny, używany do śledzenia.
resourceId | Identyfikator zasobu usługi Azure Resource Manager.
eventHub | Pełna nazwa Centrum zdarzeń (zawiera nazwę obszaru nazw).
Partitionid | Partycja Centrum zdarzeń jest zapisywany do.
archiwumStep | ArchiwumFlushWriter
startTime | Czas rozpoczęcia awarii.
Awarii | Liczba wystąpienia awarii.
durationInSeconds | Czas trwania awarii.
message | Komunikat o błędzie.
category | ArchiveLogs (Dzienniki archiwalne)

Poniższy kod jest przykładem ciągu JSON dziennika archiwum:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Schemat dzienników operacyjnych

Parametry JSON dziennika operacyjnego zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | -------
Activityid | Identyfikator wewnętrzny, używany do śledzenia celu.
EventName | nazwa operacji.  
resourceId | Identyfikator zasobu usługi Azure Resource Manager.
SubscriptionId | Identyfikator subskrypcji.
EventTimeString | Czas pracy.
EventProperties | Właściwości operacji.
Stan | Stan operacji.
Obiekt wywołujący | Obiekt wywołujący operację (portal Azure lub klient zarządzania).
category | Dzienniki operacyjne

Poniższy kod jest przykładem ciągu JSON dziennika operacyjnego:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do centrów zdarzeń](event-hubs-what-is-event-hubs.md)
- [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
