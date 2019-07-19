---
title: Cele dotyczące skalowalności i wydajności usługi Azure Storage — konta magazynu
description: Dowiedz się więcej na temat skalowalności i wydajności, takich jak pojemność, szybkość żądania oraz przepustowość ruchu przychodzącego i wychodzącego, dla kont usługi Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326974"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Cele dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu

W tym artykule opisano elementy docelowe skalowalności i wydajności dla kont usługi Azure Storage. Elementy docelowe skalowalności i wydajności wymienione w tym miejscu są obiektami docelowymi wysokiej klasy, ale są osiągalne. We wszystkich przypadkach częstotliwość żądań i przepustowość osiągnięta przez konto magazynu zależy od rozmiaru przechowywanych obiektów, użytych wzorców dostępu oraz typu obciążenia wykonywanego przez aplikację.

Upewnij się, że usługa została przetestowana, aby określić, czy jej wydajność spełnia Twoje wymagania. Jeśli to możliwe, unikaj nagłych skoków natężenia ruchu i upewnij się, że ruch jest prawidłowo dystrybuowany między partycjami.

Gdy aplikacja osiągnie limit, co może obsłużyć partycja w obciążeniu, usługa Azure Storage zaczyna zwracać kod błędu 503 (serwer zajęty) lub kod błędu 500 (limit czasu operacji). Jeśli wystąpią błędy 503, Rozważ zmodyfikowanie aplikacji tak, aby korzystała z zasad wycofywania wykładniczych na potrzeby ponownych prób. Wykładniczy wycofywania umożliwia zmniejszenie obciążenia partycji i ułatwienie wzrostu ruchu do tej partycji.

## <a name="storage-account-scale-limits"></a>Limity skalowania konta magazynu

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limity skalowania konta magazynu wydajności w warstwie Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limity skalowania dostawcy zasobów magazynu

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Cele skalowania magazynu obiektów blob platformy Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Usługa Azure celów skalowania plików

Aby uzyskać więcej informacji na temat elementów docelowych skalowania i wydajności dla Azure Files i Azure File Sync, zobacz [Azure Files cele dotyczące skalowalności i wydajności](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Limity konta magazynu dotyczą wszystkich udziałów. Skalowanie w górę do wartości maksymalnej dla kont magazynu jest osiągalne tylko wtedy, gdy istnieje tylko jeden udział na konto magazynu.
>
> Standardowe udziały plików większe niż 5 TiB są w wersji zapoznawczej i mają pewne ograniczenia.
> Aby zapoznać się z listą ograniczeń i dołączyć do wersji zapoznawczej tych większych rozmiarów udziałów plików, zobacz sekcję [standardowe udziały plików](../files/storage-files-planning.md#standard-file-shares) w przewodniku planowania Azure Files.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Cele skalowania plików w warstwie Premium

Istnieją trzy kategorie ograniczeń, które należy wziąć pod uwagę w przypadku plików Premium: konta magazynu, udziały i pliki.

Na przykład: Pojedynczy udział może osiągać 100 000 operacji we/wy, a pojedynczy plik może być skalowany w górę do 5 000 operacji we/wy na sekundę. Jeśli na przykład masz trzy pliki w jednym udziale, Maksymalna liczba IOPs, którą można uzyskać z tego udziału, to 15 000.

#### <a name="premium-file-share-limits"></a>Limity udziałów plików w warstwie Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync

Azure File Sync został zaprojektowany z myślą o celu nieograniczonego użycia, ale nieograniczone użycie nie zawsze jest możliwe. Poniższa tabela wskazuje granice testowania firmy Microsoft, a także wskazuje, które cele są sztywne:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Cele skalowania usługi Azure queue storage

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Cele skalowania w usłudze Azure Table Storage

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Zobacz także

- [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/)
- [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-subscription-service-limits.md)
- [Replikacja usługi Azure Storage](../storage-redundancy.md)
- [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage-performance-checklist.md)
