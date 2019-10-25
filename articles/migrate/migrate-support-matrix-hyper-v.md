---
title: Macierz obsługi Azure Migrate na potrzeby oceny i migracji funkcji Hyper-V
description: Podsumowuje ustawienia i ograniczenia dotyczące oceny i migracji funkcji Hyper-V przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 0964dac6b4f381e2ec52bd9ec078741d0ee85712
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802183"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Macierz obsługi dotycząca oceny i migracji środowiska funkcji Hyper-V

Za pomocą [usługi Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. W tym artykule opisano ustawienia i ograniczenia dotyczące oceny i migracji lokalnych maszyn wirtualnych funkcji Hyper-V.



## <a name="hyper-v-scenarios"></a>Scenariusze funkcji Hyper-V

W tabeli zestawiono obsługiwane scenariusze dotyczące maszyn wirtualnych funkcji Hyper-V.

**Wdrożenie** | **Szczegóły***
--- | ---
**Ocenianie lokalnych maszyn wirtualnych funkcji Hyper-V** | [Skonfiguruj](tutorial-prepare-hyper-v.md) swoją pierwszą ocenę.<br/><br/> [Uruchom](scale-hyper-v-assessment.md) ocenę na dużą skalę.
**Migrowanie maszyn wirtualnych funkcji Hyper-V na platformę Azure** | [Wypróbuj](tutorial-migrate-hyper-v.md) migrację do platformy Azure.

## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Pomoc techniczna** | **Szczegóły**
--- | ---
Uprawnienia platformy Azure | Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.
Maszyny wirtualne funkcji Hyper-V | Oceń do 35 000 maszyn wirtualnych funkcji Hyper-V w jednym projekcie. W ramach subskrypcji platformy Azure można mieć wiele projektów. Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.
Geograficzne | Możesz tworzyć Azure Migrate projekty w wielu lokalizacje geograficzneach. Chociaż można tworzyć projekty w określonych lokalizacje geograficzne, można ocenić lub migrować maszyny pod kątem innych lokalizacji docelowych. Lokalizacja geograficzna projektu służy tylko do przechowywania odnalezionych metadanych.

  **Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
  --- | ---
  Platforma Azure dla instytucji rządowych | US Gov Wirginia
  Azja i Pacyfik | Azja Wschodnia lub Azja Południowo-Wschodnia
  Australia | Australia Wschodnia lub Australia Południowo-Wschodnia
  Brazylia | Brazylia Południowa
  Kanada | Kanada środkowa lub Kanada Wschodnia
  Europa | Europa Północna lub Europa Zachodnia
  Francja | Francja Środkowa
  Indie | Indie Środkowe lub Indie Południowe
  Japonia |  Japonia Wschodnia lub Japonia Zachodnia
  Korea Południowa | Korea środkowa lub Korea Południowa
  Zjednoczone Królestwo | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
  Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2


 > [!NOTE]
 > Obsługa Azure Government jest obecnie dostępna tylko dla [starszej wersji](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) programu Azure Migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Ocena — wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie hosta**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. <br/> Alternatywnie, jeśli nie chcesz przypisywać uprawnień administratora, Utwórz konto użytkownika lokalnego lub domeny i Dodaj tego użytkownika do tych grup — Użytkownicy zarządzania zdalnego, Administratorzy funkcji Hyper-V i użytkownicy monitora wydajności. |
| **System operacyjny hosta** | Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2.<br/> Nie można ocenić maszyn wirtualnych znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2012. |
| **Komunikacja zdalna programu PowerShell**   | Musi być włączona na każdym hoście. |
| **Funkcja Hyper-V Replica**       | W przypadku korzystania z funkcji Hyper-V Replica (lub korzystania z wielu maszyn wirtualnych z tymi samymi identyfikatorami maszyn wirtualnych) i odnajdywania zarówno oryginalnej, jak i zreplikowanej maszyny wirtualnej przy użyciu Azure Migrate, Ocena wygenerowana przez Azure Migrate może być niedokładna. |


## <a name="assessment-hyper-v-vm-requirements"></a>Ocena — wymagania dotyczące maszyny wirtualnej funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure. |
| **Usługi integracji**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na maszynach wirtualnych, które oceniasz, aby przechwycić informacje o systemie operacyjnym. |



## <a name="assessment-appliance-requirements"></a>Ocena — wymagania dotyczące urządzenia

W celu oceny Azure Migrate uruchamia lekkie urządzenie w celu odnajdywania maszyn wirtualnych funkcji Hyper-V oraz wysyłania metadanych maszyn wirtualnych i danych wydajności do Azure Migrate. Urządzenie jest uruchamiane na maszynie wirtualnej funkcji Hyper-V i konfigurowane przy użyciu skompresowanego wirtualnego dysku twardego funkcji Hyper-V pobranego z Azure Portal. Poniższa tabela zawiera podsumowanie wymagań dotyczących urządzeń.

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrażanie urządzenia**   |  Urządzenie jest wdrażane jako maszyna wirtualna funkcji Hyper-V.<br/> Maszyna wirtualna z urządzeniem Azure Migrate jest maszyną wirtualną funkcji Hyper-V w wersji 5,0.<br/> Na hoście funkcji Hyper-V musi być uruchomiony system Windows Server 2012 R2 lub nowszy.<br/> Host wymaga wystarczającej ilości miejsca, aby przydzielić 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca do magazynowania oraz przełącznik zewnętrzny dla maszyny wirtualnej urządzenia.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP oraz dostęp do Internetu.
| **Projekt Azure Migrate**  |  Urządzenie może być skojarzone z pojedynczym projektem.<br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> Można ocenić do 35 000 maszyn wirtualnych w projekcie.
| **Hosty funkcji Hyper-V**          | Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
| **Odnajdowa**              | Pojedyncze urządzenie może wykryć do 5000 maszyn wirtualnych.
| **Grupa oceny**       | Można dodać do 35 000 maszyn w jednej grupie.
| **Ocena**             | W ramach jednej oceny można ocenić do 35 000 maszyn wirtualnych.



## <a name="assessment-appliance-url-access"></a>Ocena — dostęp do adresu URL urządzenia

Do oceny maszyn wirtualnych urządzenie Azure Migrate musi mieć łączność z Internetem.

- Podczas wdrażania urządzenia Azure Migrate sprawdza połączenie adresów URL, które zostały podsumowane w poniższej tabeli.
- Jeśli używasz serwera proxy opartego na adresie URL, Zezwól na dostęp do adresów URL w tabeli, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.
- W przypadku przechwycenia serwera proxy może zaistnieć konieczność zaimportowania certyfikatu serwera z serwera proxy do urządzenia.


**Adres URL** | **Szczegóły**  
--- | ---
*.portal.azure.com | Nawigacja do Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Zaloguj się do Twojej subskrypcji platformy Azure.
*.microsoftonline.com <br/> *. microsoftonline-p.com | Tworzenie aplikacji Azure Active Directory na potrzeby komunikacji urządzeń z usługą.
management.azure.com | Tworzenie aplikacji Azure Active Directory na potrzeby komunikacji urządzeń z usługą.
dc.services.visualstudio.com | Rejestrowanie i monitorowanie
*.vault.azure.net | Zarządzaj wpisami tajnymi w Azure Key Vault podczas komunikacji między urządzeniem i usługą.
aka.ms/* | Zezwalaj na dostęp do linków aliasów.
https://download.microsoft.com/download/* | Zezwala na pobieranie z witryny pobierania firmy Microsoft.



## <a name="assessment-port-requirements"></a>Ocena — wymagania dotyczące portów

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**Pliku** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Połączenia wychodzące na portach 443, 5671 i 5672 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Host/klaster funkcji Hyper-V** | Połączenia przychodzące na portach usługi WinRM 5985 (HTTP) i 5986 (HTTPS) do ściągania metadanych konfiguracji i wydajności maszyn wirtualnych funkcji Hyper-V przy użyciu sesji model wspólnych informacji (CIM).

## <a name="migration-limitations"></a>Migracja — ograniczenia
Można wybrać maksymalnie 10 maszyn wirtualnych na potrzeby replikacji. Jeśli chcesz migrować więcej maszyn, wykonaj replikację w grupach o wartości 10.

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
| **Wymagane zmiany dotyczące platformy Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Azure Migrate automatycznie wprowadza te zmiany w następujących systemach operacyjnych:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> W przypadku innych systemów operacyjnych należy ręcznie wprowadzić zmiany przed migracją. Odpowiednie artykuły zawierają instrukcje, jak to zrobić. |
| **Rozruch systemu Linux**                 | Jeśli/Boot znajduje się na dedykowanej partycji, powinien znajdować się na dysku systemu operacyjnego i nie można go rozłożyć na wiele dysków.<br/> Jeśli/boot jest częścią partycji głównej (/), partycja "/" powinna znajdować się na dysku systemu operacyjnego i nie może obejmować innych dysków. |
| **Rozruch z interfejsem UEFI**                  | Migrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową w systemie BIOS. Na maszynie wirtualnej powinien działać system Windows Server 2012 lub nowszy. Dysk systemu operacyjnego powinien mieć maksymalnie pięć partycji lub mniej, a rozmiar dysku systemu operacyjnego musi być mniejszy niż 300 GB.
  |
| **Rozmiar dysku**                  | 2 TB dla dysku systemu operacyjnego, 4 TB dla dysków z danymi.
| **Numer dysku** | Maksymalnie 16 dysków na maszynę wirtualną.
| **Zaszyfrowane dyski/woluminy**    | Migracja nie jest obsługiwana. |
| **RDM/przekazywanie dysków**      | Migracja nie jest obsługiwana. |
| **Dysk udostępniony** | Maszyny wirtualne korzystające z dysków udostępnionych nie są obsługiwane na potrzeby migracji.
| **NFS**                        | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane. |
| **MAGAZYNU**                      | Maszyny wirtualne z obiektami docelowymi iSCSI nie są obsługiwane na potrzeby migracji.
| **Dysk docelowy**                | Można przeprowadzić migrację do maszyn wirtualnych platformy Azure tylko z dyskami zarządzanymi. |
| **If** | Nieobsługiwane.
| **Tworzenie zespołu kart interfejsu sieciowego** | Nieobsługiwane.
| **Azure Site Recovery** | Nie można przeprowadzić replikacji przy użyciu migracji serwera Azure Migrate, jeśli maszyna wirtualna ma włączoną replikację z Azure Site Recovery.





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

**Pliku** | **Połączenie**
--- | ---
Hosty/maszyny wirtualne funkcji Hyper-V | Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej do Azure Migrate.




## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md) na potrzeby migracji.
