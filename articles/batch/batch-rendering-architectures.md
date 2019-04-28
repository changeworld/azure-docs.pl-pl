---
title: Architektury referencyjne renderowania platformy Azure — usłudze Azure Batch
description: Architektury przy użyciu usługi Azure Batch i innymi usługami platformy Azure, rozszerzenie lokalnego renderować farmy za przenoszenie obsługi dużego ruchu do chmury
services: batch
ms.service: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: ae4680c948ce8e1efd32207dc37821d61182f2d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774191"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architektury referencyjne platformy Azure renderowanie

W tym artykule przedstawiono architekturę wysokiego poziomu diagramy dotyczące scenariusze dotyczące rozszerzania lub "serii" lokalną renderowania farmy na platformie Azure. W przykładach przedstawiono różne opcje dla usług obliczeniowych, sieci i magazynu platformy Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrydowe za pomocą systemu plików NFS lub CFS

Na poniższym diagramie przedstawiono scenariusza hybrydowego, który zawiera następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Azure VNet.

* **Magazynu** — danych wejściowych i wyjściowych plików: Systemu plików NFS lub CFS przy użyciu maszyn wirtualnych platformy Azure, są synchronizowane z magazynu lokalnego przy użyciu usługi Azure File Sync lub polecenia RSync. Alternatywnie: VFXT Avere jako danych wejściowych lub wyjściowych plików z urządzeń NAS w środowisku lokalnym przy użyciu systemu plików NFS.

  ![Rozszerzanie możliwości — chmury hybrydowej przy użyciu systemu plików NFS lub CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrydowe za pomocą Blobfuse

Na poniższym diagramie przedstawiono scenariusza hybrydowego, który zawiera następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Azure VNet.

* **Magazynu** — danych wejściowych i wyjściowych plików: Magazyn obiektów blob, zainstalowany zasobów za pomocą usługi Azure Blobfuse obliczeniowych.

  ![Rozszerzanie możliwości — chmury hybrydowej przy użyciu Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrydowe zasobów obliczeniowych i magazynu

Poniższy diagram przedstawia scenariusza hybrydowego pełni połączone zarówno zasobów obliczeniowych i magazynu i obejmuje następujących usług platformy Azure:

* **Obliczenia** -puli Azure Batch lub zestawu skalowania maszyn wirtualnych.

* **Sieć** -On-premises: Usługa Azure ExpressRoute lub sieci VPN. Azure: Azure VNet.

* **Magazyn** — między środowiskami lokalnymi: Avere vFXT. Opcjonalne archiwizacji lokalnych plików za pomocą usługi Azure Data Box do usługi Blob storage lub on-premises Avere FXT dla NAS przyspieszenia.

  ![Rozszerzanie możliwości — chmury hybrydowej zasobów obliczeniowych i magazynu](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o korzystaniu z [renderowania menedżerów](batch-rendering-render-managers.md) przy użyciu usługi Azure Batch.

* Dowiedz się więcej o opcjach [renderowania na platformie Azure](batch-rendering-service.md).