---
title: Często zadawane pytania — VMware do platformy Azure replikacji z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono często zadawane pytania podczas replikowania lokalnych maszyn wirtualnych VMware do platformy Azure przy użyciu usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 1764f4ed9dfe73763c288844be85d4805401887e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285971"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Często zadawane pytania — VMware do platformy Azure replikacji

Ten artykuł zawiera odpowiedzi na często zadawane pytania, które widzimy podczas replikowania lokalnych maszyn wirtualnych VMware do platformy Azure. Jeśli masz pytania po przeczytaniu tego artykułu, opublikuj je na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Ogólne
### <a name="how-is-site-recovery-priced"></a>Jak kosztuje usługa Site Recovery?
Przegląd [cennik usługi Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/) szczegóły.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak płacić za maszyny wirtualne Azure
Podczas replikacji dane są replikowane do magazynu Azure, a nie płacisz zmiany maszyny Wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne Azure IaaS. Po wykonaniu tej są rozliczane za zasoby obliczeniowe, które zostaną zużyte na platformie Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Co mogę zrobić przy VMware do platformy Azure replikacji?
- **Odzyskiwanie po awarii**: można skonfigurować pełne odzyskiwanie. W tym scenariuszu lokalnych maszyn wirtualnych VMware są replikowane do magazynu Azure. Następnie jeśli infrastruktury lokalnej jest niedostępny, możesz w trybie Failover na platformie Azure. Podczas przejścia w tryb failover, maszynach wirtualnych platformy Azure są tworzone przy użyciu zreplikowanych danych. Dostępne aplikacje i obciążenia na maszynach wirtualnych Azure, do momentu lokalnego centrum danych jest ponownie dostępny. Następnie możesz w trybie platformy Azure z do lokacji lokalnej.
- **Migracja**: można użyć usługi Site Recovery do migrowania maszyn wirtualnych VMware lokalnego do platformy Azure. W tym scenariuszu lokalnych maszyn wirtualnych VMware są replikowane do magazynu Azure. Następnie należy przełączyć się z lokalnymi na platformie Azure. Po przejściu w tryb failover aplikacji i obciążeń są dostępne i działają na maszynach wirtualnych platformy Azure.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Co należy na platformie Azure?
Potrzebujesz subskrypcji platformy Azure, Magazyn usług odzyskiwania konta magazynu i sieci wirtualnej. Magazyn, konta magazynu i sieci musi być w tym samym regionie.

### <a name="what-azure-storage-account-do-i-need"></a>Konto magazynu Azure, jakie potrzebne?
Potrzebujesz konta magazynu LRS lub GRS. Zalecamy użycie konta GRS, dzięki czemu dane będą odporne w przypadku regionalnej awarii lub sytuacji, w której nie można odzyskać regionu podstawowego. Magazyn w warstwie Premium jest obsługiwana.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Czy Moje konto Azure potrzebuje uprawnień do tworzenia maszyn wirtualnych?
Jeśli jesteś administratorem subskrypcji, masz uprawnienia do replikacji, które są potrzebne. Jeśli nie masz potrzebne uprawnienia do tworzenia maszyny Wirtualnej platformy Azure w grupie zasobów i sieć wirtualną, której można określić podczas konfigurowania usługi Site Recovery i uprawnienia do zapisywania na wybranym koncie magazynu. [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Lokalnie 

### <a name="what-do-i-need-on-premises"></a>Czego potrzebuję lokalnej?
Na lokalnych należy zainstalowane na jednej maszynie Wirtualnej VMware składniki usługi Site Recovery. Infrastruktury VMware, należy również z co najmniej jednego hosta ESXi, a firma Microsoft zaleca serwer vCenter. Ponadto należy przynajmniej jednej maszyny wirtualnej VMware do replikacji. [Dowiedz się więcej](vmware-azure-architecture.md) o VMware do platformy Azure architektury.

Serwer konfiguracji lokalnej można wdrożyć w jednej z dwóch sposobów

1. Wdróż je za pomocą szablonu maszyny Wirtualnej, który został wstępnie zainstalowany serwer konfiguracji. [Przeczytaj więcej tutaj](vmware-azure-tutorial.md#download-the-vm-template).
2. Wdróż je przy użyciu instalacji na komputerze z systemem Windows Server 2016 wybranych przez użytkownika. [Przeczytaj więcej tutaj](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Aby dowiedzieć się wprowadzenie kroki wdrażania serwera konfiguracji na własnych maszynach systemu Windows Server, w celu ochrony włączania ochrony, wybierz **do platformy Azure > nie zwirtualizowanych/inne**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Gdzie lokalnych maszyn wirtualnych są replikowane do?
Dane są replikowane do magazynu Azure. Po uruchomieniu trybu failover Usługa Site Recovery automatycznie tworzy maszynach wirtualnych platformy Azure z konta magazynu.

### <a name="what-apps-can-i-replicate"></a>Jakie aplikacje można replikować?
Można replikować dowolnej aplikacji lub obciążenia uruchomione na maszynie Wirtualnej VMware, który jest zgodny z [wymagania replikacji](vmware-physical-azure-support-matrix.md##replicated-machines). Usługa Site Recovery zapewnia obsługę replikacji obsługującej, dzięki czemu aplikacje mogą być przełączone do trybu failover i nie inteligentnego stanu. Usługa Site Recovery integruje się z aplikacjami firmy Microsoft, takich jak SharePoint, Exchange, Dynamics, SQL Server i usługi Active Directory i ściśle współpracuje z wiodącymi dostawcami oprogramowania, w tym Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Można replikować na platformę Azure, korzystając z sieci VPN lokacja lokacja?
Usługa Site Recovery replikuje dane z lokalnej do magazynu Azure za pośrednictwem publicznego punktu końcowego lub przy użyciu publicznej komunikacji równorzędnej ExpressRoute. Replikacja przez sieć VPN lokacja lokacja nie jest obsługiwana.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Można replikować do platformy Azure z ExpressRoute?
Tak, ExpressRoute można replikować maszyny wirtualne na platformie Azure. Usługa Site Recovery replikuje dane do konta magazynu platformy Azure za pośrednictwem publicznego punktu końcowego i należy skonfigurować [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) replikacji usługi Site Recovery. Po maszyn wirtualnych nie za pośrednictwem sieci wirtualnej platformy Azure, użytkownik może uzyskiwać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

Podczas replikacji do platformy Azure, ruch związany z replikacją osiągnie publiczne punkty końcowe konta usługi Azure Storage, w związku z tym tylko można replikować za pośrednictwem publicznego Internetu z z ExpressRoute (publicznej komunikacji równorzędnej) i sieci VPN nie działa. 



## <a name="what-are-the-replicated-vm-requirements"></a>Jakie są wymagania zreplikowanej maszyny Wirtualnej?

W przypadku replikacji maszyny Wirtualnej VMware musi działać system operacyjny obsługiwany. Ponadto maszyny Wirtualnej musi spełniać wymagania dotyczące maszyn wirtualnych platformy Azure. [Dowiedz się więcej](vmware-physical-azure-support-matrix.md##replicated-machines) w macierzy obsługi.

## <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować do platformy Azure?
Replikacja jest ciągły podczas replikowania maszyn wirtualnych VMware do platformy Azure.

## <a name="can-i-extend-replication"></a>Czy mogę rozszerzyć replikację?
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Żądanie tę funkcję w [forum opinii](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Czy mogę początkowej replikacji offline?
Ta funkcja nie jest obsługiwana. Żądanie tę funkcję w [forum opinii](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Czy można wyłączyć dyski?
Tak, dyski można wykluczyć z replikacji. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne mogą być replikowane. Dysk systemu operacyjnego musi być dyskiem podstawowym.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>Do istniejącej grupy replikacji można dodać nowej maszyny Wirtualnej?
Tak.

## <a name="configuration-server"></a>Serwer konfiguracji

### <a name="what-does-the-configuration-server-do"></a>Do czego służy serwer konfiguracji?
Serwer konfiguracji jest uruchamiana lokalną składników usługi Site Recovery, w tym: 
- Serwer konfiguracji, który koordynuje komunikacji między lokalną i platformą Azure i zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła go do platformy Azure magazynu., serwer przetwarzania instaluje usługi mobilności na maszynach wirtualnych do replikacji i przeprowadza automatyczne odnajdywanie maszyn wirtualnych VMware lokalnymi.
- Serwer główny cel, który obsługuje replikację danych podczas powrotu po awarii z platformy Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Gdy konfigurowanie serwera konfiguracji
Należy VMware pojedynczego wysokiej dostępności lokalnej maszyny Wirtualnej na serwerze konfiguracji.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Jakie są wymagania dotyczące serwera konfiguracji?

Przegląd [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Można ręcznie skonfigurować serwer konfiguracji zamiast przy użyciu szablonu?
Zaleca się, że używasz najnowszej wersji szablonu pakietu OVF do [utworzyć serwera konfiguracji maszyny Wirtualnej](vmware-azure-deploy-configuration-server.md). Jeśli z jakiegoś powodu nie można na przykład nie masz dostępu do serwera VMware, możesz [Pobierz plik Instalatora Unified](physical-azure-set-up-source.md) z portalu, i uruchom go na maszynie Wirtualnej. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Serwer konfiguracji można replikować do więcej niż jeden region?
Nie. Aby to zrobić, należy skonfigurować serwer konfiguracji w każdym regionie.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Czy można udostępniać serwer konfiguracji na platformie Azure?
Ile jest to możliwe uruchomiony serwer konfiguracji maszyny Wirtualnej Azure musi komunikować się z lokalną infrastrukturą VMware i maszyn wirtualnych. Obciążenie prawdopodobnie nie działało.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Gdzie można uzyskać najnowszą wersję szablonu serwera konfiguracji?
Pobierz najnowszą wersję ze [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Jak zaktualizować serwer konfiguracji?
Należy zainstalować pakiety zbiorcze aktualizacji. Można znaleźć informacje o najnowszych aktualizacji w [strona typu wiki aktualizacji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Usługa mobilności

### <a name="where-can-i-find-the-mobility-service-installers"></a>Gdzie można znaleźć instalatorów usługi mobilności?
Pliki instalacyjne są przechowywane w **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** folderu na serwerze konfiguracji.

## <a name="how-do-i-install-the-mobility-service"></a>Jak zainstalować usługi mobilności?
Zainstaluj na każdej maszynie Wirtualnej, którą chcesz replikować, za pomocą [instalacji wypychanej](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), lub instalacja ręczna z [interfejsu użytkownika](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), lub [przy użyciu programu PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Alternatywnie można wdrożyć przy użyciu narzędzia wdrażania, takich jak [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), lub [usługi Automatyzacja Azure i DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Bezpieczeństwo

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Jakie dostępu czy usługa Site Recovery należy serwerów VMware?
Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Konfigurowanie uruchomiony serwer konfiguracji oraz inne składniki usługi Site Recovery lokalnej maszyny Wirtualnej VMware. [Dowiedz się więcej](vmware-azure-deploy-configuration-server.md)
- Automatyczne odnajdywanie maszyn wirtualnych do replikacji. Więcej informacji na temat przygotowywania konta automatycznego wykrywania. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Maszyny wirtualne VMware rodzaju dostępu jest konieczne Site Recovery?

- Aby replikować, maszyny Wirtualnej VMware, należy skonfigurować usługi mobilności odzyskiwania lokacji, zainstalowano i uruchomiono. Ręczne wdrażanie narzędzia lub określ odzyskiwania lokacji należy wykonać instalację wypychaną usługi po włączeniu replikacji dla maszyny Wirtualnej. Dla instalacji wypychanej usługi Site Recovery potrzebuje konta, które mogą być używane do instalowania składnika usługi. [Dowiedz się więcej](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Serwer przetwarzania (domyślnie uruchamiane na serwerze konfiguracji) wykonuje tę instalację. [Dowiedz się więcej](vmware-azure-install-mobility-service.md) informacje dotyczące instalacji usługi mobilności.
- Podczas replikacji maszyn wirtualnych komunikują się z usługą Site Recovery w następujący sposób:
    - Maszyny wirtualne komunikowania się z serwerem konfiguracji na porcie 443 protokołu HTTPS do zarządzania replikacją.
    - Maszyny wirtualne wysyłanie danych replikacji na serwer przetwarzania na porcie HTTPS 9443 (może być modyfikowany).
    - Włączenie spójności wielu maszyn wirtualnych, maszyny wirtualne komunikują się ze sobą za pośrednictwem portu 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji o uruchomionej w maszyn wirtualnych. Dane replikacji są wymieniane między funkcjami hypervisor VMware i Magazyn Azure. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie oceny SOC2 i FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Firma Microsoft zachować metadanych lokalnymi w regionach geograficznych?
Tak. Po utworzeniu magazynu w regionie, Upewniamy się, że wszystkie metadane używane przez usługi Site Recovery pozostaje w tym regionie geograficznym granic.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, zarówno szyfrowanie podczas przesyłania danych i [szyfrowania na platformie Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.


## <a name="failover-and-failback"></a>Praca w trybie failover i powrót po awarii
### <a name="how-far-back-can-i-recover"></a>Jak daleko można odzyskać?
Dla VMware do platformy Azure starsze punkty odzyskiwania, których można używać jest 72 godzin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak dostęp do maszyn wirtualnych Azure po trybu failover?
Po przejściu w tryb failover za pośrednictwem bezpiecznego połączenia internetowego, za pośrednictwem sieci VPN lokacja lokacja lub za pośrednictwem usługi Azure może uzyskać dostępu do maszyn wirtualnych platformy Azure. Musisz przygotować kilka rzeczy, aby można było połączyć. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Nie powiodło się nad danymi odporne?
Platforma Azure została zaprojektowana z myślą o odporności danych. Usługa Site Recovery jest zaprojektowany z myślą o trybu failover do pomocniczego centrum danych Azure, zgodnie z warunkami umowy SLA platformy Azure. Podczas pracy awaryjnej, Upewniamy metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla magazynu.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
[Tryb failover](site-recovery-failover.md) nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub użyć [ PowerShell](/powershell/module/azurerm.siterecovery) aby wyzwolić tryb failover.

### <a name="can-i-fail-back-to-a-different-location"></a>Można nie do innej lokalizacji?
Tak, jeśli nie za pośrednictwem usługi Azure możesz w trybie do innej lokalizacji Jeśli oryginalny jest niedostępna. [Dowiedz się więcej](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Dlaczego należy sieci VPN i ExpressRoute wykonaj powrót po awarii

Jeśli nie zostanie ponownie z platformy Azure, kopiowaniu danych platformy Azure z powrotem do lokalnej maszyny Wirtualnej, a dostępu prywatnego jest wymagana. 



## <a name="automation-and-scripting"></a>Automatyzacja i skryptów

### <a name="can-i-set-up-replication-with-scripting"></a>Można skonfigurować replikację przy użyciu skryptów?
Tak. Można automatyzować przepływy pracy usługi Site Recovery przy użyciu interfejsu API Rest, programu PowerShell lub zestawu Azure SDK. [Więcej](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Wydajność i pojemność
### <a name="can-i-throttle-replication-bandwidth"></a>Czy mogę ograniczyć przepustowość replikacji?
Tak. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd](vmware-physical-azure-support-matrix.md) spełnienia wymagań.
* [Konfigurowanie](vmware-azure-tutorial.md) VMware do platformy Azure replikacji.
