---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471708"
---
W wersji zapoznawczej dyski zarządzane z włączonymi dyskami udostępnionymi podlegają następującym ograniczeniom:

- Obecnie dostępne tylko z najwyższej jakości ssdami.
- Obecnie obsługiwane tylko w regionie Środkowo-Zachodnie Stany Zjednoczone.
- Wszystkie maszyny wirtualne współużytkujące dysk muszą być wdrożone w tych [samych grupach miejsc docelowych zbliżeniowych](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Można włączyć tylko na dyskach z danymi, a nie na dyskach systemu operacyjnego.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [Wymagania sprzętowe klastrowania trybu failover i opcje magazynowania](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadOnly host buforowanie nie jest dostępna dla `maxShares>1`premium SSD z .
- Zestawy dostępności i zestawy skalowania maszyny `FaultDomainCount` wirtualnej mogą być używane tylko z zestawem 1.
- Pomoc techniczna usługi Azure Backup i Azure Site Recovery nie jest jeszcze dostępna.

Jeśli chcesz spróbować dysków udostępnionych, zarejestruj się w [naszej wersji zapoznawczej.](https://aka.ms/AzureSharedDiskPreviewSignUp)
