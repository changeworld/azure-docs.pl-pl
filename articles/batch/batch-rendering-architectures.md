---
title: Renderowanie platformy Azure — architektury referencyjne
description: Architektury przy użyciu usługi Azure Batch i innymi usługami platformy Azure, rozszerzenie lokalnego renderować farmy za przenoszenie obsługi dużego ruchu do chmury
services: batch
author: davefellows
manager: jeconnoc
ms.author: danlep
ms.date: 08/13/2018
ms.topic: conceptual
ms.openlocfilehash: 0fe101ee6eb88094034b90c4d39f06ba509c9512
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099895"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architektury referencyjne platformy Azure renderowanie

W tym artykule przedstawiono architekturę wysokiego poziomu diagramy dotyczące scenariusze dotyczące rozszerzania lub "serii" lokalną renderowania farmy na platformie Azure. W przykładach przedstawiono różne opcje dla usług obliczeniowych, sieci i magazynu platformy Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrydowe za pomocą systemu plików NFS lub CFS

Na poniższym diagramie przedstawiono scenariusza hybrydowego, który zawiera następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Azure sieci wirtualnej.

* **Magazyn** — wejściowe i pliki wyjściowe: NFS lub CFS przy użyciu maszyn wirtualnych platformy Azure, synchronizowane z magazynu lokalnego przy użyciu usługi Azure File Sync lub polecenia RSync.

  ![Rozszerzanie możliwości — chmury hybrydowej przy użyciu systemu plików NFS lub CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hybrydowe za pomocą Blobfuse

Na poniższym diagramie przedstawiono scenariusza hybrydowego, który zawiera następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Azure sieci wirtualnej.

* **Magazynu** — danych wejściowych i wyjściowych plików: zainstalowanego zasobów za pomocą usługi Azure Blobfuse obliczeniowych magazynu usługi Blob.

  ![Rozszerzanie możliwości — chmury hybrydowej przy użyciu Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrydowe zasobów obliczeniowych i magazynu

Poniższy diagram przedstawia scenariusza hybrydowego pełni połączone zarówno zasobów obliczeniowych i magazynu i obejmuje następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Azure sieci wirtualnej.

* **Magazyn** — między środowiskami lokalnymi: Avere vFXT. Opcjonalne archiwizację lokalnych plików przy użyciu usługi Azure Data Box do usługi Blob storage.

  ![Rozszerzanie możliwości — chmury hybrydowej zasobów obliczeniowych i magazynu](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o korzystaniu z [renderowania menedżerów](batch-rendering-render-managers.md) przy użyciu usługi Azure Batch.

* Dowiedz się więcej o opcjach [renderowania na platformie Azure](batch-rendering-service.md).