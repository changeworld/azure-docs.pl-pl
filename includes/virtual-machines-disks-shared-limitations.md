---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008358"
---
W wersji zapoznawczej włączenie dysków udostępnionych jest dostępne tylko dla podzbioru typów dysków. Obecnie tylko dyski ultra i dyski SSD premium mogą włączyć dyski współdzielone. Każdy dysk zarządzany z włączonymi dyskami udostępnionymi podlega następującym ograniczeniom, uporządkowanym według typu dysku:

### <a name="ultra-disks"></a>Dyski w warstwie Ultra

Dyski ultra mają własną oddzielną listę ograniczeń, niezwiązanych z dyskami współdzielonymi. Aby zapoznać się z ograniczeniami ultra [dysków,](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)zobacz Korzystanie z ultra dysków platformy Azure .

Podczas udostępniania dysków ultra mają następujące dodatkowe ograniczenia:

- Obecnie obsługiwane tylko w zachodnich stanach USA.
- Obecnie ograniczone do usługi Azure Resource Manager lub SDK pomocy technicznej.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [Wymagania sprzętowe klastrowania trybu failover i opcje magazynowania](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Dyski SSD w warstwie Premium

- Obecnie obsługiwane tylko w regionie Środkowo-Zachodnie Stany Zjednoczone.
- Wszystkie maszyny wirtualne współużytkujące dysk muszą być wdrożone w tych [samych grupach miejsc docelowych zbliżeniowych](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Można włączyć tylko na dyskach z danymi, a nie na dyskach systemu operacyjnego.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [Wymagania sprzętowe klastrowania trybu failover i opcje magazynowania](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadOnly host buforowanie nie jest dostępna dla `maxShares>1`premium SSD z .
- Zestawy dostępności i zestawy skalowania maszyny `FaultDomainCount` wirtualnej mogą być używane tylko z zestawem 1.
- Pomoc techniczna usługi Azure Backup i Azure Site Recovery nie jest jeszcze dostępna.

Jeśli chcesz spróbować dysków udostępnionych, zarejestruj się w [naszej wersji zapoznawczej.](https://aka.ms/AzureSharedDiskPreviewSignUp)
