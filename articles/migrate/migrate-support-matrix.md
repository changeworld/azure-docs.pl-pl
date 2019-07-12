---
title: Macierz obsługi usługi Azure Migrate
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczenia dotyczące usługi Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811559"
---
# <a name="azure-migrate-support-matrix"></a>Macierz obsługi usługi Azure Migrate

Możesz użyć [usługi Azure Migrate](migrate-overview.md) ocenianie i Migrowanie maszyn do chmury Microsoft Azure. Ten artykuł zawiera podsumowanie ustawień ogólną pomoc techniczną i ograniczenia dotyczące usługi Azure Migrate scenariuszy i wdrożenia.


## <a name="azure-migrate-versions"></a>Usługa Azure Migrate wersji

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Przy użyciu tej wersji można tworzyć nowe projekty usługi Azure Migrate, Dowiedz się, w środowisku lokalnym ocenia i organizowanie oceny i migracji. [Dowiedz się więcej](whats-new.md#azure-migrate-new-version).
- **Poprzednia wersja**: Dla klientów przy użyciu poprzedniej wersji usługi Azure Migrate (obsługiwał tylko ocena lokalnych maszyn wirtualnych programu VMware) teraz należy używać bieżącej wersji. W poprzedniej wersji nie można tworzyć nowe projekty usługi Azure Migrate lub wykonywać nowych operacji odnajdywania.

## <a name="supported-migration-scenarios"></a>Obsługiwane scenariusze migracji

Tabela zawiera podsumowanie obsługiwanych scenariuszy migracji.

**Wdrożenie** | **Szczegółowe informacje*** 
--- | --- 
**Ocena w środowisku lokalnym** | Ocena obciążeń lokalnych i danych uruchomiony na maszynach wirtualnych VMware i maszyny wirtualne funkcji Hyper-V. Oceń, używając oceny Server migracji platformy Azure i Microsoft Data Migration Assistant (DMA) oraz narzędzi innych firm, obejmujących Cloudamize, Corent Tech i Turbonomic serwera.
**Migracja lokalnych na platformę Azure** | Przeprowadź migrację obciążeń oraz danych, uruchomione na serwerach fizycznych, maszyn wirtualnych VMware, maszyny wirtualne funkcji Hyper-V, a na wystąpień usługi AWS/GCP, na platformie Azure. Migrowanie za pomocą oceny Server migracji platformy Azure i usługi Azure Database Migration Service (DMS), a także przy użyciu narzędzi innych firm, które obejmują Carbonite i CorentTech.

Poniżej przedstawiono streszczenie określonego narzędzia pomocy technicznej.

**Narzędzie** | **Ocena/migracja** | **Szczegóły**
--- | --- | ---
Usługa Azure Migrate oceny Server | Ocena | Wypróbuj server assessment dla [funkcji Hyper-V](tutorial-prepare-hyper-v.md) i [VMware](tutorial-prepare-vmware.md).
Cloudamize | Ocena | [Dowiedz się więcej](https://www.cloudamize.com/platform#tab-0).
CorentTech | Ocena | [Dowiedz się więcej](https://www.corenttech.com/).
Turbonomic | Ocena | [Dowiedz się więcej](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure Migrate Server Migration | Migracja | Wypróbuj migracji serwera dla [funkcji Hyper-V](tutorial-migrate-hyper-v.md) i [VMware](tutorial-migrate-vmware.md).
Carbonite | Migracja | [Dowiedz się więcej](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migracja | [Dowiedz się więcej](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projekty migracji platformy Azure

**Pomoc techniczna** | **Szczegóły**
--- | ---
Subscription | Do tego pojedynczego projektu Azure Migrate może mieć w ramach subskrypcji.
Uprawnienia platformy Azure | Musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji, aby utworzyć projekt usługi Azure Migrate.
Maszyny wirtualne VMware  | Oceń maksymalnie 35 000 maszyn wirtualnych programu VMware w jednym projekcie.
Maszyny wirtualne funkcji Hyper-V | Oceń maksymalnie 10 000 maszyn wirtualnych funkcji Hyper-V na platformie pojedynczego projektu.

Projekt może zawierać zarówno maszyny wirtualne VMware i maszyn wirtualnych funkcji Hyper-V, w granicach oceny.


## <a name="vmware-assessment-and-migration"></a>Ocena programu VMware i migracji

[Przegląd](migrate-support-matrix-vmware.md) Azure migracji serwera oceny i migracji serwera macierz obsługi dla maszyn wirtualnych VMware.

## <a name="hyper-v-assessment-and-migration"></a>Ocena funkcji Hyper-V i migracja

[Przegląd](migrate-support-matrix-hyper-v.md) Azure migracji serwera oceny i migracji serwera macierz obsługi dla maszyn wirtualnych funkcji Hyper-V.


## <a name="next-steps"></a>Następne kroki

- [Ocenianie maszyn wirtualnych VMware](tutorial-assess-vmware.md) do migracji.
- [Ocenianie maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md) do migracji.

