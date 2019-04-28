---
title: Korzystanie z analizy usługi Azure Storage w celu zbierania danych dzienników i metryk | Dokumentacja firmy Microsoft
description: Usługa Storage Analytics umożliwia śledzenie danych metryk dla wszystkich usług magazynu oraz w celu zbierania dzienników na potrzeby magazynu obiektów Blob, kolejek i tabel.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483540"
---
# <a name="storage-analytics"></a>Analityka magazynu

Analityka usługi Azure Storage umożliwia rejestrowanie i dostarcza danych metrycznych dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu.

Aby korzystać z usługi Storage Analytics, należy włączyć je osobno dla każdej usługi, które chcesz monitorować. Można je włączyć z [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [monitorowania na koncie magazynu w witrynie Azure portal](storage-monitor-storage-account.md). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Użyj [Ustaw właściwości usługi obiektów Blob](/rest/api/storageservices/set-blob-service-properties), [Ustaw właściwości usługi kolejki](/rest/api/storageservices/set-queue-service-properties), [ustawiać właściwości usługi tabeli](/rest/api/storageservices/set-table-service-properties), i [Ustaw właściwości usługi plików](/rest/api/storageservices/Get-File-Service-Properties)Procedura włączania analityka magazynu dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanych obiektów blob (w przypadku rejestrowania) i dobrze znanych tabelach (w przypadku metryk), które mogą być dostępne za pomocą usługi obiektów Blob i Table service API.

Usługa Storage Analytics obowiązuje limit 20 TB na ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md).

Aby uzyskać szczegółowy przewodnik na temat korzystania z usługi Storage Analytics i inne narzędzia do identyfikowania, diagnozowanie i rozwiązywanie problemów związanych z usługi Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Rozliczenia dla analityka magazynu

Wszystkie dane metryk są zapisywane przez usługi na koncie magazynu. W rezultacie każdej operacji zapisu, wykonywane przez analityka magazynu jest płatne. Ponadto ilość miejsca używanego przez dane metryk są również jest płatne.

Następujące akcje wykonywane przez analityka magazynu są płatne:

* Utwórz obiekty BLOB do rejestrowania żądań.
* Żądań w celu utworzenia tabeli jednostki dla metryki.

Jeśli skonfigurowano zasady przechowywania danych nie są naliczane za transakcje usuwania gdy analityka magazynu usuwa stare dane rejestrowania i metryk. Jednak usunięcie transakcji od klienta są płatne. Aby uzyskać więcej informacji na temat zasad przechowywania, zobacz [ustawienie zasady przechowywania danych analizy magazynu](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Opis płatnych żądań

Wszystkie żądania skierowane do kont usługi storage jest rozliczana lub niepodlegających rozliczaniu. Usługa Storage Analytics rejestruje każdego pojedynczego żądania do usługi, w tym komunikat o stanie, który wskazuje obsługi żądania. Podobnie usługa Storage Analytics przechowuje metryki dla usługi i operacje interfejsu API tej usługi, w tym wartości procentowych i liczbę niektórych komunikatów o stanie. Razem te funkcje mogą pomóc analizować płatnych żądań, ulepszenia w swojej aplikacji i diagnozowanie problemów z żądania do usługi. Aby uzyskać więcej informacji na temat rozliczeń, zobacz [opis rozliczeń platformy Azure Storage — przepustowość, transakcje i pojemność](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Podczas przeglądania danych analizy magazynu, można użyć w tabelach w [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) tematu, aby ustalić, jakie żądania są płatne. Następnie możesz porównać swoje dzienniki i dane metryk do komunikatów o stanie, aby zobaczyć, jeśli naliczono dla określonego żądania. Umożliwia także tabele w poprzednim temacie, aby zbadać dostępność usługi magazynu lub indywidualnych operacji interfejsu API.

## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie konta magazynu w witrynie Azure portal](storage-monitor-storage-account.md)
* [Storage Analytics Metrics](storage-analytics-metrics.md)
* [Rejestrowanie danych analizy magazynu](storage-analytics-logging.md)
