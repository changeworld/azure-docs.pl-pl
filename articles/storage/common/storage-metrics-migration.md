---
title: Migracja na platformę Azure metryki magazynu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację metryk starej do nowej metryki, które są zarządzane przez usługi Azure Monitor.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 58ac15c1aba715c9a5b67e723401b531e76608b2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153597"
---
# <a name="azure-storage-metrics-migration"></a>Migracji metryki magazynu platformy Azure

Dostosowane do strategii, ujednolicając naukę środowisko monitorowania na platformie Azure, Azure Storage integruje metryki na platformę Azure Monitor. W przyszłości usługa starego pomiarów zakończy się wcześniejsze powiadomienie na podstawie zasad platformy Azure. Jeśli polegać na starym metryk usługi storage, które chcesz zmigrować przed datą zakończenia usługi, aby zachować dane metryk.

W tym artykule pokazano, jak przeprowadzić migrację z metryk starej do nowej metryki.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Zrozumienie stare metryki, które są zarządzane przez usługę Azure Storage

Usługa Azure Storage umożliwia zbieranie informacji o stare wartości metryk, agreguje i przechowuje je w tabelach $Metric w obrębie tego samego konta magazynu. Witryna Azure portal umożliwia konfigurowanie monitorowania wykresu. Można również użyć zestawów SDK usługi Azure Storage do odczytywania danych z tabel $Metric, które są oparte na schemacie. Aby uzyskać więcej informacji, zobacz [Storage Analytics](./storage-analytics.md).

Stary metryki zawierają metryki pojemności tylko na usłudze Azure Blob storage. Stary metryki zawierają metryk transakcji usługi Blob storage, Table storage, Azure Files i Queue storage.

Stary metryki zostały zaprojektowane w schemacie prostego. Projekt skutkuje zero wartość metryki, gdy nie masz wzorców ruchu wyzwalania metryki. Na przykład **ServerTimeoutError** wartość jest równa 0 w tabelach $Metric nawet wtedy, gdy wszystkie błędy przekroczenia limitu czasu serwera nie otrzymywać aktywny ruch do konta magazynu.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Omówienie nowych metryk, zarządzane usługi Azure Monitor

Dla nowych metryk magazynowania usługi Azure Storage emituje dane metryk do zaplecza usługi Azure Monitor. Usługa Azure Monitor zapewnia ujednolicone środowisko monitorowania, włącznie z danymi z poziomu portalu oraz wprowadzanie danych. Aby uzyskać więcej informacji, możesz zapoznać się z tym [artykułu](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nowe metryki zawierają metryki pojemności i transakcji metryki dotyczące obiektów Blob, tabel, pliku, kolejki i Magazyn w warstwie premium.

Wielu wymiarów jest jedną z funkcji udostępnianych przez usługi Azure Monitor. Usługa Azure Storage przyjmuje projektu, definiując nowe metryki schematu. Obsługiwane wymiary dla metryk, można znaleźć szczegółowe informacje w [metryk usługi Azure Storage w usłudze Azure Monitor](./storage-metrics-in-azure-monitor.md). Projekt wielu wymiarów zawiera opłacalności zarówno przepustowość danych wychodzących z pozyskiwania i pojemność przechowywanie metryki. W związku z tym jeśli ruch sieciowy nie wyzwolił powiązane metryki, powiązane dane metryk nie będą generowane. Na przykład, jeśli ruch sieciowy nie wyzwoleniu błędy przekroczenia limitu czasu serwera usługi Azure Monitor nie zwraca żadnych danych kiedy wykonujesz zapytanie o wartość metryki **transakcji** z wymiarem **ResponseType** równa **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metryki mapowanie między metryki stare i nowe metryki

Możesz programowo odczytać dane metryk, należy przyjąć nowy schemat metryki w swoich programach. Aby lepiej zrozumieć zmiany, mogą odwoływać się do mapowania wymienione w poniższej tabeli:

**Metryki wydajności**

| Stary metryki | Nową metrykę |
| ------------------- | ----------------- |
| **Dyspozycyjność**            | **BlobCapacity** z wymiarem **BlobType** równa **BlockBlob** lub **PageBlob** |
| **ObjectCount**        | **BlobCount** z wymiarem **BlobType** równa **BlockBlob** lub **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Nowe oferty, które nie obsługują stare metryki są następujące metryki:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Metryki transakcji**

| Stary metryki | Nową metrykę |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transakcje z wymiarem **ResponseType** równa **AuthorizationError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousClientOtherError** | Transakcje z wymiarem **ResponseType** równa **ClientOtherError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousClientTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ClientTimeoutError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousNetworkError** | Transakcje z wymiarem **ResponseType** równa **NetworkError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousServerOtherError** | Transakcje z wymiarem **ResponseType** równa **ServerOtherError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousServerTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ServerTimeoutError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousSuccess** | Transakcje z wymiarem **ResponseType** równa **Powodzenie** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AnonymousThrottlingError** | Transakcje z wymiarem **ResponseType** równa **ClientThrottlingError** lub **ServerBusyError** i wymiar **uwierzytelniania** równa **anonimowe** |
| **AuthorizationError** | Transakcje z wymiarem **ResponseType** równa **AuthorizationError** |
| **Dostępność** | **Dostępność** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transakcje z wymiarem **ResponseType** równa **ClientOtherError** |
| **ClientTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ClientTimeoutError** |
| **NetworkError** | Transakcje z wymiarem **ResponseType** równa **NetworkError** |
| **PercentAuthorizationError** | Transakcje z wymiarem **ResponseType** równa **AuthorizationError** |
| **PercentClientOtherError** | Transakcje z wymiarem **ResponseType** równa **ClientOtherError** |
| **PercentNetworkError** | Transakcje z wymiarem **ResponseType** równa **NetworkError** |
| **PercentServerOtherError** | Transakcje z wymiarem **ResponseType** równa **ServerOtherError** |
| **PercentSuccess** | Transakcje z wymiarem **ResponseType** równa **sukces** |
| **PercentThrottlingError** | Transakcje z wymiarem **ResponseType** równa **ClientThrottlingError** lub **ServerBusyError** |
| **Wartości PercentTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ServerTimeoutError** lub **ResponseType** równa **ClientTimeoutError** |
| **SASAuthorizationError** | Transakcje z wymiarem **ResponseType** równa **AuthorizationError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASClientOtherError** | Transakcje z wymiarem **ResponseType** równa **ClientOtherError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASClientTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ClientTimeoutError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASNetworkError** | Transakcje z wymiarem **ResponseType** równa **NetworkError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASServerOtherError** | Transakcje z wymiarem **ResponseType** równa **ServerOtherError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASServerTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ServerTimeoutError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASSuccess** | Transakcje z wymiarem **ResponseType** równa **Powodzenie** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **SASThrottlingError** | Transakcje z wymiarem **ResponseType** równa **ClientThrottlingError** lub **ServerBusyError** i wymiar **uwierzytelniania** równa **sygnatury dostępu Współdzielonego** |
| **ServerOtherError** | Transakcje z wymiarem **ResponseType** równa **ServerOtherError** |
| **ServerTimeoutError** | Transakcje z wymiarem **ResponseType** równa **ServerTimeoutError** |
| **Powodzenie** | Transakcje z wymiarem **ResponseType** równa **sukces** |
| **ThrottlingError** | **Transakcje** z wymiarem **ResponseType** równa **ClientThrottlingError** lub **ServerBusyError**|
| **TotalBillableRequests** | **Transakcje** |
| **TotalEgress** | **Ruch wychodzący** |
| **TotalIngress** | **Ruch przychodzący** |
| **TotalRequests** | **Transakcje** |

## <a name="faq"></a>Często zadawane pytania

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak przeprowadzić migrację istniejących reguł alertów

Jeśli utworzono klasyczne reguły alertów na podstawie starej metryk magazynowania, należy utworzyć nowy alert reguły na podstawie nowych metryk schematu.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Nowe dane metryk znajduje się w tym samym koncie magazynu domyślnie?

Nie. Aby zarchiwizować dane metryk do konta magazynu, należy użyć [interfejsu API ustawienie diagnostyczne usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Kolejne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metryki magazynu w usłudze Azure Monitor](./storage-metrics-in-azure-monitor.md)
