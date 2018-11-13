---
title: Cele wydajności i skalowalności usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o cele skalowalności i wydajności dla usługi Azure Storage, w tym pojemności, liczba żądań i przepustowości ruchu przychodzącego i wychodzącego dla obu kont magazynu w warstwie standardowa i premium. Informacje o wydajności związanych z partycji w ramach każdej z usług Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 758871537b89a9c010cfaddf324e2208f9846afb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241329"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Cele usługi Azure Storage dotyczące skalowalności i wydajności
## <a name="overview"></a>Przegląd
W tym artykule opisano tematy dotyczące skalowalności i wydajności dla usługi Azure Storage. Aby uzyskać podsumowanie inne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md).

> [!NOTE]
> Wszystkie konta magazynu na nową topologię siecią płaską a obsługuje cele dotyczące skalowalności i wydajności, opisane w tym artykule, niezależnie od tego, w przypadku ich utworzenia. Aby uzyskać więcej informacji na architekturę siecią płaską usługi Azure Storage i skalowalność, zobacz [usługi Microsoft Azure Storage: A o wysokiej dostępności usługi magazynu w chmurze with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Cele dotyczące skalowalności i wydajności, wymienione w tym miejscu są cele wysokiej klasy, ale są osiągalne. Wszystkie przypadki, liczba żądań i przepustowości magazynu konta zależy od rozmiaru obiektów przechowywanych, wzorce dostępu wykorzystywany, i wykonuje aplikację typu obciążenia. Pamiętaj przetestować usługi w celu określenia, czy jego wydajność, spełnia Twoje wymagania. Jeśli to możliwe Unikaj skokami szybkość ruchu i upewnij się, że ruch jest dobrze rozproszone pomiędzy partycjami.
> 
> Gdy aplikacja osiągnie limit partycji radzić dla obciążenia, usługi Azure Storage rozpoczyna zwracają kod błędu: 503 (serwer zajęty) lub kod błędu: 500 odpowiedzi (limit czasu operacji). Jeśli występują błędy, aplikacja powinna używać zasady wykładniczego wycofywania ponownych prób. Wykładniczego wycofywania umożliwia obciążenia na partycji, aby zmniejszyć i działają nawet pod dużym nagłych skoków ruchu do tej partycji.
> 
> 

Wymagania aplikacji przekroczy cele skalowalności z jednego konta magazynu, możesz tworzyć aplikacji do użycia wielu kont magazynu. Następnie można podzielić obiekty danych na tych kontach magazynu. Zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) Aby uzyskać informacje dotyczące cennika woluminów.

## <a name="scalability-targets-for-a-storage-account"></a>Wartości docelowe skalowalności konta magazynu
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

### <a name="storage-resource-provider-limits"></a>Limity dostawcy zasobów magazynu 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure celów skalowania magazynu obiektów Blob
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Usługa Azure celów skalowania plików
Aby uzyskać więcej informacji na temat cele skalowalności i wydajności dla usługi Azure Files i usługi Azure File Sync, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Files](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync
Za pomocą usługi Azure File Sync mają próbowaliśmy jak najszerzej się projektowania pod kątem użycia nieograniczone, jednak nie zawsze jest to możliwe. Poniższa tabela wskazuje granice Nasze testy i które elementy docelowe są faktycznie stałych limitów:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Skalowanie magazynu platformy Azure kolejki elementów docelowych
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Cele skalowania usługi storage dotyczące tabeli platformy Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Zobacz też
* [Szczegóły cennika usługi Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Subskrypcja platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md)
* [Replikacja usługi Azure Storage](../storage-redundancy.md)
* [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage-performance-checklist.md)
* [Microsoft Azure Storage: O wysokiej dostępności usługi magazynu w chmurze za pomocą silnej spójności](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

