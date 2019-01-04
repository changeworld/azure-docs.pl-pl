---
title: Architektury referencyjne renderowania platformy Azure — usłudze Azure Batch
description: Architektury przy użyciu usługi Azure Batch i innymi usługami platformy Azure, rozszerzenie lokalnego renderować farmy za przenoszenie obsługi dużego ruchu do chmury
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543499"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architektury referencyjne platformy Azure renderowanie

W tym artykule przedstawiono architekturę wysokiego poziomu diagramy dotyczące scenariusze dotyczące rozszerzania lub "serii" lokalną renderowania farmy na platformie Azure. W przykładach przedstawiono różne opcje dla usług obliczeniowych, sieci i magazynu platformy Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrydowe za pomocą systemu plików NFS lub CFS

Na poniższym diagramie przedstawiono scenariusza hybrydowego, który zawiera następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Sieć wirtualna platformy Azure.

* **Magazynu** — danych wejściowych i wyjściowych plików: Systemu plików NFS lub CFS przy użyciu maszyn wirtualnych platformy Azure, są synchronizowane z magazynu lokalnego przy użyciu usługi Azure File Sync lub polecenia RSync.

  ![Rozszerzanie możliwości — chmury hybrydowej przy użyciu systemu plików NFS lub CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hybrydowe za pomocą Blobfuse

Na poniższym diagramie przedstawiono scenariusza hybrydowego, który zawiera następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Sieć wirtualna platformy Azure.

* **Magazynu** — danych wejściowych i wyjściowych plików: Magazyn obiektów blob, zainstalowany zasobów za pomocą usługi Azure Blobfuse obliczeniowych.

  ![Rozszerzanie możliwości — chmury hybrydowej przy użyciu Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrydowe zasobów obliczeniowych i magazynu

Poniższy diagram przedstawia scenariusza hybrydowego pełni połączone zarówno zasobów obliczeniowych i magazynu i obejmuje następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Sieć wirtualna platformy Azure.

* **Magazyn** — między środowiskami lokalnymi: Avere vFXT. Opcjonalne archiwizację lokalnych plików przy użyciu usługi Azure Data Box do usługi Blob storage.

  ![Rozszerzanie możliwości — chmury hybrydowej zasobów obliczeniowych i magazynu](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o korzystaniu z [renderowania menedżerów](batch-rendering-render-managers.md) przy użyciu usługi Azure Batch.

* Dowiedz się więcej o opcjach [renderowania na platformie Azure](batch-rendering-service.md).