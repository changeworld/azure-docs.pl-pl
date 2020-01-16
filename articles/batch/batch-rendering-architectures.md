---
title: Architektury referencyjne renderowania platformy Azure — Azure Batch
description: Architektury służące do używania Azure Batch i innych usług platformy Azure w celu rozszerzania lokalnej farmy renderowania przez przebicie na chmurę
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: jushiman
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: a04f59983aca4b7db1a58ab4e8b8a2da47a52783
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026300"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architektury referencyjne dla renderowania na platformie Azure

W tym artykule przedstawiono diagramy architektury wysokiego poziomu na potrzeby scenariuszy rozszerzania lub "serii" lokalnej farmy renderowania na platformę Azure. Przykłady przedstawiają różne opcje usług obliczeniowych, sieciowych i magazynowych platformy Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrydowe z systemem plików NFS lub CFS

Na poniższym diagramie przedstawiono scenariusz hybrydowy, który obejmuje następujące usługi platformy Azure:

* **Obliczanie** Azure Batch puli lub zestawu skalowania maszyn wirtualnych.

* **Sieć** lokalna: Azure ExpressRoute lub sieci VPN. Azure: Sieć wirtualna platformy Azure.

* Pliki wejściowe i wyjściowe **magazynu** : system plików NFS lub CFS przy użyciu maszyn wirtualnych platformy Azure synchronizowany z magazynem lokalnym za pośrednictwem Azure File Sync lub RSync. Alternatywnie: avere vFXT do plików wejściowych lub wyjściowych z lokalnych urządzeń NAS przy użyciu systemu plików NFS.

  ![Rozrywanie w chmurze — hybrydowe z systemem plików NFS lub CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrydowe z Blobfuse

Na poniższym diagramie przedstawiono scenariusz hybrydowy, który obejmuje następujące usługi platformy Azure:

* **Obliczanie** Azure Batch puli lub zestawu skalowania maszyn wirtualnych.

* **Sieć** lokalna: Azure ExpressRoute lub sieci VPN. Azure: Sieć wirtualna platformy Azure.

* Pliki wejściowe i wyjściowe **magazynu** : usługa BLOB Storage, instalowana do zasobów obliczeniowych za pośrednictwem usługi Azure Blobfuse.

  ![Rozrywanie w chmurze — hybrydowe z Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrydowe zasoby obliczeniowe i magazynowe

Na poniższym diagramie przedstawiono w pełni połączony scenariusz hybrydowy zarówno w przypadku zasobów obliczeniowych, jak i magazynu, co obejmuje następujące usługi platformy Azure:

* **Obliczanie** Azure Batch puli lub zestawu skalowania maszyn wirtualnych.

* **Sieć** lokalna: Azure ExpressRoute lub sieci VPN. Azure: Sieć wirtualna platformy Azure.

* **Magazyn** — wiele lokalizacji: avere vFXT. Opcjonalna Archiwizacja plików lokalnych za pośrednictwem Azure Data Box do magazynu obiektów blob lub lokalnego avere FXT dla przyspieszenia serwera NAS.

  ![Przetwarzanie w chmurze — hybrydowe zasoby obliczeniowe i magazynowe](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o korzystaniu z [menedżerów renderowania](batch-rendering-render-managers.md) z Azure Batch.

* Dowiedz się więcej o opcjach [renderowania na platformie Azure](batch-rendering-service.md).