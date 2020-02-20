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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471708"
---
W wersji zapoznawczej dyski zarządzane z włączonymi dyskami udostępnionymi podlegają następującym ograniczeniom:

- Obecnie dostępna tylko w wersji Premium dysków SSD.
- Obecnie obsługiwane tylko w regionie zachodnie stany USA.
- Wszystkie maszyny wirtualne, które udostępniają dysk, muszą być wdrożone w tych samych [grupach umieszczania sąsiedztwa](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Można ją włączyć tylko na dyskach danych, a nie na dyskach systemu operacyjnego.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Buforowanie hosta tylko do odczytu nie jest dostępne dla dysków SSD Premium z `maxShares>1`.
- Zestawy dostępności i zestawy skalowania maszyn wirtualnych mogą być używane tylko z `FaultDomainCount` ustawionym na 1.
- Obsługa Azure Backup i Azure Site Recovery nie jest jeszcze dostępna.

Jeśli chcesz próbować korzystać z dysków udostępnionych, [Utwórz konto w wersji zapoznawczej](https://aka.ms/AzureSharedDiskPreviewSignUp).
