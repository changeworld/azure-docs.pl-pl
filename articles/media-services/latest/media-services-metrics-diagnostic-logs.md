---
title: Monitorowanie metryk usług Media Services i dzienniki diagnostyczne za pośrednictwem usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie sposobu monitorowania metryk usług Media Services i dzienników diagnostycznych za pomocą usługi Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 23c87ae92a0f22b4a1a31c054df730af2efc07d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322101"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorowanie metryk usług Media Services i dzienników diagnostycznych

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Wszystkie dane zebrane przez usługi Azure Monitor pasuje do jednej z dwóch typów podstawowych, metryk i dzienników. Można monitorować dzienników diagnostycznych usługi Media Services i tworzyć alerty i powiadomienia dotyczące zebranych metryk i dzienników. Możesz wizualizować i analizować dane metryk za pomocą [Eksploratora metryk](../../azure-monitor/platform/metrics-getting-started.md). Możesz wysłać dzienniki do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), ich do usługi stream [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i wyeksportować je do [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/), lub użyj 3 usługi innych firm.

Aby uzyskać szczegółowym omówieniem, zobacz [metryk usługi Azure Monitor](../../azure-monitor/platform/data-platform.md) i [dzienników diagnostycznych platformy Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

W tym temacie omówiono aktualnie dostępne [metryki usług Media](#media-services-metrics) i [dzienniki diagnostyczne usługi multimediów](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metryki usługi Media Services

Metryki są zbierane w regularnych odstępach czasu, czy po zmianie wartości. Są one przydatne w przypadku alertów, ponieważ może być próbkowany często i alertu można szybko wywoływane z logiką stosunkowo proste.

Obecnie następujące Media Services [punkty końcowe przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) metryki są emitowane przez platformę Azure:

|Name (Nazwa)|Opis|
|---|---|
|Żądania|Zwraca szczegółowe informacje dotyczące łączna liczba żądań obsłużonych przez punkt końcowy przesyłania strumieniowego.|
|Ruch wychodzący|Całkowita liczba bajtów ruchu wychodzącego. Na przykład bajtów przesyłane strumieniowo przez punkt końcowy przesyłania strumieniowego.|
|Opóźnienie typu end to end powodzenia| Zawiera informacje na temat kompleksowe opóźnienie dla żądań zakończonych powodzeniem.|

Na przykład, aby uzyskać metryki "Wyjście" z interfejsem wiersza polecenia, należy uruchomić następujące `az monitor metrics` interfejsu wiersza polecenia:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Aby uzyskać informacje na temat tworzenia alertów dotyczących metryk, zobacz [tworzenie, wyświetlanie i zarządzanie nimi za pomocą usługi Azure Monitor alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Dzienniki diagnostyczne usługi Media Services

Obecnie można uzyskać następujące dzienniki diagnostyczne:

|Name (Nazwa)|Opis|
|---|---|
|Żądanie usługi dostarczania kluczy|Dzienniki, w których wyświetlane są informacje o żądaniach usługi dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [schematów](media-services-diagnostic-logs-schema.md).|

Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, należy uruchomić następujące `az monitor diagnostic-settings` interfejsu wiersza polecenia: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Na przykład:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Kolejne kroki 

[Jak zbierać i wykorzystywać dane dzienników z zasobów platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
