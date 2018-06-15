---
title: Cele dotyczące wydajności i skalowalności magazynu platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obiekty docelowe skalowalności i wydajności usługi Azure Storage, w tym pojemności, współczynnik żądań i przepustowości ruchu przychodzącego i wychodzącego dla obu kont magazynu standard i premium. Zrozumienie celów wydajności dla partycji w ramach każdej z usług Azure Storage.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: e393bb9e7615b893699caf5a931ede5803046892
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239688"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Cele usługi Azure Storage dotyczące skalowalności i wydajności
## <a name="overview"></a>Przegląd
W tym artykule opisano tematy dotyczące skalowalności i wydajności usługi Azure Storage. Podsumowanie innych limitów Azure, zobacz [subskrypcji platformy Azure i ograniczenia usługi, przydziały i ograniczenia](../../azure-subscription-service-limits.md).

> [!NOTE]
> Wszystkie konta magazynu Uruchom na nową topologię siecią płaską i obsługuje obiekty docelowe skalowalności i wydajności, opisane w tym artykule, niezależnie od tego, gdy zostały utworzone. Aby uzyskać więcej informacji na komputerze o architekturze siecią płaską usługi Azure Storage i skalowalność, zobacz [Microsoft Azure Storage: A wysoce dostępna usługa magazynu w chmurze with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Obiekty docelowe skalowalności i wydajności wymienione w tym miejscu wysokiej klasy elementów docelowych, ale są osiągalne. Wszystkich przypadkach, współczynnik żądań i przepustowości magazynu konta zależy od rozmiaru obiekty przechowywane, wzorce dostępu do wykorzystania, i wykonuje polecenie Typ obciążenia aplikacji. Należy przetestować usługą w celu ustalenia, czy jego wydajność, spełnia wymagania. Jeśli to możliwe zapobiec nagłym wzrostów w polu szybkość ruchu i sprawdź, czy ruch jest dobrze rozproszone na partycje.
> 
> Gdy aplikacja osiągnie limit co partycji może obsłużyć dla obciążenia, usługi Azure Storage zaczyna zwraca kod błędu 503 (serwer jest zajęty) lub kod błędu 500 odpowiedzi (limit czasu operacji). Jeśli występują błędy, aplikacji należy używać zasad wykładniczego wycofywania dla ponownych prób. Wykładniczego wycofywania umożliwia obciążenia na partycji zmniejszają i ułatwić limit największego ruchu do tej partycji.
> 
> 

Wymagania aplikacji może przekraczać wartości docelowe skalowalności konta magazynu pojedynczego, po utworzeniu aplikacji do korzystania z wielu kont magazynu. Następnie można podzielić obiektów danych przez tych kont magazynu. Zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) informacje dotyczące cennika woluminów.

## <a name="scalability-targets-for-a-storage-account"></a>Wartości docelowe skalowalności dla konta magazynu
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure cele skalowania magazynu obiektów Blob
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Cele plików Azure w skali
Aby uzyskać więcej informacji na skalowalność i wydajność elementy docelowe dla plików Azure i synchronizacji plików Azure, zobacz [elementy docelowe skalowalności i wydajności usługi pliki Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure cele skali synchronizacji plików
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Skala magazynu Azure kolejki elementów docelowych
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Skala magazynu Azure tabeli obiektów docelowych
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Zobacz też
* [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/)
* [Subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md)
* [Replikacja usługi Azure Storage](../storage-redundancy.md)
* [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage-performance-checklist.md)
* [Microsoft Azure Storage: Wysokiej dostępności magazynu usługi w chmurze o wysoki poziom spójności](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

