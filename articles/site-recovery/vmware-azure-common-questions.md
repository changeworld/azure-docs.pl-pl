---
title: Często zadawane pytania — program VMware do odzyskiwania po awarii platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera podsumowanie często zadawane pytania, podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 12/31/2018
ms.topic: conceptual
ms.author: rayne
ms.openlocfilehash: 920ae8ff09cb8e936a1ba70b2c862bd9bc076046
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974696"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Często zadawane pytania — program VMware do platformy Azure replikacji

Ten artykuł zawiera odpowiedzi na typowe pytania, które widać w przypadku wdrażania odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware do platformy Azure. Jeśli masz pytania dotyczące po przeczytaniu tego artykułu, opublikuj je na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Ogólne
### <a name="how-is-site-recovery-priced"></a>Jak jest rozliczana Usługa Site Recovery?
Przegląd [cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) szczegółowe informacje.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zapłacić za maszyny wirtualne platformy Azure?
Podczas replikacji dane są replikowane do usługi Azure storage, a nie płacić za wszelkie zmiany maszyny Wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne IaaS platformy Azure. Po tym są naliczane za zasoby obliczeniowe, które zostaną zużyte na platformie Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Co można zrobić z oprogramowania VMware do platformy Azure replikacji?
- **Odzyskiwanie po awarii**: Możesz skonfigurować odzyskiwanie po awarii w pełnej. W tym scenariuszu można replikować maszyny wirtualne VMware w środowisku lokalnym, do usługi Azure storage. Następnie, jeśli z infrastruktury lokalnej jest niedostępna, może zostać przeniesiony, do platformy Azure. Po przełączeniu w tryb failover maszyn wirtualnych platformy Azure są tworzone przy użyciu replikowanych danych. Dostępne aplikacje i obciążenia na maszynach wirtualnych platformy Azure, dopóki w lokalnym centrum danych stanie się ponownie dostępny. Następnie można powrotu po awarii z platformy Azure do lokacji lokalnej.
- **Migracja**: Usługa Site Recovery umożliwia migrowanie lokalnych maszyn wirtualnych z programu VMware na platformę Azure. W tym scenariuszu można replikować maszyny wirtualne VMware w środowisku lokalnym, do usługi Azure storage. Następnie możesz Failover ze środowiska lokalnego do platformy Azure. Po przejściu w tryb failover swoje aplikacje i obciążenia są dostępne i działają na maszynach wirtualnych platformy Azure.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Czego potrzebujesz na platformie Azure?
Potrzebujesz subskrypcji platformy Azure, magazyn usługi Recovery Services, konta magazynu i sieci wirtualnej. Magazyn, konto magazynu i sieci musi być w tym samym regionie.

### <a name="what-azure-storage-account-do-i-need"></a>Konto usługi Azure storage, które jest potrzebne?
Wymagane jest konto magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Usługa Premium storage jest obsługiwana.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy Moje konto platformy Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?
Jeśli jesteś administratorem subskrypcji, masz uprawnienia do replikacji, które są potrzebne. Jeśli nie masz, potrzebujesz uprawnień, aby utworzyć Maszynę wirtualną platformy Azure w grupie zasobów i sieci wirtualnej można określić podczas konfigurowania Site Recovery i uprawnienia do zapisu do wybranego konta magazynu. [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Lokalnie

### <a name="what-do-i-need-on-premises"></a>Co zrobić, należy w środowisku lokalnym?

W lokalnym potrzebne są:
- Site Recovery zainstalowanie tych składników, pojedynczej maszyny Wirtualnej VMware.
- Infrastruktura VMware przy użyciu co najmniej jeden ESXi host, a firma Microsoft zaleca, aby serwer vCenter.
- Co najmniej jeden maszyn wirtualnych programu VMware do replikowania.

[Dowiedz się więcej](vmware-azure-architecture.md) o architektura VMware – Azure.

Lokalny serwer konfiguracji można wdrożyć w następujący sposób:

- Zalecamy wdrażanie serwera konfiguracji jako Maszynę wirtualną VMware przy użyciu szablonu usługi OVA z wstępnie zainstalowanym serwerem konfiguracji.
- Jeśli z jakiegoś powodu nie można użyć szablonu, należy ręcznie skonfigurować serwer konfiguracji. [Dowiedz się więcej](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Gdzie są lokalne maszyny wirtualne replikowane do
Dane są replikowane do usługi Azure storage. Po uruchomieniu trybu failover Usługa Site Recovery automatycznie tworzy maszyny wirtualne platformy Azure z konta magazynu.

## <a name="replication"></a>Replikacja

### <a name="what-apps-can-i-replicate"></a>Jakie aplikacje można replikować?
Można replikować dowolną aplikację lub obciążenia uruchomione na maszynie Wirtualnej VMware, który jest zgodny z [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md##replicated-machines). Usługa Site Recovery obsługuje replikację z uwzględnieniem aplikacji, dzięki czemu aplikacje mogą przełączone w tryb failover i do inteligentnego stanu. Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Można replikować na platformę Azure za pomocą połączenia VPN lokacja lokacja?
Usługa Site Recovery replikuje dane ze środowiska lokalnego do usługi Azure storage za pośrednictwem publicznego punktu końcowego lub przy użyciu usługi ExpressRoute publicznej komunikacji równorzędnej. Replikacja za pośrednictwem sieci VPN typu lokacja lokacja nie jest obsługiwana.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Można replikować na platformę Azure przy użyciu usługi ExpressRoute?
Tak, usługa ExpressRoute może służyć do replikowania maszyn wirtualnych na platformie Azure. Usługa Site Recovery replikuje dane na koncie usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#publicpeering) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) za pomocą usługi ExpressRoute dla replikacji usługi Site Recovery. Komunikacja równorzędna firmy Microsoft jest zalecanym domen routingu replikacji. Upewnij się, że [wymagania sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) spełnione są również do replikacji. Po maszyny wirtualne nie za pośrednictwem sieci wirtualnej platformy Azure, można z nich korzystać przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

Podczas replikacji do platformy Azure, ruch związany z replikacją osiągnie publiczne punkty końcowe konta usługi Azure Storage, dlatego tylko można replikować za pośrednictwem publicznej sieci internet przy użyciu usługi ExpressRoute (publicznej komunikacji równorzędnej) i sieci VPN nie działa.


### <a name="what-are-the-replicated-vm-requirements"></a>Co to są replikowane wymagań dotyczących maszyn wirtualnych?

W przypadku replikacji maszyny Wirtualnej programu VMware musi działać obsługiwany system operacyjny. Ponadto maszyna wirtualna musi spełniać wymagania dotyczące maszyn wirtualnych platformy Azure. [Dowiedz się więcej](vmware-physical-azure-support-matrix.md##replicated-machines) w macierzy obsługi.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformę Azure?
Replikacja jest ciągłe podczas replikowania maszyn wirtualnych VMware na platformę Azure.

### <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Można zrobić początkowej replikacji offline?
Ta funkcja nie jest obsługiwana. Zażądać tej funkcji w [forum z opiniami](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Czy można wykluczyć dysków?
Tak, można wykluczyć dyski z replikacji.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Maszyny wirtualne można replikować z dyskami dynamicznymi?
Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Czy w przypadku używania grup replikacji w celu zachowania spójności wielu maszyn wirtualnych, można dodać nowej maszyny Wirtualnej do istniejącej grupy replikacji?
Tak, można dodać nowe maszyny wirtualne do istniejącej grupy replikacji po włączeniu replikacji dla nich. Po zainicjowaniu replikacji i nie można utworzyć grupy replikacji dla istniejących maszyn wirtualnych, nie można dodać do istniejącej grupy replikacji maszyny Wirtualnej.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Można zmodyfikować maszyny wirtualne, które jest replikowana, dodając lub zmienianie rozmiaru dysków?

Potrzeby replikacji oprogramowania VMware do platformy Azure można zmodyfikować rozmiaru dysku. Jeśli chcesz dodać nowe dyski, czego potrzebujesz, aby dodać dysk, a następnie ponownie włączyć ochronę maszyny Wirtualnej.

## <a name="configuration-server"></a>Serwer konfiguracji

### <a name="what-does-the-configuration-server-do"></a>Do czego służy serwer konfiguracji?
Serwer konfiguracji działa lokalnie składniki usługi Site Recovery, w tym:
- Serwer konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła go do usługi Azure storage., serwer przetwarzania instaluje także usługę Mobility na maszynach wirtualnych, o których mają być replikowane i przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.
- Serwer główny serwer docelowy, który służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

[Dowiedz się więcej](vmware-azure-architecture.md) dotyczące składników serwera konfiguracji i procesów.

### <a name="where-do-i-set-up-the-configuration-server"></a>Gdzie skonfigurować serwer konfiguracji?
Potrzebujesz jednego o wysokiej dostępności lokalnych zasobów programu VMware maszyny Wirtualnej serwera konfiguracji.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Jakie są wymagania dotyczące serwera konfiguracji?

Przegląd [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Można ręcznie skonfigurować serwer konfiguracji, a nie przy użyciu szablonu?
Zaleca się, że używasz najnowszej wersji szablonu pakietu OVF do [tworzenie maszyny Wirtualnej serwera konfiguracji](vmware-azure-deploy-configuration-server.md). Jeśli z jakiegoś powodu nie można na przykład nie masz dostępu do serwera VMware, możesz to zrobić [Pobierz plik instalacyjny ujednoliconego](physical-azure-set-up-source.md) z portalu, i uruchom go na maszynie Wirtualnej.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Serwer konfiguracji można replikować do więcej niż jednym regionie?
Nie. Aby to zrobić, należy skonfigurować serwer konfiguracji w każdym regionie.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Czy można hostować serwer konfiguracji na platformie Azure?
Ile jest to możliwe maszyna wirtualna Azure, które działają na serwerze konfiguracji musi komunikować się z lokalną infrastrukturą programu VMware i maszyn wirtualnych. To jest dodawanie opóźnienia i mieć wpływ na trwającą replikację.

### <a name="how-do-i-update-the-configuration-server"></a>Jak zaktualizować serwer konfiguracji?
[Dowiedz się więcej o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) aktualizację serwera konfiguracji. Można znaleźć informacje o najnowszych aktualizacji w [aktualizacje platformy Azure, strona](https://azure.microsoft.com/updates/?product=site-recovery). Można również bezpośrednio pobrać najnowszą wersję serwera konfiguracji z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="should-i-backup-the-deployed-configuration-server"></a>Należy utworzyć kopię zapasową serwera wdrożonej konfiguracji?
Zaleca się wykonywanie regularnych zaplanowanych kopii zapasowych serwera konfiguracji. Pomyślne powrotu po awarii powrót po awarii maszyny wirtualnej musi istnieć w bazie danych serwera konfiguracji, a serwer konfiguracji musi być uruchomiona w stanie połączonym. Dowiedz się więcej na temat typowych zadań zarządzania serwerem konfiguracji [tutaj](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Gdy jestem konfigurowania serwera konfiguracji, można pobrać i ręczne instalowanie programu MySQL?
Tak. Pobierz MySQL i umieść go w **C:\Temp\ASRSetup** folderu. Następnie zainstalować go ręcznie. Podczas konfigurowania serwera konfiguracji maszyny Wirtualnej i zaakceptuj warunki, MySQL będzie wyświetlana jako **zainstalowane** w **Pobierz i zainstaluj**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Można I uniknąć pobierania MySQL, ale pozwól Site Recovery zainstaluj go?
Tak. Pobierz Instalator MySQL i umieść go w **C:\Temp\ASRSetup** folderu.  Po skonfigurowaniu serwera konfiguracji maszyny Wirtualnej, należy zaakceptować warunki, a następnie kliknij **Pobierz i zainstaluj**, portal użyje Instalator dodaje zainstalować oprogramowanie MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Serwera konfiguracji maszyny Wirtualnej można używać do innych?
Nie, możesz należy używać tylko maszyny Wirtualnej serwera konfiguracji. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Można sklonować serwer konfiguracji i używać go w ramach aranżacji?
Nie, należy skonfigurować serwer nowej konfiguracji, aby uniknąć problemów z rejestracji.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Czy można zmienić magazynu zarejestrowany na serwerze konfiguracji?
Nie. Po zarejestrowaniu magazynu z serwera konfiguracji nie można zmienić. Przegląd [w tym artykule](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) kroków ponownej rejestracji.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych można użyć tego samego serwera konfiguracji
Tak, ale należy pamiętać, ten komputer fizyczny można tylko można ponownie do maszyny Wirtualnej VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Gdzie można pobrać hasła dla serwera konfiguracji?
[Zapoznaj się z tym artykułem](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) Aby dowiedzieć się więcej o pobieraniu hasło.

### <a name="where-can-i-download-vault-registration-keys"></a>Gdzie można pobrać klucze rejestracyjne magazynu?

W **magazyn usług Recovery Services**, **zarządzanie** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**. W **serwerów**, wybierz opcję **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.



## <a name="mobility-service"></a>Usługa mobilności

### <a name="where-can-i-find-the-mobility-service-installers"></a>Gdzie mogę znaleźć pliki instalacyjne usługi mobilności
Pliki instalacyjne są przechowywane w **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** folderu na serwerze konfiguracji.

## <a name="how-do-i-install-the-mobility-service"></a>Jak zainstalować usługę mobilności?
Zainstaluj na każdej maszynie Wirtualnej, którą chcesz replikować, za pomocą [instalacja wypychana](vmware-azure-install-mobility-service.md), lub [Instalacja ręczna](vmware-physical-mobility-service-install-manual.md) z interfejsu użytkownika lub środowiska Powershell. Alternatywnie, można wdrożyć przy użyciu narzędzia wdrażania, takich jak [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Bezpieczeństwo

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Jakiego dostępu czy usługa Site Recovery należy serwery VMware?
Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Skonfiguruj Maszynę wirtualną VMware z serwera konfiguracji i inne składniki usługi Site Recovery w środowisku lokalnym. [Dowiedz się więcej](vmware-azure-deploy-configuration-server.md)
- Automatyczne odnajdywanie maszyn wirtualnych do replikacji. Więcej informacji na temat przygotowywanie konta do automatycznego odnajdowania. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Jakiego dostępu czy usługa Site Recovery należy maszyn wirtualnych programu VMware?

- Aby replikować, maszyn wirtualnych programu VMware musi mieć usługi Site Recovery Mobility zainstalowany i uruchomiony. Ręczne wdrażanie narzędzia lub określ, czy usługa Site Recovery należy wykonać instalacji wypychanej usługi po włączeniu replikacji dla maszyny Wirtualnej. Dotyczące instalacji wypychanej Site Recovery wymaga konta, które można użyć do instalacji składnika usług. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Serwer przetwarzania (domyślnie uruchamiane na serwerze konfiguracji) wykonuje tę instalację. [Dowiedz się więcej](vmware-azure-install-mobility-service.md) o instalacja usługi mobilności.
- Podczas replikacji maszyn wirtualnych komunikują się z usługą Site Recovery w następujący sposób:
    - Maszyny wirtualne komunikują się z serwerem konfiguracji na porcie HTTPS 443 na potrzeby zarządzania replikacją.
    - Maszyny wirtualne wysyłać dane replikacji do serwera przetwarzania na porcie HTTPS 9443 (można modyfikować).
    - Jeśli włączono spójność wielu maszyn wirtualnych, maszyny wirtualne komunikują się ze sobą za pośrednictwem portu 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych. Dane replikacji są wymieniane między funkcjami hypervisor programu VMware i Azure storage. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Może nam obowiązek przechowywania metadanych w środowisku lokalnym w ramach regionów geograficznych?
Tak. Po utworzeniu magazynu w regionie, Upewniamy się, że wszystkie metadane używane przez pozostaje Site Recovery w ramach tego regionu geograficznego.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, zarówno szyfrowanie przesyłanych w i [szyfrowania na platformie Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.


## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii
### <a name="how-far-back-can-i-recover"></a>Jak daleko można odzyskać?
Dla replikacji VMware – Azure najstarszy punkt odzyskiwania, których można użyć to 72 godziny.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak dostęp do maszyn wirtualnych platformy Azure po włączeniu trybu failover?
Po przejściu w tryb failover maszyn wirtualnych platformy Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN lokacja lokacja lub za pośrednictwem usługi Azure ExpressRoute. Należy przygotować kilka rzeczy w celu nawiązania połączenia. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Jest w trybie Failover odporność danych?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery został zaprojektowany do trybu failover do pomocniczego centrum danych platformy Azure, zgodnie z umową SLA platformy Azure. Po przejściu do trybu failover, możemy zagwarantować metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [ PowerShell](/powershell/module/azurerm.siterecovery) do wyzwolenia przejścia w tryb failover.

### <a name="can-i-fail-back-to-a-different-location"></a>Może nie do innej lokalizacji?
Tak, jeśli Failover na platformie Azure można powrotu po awarii do innej lokalizacji Jeśli oryginalny jest niedostępna. [Dowiedz się więcej](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Dlaczego muszę sieci VPN lub usługi ExpressRoute do powrotu po awarii?

Podczas powrotu po awarii z platformy Azure, powrót do maszyny Wirtualnej w środowisku lokalnym są kopiowane dane z platformy Azure i dostęp prywatny jest wymagany.



## <a name="automation-and-scripting"></a>Automatyzacji i opracowywaniu skryptów

### <a name="can-i-set-up-replication-with-scripting"></a>Można skonfigurować replikację za pomocą skryptów?
Tak. Możesz zautomatyzować przepływy pracy Site Recovery przy użyciu interfejsu API Rest, programu PowerShell lub zestawu SDK usługi Azure. [Więcej](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Wydajność i pojemność
### <a name="can-i-throttle-replication-bandwidth"></a>Czy mogę ograniczyć przepustowość replikacji
Tak. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd](vmware-physical-azure-support-matrix.md) wymagań.
* [Konfigurowanie](vmware-azure-tutorial.md) programu VMware do platformy Azure replikacji.
