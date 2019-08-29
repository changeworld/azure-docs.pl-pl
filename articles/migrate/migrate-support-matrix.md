---
title: Macierz obsługi Azure Migrate
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej dla usługi Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: raynew
ms.openlocfilehash: 99ef5c8a4a4291dd1b9d047da9006623dacd0b53
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147878"
---
# <a name="azure-migrate-support-matrix"></a>Macierz obsługi Azure Migrate

Za pomocą [usługi Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. W tym artykule zestawiono ogólne ustawienia i ograniczenia dotyczące Azure Migrate scenariuszy i wdrożeń.


## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Korzystając z tej wersji, można tworzyć nowe projekty Azure Migrate, wykrywać, oceniać lokalne oraz organizować oceny i migracje. [Dowiedz się więcej](whats-new.md#azure-migrate-new-version).
- **Poprzednia wersja**: W przypadku klientów korzystających z poprzedniej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware). teraz należy używać bieżącej wersji. W poprzedniej wersji nie można tworzyć nowych projektów Azure Migrate ani wykonywać nowych odkrycia.

## <a name="supported-migration-scenarios"></a>Obsługiwane scenariusze migracji

W tabeli zestawiono obsługiwane scenariusze migracji.

**Wdrożenie** | **Szczegóły** 
--- | --- 
**Ocena lokalna** | Oceniaj obciążenia lokalne i dane uruchomione na maszynach wirtualnych VMware i maszynach wirtualnych funkcji Hyper-V. Oceń przy użyciu Azure Migrate oceny i Microsoft Data Migration Assistant (DMA), a także narzędzi innych firm, które obejmują Cloudamize, telenajmu i platforma turbonomic Server.
**Migracja lokalna na platformę Azure** | Migrowanie obciążeń i danych działających na serwerach fizycznych, maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper-V, serwerach fizycznych i maszynach wirtualnych opartych na chmurze na platformie Azure. Migruj przy użyciu narzędzia do oceny i Azure Database Migration Service Azure Migrate Server (DMS) oraz narzędzi innych firm, które zawierają Carbonite i CorentTech.

Pomoc dotycząca określonego narzędzia jest podsumowana w następujący sposób.

**Narzędzie** | **Ocena/migracja** | **Szczegóły**
--- | --- | ---
Azure Migrate oceny serwera | Ocena | Wypróbuj ocenę serwera dla [funkcji Hyper-V](tutorial-prepare-hyper-v.md) i programu [VMware](tutorial-prepare-vmware.md).
Cloudamize | Ocena | [Dowiedz się więcej](https://www.cloudamize.com/platform#tab-0).
CorentTech | Ocena | [Dowiedz się więcej](https://www.corenttech.com/).
Turbonomic | Ocena | [Dowiedz się więcej](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migracja serwera Azure Migrate | Migracja | Wypróbuj migrację serwera dla [funkcji Hyper-V](tutorial-migrate-hyper-v.md) i programu [VMware](tutorial-migrate-vmware.md).
Carbonite | Migracja | [Dowiedz się więcej](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migracja | [Dowiedz się więcej](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Pomoc techniczna** | **Szczegóły**
--- | ---
Subscription | W ramach subskrypcji można mieć wiele projektów Azure Migrate.
Uprawnienia platformy Azure | Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.
Maszyny wirtualne VMware  | Oceń do 35 000 maszyn wirtualnych VMware w jednym projekcie.
Maszyny wirtualne funkcji Hyper-V | Oceń do 10 000 maszyn wirtualnych funkcji Hyper-V w jednym projekcie.

Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.


## <a name="vmware-assessment-and-migration"></a>Ocena i migracja oprogramowania VMware

[Zapoznaj](migrate-support-matrix-vmware.md) się z macierzą obsługi Azure Migrate oceny serwera i migracji serwera dla maszyn wirtualnych VMware.

## <a name="hyper-v-assessment-and-migration"></a>Ocena i migracja funkcji Hyper-V

[Zapoznaj](migrate-support-matrix-hyper-v.md) się z macierzą obsługi Azure Migrate oceny serwera i migracji serwera dla maszyn wirtualnych funkcji Hyper-V.


## <a name="next-steps"></a>Następne kroki

- [Ocenianie maszyn wirtualnych VMware](tutorial-assess-vmware.md) do migracji.
- [Oceń maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md) do migracji.

