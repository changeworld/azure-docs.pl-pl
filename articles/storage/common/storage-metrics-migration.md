---
title: Migracja metryki magazynu platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację metryki starego do nowego metryk, które są zarządzane przez Azure Monitor.
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
ms.openlocfilehash: c64061aee94e8c08a3f6bcae78cffca0b4172d97
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650676"
---
# <a name="azure-storage-metrics-migration"></a>Migracja metryki magazynu Azure

Wyrównane strategii jednorodnej obsługi monitora na platformie Azure, Azure Storage integruje metryki dla platformy Azure monitora. W przyszłości usługa starego metryki kończy się wcześniejszego powiadomienia na podstawie zasad Azure. Jeśli użytkownik korzysta z starego magazynu metryki, trzeba przeprowadzać migracji wcześniejsza od daty zakończenia usługi, aby zachować dane metryki.

W tym artykule przedstawiono sposób migracji z metryki starego do nowego metryki.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Zrozumienie starego metryk, które są zarządzane przez usługi Azure Storage

Magazyn Azure zbiera starych wartości metryki, agreguje i przechowuje je w tabelach $Metric w ramach tego samego konta magazynu. Azure portal umożliwia konfigurowanie monitorowania wykresu. Odczytywanie danych z tabel $Metric, które są oparte na schemacie umożliwia także zestawów SDK magazynu Azure. Aby uzyskać więcej informacji, zobacz [analityka magazynu](./storage-analytics.md).

Metryki starego zawierają metryki pojemności tylko magazynu obiektów Blob platformy Azure. Metryki starego zawierają metryki transakcji magazynu obiektów Blob, Magazyn tabel Azure plików i magazynu kolejek. 

Metryki starego zostały zaprojektowane w płaskiej schematu. Projekt spowoduje zero wartość metryki, gdy nie mają wzorce ruchu wyzwalania metryki. Na przykład **ServerTimeoutError** wartość jest równa 0 w tabelach $Metric nawet wtedy, gdy wszystkie błędy przekroczenia limitu czasu serwera nie otrzymywać ruchu sieciowego na żywo na konto magazynu.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Zrozumienie nowe metryki zarządzane przez Azure Monitor

Dla nowego magazynu metryki usługi Azure Storage emituje metryki danych zaplecza Azure monitora. Azure Monitor zapewnia ujednolicone funkcji monitorowania, włącznie z danymi z portalu oraz wprowadzanie danych. Aby uzyskać więcej informacji, mogą odwoływać się do tego [artykułu](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nowe metryki zawierają metryki pojemności i transakcji metryki obiektów Blob, tabel, pliku, kolejki i Magazyn w warstwie premium.

Wymiar wiele jest jednym z funkcji, które udostępnia Azure Monitor. Usługa Azure Storage przyjmuje projektu podczas definiowania nowego schematu metryki. Obsługiwane wymiary metryki, można znaleźć szczegółowe informacje w [metryk usługi Azure Storage w monitorze Azure](./storage-metrics-in-azure-monitor.md). Wymiar wiele udostępniono opłacalności zarówno przepustowość między wprowadzanie i pojemności zapisywanie metryki. W związku z tym jeśli nie ma są uruchamiane powiązane metryki ruchu, powiązanych danych metryki nie zostanie wygenerowany. Na przykład, jeśli nie ma są uruchamiane wszystkie błędy przekroczenia limitu czasu serwera ruchu, Azure Monitor nie zwraca żadnych danych kwerendy wartość metryki **transakcji** z wymiarem **wartość ResponseType** równe **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metryki mapowanie między metryki starego i nowego metryk

Programowo odczytać metryki danych należy przyjąć nowego schematu metryki w programach. Aby lepiej zrozumieć zmiany, mogą odwoływać się do mapowania wymienione w poniższej tabeli:

**Metryki pojemności**

| Metryka starego | Nowa metryka |
| ------------------- | ----------------- |
| **pojemność**            | **BlobCapacity** z wymiarem **BlobType** równa **BlockBlob** lub **PageBlob** |
| **ObjectCount**        | **Liczba obiektów blob** z wymiarem **BlobType** równa **BlockBlob** lub **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Następujące metryki są nowe oferty nieobsługujących starego metryki:
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

| Metryka starego | Nowa metryka |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transakcje z wymiarem **wartość ResponseType** równa **AuthorizationError** |
| **AnonymousClientOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientOtherError** |
| **AnonymousClientTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientTimeoutError** |
| **AnonymousNetworkError** | Transakcje z wymiarem **wartość ResponseType** równa **NetworkError** |
| **AnonymousServerOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerOtherError** |
| **AnonymousServerTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerTimeoutError** |
| **AnonymousSuccess** | Transakcje z wymiarem **wartość ResponseType** równa **Powodzenie** |
| **AnonymousThrottlingError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientThrottlingError** lub **ServerBusyError** |
| **AuthorizationError** | Transakcje z wymiarem **wartość ResponseType** równa **AuthorizationError** |
| **Dostępność** | **Dostępność** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientOtherError** |
| **ClientTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientTimeoutError** |
| **NetworkError** | Transakcje z wymiarem **wartość ResponseType** równa **NetworkError** |
| **PercentAuthorizationError** | Transakcje z wymiarem **wartość ResponseType** równa **AuthorizationError** |
| **PercentClientOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientOtherError** |
| **PercentNetworkError** | Transakcje z wymiarem **wartość ResponseType** równa **NetworkError** |
| **PercentServerOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerOtherError** |
| **PercentSuccess** | Transakcje z wymiarem **wartość ResponseType** równa **Powodzenie** |
| **PercentThrottlingError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientThrottlingError** lub **ServerBusyError** |
| **PercentTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerTimeoutError** lub **wartość ResponseType** równa **ClientTimeoutError** |
| **SASAuthorizationError** | Transakcje z wymiarem **wartość ResponseType** równa **AuthorizationError** |
| **SASClientOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientOtherError** |
| **SASClientTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientTimeoutError** |
| **SASNetworkError** | Transakcje z wymiarem **wartość ResponseType** równa **NetworkError** |
| **SASServerOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerOtherError** |
| **SASServerTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerTimeoutError** |
| **SASSuccess** | Transakcje z wymiarem **wartość ResponseType** równa **Powodzenie** |
| **SASThrottlingError** | Transakcje z wymiarem **wartość ResponseType** równa **ClientThrottlingError** lub **ServerBusyError** |
| **ServerOtherError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerOtherError** |
| **ServerTimeoutError** | Transakcje z wymiarem **wartość ResponseType** równa **ServerTimeoutError** |
| **Powodzenie** | Transakcje z wymiarem **wartość ResponseType** równa **Powodzenie** |
| **ThrottlingError** | **Transakcje** z wymiarem **wartość ResponseType** równa **ClientThrottlingError** lub **ServerBusyError**|
| **TotalBillableRequests** | **Transakcje** |
| **TotalEgress** | **Ruch wychodzący** |
| **TotalIngress** | **Ruch przychodzący** |
| **TotalRequests** | **Transakcje** |

## <a name="faq"></a>Często zadawane pytania

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak przeprowadzić migrację istniejących reguł alertów

Jeśli utworzono klasycznego reguły alertu oparte na starym metryki magazynu, musisz utworzyć nowy alert reguły oparte na nowym schematem metryki.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Nowe dane metryki znajduje się w tym samym koncie magazynu domyślnie?

Nie. Archiwizowanie danych metryki na konto magazynu, należy użyć [interfejsu API ustawienia diagnostyki Azure Monitor](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Kolejne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metryki magazynu w monitorze Azure](./storage-metrics-in-azure-monitor.md)
