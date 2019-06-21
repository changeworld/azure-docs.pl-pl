---
title: Usługa Azure Storage cele skalowalności i wydajności - kont magazynu
description: Więcej informacji na temat cele skalowalności i wydajności, w tym pojemności, liczba żądań i przepustowości ruchu przychodzącego i wychodzącego, do konta usługi Azure storage.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 932d250d6685a1b905e4a03a0118d8c8f1f26418
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151241"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Usługa Azure Storage cele skalowalności i wydajności dla kont magazynu

Ten artykuł szczegółowo opisuje cele skalowalności i wydajności dla konta usługi Azure storage. Cele dotyczące skalowalności i wydajności, wymienione w tym miejscu są cele wysokiej klasy, ale są osiągalne. Wszystkie przypadki, liczba żądań i przepustowości magazynu konta zależy od rozmiaru obiektów przechowywanych, wzorce dostępu wykorzystywany, i wykonuje aplikację typu obciążenia.

Pamiętaj przetestować usługi w celu określenia, czy jego wydajność, spełnia Twoje wymagania. Jeśli to możliwe Unikaj skokami szybkość ruchu i upewnij się, że ruch jest dobrze rozproszone pomiędzy partycjami.

Gdy aplikacja osiągnie limit partycji radzić dla obciążenia, usługi Azure Storage rozpoczyna zwracają kod błędu: 503 (serwer zajęty) lub kod błędu: 500 odpowiedzi (limit czasu operacji). Jeśli występują błędy 503 należy rozważyć zmodyfikowanie aplikacji umożliwiająca użycie zasady wykładniczego wycofywania ponownych prób. Wykładniczego wycofywania umożliwia obciążenia na partycji, aby zmniejszyć i działają nawet pod dużym nagłych skoków ruchu do tej partycji.

## <a name="storage-account-scale-limits"></a>Limity skalowania dla konta magazynu

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limity skalowania dla konta magazynu Premium wydajności

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limity skalowania dostawcy zasobów magazynu

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure celów skalowania magazynu obiektów Blob

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Usługa Azure celów skalowania plików

Aby uzyskać więcej informacji na temat cele skalowalności i wydajności dla usługi Azure Files i usługi Azure File Sync, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Files](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Limity konta magazynu mają zastosowanie do wszystkich udziałów. Skalowanie do maksymalna liczba kont magazynu tylko jest osiągalna, jeśli istnieje tylko jeden udział przypada na koncie magazynu.
>
> Udziały plików standardowych większy niż wynosi 5 TiB są w wersji zapoznawczej i mają pewne ograniczenia.
> Lista ograniczeń i dołączyć do wersji zapoznawczej te większe rozmiary udziału plików, zobacz [standardowych plikowych udziałów](../files/storage-files-planning.md#standard-file-shares) przewodnik sekcji planowania usługi Azure Files.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Files w warstwie Premium skalowanie elementów docelowych

Istnieją trzy kategorie ograniczenia do uwzględnienia w przypadku plików — wersja premium: kont magazynu, udziały i plików.

Na przykład: Pojedynczy udział może osiągnąć 100 000 operacji We/Wy i maksymalnie 5000 operacji We/Wy można skalować w jednym pliku. Tak na przykład, jeśli masz trzy pliki w jednym udziale, max operacje We/Wy, można uzyskać z tego folderu wspólnego jest 15 000.

#### <a name="premium-file-share-limits"></a>Limity udziału plików — wersja Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync

Usługa Azure File Sync został zaprojektowany w celu nieograniczonego użycia, ale nie nieograniczonego użycia zawsze jest możliwe. Poniższa tabela wskazuje granice badania firmy Microsoft oraz wskazuje, które elementy docelowe są limity stałe:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Skalowanie magazynu platformy Azure kolejki elementów docelowych

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Cele skalowania usługi storage dotyczące tabeli platformy Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Zobacz także

- [Szczegóły cennika usługi Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Subskrypcja platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md)
- [Replikacja usługi Azure Storage](../storage-redundancy.md)
- [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage-performance-checklist.md)