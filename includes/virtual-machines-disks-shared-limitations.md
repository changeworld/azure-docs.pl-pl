---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202218"
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

Jeśli chcesz próbować korzystać z dysków udostępnionych, [Utwórz konto w wersji zapoznawczej](https://aka.ms/shareddisksignup).
