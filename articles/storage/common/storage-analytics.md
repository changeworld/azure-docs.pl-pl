---
title: Zbieranie danych dotyczących dzienników i danych dotyczących metryk za pomocą usługi Azure Storage | Dokumenty firmy Microsoft
description: Analiza magazynu umożliwia śledzenie danych metryk dla wszystkich usług magazynu i zbieranie dzienników dla obiektów Blob, Queue i magazynu tabel.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 86b399879807e480176ee9a3ca3feaba1ec5dd85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250878"
---
# <a name="storage-analytics"></a>Analityka magazynu

Usługa Azure Storage Analytics wykonuje rejestrowanie i udostępnia dane metryki dla konta magazynu. Te dane można używać do śledzenia żądań, analizowania trendów użycia i diagnozowania problemów z kontem magazynu.

Aby korzystać z analizy magazynu, należy włączyć ją indywidualnie dla każdej usługi, którą chcesz monitorować. Można go włączyć w [witrynie Azure portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w witrynie Azure portal](storage-monitor-storage-account.md). Można również włączyć usługi Storage Analytics programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj [funkcji Ustaw właściwości usługi obiektów blob,](/rest/api/storageservices/set-blob-service-properties) [Ustaw właściwości usługi kolejki,](/rest/api/storageservices/set-queue-service-properties) [Ustaw właściwości usługi tabeli](/rest/api/storageservices/set-table-service-properties)i Ustaw właściwości [usługi plików,](/rest/api/storageservices/Get-File-Service-Properties) aby włączyć analizę magazynu dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanym obiekcie blob (do rejestrowania) i w dobrze znanych tabelach (dla metryk), które mogą być dostępne przy użyciu interfejsów API usługi obiektów Blob i usługi tabel.

Usługa Storage Analytics ma limit 20 TB ilości przechowywanych danych, która jest niezależna od całkowitego limitu dla twojego konta magazynu. Aby uzyskać więcej informacji na temat limitów kont magazynu, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu](scalability-targets-standard-account.md).

Aby uzyskać szczegółowy przewodnik dotyczący korzystania z analizy magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="billing-for-storage-analytics"></a>Rozliczenia za analizę pamięci masowej

Wszystkie dane metryki są zapisywane przez usługi konta magazynu. W rezultacie każda operacja zapisu wykonywana przez storage analytics jest rozliczalna. Ponadto ilość miejsca do magazynowania używanego przez dane metryki jest również rozliczane.

Następujące akcje wykonywane przez storage Analytics podlegają rozliczaniu:

* Żądania utworzenia obiektów blob do rejestrowania.
* Żądania tworzenia jednostek tabeli dla metryk.

Jeśli skonfigurowano zasady przechowywania danych, nie są naliczane opłaty za usuwanie transakcji, gdy usługa Storage Analytics usuwa stare dane dotyczące rejestrowania i metryki. Jednak transakcje usuwania z klienta są rozliczane. Aby uzyskać więcej informacji na temat zasad przechowywania, zobacz [Ustawianie zasad przechowywania danych analizy magazynu](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Opis żądań podlegania rozliczania

Każde żądanie złożone do usługi magazynu konta jest rozliczane lub niepodejmowalne. Usługa Storage Analytics rejestruje każde indywidualne żądanie wykonane w usłudze, w tym komunikat o stanie, który wskazuje sposób obsługi żądania. Podobnie usługa Storage Analytics przechowuje metryki zarówno dla usługi, jak i operacji interfejsu API tej usługi, w tym wartości procentowe i liczbę niektórych komunikatów o stanie. Razem te funkcje mogą pomóc w analizowaniu żądań podlegania rozliczaniu, wprowadzaniu ulepszeń w aplikacji i diagnozowaniu problemów z żądaniami do usług. Aby uzyskać więcej informacji na temat rozliczeń, zobacz [Opis rozliczeń usługi Azure Storage — przepustowość, transakcje i pojemność](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

Patrząc na dane analizy magazynu, można użyć tabel w [analizie magazynu logged operacji i komunikatów o stanie,](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) aby określić, jakie żądania są rozliczane. Następnie można porównać dzienniki i dane metryki do komunikatów o stanie, aby sprawdzić, czy zostały obciążone za określone żądanie. Tabele w poprzednim temacie można również użyć do zbadania dostępności usługi magazynu lub operacji pojedynczego interfejsu API.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md)
* [Dane analizy magazynu](storage-analytics-metrics.md)
* [Rejestrowanie analityki magazynu](storage-analytics-logging.md)
