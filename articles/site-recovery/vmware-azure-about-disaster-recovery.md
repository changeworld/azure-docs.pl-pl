---
title: Odzyskiwanie po awarii VMware za pomocą usługi Azure Site Recovery
description: Ten artykuł zawiera omówienie odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954390"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Odzyskiwanie po awarii maszyn wirtualnych VMware na platformie Azure – informacje

Ten artykuł zawiera omówienie odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware na platformie Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)

## <a name="what-is-bcdr"></a>Co to jest BCDR?

Strategia ciągłości działania i odzyskiwania po awarii (BCDR) pomaga utrzymać działalność firmy. Podczas planowanych przestojów i nieoczekiwanych przestojów BCDR zapewnia bezpieczeństwo i dostęp danych oraz zapewnia, że aplikacje będą nadal działać. Oprócz funkcji platformy BCDR, takich jak regionalne parowanie i magazyn o wysokiej dostępności, platforma Azure udostępnia usługi odzyskiwania jako integralną część rozwiązania BCDR. Usługi odzyskiwania obejmują: 

- [Usługa Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) kopiuje kopie zapasowe danych lokalnych i maszyn wirtualnych platformy Azure. Można wywrzeć zapas zapasowy pliku i folderów, określonych obciążeń lub całej maszyny Wirtualnej. 
- [Usługa Azure Site Recovery](site-recovery-overview.md) zapewnia odporność i odzyskiwanie po awarii dla aplikacji i obciążeń działających na komputerach lokalnych lub maszynach wirtualnych usługi Azure IaaS. Usługa Site Recovery organizuje replikację i obsługuje przejście w błąd na platformie Azure w przypadku wystąpienia awarii. Obsługuje również odzyskiwanie z platformy Azure do lokacji głównej. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>W jaki sposób odzyskiwanie po awarii powoduje odzyskiwanie po awarii?

1. Po przygotowaniu platformy Azure i witryny lokalnej można skonfigurować i włączyć replikację dla komputerów lokalnych.
2. Usługa Site Recovery organizuje początkową replikację komputera zgodnie z ustawieniami zasad.
3. Po replikacji początkowej usługa Site Recovery replikuje zmiany różnicowe na platformie Azure. 
4. Gdy wszystko jest replikowane zgodnie z oczekiwaniami, należy uruchomić wiertła odzyskiwania po awarii.
    - Wiertło pomaga zapewnić, że praca awaryjna będzie działać zgodnie z oczekiwaniami, gdy pojawi się rzeczywista potrzeba.
    - Wiertło wykonuje test pracy awaryjnej bez wpływu na środowisko produkcyjne.
5. W przypadku wystąpienia awarii, należy uruchomić pełne pracy awaryjnej na platformie Azure. Można w trybie fail over jednego komputera lub można utworzyć plan odzyskiwania, który działa w trybie fail,200 na wielu komputerach w tym samym czasie.
6. W pracy awaryjnej maszyny wirtualne platformy Azure są tworzone na podstawie danych maszyny wirtualnej na dyskach zarządzanych lub kontach magazynu. Użytkownicy mogą kontynuować uzyskiwanie dostępu do aplikacji i obciążeń z maszyny Wirtualnej platformy Azure
7. Gdy witryna lokalna jest ponownie dostępna, powrót z platformy Azure zakończy się niepowodzeniem.
8. Po ponownym powrocie po awarii i ponownym uruchomieniu z lokacji głównej ponownie należy ponownie rozpocząć replikowanie lokalnych maszyn wirtualnych na platformę Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Skąd mam wiedzieć, czy moje środowisko jest odpowiednie do odzyskiwania po awarii na platformie Azure?

Usługa Site Recovery może replikować dowolne obciążenie uruchomione na obsługiwanej maszynie wirtualnej VMware lub na serwerze fizycznym. Oto rzeczy, które należy sprawdzić w swoim środowisku:

- Jeśli replikujesz maszyny wirtualne VMware, czy używasz odpowiednich wersji serwerów wirtualizacji VMware? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Czy maszyny, które chcesz replikować z uruchomionym systemem operacyjnym? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#replicated-machines).
- Czy w przypadku odzyskiwania po awarii systemu Linux na komputerach z obsługiwanym systemem plików/magazynem gościnnym? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Czy maszyny, które chcesz replikować, są zgodne z wymaganiami platformy Azure? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Czy konfiguracja sieci jest obsługiwana? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#network).
- Czy konfiguracja magazynu jest obsługiwana? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Co jest potrzebne do skonfigurowania na platformie Azure przed rozpoczęciem pracy?

Na platformie Azure należy przygotować następujące elementy:

1. Sprawdź, czy twoje konto platformy Azure ma uprawnienia do tworzenia maszyn wirtualnych na platformie Azure.
2. Utwórz sieć platformy Azure, do którą dołączą maszyny wirtualne platformy Azure, gdy zostaną utworzone na podstawie kont magazynu lub dysków zarządzanych po przełączeniu awaryjnym.
3. Konfigurowanie magazynu usług odzyskiwania platformy Azure dla odzyskiwania witryny. Przechowalnia znajduje się w witrynie Azure portal i jest używana do wdrażania, konfigurowania, organizowania, monitorowania i rozwiązywania problemów z wdrożeniem usługi Site Recovery.

*Potrzebujesz dalszej pomocy?*

Dowiedz się, jak skonfigurować platformę Azure, [weryfikując konto,](tutorial-prepare-azure.md#verify-account-permissions)tworząc [sieć](tutorial-prepare-azure.md#set-up-an-azure-network)i [konfigurując przechowalnię.](tutorial-prepare-azure.md#create-a-recovery-services-vault)



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Co jest potrzebne do skonfigurowania lokalnie przed rozpoczęciem?

Lokalnie oto, co musisz zrobić:

1. Musisz skonfigurować kilka kont:

    - W przypadku replikowania maszyn wirtualnych VMware do odzyskiwania witryny potrzebne jest konto, aby uzyskać dostęp do hostów vCenter Server lub vSphere ESXi w celu automatycznego odnajdywanie maszyn wirtualnych.
    - Konto jest potrzebne do zainstalowania agenta usługi mobilności odzyskiwania witryny na każdym komputerze fizycznym lub maszynie wirtualnej, którą chcesz replikować.

2. Należy sprawdzić zgodność infrastruktury VMware, jeśli wcześniej tego nie zrobiłeś.
3. Upewnij się, że można połączyć się z maszynami wirtualnymi platformy Azure po przełączeniu awaryjnym. Konfigurowanie protokołu RDP na lokalnych komputerach z systemem Windows lub SSH na komputerach z systemem Linux.

*Potrzebujesz dalszej pomocy?*
- Przygotuj konta do [automatycznego wykrywania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) i [instalacji usługi Mobilności](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Sprawdź, czy](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) ustawienia VMware są zgodne.
- [Przygotuj się](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) tak, aby łączyć się na platformie Azure po przełączeniu w stan failover.
- Jeśli chcesz uzyskać bardziej szczegółową pomoc dotyczącą konfigurowania adresowania IP dla maszyn wirtualnych platformy Azure po przełączeniu w stan failover, [przeczytaj ten artykuł](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Jak skonfigurować odzyskiwanie po awarii?

Po utworzeniu platformy Azure i infrastruktury lokalnej można skonfigurować odzyskiwanie po awarii.

1. Aby zrozumieć składniki, które należy wdrożyć, przejrzyj [architekturę VMware na platformie Azure](vmware-azure-architecture.md)oraz [architekturę fizyczną na platformę Azure.](physical-azure-architecture.md) Istnieje wiele składników, więc ważne jest, aby zrozumieć, jak wszystkie pasują do siebie.
2. **Środowisko źródłowe:** W pierwszym kroku wdrażania skonfigurować środowisko źródłowe replikacji. Można określić, co chcesz replikować i gdzie chcesz replikować.
3. **Serwer konfiguracji:** Należy skonfigurować serwer konfiguracji w lokalnym środowisku źródłowym:
    - Serwer konfiguracji jest pojedynczym komputerem lokalnym. W przypadku odzyskiwania po awarii VMware zaleca się wdrożenie go jako maszyny wirtualnej VMware, którą można wdrożyć z szablonu OVF do pobrania.
    - Serwer konfiguracji koordynuje komunikację między lokalną a platformą Azure
    - Kilka innych składników jest uruchamianych na komputerze serwera konfiguracji.
        - Serwer przetwarzania odbiera, optymalizuje i wysyła dane replikacji do konta magazynu pamięci podręcznej na platformie Azure. Obsługuje również automatyczną instalację usługi mobilności na komputerach, które chcesz replikować, i wykonuje automatyczne odnajdowanie maszyn wirtualnych na serwerach VMware.
        - Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.
    - Konfiguracja obejmuje rejestrowanie serwera konfiguracji w przechowalni, pobieranie serwera MySQL i programu VMware PowerCLI oraz określanie kont utworzonych do automatycznego wykrywania i instalacji usługi mobilności.
4. **Środowisko docelowe:** Skonfiguruj docelowe środowisko platformy Azure, określając subskrypcję platformy Azure i ustawienia sieciowe.
5. **Zasady replikacji:** Należy określić sposób replikacji. Ustawienia obejmują, jak często punkty odzyskiwania są tworzone i przechowywane oraz czy należy tworzyć migawki spójne z aplikacjami.
6. **Włącz replikację**. Replikacja jest włączona dla komputerów lokalnych. Jeśli utworzono konto do zainstalowania usługi mobilności, zostanie ono zainstalowane po włączeniu replikacji komputera. 

*Potrzebujesz dalszej pomocy?*

- Aby uzyskać szybki przewodnik po tych krokach, możesz wypróbować nasz [samouczek VMware](vmware-azure-tutorial.md)i [przewodnik po serwerze fizycznym.](physical-azure-disaster-recovery.md)
- [Dowiedz się więcej](vmware-azure-set-up-source.md) o konfigurowaniu środowiska źródłowego.
- [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md) wymaganiach serwera konfiguracji i konfigurowaniu serwera konfiguracji za pomocą szablonu OVF dla replikacji VMware. Jeśli z jakiegoś powodu nie możesz użyć szablonu lub replikujesz serwery fizyczne, [użyj tych instrukcji.](physical-azure-set-up-source.md#set-up-the-source-environment)
- [Dowiedz się więcej](vmware-azure-set-up-target.md) o ustawieniach docelowych.
- [Uzyskaj więcej informacji](vmware-azure-set-up-replication.md) na temat konfigurowania zasad replikacji.
- [Dowiedz się,](vmware-azure-enable-replication.md) jak włączyć replikację i [wykluczyć](vmware-azure-exclude-disk.md) dyski z replikacji.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Coś poszło nie tak, jak rozwiązać problem?

- W pierwszym kroku spróbuj [monitorować wdrożenie,](site-recovery-monitor-and-troubleshoot.md) aby zweryfikować stan zreplikowanych elementów, zadań i problemów z infrastrukturą i zidentyfikować wszelkie błędy.
- Jeśli nie możesz ukończyć replikacji początkowej lub ciągła replikacja nie działa zgodnie z oczekiwaniami, [zapoznaj się z tym artykułem, aby](vmware-azure-troubleshoot-replication.md) uzyskać typowe błędy i wskazówki dotyczące rozwiązywania problemów.
- Jeśli masz problemy z automatyczną instalacją usługi mobilności na komputerach, które chcesz replikować, zapoznaj się z typowymi błędami w [tym artykule](vmware-azure-troubleshoot-push-install.md).
- Jeśli praca awaryjna nie działa zgodnie z oczekiwaniami, sprawdź typowe błędy w [tym artykule](site-recovery-failover-to-azure-troubleshoot.md).
- Jeśli powrót po awarii nie działa, sprawdź, czy problem pojawia się w [tym artykule](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Następne kroki

Z replikacji teraz w miejscu, należy [uruchomić wiertła odzyskiwania po awarii,](tutorial-dr-drill-azure.md) aby upewnić się, że praca awaryjna działa zgodnie z oczekiwaniami. 
