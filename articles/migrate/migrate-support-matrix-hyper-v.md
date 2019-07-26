---
title: Macierz obsługi Azure Migrate na potrzeby oceny i migracji funkcji Hyper-V
description: Podsumowuje ustawienia i ograniczenia dotyczące oceny i migracji funkcji Hyper-V przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 507ca6daa30a19b73848d6d3cf253390baf496af
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372459"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Macierz obsługi dotycząca oceny i migracji środowiska funkcji Hyper-V

Za pomocą [usługi Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. W tym artykule opisano ustawienia i ograniczenia dotyczące oceny i migracji lokalnych maszyn wirtualnych funkcji Hyper-V.



## <a name="hyper-v-scenarios"></a>Scenariusze funkcji Hyper-V

W tabeli zestawiono obsługiwane scenariusze dotyczące maszyn wirtualnych funkcji Hyper-V.

**Wdrożenie** | **Uzyskać***
--- | ---
**Ocenianie lokalnych maszyn wirtualnych funkcji Hyper-V** | [Skonfiguruj](tutorial-prepare-hyper-v.md) swoją pierwszą ocenę.<br/><br/> [Uruchom](scale-hyper-v-assessment.md) ocenę na dużą skalę.
**Migrowanie maszyn wirtualnych funkcji Hyper-V na platformę Azure** | [Wypróbuj](tutorial-migrate-hyper-v.md) migrację do platformy Azure.



## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Pomoc techniczna** | **Szczegóły**
--- | ---
Uprawnienia platformy Azure | Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.
Maszyny wirtualne funkcji Hyper-V | Oceń do 10 000 maszyn wirtualnych funkcji Hyper-V w jednym projekcie.

Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.

**Geograficzne** Istnieje wiele lokalizacje geograficzne, w których można utworzyć projekt Azure Migrate. Mimo że można tworzyć tylko projekty w tych lokalizacje geograficzne, można nadal oceniać lub migrować maszyny pod kątem innych lokalizacji docelowych. Lokalizacja geograficzna projektu służy tylko do przechowywania odnalezionych metadanych.


 **Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
 --- | ---
 Azure Government | Administracja USA — Wirginia
 Azja i Pacyfik | Azja Południowo-Wschodnia lub Azja Wschodnia
 Europa | Europa Południowa lub Europa Zachodnia
 Zjednoczone Królestwo | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
 Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2


 > [!NOTE]
 > Obsługa Azure Government jest obecnie dostępna tylko dla starszej [wersji](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) programu Azure Migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Ocena — wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie hosta**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2016 lub Windows Server 2012 R2.<br/> Nie można ocenić maszyn wirtualnych znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2019. |
| **Komunikacja zdalna programu PowerShell**   | Musi być włączona na każdym hoście. |
| **Funkcja Hyper-V Replica**       | W przypadku korzystania z funkcji Hyper-V Replica (lub korzystania z wielu maszyn wirtualnych z tymi samymi identyfikatorami maszyn wirtualnych) i odnajdywania zarówno oryginalnej, jak i zreplikowanej maszyny wirtualnej przy użyciu Azure Migrate, Ocena wygenerowana przez Azure Migrate może być niedokładna. |


## <a name="assessment-hyper-v-vm-requirements"></a>Ocena — wymagania dotyczące maszyny wirtualnej funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure. |
| **Uprawnienia**           | Musisz mieć uprawnienia administratora na wszystkich maszynach wirtualnych funkcji Hyper-V, które chcesz ocenić. |
| **Usługi integracji**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na maszynach wirtualnych, które oceniasz, aby przechwycić informacje o systemie operacyjnym. |
| **Wymagane zmiany dotyczące platformy Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Azure Migrate automatycznie wprowadza te zmiany w następujących systemach operacyjnych:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> W przypadku innych systemów operacyjnych należy ręcznie wprowadzić zmiany przed migracją. Odpowiednie artykuły zawierają instrukcje, jak to zrobić. |
| **Rozruch systemu Linux**                 | Jeśli/Boot znajduje się na dedykowanej partycji, powinien znajdować się na dysku systemu operacyjnego i nie można go rozłożyć na wiele dysków.<br/> Jeśli/boot jest częścią partycji głównej (/), partycja "/" powinna znajdować się na dysku systemu operacyjnego i nie może obejmować innych dysków. |
| **Rozruch z interfejsem UEFI**                  | Maszyny wirtualne z rozruchem UEFI nie są obsługiwane w przypadku migracji. |
| **Zaszyfrowane dyski/woluminy**    | Maszyny wirtualne z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji. |
| **RDM/przekazywanie dysków**      | Jeśli maszyny wirtualne mają dyski RDM lub przekazujących, te dyski nie będą replikowane do platformy Azure. |
| **NFS**                        | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane. |
| **Dysk docelowy**                | Azure Migrate oceny zalecają migrację do maszyn wirtualnych platformy Azure tylko z dyskami zarządzanymi. |


## <a name="assessment-appliance-requirements"></a>Ocena — wymagania dotyczące urządzenia

W celu oceny Azure Migrate uruchamia lekkie urządzenie w celu odnajdywania maszyn wirtualnych funkcji Hyper-V oraz wysyłania metadanych maszyn wirtualnych i danych wydajności do Azure Migrate. Urządzenie jest uruchamiane na maszynie wirtualnej funkcji Hyper-V i konfigurowane przy użyciu skompresowanego wirtualnego dysku twardego funkcji Hyper-V pobranego z Azure Portal. Poniższa tabela zawiera podsumowanie wymagań dotyczących urządzeń.

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Projekt Azure Migrate**  |  Urządzenie może być skojarzone z pojedynczym projektem.<br/> Możesz odkryć do 5000 maszyn wirtualnych funkcji Hyper-V za pomocą jednego urządzenia.
| **Ograniczenia dotyczące funkcji Hyper-V**    |  Urządzenie jest wdrażane jako maszyna wirtualna funkcji Hyper-V.<br/> Podana maszyna wirtualna urządzenia ma maszynę wirtualną funkcji Hyper-V w wersji 5,0.<br/> Na hoście maszyny wirtualnej musi działać system Windows Server 2012 R2 lub nowszy.<br/> Potrzebuje wystarczającej ilości miejsca, aby przydzielić 16 GB pamięci RAM, 4 procesory wirtualne i 1 Przełącznik zewnętrzny dla maszyny wirtualnej urządzenia.<br/> Urządzenie wymaga statycznego lub dynamicznego adresu IP i dostępu do Internetu.
| **Urządzenie funkcji Hyper-V**      |  Urządzenie jest skonfigurowane jako maszyna wirtualna funkcji Hyper-V.<br/> Wirtualny dysk twardy udostępniony do pobrania to maszyna wirtualna funkcji Hyper-V w wersji 5,0.
| **Host**                   | Na hoście maszyny wirtualnej z uruchomioną maszyną wirtualną musi być uruchomiony system Windows Server 2012 R2 lub nowszy.<br/> Potrzebuje wystarczającej ilości miejsca, aby przydzielić 16 GB pamięci RAM, 4 procesory wirtualne i jeden przełącznik zewnętrzny dla maszyny wirtualnej urządzenia.<br/> Urządzenie wymaga statycznego lub dynamicznego adresu IP i dostępu do Internetu. |
| **Obsługa migracji**      | Aby można było uruchomić replikację maszyn, Usługa bramy migracji na urządzeniu musi być 1.18.7141.12919 lub nowsza. Zaloguj się do aplikacji sieci Web urządzenia, aby sprawdzić wersję. |

## <a name="assessment-appliance-url-access"></a>Ocena — dostęp do adresu URL urządzenia

Do oceny maszyn wirtualnych urządzenie Azure Migrate musi mieć łączność z Internetem.

- Podczas wdrażania urządzenia Azure Migrate sprawdza połączenie adresów URL, które zostały podsumowane w poniższej tabeli.
- Jeśli używasz zapory. proxy oparty na adresie URL, Zezwól na dostęp do adresów URL w tabeli, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.
- W przypadku przechwycenia serwera proxy może zaistnieć konieczność zaimportowania certyfikatu serwera z serwera proxy do urządzenia.


**Adres URL** | **Szczegóły**  
--- | ---
*.portal.azure.com | Nawigacja do Azure Portal
*.windows.net | Zaloguj się do swojej subskrypcji platformy Azure
*.microsoftonline.com | Tworzenie aplikacji Azure Active Directory na potrzeby komunikacji urządzeń z usługą.
management.azure.com | Tworzenie aplikacji Azure Active Directory na potrzeby komunikacji urządzeń z usługą.
dc.services.visualstudio.com | Rejestrowanie i monitorowanie
*.vault.azure.net | Zarządzaj wpisami tajnymi w Azure Key Vault podczas komunikacji między urządzeniem i usługą.


## <a name="assessment-port-requirements"></a>Ocena — wymagania dotyczące portów

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**urządzenia** | **połączenia**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/> Połączenia przychodzące na porcie 44368 umożliwiające zdalny dostęp do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: https://< urządzenie-IP-lub-Name >: 44368<br/> Połączenia wychodzące na porcie 443 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Host/klaster funkcji Hyper-V** | Połączenia przychodzące na portach usługi WinRM 5985 (HTTP) i 5986 (HTTPS) do ściągania metadanych konfiguracji i wydajności maszyn wirtualnych funkcji Hyper-V przy użyciu sesji model wspólnych informacji (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Migracja — wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie hosta**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migracja — wymagania dotyczące maszyn wirtualnych funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure. |
| **Uprawnienia**           | Musisz mieć uprawnienia administratora na wszystkich maszynach wirtualnych funkcji Hyper-V, które chcesz ocenić. |
| **Usługi integracji**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na maszynach wirtualnych, które oceniasz, aby przechwycić informacje o systemie operacyjnym. |
| **Wymagane zmiany dotyczące platformy Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Azure Migrate automatycznie wprowadza te zmiany w następujących systemach operacyjnych:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> W przypadku innych systemów operacyjnych należy ręcznie wprowadzić zmiany przed migracją. Odpowiednie artykuły zawierają instrukcje, jak to zrobić. |
| **Rozruch systemu Linux**                 | Jeśli/Boot znajduje się na dedykowanej partycji, powinien znajdować się na dysku systemu operacyjnego i nie można go rozłożyć na wiele dysków.<br/> Jeśli/boot jest częścią partycji głównej (/), partycja "/" powinna znajdować się na dysku systemu operacyjnego i nie może obejmować innych dysków. |
| **Rozruch z interfejsem UEFI**                  | Maszyny wirtualne z rozruchem UEFI nie są obsługiwane w przypadku migracji. |
| **Zaszyfrowane dyski/woluminy**    | Maszyny wirtualne z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji. |
| **RDM/przekazywanie dysków**      | Jeśli maszyny wirtualne mają dyski RDM lub przekazujących, te dyski nie będą replikowane do platformy Azure. |
| **NFS**                        | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane. |
| **Dysk docelowy**                | Można przeprowadzić migrację do maszyn wirtualnych platformy Azure tylko z dyskami zarządzanymi. |




## <a name="migration-hyper-v-host-url-access"></a>Migracja — dostęp do adresu URL hosta funkcji Hyper-V

Poniższa tabela zawiera podsumowanie wymagań dostępu do adresów URL dla hostów funkcji Hyper-V.

**Adres URL** | **Szczegóły**  
--- | ---
login.microsoftonline.com | Kontrola dostępu i zarządzanie tożsamościami przy użyciu Active Directory.
*.backup.windowsazure.com | Transfer i koordynacja danych replikacji.
*.hypervrecoverymanager.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do kont magazynu.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
time.windows.com | Weryfikuje synchronizację czasu między systemem i czasem globalnym.

## <a name="migration-port-access"></a>Migracja — dostęp do portu

W poniższej tabeli zestawiono wymagania dotyczące portów na hostach i maszynach wirtualnych funkcji Hyper-V na potrzeby migracji maszyn wirtualnych.

**urządzenia** | **połączenia**
--- | ---
Hosty/maszyny wirtualne funkcji Hyper-V | Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej do Azure Migrate.


## <a name="migration-vm-disk-support"></a>Migracja — Obsługa dysków maszyny wirtualnej

**Pomoc techniczna** | **Szczegóły**
--- | ---
Zmigrowane dyski | Maszyny wirtualne można migrować tylko do usługi Managed disks (standardowa HHD lub Premium SSD) na platformie Azure.


## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md) na potrzeby migracji.
