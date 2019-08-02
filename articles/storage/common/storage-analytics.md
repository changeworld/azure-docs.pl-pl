---
title: Zbieranie danych dzienników i metryk przy użyciu usługi Azure Storage Analytics | Microsoft Docs
description: Analityka magazynu umożliwia śledzenie danych metryk dla wszystkich usług magazynu oraz zbieranie dzienników dla magazynu obiektów blob, kolejek i tabel.
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: e812bf03bf2adb852becf56954e7039aa3eb0633
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565907"
---
# <a name="storage-analytics"></a>Analityka magazynu

Analityka usługi Azure Storage umożliwia rejestrowanie i dostarcza danych metrycznych dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu.

Aby użyć analityka magazynu, należy włączyć ją indywidualnie dla każdej usługi, którą chcesz monitorować. Można ją włączyć z poziomu [Azure Portal](https://portal.azure.com). Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w Azure Portal](storage-monitor-storage-account.md). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Aby włączyć analityka magazynu dla każdej usługi, użyj [Właściwości Ustawianie usługi obiektów BLOB](/rest/api/storageservices/set-blob-service-properties), [Ustawianie właściwości usługi kolejki](/rest/api/storageservices/set-queue-service-properties), [Ustawianie właściwości usługi Table](/rest/api/storageservices/set-table-service-properties)i [Ustawianie właściwości usługi plików](/rest/api/storageservices/Get-File-Service-Properties) .

Zagregowane dane są przechowywane w dobrze znanym obiekcie BLOB (na potrzeby rejestrowania) i w dobrze znanych tabelach (dla metryk), do których można uzyskać dostęp za pomocą interfejsów API Blob service i Table service.

Analityka magazynu ma limit 20 TB ilości przechowywanych danych, które są niezależne od łącznego limitu dla konta magazynu. Aby uzyskać więcej informacji na temat limitów kont magazynu, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](storage-scalability-targets.md).

Aby uzyskać szczegółowy przewodnik dotyczący używania analityka magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="billing-for-storage-analytics"></a>Rozliczanie dla analityka magazynu

Wszystkie dane metryk są zapisywane przez usługi konta magazynu. W związku z tym każda operacja zapisu wykonywana przez analityka magazynu jest rozliczana. Dodatkowo opłaty za magazyn używany przez dane metryk są również płatne.

Następujące akcje wykonywane przez analityka magazynu są rozliczane:

* Żądania tworzenia obiektów BLOB do rejestrowania.
* Żądania utworzenia jednostek tabeli dla metryk.

W przypadku skonfigurowania zasad przechowywania danych nie jest naliczana opłata za usuwanie transakcji, gdy analityka magazynu usuwa stare dane rejestrowania i metryk. Jednak usuwanie transakcji z klienta jest obciążane. Aby uzyskać więcej informacji na temat zasad przechowywania, zobacz [Ustawianie zasad przechowywania danych analityka magazynu](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Zrozumienie żądań rozliczanych

Każde żądanie wysłane do usługi magazynu konta jest obciążane lub nieobciążane. Analityka magazynu rejestruje każde żądanie wysyłane do usługi, w tym komunikat o stanie, który wskazuje, jak zostało obsłużone żądanie. Podobnie analityka magazynu przechowuje metryki dla usługi i operacji interfejsu API tej usługi, w tym wartości procentowe i liczby niektórych komunikatów o stanie. Funkcje te mogą pomóc analizować żądania rozliczane, wprowadzać ulepszenia aplikacji i diagnozować problemy z żądaniami do usług. Aby uzyskać więcej informacji na temat rozliczeń, zobacz Opis rozliczeń, [transakcji i pojemności usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Podczas wyszukiwania analityka magazynu danych można użyć tabel w temacie [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) , aby określić, które żądania są rozliczane. Następnie można porównać dane dzienników i metryk do komunikatów o stanie, aby sprawdzić, czy opłata została naliczona za określone żądanie. Możesz również użyć tabel w poprzednim temacie, aby zbadać dostępność usługi magazynu lub operacji poszczególnych interfejsów API.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie konta magazynu w Azure Portal](storage-monitor-storage-account.md)
* [Metryki analityka magazynu](storage-analytics-metrics.md)
* [Rejestrowanie analityka magazynu](storage-analytics-logging.md)
