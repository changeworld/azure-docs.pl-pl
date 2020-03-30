---
title: Wybierz opcję migracji VMware za pomocą migracji serwera migracji usługi Azure | Dokumenty firmy Microsoft
description: Zawiera omówienie opcji migracji maszyn wirtualnych VMware na platformę Azure za pomocą migracji serwera migracji usługi Azure Migrate Server
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028719"
---
# <a name="select-a-vmware-migration-option"></a>Wybieranie opcji migracji VMware

Maszyny wirtualne VMware można migrować na platformę Azure za pomocą narzędzia migracji serwera migracji platformy Azure. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez agenta. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich niczego.
- Migracja z agentem replikacji. Zainstaluj agenta na maszynie wirtualnej do replikacji.




## <a name="compare-migration-methods"></a>Porównaj metody migracji

Użyj tych wybranych porównań, aby ułatwić podjęcie decyzji, której metody użyć. Można również przejrzeć pełne wymagania dotyczące obsługi technicznej dla migracji [bez agenta](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) i [oparte na agentach.](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)

**Ustawienie** | **Bez agenta** | **Oparte na agentach**
--- | --- | ---
**Uprawnienia platformy Azure** | Potrzebujesz uprawnień do tworzenia projektu migracji platformy Azure i rejestrowania aplikacji usługi Azure AD utworzonych podczas wdrażania urządzenia migracji platformy Azure. | Potrzebujesz uprawnień współautora w ramach subskrypcji platformy Azure. 
**Replikacja jednoczesna** | Maksymalnie 100 maszyn wirtualnych można jednocześnie replikować z serwera vCenter.<br/> Jeśli masz więcej niż 50 maszyn wirtualnych do migracji, utwórz wiele partii maszyn wirtualnych.<br/> Replikowanie więcej w jednym czasie wpłynie na wydajność. | Nie dotyczy
**Wdrażanie urządzeń** | [Urządzenie migracji platformy Azure](migrate-appliance.md) jest wdrażane lokalnie. | [Urządzenie replikacji migracji platformy Azure](migrate-replication-appliance.md) jest wdrażane lokalnie.
**Zgodność z odzyskiwaniem witryny** | Zgodny. | Nie można replikować za pomocą migracji serwera migracji platformy Azure, jeśli została skonfigurowana replikacja dla komputera przy użyciu usługi Site Recovery.
**Dysk docelowy** | Dyski zarządzane | Dyski zarządzane
**Limity dysków** | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 4 TB<br/><br/> Maksymalna liczba dysków: 60 | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 8 TB<br/><br/> Maksymalna liczba dysków: 63
**Dyski przekazywania** | Nieobsługiwane | Obsługiwane
**Rozruch UEFI** | Nieobsługiwane | Zmigrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową systemu BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane za pomocą systemu plików NTFS.


## <a name="deployment-steps-comparison"></a>Porównanie kroków wdrażania

Po zapoznaniu się z ograniczeniami zrozumienie kroków związanych z wdrażaniem każdego rozwiązania może pomóc w podjęciu decyzji, którą opcję wybrać.

**Zadanie** | **Szczegóły** |**Bez agenta** | **Oparte na agentach**
--- | --- | --- | ---
**Ocena** | Oceń serwery przed migracją.  Ocena jest fakultatywna. Zalecamy ocenę maszyn przed ich migracją, ale nie musisz tego robić. <br/><br/> Do oceny usługa Azure Migrate konfiguruje lekkie urządzenie do odnajdywania i oceny maszyn wirtualnych. | Jeśli po zakończeniu oceny zostanie uruchomiony migracja bez agenta, ten sam urząd migracji platformy Azure skonfigurowany do oceny jest używany do migracji bez agenta.  |  Jeśli po zakończeniu oceny zostanie uruchomiony migracja oparta na agentach, urządzenie skonfigurowane do oceny nie będzie używane podczas migracji bez agenta. Możesz pozostawić urządzenie na swoim miejscu lub usunąć je, jeśli nie chcesz przeprowadzać dalszych odkryć i oceny.
**Przygotowywanie serwerów VMware i maszyn wirtualnych do migracji** | Skonfiguruj szereg ustawień na serwerach VMware i maszynach wirtualnych. | Wymagany | Wymagany
**Dodawanie narzędzia Migracja serwera** | Dodaj narzędzie migracji serwera migracji platformy Azure w projekcie migracji platformy Azure. | Wymagany | Wymagany
**Wdrażanie urządzenia migracji platformy Azure** | Skonfiguruj lekkie urządzenie na maszynie wirtualnej VMware do odnajdowania i oceny maszyn wirtualnych. | Wymagany | Niewymagane.
**Instalowanie usługi mobilności na maszynach wirtualnych** | Instalowanie usługi mobilności na każdej maszynie wirtualnej, którą chcesz replikować | Niewymagane | Wymagany
**Wdrażanie urządzenia replikacji migracji serwera migracji platformy Azure** | Konfigurowanie urządzenia na maszynie wirtualnej VMware w celu odnajdywanie maszyn wirtualnych i mostowanie między usługą mobilności działającą na maszynach wirtualnych a migracją serwera migracji usługi Azure Migrate Server | Niewymagane | Wymagany
**Replikowanie maszyn wirtualnych**. Włącz replikację maszyny Wirtualnej. | Konfigurowanie ustawień replikacji i wybieranie maszyn wirtualnych do replikacji | Wymagany | Wymagany
**Uruchamianie migracji testowej** | Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. | Wymagany | Wymagany
**Uruchamianie pełnej migracji** | Migrowanie maszyn wirtualnych. | Wymagany | Wymagany




## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych VMware](tutorial-migrate-vmware.md) z migracją bez agenta.



