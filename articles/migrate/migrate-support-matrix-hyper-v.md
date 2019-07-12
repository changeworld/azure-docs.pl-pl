---
title: Macierz obsługi migracji usługi Azure na potrzeby oceny funkcji Hyper-V i migracja
description: Zawiera podsumowanie ustawień i ograniczenia dotyczące funkcji Hyper-V, oceny i migracji przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811351"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Macierz obsługi dla funkcji Hyper-V, oceny i migracji

Możesz użyć [usługi Azure Migrate](migrate-overview.md) ocenianie i Migrowanie maszyn do chmury Microsoft Azure. Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia dotyczące oceny i migracji maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym.



## <a name="hyper-v-scenarios"></a>Scenariusze funkcji Hyper-V

Tabela zawiera podsumowanie obsługiwanych scenariuszy dla maszyn wirtualnych funkcji Hyper-V.

**Wdrożenie** | **Szczegółowe informacje*** 
--- | --- 
**Ocenianie maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym** | [Konfigurowanie](tutorial-prepare-hyper-v.md) przy pierwszej ocenie.<br/><br/> [Uruchom](scale-hyper-v-assessment.md) oceny na dużą skalę.
**Migrowanie maszyn wirtualnych funkcji Hyper-V do platformy Azure** | [Wypróbuj](tutorial-migrate-hyper-v.md) migracji na platformę Azure.

    

## <a name="azure-migrate-projects"></a>Projekty migracji platformy Azure

**Pomoc techniczna** | **Szczegóły**
--- | ---
Uprawnienia platformy Azure | Musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji, aby utworzyć projekt usługi Azure Migrate.
Maszyny wirtualne funkcji Hyper-V | Oceń maksymalnie 10 000 maszyn wirtualnych funkcji Hyper-V na platformie pojedynczego projektu.

Projekt może zawierać zarówno maszyny wirtualne VMware i maszyn wirtualnych funkcji Hyper-V, w granicach oceny.


## <a name="assessment-hyper-v-host-requirements"></a>Wymagania dotyczące hosta oceny — funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrażanie hosta**       | Host funkcji Hyper-V może być autonomiczny lub wdrożyć w klastrze. |
| **Uprawnienia**           | Musisz mieć uprawnienia administratora na hoście funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2016 lub Windows Server 2012 R2.<br/> Nie można ocenić maszyn wirtualnych znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2019. |
| **Komunikacja zdalna programu PowerShell**   | Musi być włączona na każdym hoście. |
| **Funkcji Hyper-V Replica**       | Jeśli używasz funkcji Hyper-V Replica (lub ma wiele maszyn wirtualnych przy użyciu tych samych identyfikatorów maszyn wirtualnych) i Odkryj obie oryginalnego i replikowane maszyny wirtualne przy użyciu usługi Azure Migrate, oceny generowane przez usługę Azure Migrate może być niedokładna. |


## <a name="assessment-hyper-v-vm-requirements"></a>Wymagania dotyczące oceny — funkcji Hyper-V maszyny Wirtualnej

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) systemów operacyjnych obsługiwanych przez platformę Azure. |
| **Uprawnienia**           | Musisz mieć uprawnienia administratora na każdej maszynie Wirtualnej funkcji Hyper-V, którą chcesz ocenić. |
| **Usługi integracji**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) musi być uruchomiony na maszynach wirtualnych, które należy ocenić, aby można było przechwytywać informacje dotyczące systemu operacyjnego. |
| **Wymagane zmiany na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, dzięki czemu mogą uruchamiać na platformie Azure. Usługa Azure Migrate sprawia, że te zmiany automatycznie dla następujących systemów operacyjnych:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> — 7,8 debian<br/><br/> Dla innych systemów operacyjnych należy wprowadzić korekty ręcznie przed migracją. Odpowiednie artykuły zawierają instrukcje dotyczące sposobu wykonania tego zadania. |
| **Rozruch systemu Linux**                 | Jeśli/Boot znajduje się na dedykowanym partycji, powinien znajdować się na dysku systemu operacyjnego, a nie być rozmieszczone na wielu dyskach.<br/> Jeśli/Boot jest częścią partycji głównej (/), następnie partycji "/" powinien znajdować się na dysku systemu operacyjnego, a nie obejmować inne dyski. |
| **Rozruch z interfejsem UEFI**                  | Maszyny wirtualne z rozruchem z interfejsem UEFI nie są obsługiwane na potrzeby migracji. |
| **Zaszyfrowanych dysków/woluminów**    | Maszyny wirtualne z zaszyfrowanych dysków/woluminów nie są obsługiwane na potrzeby migracji. |
| **Dyski RDM/przekazujące**      | Jeśli maszyny wirtualne dyski RDM lub atrybut passthrough, te dyski nie będą replikowane na platformie Azure. |
| **NFS**                        | Woluminy systemu plików NFS zainstalowany jako woluminy na maszynach wirtualnych nie będą replikowane. |
| **Dysk docelowy**                | Oceny usługi Azure Migrate zaleca się migrację do maszyn wirtualnych platformy Azure tylko w przypadku zarządzanych dysków. |


## <a name="assessment-appliance-requirements"></a>Wymagania dotyczące oceny urządzenia

Ocena usługi Azure Migrate uruchamiane uproszczone urządzenia do odnalezienia maszyn wirtualnych funkcji Hyper-V i wysyłać dane wydajności i metadanych maszyny Wirtualnej do usługi Azure Migrate. Urządzenie jest uruchamiane na maszynie Wirtualnej funkcji Hyper-V, a następnie skonfigurować przy użyciu skompresowany funkcji Hyper-V wirtualnego dysku twardego, możesz pobrać z witryny Azure portal. Poniższa tabela zawiera podsumowanie wymagań urządzenia.

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Projekt migracji platformy Azure**  |  Urządzenie może być skojarzony z jednym projektem.<br/> Można odnajdywać maksymalnie 5000 maszyn wirtualnych funkcji Hyper-V przy użyciu pojedynczego urządzenia.
| **Ograniczenia dotyczące funkcji Hyper-V**    |  Urządzenie możesz wdrożyć jako Maszynę wirtualną funkcji Hyper-V.<br/> Urządzenie maszyn wirtualnych pod warunkiem jest maszyna wirtualna funkcji Hyper-V w wersji 5.0.<br/> Host maszyny Wirtualnej musi działać system Windows Server 2012 R2 lub nowszym.<br/> Potrzebuje wystarczającej ilości miejsca, aby przydzielić 16 GB pamięci RAM, 4 procesory wirtualne, a 1 zewnętrznego przełącznika dla urządzenia maszyny Wirtualnej.<br/> Urządzenie wymaga statyczny lub dynamiczny adres IP, a dostęp do Internetu.
| **Urządzenie w funkcji Hyper-V**      |  Urządzenie jest konfigurowany jako Maszynę wirtualną funkcji Hyper-V.<br/> Wirtualny dysk twardy, udostępniane do pobrania jest maszyna wirtualna funkcji Hyper-V w wersji 5.0.
| **Host**                   | Host maszyny Wirtualnej, systemem urządzenia maszyny Wirtualnej musi działać system Windows Server 2012 R2 lub nowszym.<br/> Musi ona wystarczająco dużo miejsca do przydzielenia 16 GB pamięci RAM, 4 procesory wirtualne i jeden zewnętrzny przełącznik dla urządzenia maszyny Wirtualnej.<br/> Urządzenie wymaga statyczny lub dynamiczny adres IP, a dostęp do Internetu. |
| **Obsługa migracji**      | Aby rozpocząć replikowanie maszyn, Usługa bramy migracji na urządzeniu musi być 1.18.7141.12919 lub nowszej. Zaloguj się do aplikacji sieci web urządzenia, aby sprawdzić wersję. |

## <a name="assessment-appliance-url-access"></a>Dostęp do adresu URL oceny urządzenia

Aby ocenić maszyny wirtualne, urządzenie Azure Migrate wymaga łączności z Internetem.

- Podczas wdrażania urządzenia usługi Azure Migrate wykonuje sprawdzenie łączności z adresami URL podsumowane w poniższej tabeli.
- Jeśli używasz firewall.proxy opartego na adresach URL umożliwiają dostęp do adresów URL w tabeli, upewniając się, że serwer proxy usuwa wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.
- Jeśli masz przechwytujący serwer proxy, może być konieczne Importowanie certyfikatu serwera z serwera proxy do urządzenia. 

    
**Adres URL** | **Szczegóły**  
--- | --- 
*.portal.azure.com | Nawigacja w witrynie Azure Portal
*.windows.net | Zaloguj się do Twojej subskrypcji platformy Azure.
*.microsoftonline.com | Tworzenie usługi Azure Active Directory aplikacje dla urządzenia do obsługi komunikacji.
management.azure.com | Tworzenie usługi Azure Active Directory aplikacje dla urządzenia do obsługi komunikacji.
dc.services.visualstudio.com | Rejestrowanie i monitorowanie 
*.vault.azure.net | Zarządzaj wpisów tajnych w usłudze Azure Key Vault podczas komunikacji między urządzeniem a usługą.


## <a name="assessment-port-requirements"></a>Wymagania dotyczące portów oceny

Poniższa tabela zawiera podsumowanie wymaganiach dotyczących portów dla oceny.

**urządzenia** | **połączenia**
--- | --- 
**Urządzenia** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego do urządzenia.<br/> Liczba przychodzących połączeń na porcie 44368 zdalnie uzyskiwać dostęp do aplikacji zarządzania urządzeniem przy użyciu adresu URL: https://<appliance-ip-or-name>:44368<br/> Połączenia wychodzące na porcie 443 w celu wysyłania metadanych odnajdywania i wydajności do usługi Azure Migrate.
**Klaster hostów funkcji Hyper-V** | Liczba przychodzących połączeń na portach WinRM 5985 (HTTP) i 5986 (HTTPS), aby ściągnąć metadanych konfiguracji i wydajności maszyn wirtualnych funkcji Hyper-V przy użyciu sesji modelu wspólnych informacji (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Wymagania dotyczące migracji — funkcji Hyper-V hosta

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrażanie hosta**       | Host funkcji Hyper-V może być autonomiczny lub wdrożyć w klastrze. |
| **Uprawnienia**           | Musisz mieć uprawnienia administratora na hoście funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2019 r, system Windows Server 2016 lub Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Wymagania dotyczące migracji — funkcji Hyper-V maszyny Wirtualnej

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) systemów operacyjnych obsługiwanych przez platformę Azure. |
| **Uprawnienia**           | Musisz mieć uprawnienia administratora na każdej maszynie Wirtualnej funkcji Hyper-V, którą chcesz ocenić. |
| **Usługi integracji**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) musi być uruchomiony na maszynach wirtualnych, które należy ocenić, aby można było przechwytywać informacje dotyczące systemu operacyjnego. |
| **Wymagane zmiany na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, dzięki czemu mogą uruchamiać na platformie Azure. Usługa Azure Migrate sprawia, że te zmiany automatycznie dla następujących systemów operacyjnych:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> — 7,8 debian<br/><br/> Dla innych systemów operacyjnych należy wprowadzić korekty ręcznie przed migracją. Odpowiednie artykuły zawierają instrukcje dotyczące sposobu wykonania tego zadania. |
| **Rozruch systemu Linux**                 | Jeśli/Boot znajduje się na dedykowanym partycji, powinien znajdować się na dysku systemu operacyjnego, a nie być rozmieszczone na wielu dyskach.<br/> Jeśli/Boot jest częścią partycji głównej (/), następnie partycji "/" powinien znajdować się na dysku systemu operacyjnego, a nie obejmować inne dyski. |
| **Rozruch z interfejsem UEFI**                  | Maszyny wirtualne z rozruchem z interfejsem UEFI nie są obsługiwane na potrzeby migracji. |
| **Zaszyfrowanych dysków/woluminów**    | Maszyny wirtualne z zaszyfrowanych dysków/woluminów nie są obsługiwane na potrzeby migracji. |
| **Dyski RDM/przekazujące**      | Jeśli maszyny wirtualne dyski RDM lub atrybut passthrough, te dyski nie będą replikowane na platformie Azure. |
| **NFS**                        | Woluminy systemu plików NFS zainstalowany jako woluminy na maszynach wirtualnych nie będą replikowane. |
| **Dysk docelowy**                | Możesz migrować maszyny wirtualne platformy Azure, tylko w przypadku zarządzanych dysków. |




## <a name="migration-hyper-v-host-url-access"></a>Dostęp do adresu URL hosta migracji — funkcji Hyper-V

Poniższa tabela zawiera podsumowanie dla hostów funkcji Hyper-V, wymagania dotyczące dostępu do adresu URL.

**Adres URL** | **Szczegóły**  
--- | ---
login.microsoftonline.com | Zarządzanie dostępem do kontroli i tożsamość usługi Active Directory.
*.backup.windowsazure.com | Transfer danych replikacji i koordynacji.
*.hypervrecoverymanager.windowsazure.com | Połącz z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do konta magazynu.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji, używany do monitorowania wewnętrznego.
time.windows.com | Weryfikuje synchronizacji czasu między systemem i czasem globalnym.

## <a name="migration-port-access"></a>Dostępu do portu migracji

Poniższa tabela zawiera podsumowanie wymagania dotyczące portów na hostach funkcji Hyper-V i maszyn wirtualnych do migracji maszyny Wirtualnej.

**urządzenia** | **połączenia**
--- | --- 
Hosty funkcji Hyper-V lub maszyny wirtualne | Połączenia wychodzące przy użyciu protokołu HTTPS portu 443 do przesyłania danych replikacji maszyny Wirtualnej do usługi Azure Migrate.

  
## <a name="migration-vm-disk-support"></a>Obsługa dysku maszyny Wirtualnej na migracji 

**Pomoc techniczna** | **Szczegóły**
--- | ---
Migrowane dyski | Tylko maszyny wirtualne można migrować do usługi managed disks (HHD standardowa, premium SSD) na platformie Azure.
   

## <a name="next-steps"></a>Następne kroki

[Przygotowywanie oceny maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md) do migracji.




 
