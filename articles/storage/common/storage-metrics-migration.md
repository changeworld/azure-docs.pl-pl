---
title: Migracja metryki magazynu platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie migrowania starszych metryki na nowe metryki zarządzane przez Azure Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: da8eb0b9e2e5aba60b61a36d83f525c7ce4a7958
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="azure-storage-metrics-migration"></a>Migracja metryki magazynu Azure

Wyrównane strategii ujednolicających środowisko monitora na platformie Azure, Azure Storage na karty metryki dla platformy Azure monitora. W przyszłości usługa metryk starszych zostaną zakończone z wcześniejszego powiadomienia na podstawie zasad Azure. Jeśli użytkownik korzysta z magazynu starszych metryki, trzeba przeprowadzać migracji wcześniejsza od daty zakończenia usługi, aby zachować dane metryki.

W tym artykule opisano w migracji ze starszej wersji metryki nowe metryki.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Zrozumienie starszych metryki zarządzanych przez usługi Azure Storage

Usługa Azure Storage zbiera starszych wartości metryki, agregacje i zapisuje je w tabelach $Metric w ramach tego samego konta magazynu. Azure portal umożliwia konfigurowanie monitorowania wykresu, i umożliwia także zestawów SDK magazynu Azure do odczytu danych z tabel $Metric na podstawie schematu. Aby uzyskać więcej informacji, mogą odwoływać się do tego [analityka magazynu](./storage-analytics.md).

Metryki starszych zawierają metryki o pojemności dla obiektu Blob tylko i metryki transakcji obiektów Blob, tabel, pliku i kolejki.

Metryki starszych zostały zaprojektowane w płaskiej schematu. Projekt spowoduje zero wartość metryki, gdy nie mają wzorce ruchu wyzwalania metryki. Na przykład **ServerTimeoutError** jest ustawiona na 0 w tabelach $Metric, nawet wtedy, gdy wszystkie błędy przekroczenia limitu czasu serwera nie otrzymywać ruchu sieciowego na żywo do konta magazynu.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Zrozumienie nowe metryki zarządzane przez Azure Monitor

Dla nowego magazynu metryki usługi Azure Storage emituje dane metryk do wewnętrznej bazy danych Azure monitora. Azure monitor zapewnia ujednolicone funkcji monitorowania, włącznie z danymi z portalu oraz wprowadzanie danych. Aby uzyskać więcej informacji, mogą odwoływać się do tego [artykułu](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nowe metryki zawierają metryki pojemności i transakcji metryki obiektów Blob, tabel, pliku, kolejki i Magazyn w warstwie premium.

Wymiar wiele jest jednym z funkcji udostępnianych przez Azure Monitor. Usługa Azure Storage przyjmuje projektu podczas definiowania nowego schematu metryki. Obsługiwane wymiary metryki szczegółowe informacje można znaleźć w tym [metryk usługi Azure Storage w monitorze Azure](./storage-metrics-in-azure-monitor.md). Wymiar wiele udostępniono opłacalności zarówno przepustowość między wprowadzanie i pojemności zapisywanie metryki. W związku z tym jeśli nie ma są uruchamiane powiązane metryki ruchu, powiązanych danych metryki nie zostanie wygenerowany. Na przykład, jeśli ruchu nie wyzwolił wszelkie błędy przekroczenia limitu czasu serwera, Azure Monitor zwraca żadnych danych kwerendy wartość metryki **transakcji** z wymiarem **wartość ResponseType** równa **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Metryki mapowanie między starszych metryki i nowe metryki

Programowo odczytać metryki danych należy przyjąć nowego schematu metryki w programach. Aby lepiej zrozumieć zmiany, mogą odwoływać się do mapowania wymienione w poniższej tabeli:

**Metryki pojemności**

| Metryka starszej wersji | Nowa metryka |
| ------------------- | ----------------- |
| Pojemność            | BlobCapacity z wymiarem BlobType BlockBlob lub PageBlob |
| ObjectCount         | Liczba obiektów blob z wymiarem BlobType BlockBlob lub PageBlob |
| ContainerCount      | ContainerCount |

Następujące metryki są nowe oferty, które nie obsługują starszych metryki:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Metryki transakcji**

| Metryka starszej wersji | Nowa metryka |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transakcje z wymiarem wartość ResponseType równa AuthorizationError |
| AnonymousClientOtherError | Transakcje z wymiarem wartość ResponseType równa ClientOtherError |
| AnonymousClientTimeoutError | Transakcje z wymiarem wartość ResponseType równa ClientTimeoutError |
| AnonymousNetworkError | Transakcje z wymiarem wartość ResponseType równa NetworkError |
| AnonymousServerOtherError | Transakcje z wymiarem wartość ResponseType równa ServerOtherError |
| AnonymousServerTimeoutError | Transakcje z wymiarem wartość ResponseType równa ServerTimeoutError |
| AnonymousSuccess | Transakcje z wymiarem wartość ResponseType równa Powodzenie |
| AnonymousThrottlingError | Transakcje z wymiarem wartość ResponseType równa ClientThrottlingError lub ServerBusyError |
| AuthorizationError | Transakcje z wymiarem wartość ResponseType równa AuthorizationError |
| Dostępność | Dostępność |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transakcje z wymiarem wartość ResponseType równa ClientOtherError |
| ClientTimeoutError | Transakcje z wymiarem wartość ResponseType równa ClientTimeoutError |
| NetworkError | Transakcje z wymiarem wartość ResponseType równa NetworkError |
| PercentAuthorizationError | Transakcje z wymiarem wartość ResponseType równa AuthorizationError |
| PercentClientOtherError | Transakcje z wymiarem wartość ResponseType równa ClientOtherError |
| PercentNetworkError | Transakcje z wymiarem wartość ResponseType równa NetworkError |
| PercentServerOtherError | Transakcje z wymiarem wartość ResponseType równa ServerOtherError |
| PercentSuccess | Transakcje z wymiarem wartość ResponseType równa Powodzenie |
| PercentThrottlingError | Transakcje z wymiarem wartość ResponseType równa ClientThrottlingError lub ServerBusyError |
| PercentTimeoutError | Transakcje z wymiarem wartość ResponseType równa ServerTimeoutError lub wartość ResponseType równa ClientTimeoutError|
| SASAuthorizationError | Transakcje z wymiarem wartość ResponseType równa AuthorizationError |
| SASClientOtherError | Transakcje z wymiarem wartość ResponseType równa ClientOtherError |
| SASClientTimeoutError | Transakcje z wymiarem wartość ResponseType równa ClientTimeoutError |
| SASNetworkError | Transakcje z wymiarem wartość ResponseType równa NetworkError |
| SASServerOtherError | Transakcje z wymiarem wartość ResponseType równa ServerOtherError |
| SASServerTimeoutError | Transakcje z wymiarem wartość ResponseType równa ServerTimeoutError |
| SASSuccess | Transakcje z wymiarem wartość ResponseType równa Powodzenie |
| SASThrottlingError | Transakcje z wymiarem wartość ResponseType równa ClientThrottlingError lub ServerBusyError |
| ServerOtherError | Transakcje z wymiarem wartość ResponseType równa ServerOtherError |
| ServerTimeoutError | Transakcje z wymiarem wartość ResponseType równa ServerTimeoutError |
| Powodzenie | Transakcje z wymiarem wartość ResponseType równa Powodzenie |
| ThrottlingError | Transakcje z wymiarem wartość ResponseType równa ClientThrottlingError lub ServerBusyError |
| TotalBillableRequests | Transakcje |
| TotalEgress | Ruch wychodzący |
| TotalIngress | Zdarzenia związane z transferem danych przychodzących |
| TotalRequests | Transakcje |

## <a name="faq"></a>Często zadawane pytania

* Jak przeprowadzić migrację istniejących reguł alertów

A. Jeśli utworzono klasycznego alert reguły oparte na metryki starszych magazynu, musisz utworzyć nowy alert reguły na podstawie nowych metryki schematu.

* Zostanie nowych danych metryki domyślnie przechowywane w tym samym koncie magazynu?

Odpowiedź: nie. Jeśli chcesz zarchiwizować metryki danych do konta magazynu, można użyć [interfejsu API ustawienia diagnostyki Azure monitora](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate)

## <a name="next-steps"></a>Kolejne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metryki magazynu w Monitorze systemu Azure](./storage-metrics-in-azure-monitor.md)
