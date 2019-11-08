---
title: Macierz obsługi Azure Migrate
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej dla usługi Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: e752d52d0f792d39f417031c26d923fce07ccdf8
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747981"
---
# <a name="azure-migrate-support-matrix"></a>Macierz obsługi Azure Migrate

Za pomocą [usługi Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. W tym artykule zestawiono ogólne ustawienia i ograniczenia dotyczące Azure Migrate scenariuszy i wdrożeń.


## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: korzystając z tej wersji, można tworzyć nowe projekty Azure Migrate, odkrywać, oceniać lokalne oraz organizować oceny i migracje. [Dowiedz się więcej](whats-new.md#release-version-july-2019).
- **Poprzednia wersja**: dla klienta korzystającego z poprzedniej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware). teraz należy używać bieżącej wersji. W poprzedniej wersji nie można tworzyć nowych projektów Azure Migrate ani wykonywać nowych odkrycia.

## <a name="supported-assessmentmigration-scenarios"></a>Obsługiwane scenariusze oceny/migracji

W tabeli zestawiono obsługiwane scenariusze odnajdywania, oceny i migracji.

**Wdrożenie** | **Szczegóły** 
--- | --- 
**Odnajdywanie specyficzne dla aplikacji** | Możesz wykrywać aplikacje, role i funkcje działające na maszynach wirtualnych VMware. Obecnie ta funkcja jest ograniczona tylko do odnajdowania. Ocena jest obecnie na poziomie komputera. Nie oferujemy jeszcze oceny aplikacji, roli lub funkcji. 
**Ocena lokalna** | Oceniaj obciążenia lokalne i dane uruchomione na maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper-V i serwerach fizycznych. Oceń przy użyciu Azure Migrate oceny i Microsoft Data Migration Assistant (DMA), a także inne narzędzia i oferty niezależnych dostawców oprogramowania.
**Migracja lokalna na platformę Azure** | Migrowanie obciążeń i danych działających na serwerach fizycznych, maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper-V, serwerach fizycznych i maszynach wirtualnych opartych na chmurze na platformie Azure. Migruj przy użyciu Azure Migrate oceny i Azure Database Migration Service (DMS), a także innych narzędzi i ofert niezależnych dostawców oprogramowania.


## <a name="supported-tools"></a>Obsługiwane narzędzia

W tabeli przedstawiono obsługę określonego narzędzia.

**Narzędzie** | **Szacowaniu** | **Migrate (Migracja)** 
--- | --- | ---
Azure Migrate oceny serwera | Oceniaj [maszyny wirtualne programu VMware](tutorial-prepare-vmware.md), [maszyny wirtualne funkcji Hyper-V](tutorial-prepare-hyper-v.md)i [serwery fizyczne](tutorial-prepare-physical.md). |  Niedostępne (NA)
Migracja serwera Azure Migrate | Nie dotyczy | Migrowanie [maszyn wirtualnych VMware](tutorial-migrate-vmware.md), [maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md)i [serwerów fizycznych](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Nie dotyczy | Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, obciążeń chmury publicznej. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, obciążenia chmury publicznej. | Nie dotyczy
[Technologia najmu](https://go.microsoft.com/fwlink/?linkid=2084928) | Ocenianie i migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, obciążeń chmury publicznej. |  Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, obciążeń chmury publicznej.
[Urządzenie 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, obciążenia chmury publicznej.| Nie dotyczy
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Oceniaj lokalne bazy danych SQL Server. | Nie dotyczy
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | Nie dotyczy | Migrowanie SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocenianie infrastruktury pulpitu wirtualnego (VDI) | Nie dotyczy
[Przeniesienie](https://go.microsoft.com/fwlink/?linkid=2109528) | Oceniaj maszyny wirtualne VMWare, maszyny wirtualne funkcji Hyper-V, maszyny wirtualne Xen, maszyny fizyczne, stacje robocze (w tym infrastruktury VDI), obciążenia chmury publicznej | Nie dotyczy
[Stojaki](https://go.microsoft.com/fwlink/?linkid=2102735) | Nie dotyczy | Migrowanie maszyn wirtualnych VMWare, maszyn wirtualnych funkcji Hyper-V, maszyn wirtualnych Xen, maszyn wirtualnych KVM, komputerów fizycznych, obciążeń chmury publicznej 
[Platforma turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, obciążenia chmury publicznej. | Nie dotyczy
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, obciążenia chmury publicznej i bazy danych SQL Server. | Nie dotyczy
[Webapp Asystent migracji](https://appmigration.microsoft.com/) | Ocenianie aplikacji sieci Web | Migrowanie aplikacji sieci Web.


## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Pomoc techniczna** | **Szczegóły**
--- | ---
Subskrypcja | W ramach subskrypcji można mieć wiele projektów Azure Migrate.
Uprawnienia platformy Azure | Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.
Maszyny wirtualne VMware  | Oceń do 35 000 maszyn wirtualnych VMware w jednym projekcie.
Maszyny wirtualne funkcji Hyper-V | Oceń do 35 000 maszyn wirtualnych funkcji Hyper-V w jednym projekcie.

Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.

## <a name="supported-geographies"></a>Obsługiwane lokalizacje geograficzne

Projekt Azure Migrate można utworzyć w wielu lokalizacje geograficzneach. Chociaż można tworzyć tylko projekty w tych lokalizacje geograficzne, można ocenić lub migrować maszyny dla innych lokalizacji docelowych. Lokalizacja geograficzna projektu służy tylko do przechowywania odnalezionych metadanych.

**Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
--- | ---
Azure Government | Administracja USA — Wirginia
Azja i Pacyfik | Azja Wschodnia lub Azja Południowo-Wschodnia
Australia | Australia Wschodnia lub Australia Południowo-Wschodnia
Brazylia | Brazylia Południowa
Kanada | Kanada środkowa lub Kanada Wschodnia
Europa | Europa Północna lub Europa Zachodnia
Francja | Francja Środkowa
Indie | Indie Środkowe lub Indie Południowe
Japonia |  Japonia Wschodnia lub Japonia Zachodnia
Korea | Korea środkowa lub Korea Południowa
Wielka Brytania | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2


 > [!NOTE]
 > Obsługa Azure Government jest obecnie dostępna tylko dla [starszej wersji](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) programu Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>Ocena i migracja oprogramowania VMware

[Zapoznaj](migrate-support-matrix-vmware.md) się z macierzą obsługi Azure Migrate oceny serwera i migracji serwera dla maszyn wirtualnych VMware.

## <a name="hyper-v-assessment-and-migration"></a>Ocena i migracja funkcji Hyper-V

[Zapoznaj](migrate-support-matrix-hyper-v.md) się z macierzą obsługi Azure Migrate oceny serwera i migracji serwera dla maszyn wirtualnych funkcji Hyper-V.


## <a name="next-steps"></a>Następne kroki

- [Ocenianie maszyn wirtualnych VMware](tutorial-assess-vmware.md) do migracji.
- [Oceń maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md) do migracji.

