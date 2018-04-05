---
title: Włączanie rejestrowania diagnostycznego partii zdarzeń - Azure | Dokumentacja firmy Microsoft
description: Rejestruj i analizowanie dzienników diagnostycznych zdarzeń dla zasobów konta partii zadań Azure, takich jak pule i zadania.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41c8c9f8fd9302c610ce356b0485e33ea3c967d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Rejestrowanie zdarzeń diagnostycznych oceny i monitorowania rozwiązań partii

Podobnie jak w przypadku wielu usług Azure, usługa partia zadań emituje dziennika zdarzeń dla niektórych zasobów przez cały okres istnienia zasobu. Można Włączanie dzienników diagnostycznych partii zadań Azure rejestrowanie zdarzeń dla zasobów, takich jak pule i zadań, a następnie za pomocą dzienników diagnostycznych oceny i monitorowania. Tworzenie zdarzeń, takich jak puli, Usuń puli, uruchamiania zadań zadanie ukończone i inne są wyświetlane w partii dzienników diagnostycznych.

> [!NOTE]
> W tym artykule omówiono rejestrowania zdarzeń dla samych zasobach konta usługi partia zadań, nie zadania i dane wyjściowe zadań. Aby uzyskać więcej informacji na temat przechowywania danych wyjściowych zadań i zadań, zobacz [danych wyjściowych i zadań utrwalić partii zadań Azure](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* [Konto usługi partia zadań Azure](batch-account-create-portal.md)
* [konto usługi Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Aby utrwalić dzienników diagnostycznych partii, należy utworzyć konta usługi Azure Storage, w którym Azure będą przechowywane dzienniki. To konto magazynu można określić podczas możesz [włączyć rejestrowanie diagnostyczne](#enable-diagnostic-logging) dla Twojego konta usługi partia zadań. Konto magazynu, możesz określić, że po włączeniu zbierania dzienników nie jest taka sama jak połączonym koncie magazynu określonym w [pakietów aplikacji](batch-application-packages.md) i [trwałości danych wyjściowych zadania](batch-task-output.md) artykułów.
  
  > [!WARNING]
  > Jesteś **obciążona** dane przechowywane na koncie magazynu Azure. W tym dzienniki diagnostyczne omówione w tym artykule. Należy o tym pamiętać podczas projektowania sieci [logowania zasady przechowywania](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Włączanie rejestrowania diagnostycznego
Rejestrowanie diagnostyczne nie jest włączone domyślnie dla Twojego konta usługi partia zadań. Musisz jawnie włączyć rejestrowanie diagnostyczne dla każdego konta usługi partia zadań, które chcesz monitorować:

[Jak włączyć kolekcję dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Zalecamy przeczytanie pełnego [Omówienie programu Azure dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artykuł, aby poznać nie tylko sposób włączania rejestrowania, ale kategorie dziennika obsługiwanych przez różne usługi platformy Azure. Na przykład w partii zadań Azure obsługuje obecnie jedną kategorię dziennika: **dzienniki usługi**.

## <a name="service-logs"></a>Dzienniki usługi
Dzienniki usługi usługi partia zadań Azure zawiera zdarzenia emitowane przez usługi partia zadań Azure przez cały okres istnienia zasobu partii puli lub zadania. Każdego zdarzenia emitowane przez partię znajduje się na określonym koncie magazynu w formacie JSON. Na przykład to jest treść próbki **puli utworzyć zdarzenia**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Każda jednostka zdarzenia znajduje się w pliku JSON w określonym koncie magazynu Azure. Jeśli chcesz uzyskać bezpośredni dostęp dzienniki, możesz przejrzeć [schematu dzienników diagnostycznych na koncie magazynu](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Usługa dziennika zdarzeń
Usługa partia zadań emituje obecnie następujące zdarzenia logowania do usługi. Ta lista nie może być pełne, ponieważ dodatkowe zdarzenia mogły zostać dodane od momentu ostatniej aktualizacji w tym artykule.

| **Usługa dziennika zdarzeń** |
| --- |
| [Tworzenie puli][pool_create] |
| [Start usuwania puli][pool_delete_start] |
| [Usuwanie puli ukończone][pool_delete_complete] |
| [Początkowy rozmiar puli][pool_resize_start] |
| [Zmiana rozmiaru puli ukończone][pool_resize_complete] |
| [Rozpoczęcia zadania][task_start] |
| [Zadania ukończone][task_complete] |
| [Niepowodzenie zadania][task_fail] |

## <a name="next-steps"></a>Kolejne kroki
Oprócz zdarzeń diagnostycznych dziennika są przechowywane na koncie magazynu Azure, możesz również strumienia zdarzeń logowania do usługi partii [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md)i wyślij je do [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Strumienia Azure dzienników diagnostycznych do usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Strumienia zdarzeń diagnostycznych partii do wysoce skalowalna Usługa transferu danych, usługa Event Hubs. Centra zdarzeń może obsługiwać miliony zdarzeń na sekundę, który można przekształcić i magazynu przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym.
* [Analizowanie Azure dzienników diagnostycznych przy użyciu analizy dzienników](../log-analytics/log-analytics-azure-storage.md)
  
  Wysyłanie dzienników diagnostycznych do analizy dzienników, w którym można analizować je w portalu Operations Management Suite (OMS), lub wyeksportować je do analizy w programie Excel lub usługi Power BI.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
