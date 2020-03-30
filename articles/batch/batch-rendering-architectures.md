---
title: Architektury referencyjne renderowania platformy Azure — usługa Azure Batch
description: Architektury do korzystania z usługi Azure Batch i innych usług platformy Azure w celu rozszerzenia lokalnej farmy renderowania przez rozerwanie do chmury
services: batch
ms.service: batch
author: davefellows
manager: evansma
ms.author: labrenne
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: 20442a6618ca9357bb3be95879b68bffca45a40d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022957"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architektury referencyjne dla renderowania platformy Azure

W tym artykule przedstawiono diagramy architektury wysokiego poziomu dla scenariuszy, aby rozszerzyć lub "burst", lokalnej farmy renderowania na platformie Azure. W przykładach pokazano różne opcje dla usług obliczeniowych platformy Azure, sieci i magazynu.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybryda z NFS lub CFS

Na poniższym diagramie przedstawiono scenariusz hybrydowy, który zawiera następujące usługi platformy Azure:

* **Oblicz —** pula partii platformy Azure lub zestaw skalowania maszyny wirtualnej.

* **Sieć** — lokalnie: Azure ExpressRoute lub VPN. Azure: Azure VNet.

* **Magazyn** — pliki wejściowe i wyjściowe: NFS lub CFS przy użyciu maszyn wirtualnych platformy Azure, zsynchronizowanych z magazynem lokalnym za pośrednictwem usługi Azure File Sync lub RSync. Alternatywnie: Avere vFXT do plików wejściowych lub wyjściowych z lokalnych urządzeń NAS przy użyciu nfs.

  ![Chmura pękająca - Hybryda z NFS lub CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybryda z bluzką

Na poniższym diagramie przedstawiono scenariusz hybrydowy, który zawiera następujące usługi platformy Azure:

* **Oblicz —** pula partii platformy Azure lub zestaw skalowania maszyny wirtualnej.

* **Sieć** — lokalnie: Azure ExpressRoute lub VPN. Azure: Azure VNet.

* **Magazyn** — pliki wejściowe i wyjściowe: magazyn obiektów Blob, zainstalowany do zasobów obliczeniowych za pośrednictwem narzędzia Azure Blobfuse.

  ![Chmura pękająca - Hybryda z bluzką](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrydowe przetwarzanie danych i pamięć masowa

Na poniższym diagramie przedstawiono w pełni połączony scenariusz hybrydowy zarówno dla obliczeń, jak i magazynu i zawiera następujące usługi platformy Azure:

* **Oblicz —** pula partii platformy Azure lub zestaw skalowania maszyny wirtualnej.

* **Sieć** — lokalnie: Azure ExpressRoute lub VPN. Azure: Azure VNet.

* **Magazyn** — międzylokacje: Avere vFXT. Opcjonalna archiwizacja plików lokalnych za pośrednictwem usługi Azure Data Box do magazynu obiektów Blob lub lokalnego programu Avere FXT w celu przyspieszenia serwera NAS.

  ![Chmura pęknięcie - hybrydowe obliczeń i pamięci masowej](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [używaniu menedżerów renderowania](batch-rendering-render-managers.md) za pomocą usługi Azure Batch.

* Dowiedz się więcej o [opcjach renderowania](batch-rendering-service.md)na platformie Azure .