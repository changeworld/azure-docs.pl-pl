---
title: Konfigurowanie dzienników diagnostycznych — usługi Azure Event Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dzienniki aktywności i dzienników diagnostycznych usługi event hubs w systemie Azure.
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162314"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Skonfiguruj dzienniki diagnostyczne na potrzeby Centrum zdarzeń platformy Azure

Możesz wyświetlić dwa typy dzienników dla usługi Azure Event Hubs:

* **[Dzienniki aktywności](../azure-monitor/platform/platform-logs-overview.md)** : te dzienniki zawierają informacje o operacjach wykonywanych w ramach zadania. Dzienniki są zawsze włączone.
* **[Dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md)** : można skonfigurować dzienniki diagnostyczne, aby uzyskać bogatszy widok wszystkiego, co się dzieje z zadaniami. Dzienniki diagnostyczne czynności tytułowa, od czasu utworzenia zadania do momentu usunięcia zadania, w tym aktualizacje i działań, które występują, gdy zadanie jest uruchomione.

## <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  W [Azure Portal](https://portal.azure.com)w obszarze **monitorowanie + zarządzanie**kliknij pozycję **dzienniki diagnostyczne**.

    ![W okienku nawigacji do dzienników diagnostycznych](./media/event-hubs-diagnostic-logs/image1.png)

2.  Kliknij zasób, który chcesz monitorować.

3.  Kliknij pozycję **Włącz diagnostykę**.

    ![Włączanie dzienników diagnostycznych](./media/event-hubs-diagnostic-logs/image2.png)

4.  W obszarze **stan**kliknij pozycję **włączone**.

    ![Zmiana stanu dzienników diagnostycznych](./media/event-hubs-diagnostic-logs/image3.png)

5.  Ustaw żądany cel Archiwum; na przykład konto magazynu, centrum zdarzeń lub dzienniki Azure Monitor.

6.  Zapisz nowe ustawienia diagnostyki.

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w okienku **dzienniki diagnostyczne** .

Więcej informacji o konfigurowaniu diagnostyki znajduje się w temacie [Omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Kategorie dzienników diagnostycznych

Usługa Event Hubs przechwytuje dzienniki diagnostyczne na dwie kategorie:

* **Dzienniki archiwum**: dzienniki związane z archiwami Event Hubs, w tym dzienniki związane z błędami archiwum.
* **Dzienniki operacyjne**: informacje o tym, co się dzieje w trakcie operacji Event Hubs, w szczególności o typie operacji, w tym o tworzeniu centrum zdarzeń, używanych zasobach i stanie operacji.

## <a name="diagnostic-logs-schema"></a>Dzienniki diagnostyczne schematu

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis ma pola ciągów, w formacie opisane w poniższych sekcjach.

### <a name="archive-logs-schema"></a>Archiwizuj dzienniki schematu

Ciągi JSON dziennika archiwum obejmują elementy wymienione w poniższej tabeli:

Name (Nazwa) | Opis
------- | -------
TaskName | Opis zadania, które nie powiodło się.
Identyfikator działania | Wewnętrzny identyfikator używany do śledzenia.
trackingId | Wewnętrzny identyfikator używany do śledzenia.
resourceId | Identyfikator usługi Azure Resource Manager zasobu.
eventHub | Centrum zdarzeń Pełna nazwa (z uwzględnieniem nazwy obszaru nazw).
partitionId | Trwa zapisywanie do partycji Centrum zdarzeń.
archiveStep | ArchiveFlushWriter
startTime | Godzina rozpoczęcia awarii.
niepowodzenia | Liczba przypadków, gdy wystąpił błąd.
durationInSeconds | Czas trwania awarii.
message | Komunikat o błędzie.
category | ArchiveLogs

Poniższy kod jest przykładem Archiwizowanie dziennika ciągu JSON:

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

### <a name="operational-logs-schema"></a>Dzienniki operacyjne schematu

Dziennik operacyjny ciągów JSON obejmują elementy wymienione w poniższej tabeli:

Name (Nazwa) | Opis
------- | -------
Identyfikator działania | Wewnętrzny identyfikator służący do śledzenia celów.
EventName | Nazwa operacji.  
resourceId | Identyfikator usługi Azure Resource Manager zasobu.
SubscriptionId | Identyfikator subskrypcji.
EventTimeString | Czas operacji.
EventProperties | Właściwości operacji.
Stan | Stan operacji.
Obiekt wywołujący | Obiekt wywołujący operacji (klient usługi Azure portal lub zarządzania).
category | OperationalLogs

Poniższy kod jest przykładem dziennik operacyjny ciągu JSON:

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
- [Wprowadzenie do Event Hubs](event-hubs-what-is-event-hubs.md)
- [Przegląd interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
