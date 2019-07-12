---
title: Wybierz opcję migracji oprogramowania VMware za pomocą usługi Azure Migration migracji serwera | Dokumentacja firmy Microsoft
description: Zawiera omówienie opcji migrowania maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure Migration migracji serwera
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811572"
---
# <a name="select-a-vmware-migration-option"></a>Wybierz opcję migracji programu VMware

Można przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure za pomocą narzędzia do migracji serwera migracji platformy Azure. To narzędzie oferuje kilka opcji do migracji maszyn wirtualnych programu VMware:

- Migracja za pomocą replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania żadnych narzędzi na nich.
- Migracja z agenta replikacji. Zainstaluj agenta na maszynie Wirtualnej w celu replikacji.


Mimo że łatwiej z punktu widzenia wdrażania bez agentów replikacji obecnie ma kilka ograniczeń.

## <a name="agentless-migration-limitations"></a>Ograniczenia migracji bez wykorzystania agentów

Dostępne są następujące ograniczenia:

- **Jednoczesne replikacji**: Maksymalnie 50 maszyn wirtualnych mogą być replikowane równocześnie z programu vCenter Server.<br/> Jeśli masz więcej niż 50 VMs do migracji, należy utworzyć wiele instancji maszyn wirtualnych.<br/> Więcej replikowanych w jednym czasie będzie wpływać na wydajność.
- **Dyski maszyny Wirtualnej**: Maszynę wirtualną, którą chcesz migrować musi mieć dyski 60 lub mniej.
- **Systemy operacyjne maszyny Wirtualnej**: Ogólnie rzecz biorąc usługa Azure Migrate można migrować żadnych systemu Windows Server lub systemu operacyjnego Linux, ale mogą wymagać zmiany na maszynach wirtualnych, dzięki czemu mogą uruchamiać na platformie Azure. Usługa Azure Migrate sprawia, że zmiany automatycznie dla tych systemów operacyjnych:
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Dla innych systemów operacyjnych należy wprowadzić korekty ręcznie przed migracją. [Migracji samouczek](tutorial-migrate-vmware.md) wyjaśnia, jak to zrobić.
- **Rozruch systemu Linux**: Jeśli/Boot znajduje się na dedykowanym partycji, powinien znajdować się na dysku systemu operacyjnego, a nie być rozmieszczone na wielu dyskach.<br/> Jeśli/Boot jest częścią partycji głównej (/), następnie partycji "/" powinien znajdować się na dysku systemu operacyjnego, a nie obejmować inne dyski.
- **Rozruch z interfejsem UEFI**: Maszyny wirtualne z rozruchem z interfejsem UEFI nie są obsługiwane na potrzeby migracji.
- **Zaszyfrowanych dysków/woluminów (funkcji BitLocker, cryptfs)** : Maszyny wirtualne z zaszyfrowanych dysków/woluminów nie są obsługiwane na potrzeby migracji.
- **Dyski RDM/przekazujące**: Jeśli maszyny wirtualne dyski RDM lub atrybut passthrough, te dyski nie będą replikowane na platformie Azure
- **SYSTEMU PLIKÓW NFS**: Woluminy systemu plików NFS zainstalowany jako woluminy na maszynach wirtualnych nie będą replikowane.
- **Docelowy magazyn**: Można tylko migrowania maszyn wirtualnych VMware na maszyny wirtualne platformy Azure z dyskami zarządzanymi (HDD standardowa, Premium SSD).



## <a name="deployment-steps-comparison"></a>Porównanie kroki wdrażania

Po przejrzeniu ograniczenia opis kroków związanych z wdrażaniem każde z tych rozwiązań może pomóc zdecydować, którą opcję wybrać.

**Zadanie** | **Szczegóły** |**Bez wykorzystania agentów** | **Oparte na agentach**
--- | --- | --- | ---
**Przygotuj serwery VMware i maszyny wirtualne do migracji** | Na maszynach wirtualnych i serwerów VMware, należy skonfigurować kilka ustawień. | Wymagane | Wymagane
**Dodaj narzędzie do migracji serwera** | Dodaj narzędzie do migracji serwera migracji platformy Azure w projekcie usługi Azure Migrate. | Wymagane | Wymagane
**Wdrażanie urządzenia usługi Azure Migrate** | Skonfiguruj uproszczone urządzenie na maszynie Wirtualnej VMware dla maszyn wirtualnych, odnajdywania i oceny. | Wymagane | Nie jest wymagane.
**Instalowanie usługi mobilności na maszynach wirtualnych** | Instalowanie usługi mobilności na każdej maszynie Wirtualnej, którą chcesz replikować | Niewymagane | Wymagane
**Wdrażanie urządzenia replikacji migracji serwera migracji platformy Azure** | Konfigurowanie urządzenia na maszynie Wirtualnej VMware w celu odnalezienia maszyn wirtualnych i stanowić pomost pomiędzy usługi mobilności uruchomioną na maszynach wirtualnych oraz Migrowanie serwera migracji platformy Azure | Niewymagane | Wymagane
**Replikowanie maszyn wirtualnych**. Włączanie replikacji maszyny Wirtualnej. | Konfigurowanie ustawień replikacji, a następnie wybierz maszynę wirtualną do replikacji | Wymagane | Wymagane
**Uruchamianie migracji testowej** | Uruchamianie migracji testowej, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. | Wymagane | Wymagane
**Uruchamianie pełnej migracji** | Migrowanie maszyn wirtualnych. | Wymagane | Wymagane




## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych VMware](tutorial-migrate-vmware.md) przy użyciu funkcji migracji.



