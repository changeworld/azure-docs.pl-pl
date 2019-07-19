---
title: Architektury referencyjne renderowania platformy Azure — Azure Batch
description: Architektury służące do używania Azure Batch i innych usług platformy Azure w celu rozszerzania lokalnej farmy renderowania przez przebicie na chmurę
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: fa2d59b2a6d2dea72276ab38a5cb1ca7bfb579a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323108"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architektury referencyjne dla renderowania na platformie Azure

W tym artykule przedstawiono diagramy architektury wysokiego poziomu na potrzeby scenariuszy rozszerzania lub "serii" lokalnej farmy renderowania na platformę Azure. Przykłady przedstawiają różne opcje usług obliczeniowych, sieciowych i magazynowych platformy Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrydowe z systemem plików NFS lub CFS

Na poniższym diagramie przedstawiono scenariusz hybrydowy, który obejmuje następujące usługi platformy Azure:

* **Obliczanie** Azure Batch puli lub zestawu skalowania maszyn wirtualnych.

* **Sieć** lokalna: Azure ExpressRoute lub sieci VPN. Azure: Azure VNet.

* Pliki wejściowe i wyjściowe **magazynu** : System plików NFS lub CFS przy użyciu maszyn wirtualnych platformy Azure synchronizowany z magazynem lokalnym za pośrednictwem Azure File Sync lub RSync. Alternatywnie: Avere vFXT do plików wejściowych lub wyjściowych z lokalnych urządzeń NAS przy użyciu systemu plików NFS.

  ![Rozrywanie w chmurze — hybrydowe z systemem plików NFS lub CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrydowe z Blobfuse

Na poniższym diagramie przedstawiono scenariusz hybrydowy, który obejmuje następujące usługi platformy Azure:

* **Obliczanie** Azure Batch puli lub zestawu skalowania maszyn wirtualnych.

* **Sieć** lokalna: Azure ExpressRoute lub sieci VPN. Azure: Azure VNet.

* Pliki wejściowe i wyjściowe **magazynu** : Usługa BLOB Storage zainstalowana do zasobów obliczeniowych za pośrednictwem usługi Azure Blobfuse.

  ![Rozrywanie w chmurze — hybrydowe z Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrydowe zasoby obliczeniowe i magazynowe

Na poniższym diagramie przedstawiono w pełni połączony scenariusz hybrydowy zarówno w przypadku zasobów obliczeniowych, jak i magazynu, co obejmuje następujące usługi platformy Azure:

* **Obliczanie** Azure Batch puli lub zestawu skalowania maszyn wirtualnych.

* **Sieć** lokalna: Azure ExpressRoute lub sieci VPN. Azure: Azure VNet.

* **Magazyn** — wiele lokalizacji: Avere vFXT. Opcjonalna Archiwizacja plików lokalnych za pośrednictwem Azure Data Box do magazynu obiektów blob lub lokalnego avere FXT dla przyspieszenia serwera NAS.

  ![Przetwarzanie w chmurze — hybrydowe zasoby obliczeniowe i magazynowe](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o korzystaniu z [menedżerów renderowania](batch-rendering-render-managers.md) z Azure Batch.

* Dowiedz się więcej o opcjach [renderowania na platformie Azure](batch-rendering-service.md).