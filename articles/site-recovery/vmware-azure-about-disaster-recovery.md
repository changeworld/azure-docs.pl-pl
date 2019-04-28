---
title: Temat odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Site Recovery.
author: raynew
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: raynew
ms.openlocfilehash: 9d7b94500320315c1379ba3dfb8b6460bc105b49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61272697"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Temat odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure

Ten artykuł zawiera omówienie odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware do platformy Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

## <a name="what-is-bcdr"></a>Co to jest BCDR?

Ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR) odzyskiwania strategii biznesowej pomaga firmie działanie. Podczas planowanych i awarii BCDR przechowuje dane pozostają bezpieczne i dostępne i gwarantuje, że aplikacje mogą nadal działa. Oprócz funkcji BCDR platformy, takich jak parowanie regionalne, a magazyn o wysokiej dostępności platforma Azure oferuje usługi Recovery Services w ramach rozwiązania BCDR. Usługi Recovery services obejmują: 

- [Usługa Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) tworzy kopię zapasową sieci — lokalnych i danych maszyny Wirtualnej platformy Azure. Można utworzyć kopię zapasową plików i folderów, konkretnych obciążeń lub całą maszynę Wirtualną. 
- [Usługa Azure Site Recovery](site-recovery-overview.md) zapewnia odporności i odzyskiwania po awarii dla aplikacji i obciążeń uruchamianych na maszynach lokalnych lub maszyn wirtualnych IaaS platformy Azure. Usługa Site Recovery organizuje replikację i obsługuje tryb failover na platformie Azure w przypadku wystąpienia awarii. Obsługuje ona również odzyskiwanie z platformy Azure do lokacji głównej. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Jak działa usługa Site Recovery? odzyskiwania po awarii

1. Po przygotowaniu platformy Azure i lokacją lokalną, skonfiguruj i Włącz replikację dla maszyn lokalnych.
2. Usługa Site Recovery organizuje replikację początkową maszyny, zgodnie z ustawieniami zasad.
3. Po początkowej replikacji usługa Site Recovery replikuje replikowanie zmian różnicowych do platformy Azure. 
4. Gdy wszystko jest w stanie replikowania zgodnie z oczekiwaniami, możesz uruchomić odzyskiwanie po awarii.
    - Przechodzenie do szczegółów pomaga, upewnij się, że tryb failover będą działać zgodnie z oczekiwaniami, gdy zajdzie potrzeba rzeczywistych.
    - Przechodzenie do szczegółów wykonuje test trybu failover bez wywierania wpływu na środowisko produkcyjne.
5. Jeśli wystąpi awaria, możesz uruchomić pełną przejścia w tryb failover na platformie Azure. Możesz przełączać awaryjnie pojedynczą maszynę lub można utworzyć plan odzyskiwania, który przechodzi w trybie Failover wiele maszyn w tym samym czasie.
6. W tryb failover maszyn wirtualnych platformy Azure są tworzone na podstawie danych maszyny Wirtualnej w Managed disks i kont magazynu. Użytkownicy mogą nadal dostęp do aplikacji i obciążeń maszyny wirtualnej platformy Azure
7. Gdy w lokacji sieci lokalnej będzie znowu dostępna, powrotu po awarii z platformy Azure.
8. Po powrotu po awarii i pracy z witryny głównej raz, uruchom ponownie replikowania lokalnych maszyn wirtualnych do platformy Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Jak sprawdzić, czy moje środowisko jest odpowiedni dla odzyskiwania po awarii na platformie Azure?

Site Recovery może replikować dowolne obciążenia uruchomione na obsługiwanej maszynie Wirtualnej VMware lub serwera fizycznego. Oto czynności, które należy sprawdzić w danym środowisku:

- Jeśli replikujesz maszyny wirtualne VMware, czy używasz właściwych wersji serwerami wirtualizacji oprogramowania VMware? [Tu](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Czy maszyny, którą chcesz replikować z obsługiwanym systemem operacyjnym? [Tu](vmware-physical-azure-support-matrix.md#replicated-machines).
- Podczas odzyskiwania systemu Linux maszyny działają magazynu systemu/gościa obsługiwane plików? [Sprawdź tutaj](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Czy maszyny, które mają być replikowane są zgodne z wymaganiami platformy Azure? [Tu](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Konfiguracja sieci jest obsługiwana? [Tu](vmware-physical-azure-support-matrix.md#network).
- Konfigurację magazynu są obsługiwane? [Tu](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Co należy skonfigurować na platformie Azure, przed rozpoczęciem utworzeniem?

Na platformie Azure, należy przygotować następujące elementy:

1. Sprawdź, czy Twoje konto platformy Azure ma uprawnienia do tworzenia maszyn wirtualnych na platformie Azure.
2. Utwórz sieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure, gdy są tworzone z konta magazynu lub dyski zarządzane po włączeniu trybu failover.
3. Skonfiguruj magazyn usługi Azure Recovery Services dla usługi Site Recovery. Magazyn znajduje się w witrynie Azure portal i służy do wdrażania, konfigurowania, możesz odpowiednio organizować w, monitorowanie i rozwiązywanie problemów z wdrożenia usługi Site Recovery.

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak konfigurowanie platformy Azure przez [weryfikowanie konta](tutorial-prepare-azure.md#verify-account-permissions), tworzenie [sieci](tutorial-prepare-azure.md#set-up-an-azure-network), i [konfigurowania magazynu](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Co należy skonfigurować w środowisku lokalnym przed przystąpieniem?

Lokalne Oto co należy zrobić:

1. Musisz skonfigurować kilka kont:

    - Jeśli replikujesz maszyny wirtualne VMware, usługi Site Recovery uzyskać dostęp do serwera vCenter lub hostami vSphere ESXi jest potrzebne konto do automatycznego wykrywania maszyn wirtualnych.
    - Konto jest niezbędne do zainstalowania agenta usługi Site Recovery Mobility na każdym komputerze fizycznym lub maszyny Wirtualnej, którą chcesz replikować.

2. Musisz sprawdzić zgodność infrastrukturą produktów VMware, jeśli wcześniej nie zrobił.
3. Upewnij się, że można połączyć z maszyn wirtualnych platformy Azure po przejściu w tryb failover. Można skonfigurować protokołu RDP na maszyn Windows lokalnych lub SSH na maszynach z systemem Linux.

*Potrzebujesz dodatkowej pomocy?*
- Przygotowywanie konta do [automatyczne odnajdowanie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) i [instalacji usługi mobilności](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Sprawdź](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) zgodnych ustawień programu VMware.
- [Przygotowanie](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) tak, aby połączyć się na platformie Azure po włączeniu trybu failover.
- Jeśli chcesz, aby szczegółowe pomocy na temat konfigurowania adresów IP maszyn wirtualnych platformy Azure po przejściu w tryb failover [przeczytaj ten artykuł](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Jak skonfigurować odzyskiwanie po awarii?

Po utworzeniu infrastruktury platformy Azure i lokalnie w miejscu, możesz skonfigurować odzyskiwanie po awarii.

1. Aby poznać składniki, które należy wdrożyć, zobacz [architektura VMware – Azure](vmware-azure-architecture.md)i [środowisko fizyczne do platformy Azure architektury](physical-azure-architecture.md). Istnieje wiele składników, więc jest ważne zrozumieć, jak one współdziałają ze sobą.
2. **Środowisko źródłowe**: Pierwszym krokiem we wdrożeniu należy skonfigurować środowiska źródłowego replikacji. Należy określić, co chcesz replikować, i gdzie ma zostać zreplikowana na.
3. **Serwer konfiguracji**: Należy skonfigurować serwer konfiguracji w środowisku źródłowym w środowisku lokalnym:
    - Serwer konfiguracji jest pojedynczy na komputerze lokalnym. Do odzyskiwania po awarii programu VMware firma Microsoft zaleca, wdrożyć go jako maszyny Wirtualnej VMware, które mogą być wdrażane z szablonu pakietu OVF do pobrania.
    - Serwer konfiguracji służy do koordynowania komunikacji między lokalną i platformą Azure
    - Uruchom kilka innych składników na komputerze z serwerem konfiguracji.
        - Serwer przetwarzania odbiera optymalizuje i wysyła dane replikacji do konta magazynu pamięci podręcznej na platformie Azure. Obsługuje także automatycznej instalacji usługi mobilności na maszynach, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie maszyn wirtualnych na serwerach VMware.
        - Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.
    - Konfiguracja obejmuje rejestrowania serwera konfiguracji w magazynie, pobieranie serwera MySQL i program VMware PowerCLI i określania kont utworzonych dla automatyczne odnajdywanie i instalacja usługi mobilności.
4. **Środowisko docelowe**: Należy skonfigurować urządzenie docelowe środowisko platformy Azure, określając Twoich subskrypcji platformy Azure i ustawień sieci.
5. **Zasady replikacji**: Należy określić, jak powinna być wykonywana replikacja. Ustawienia obejmują, jak często punkty odzyskiwania są tworzone i przechowywane, i czy powinny być tworzone migawki spójne z aplikacji.
6. **Włącz replikację**. Należy włączyć replikację dla maszyn lokalnych. Jeśli utworzono konto, aby zainstalować usługę mobilności, następnie zostanie zainstalowana po włączeniu replikacji dla maszyny. 

*Potrzebujesz dodatkowej pomocy?*

- Szybki przewodnik te kroki, możesz wypróbować nasze [samouczek VMware](vmware-azure-tutorial.md), i [wskazówki serwera fizycznego](physical-azure-disaster-recovery.md).
- [Dowiedz się więcej](vmware-azure-set-up-source.md) Konfigurowanie środowiska źródłowego.
- [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md) wymagania dotyczące serwera konfiguracji i konfigurowania serwera konfiguracji za pomocą szablonu pakietu OVF dla replikacji VMware. Jeśli z jakiegoś powodu nie można użyć szablonu lub w przypadku replikowania serwerów fizycznych, [przy użyciu tych instrukcji](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Dowiedz się więcej](vmware-azure-set-up-target.md) o ustawienia obiektu docelowego.
- [Uzyskaj więcej informacji](vmware-azure-set-up-replication.md) o konfigurowaniu zasad replikacji.
- [Dowiedz się,](vmware-azure-enable-replication.md) włączania replikacji i [wykluczyć](vmware-azure-exclude-disk.md) dysków z replikacji.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Wystąpił błąd, jak rozwiązywać problemy?

- Pierwszym krokiem, spróbuj [monitorowania wdrożenia](site-recovery-monitor-and-troubleshoot.md) Sprawdź stan zreplikowane elementy, zadania i problemy dotyczące infrastruktury i zidentyfikować błędy.
- Jeśli nie możesz się ukończenia replikacji początkowej lub trwającej replikacji nie działa zgodnie z oczekiwaniami, [zapoznaj się z tym artykułem](vmware-azure-troubleshoot-replication.md) dla typowych błędów i wskazówki dotyczące rozwiązywania problemów.
- Jeśli występują problemy z automatycznej instalacji usługi mobilności na maszynach, które mają być replikowane, zapoznaj się z typowymi błędami w [w tym artykule](vmware-azure-troubleshoot-push-install.md).
- W przypadku pracy awaryjnej nie działa zgodnie z oczekiwaniami, zaewidencjonuj typowych błędów [w tym artykule](site-recovery-failover-to-azure-troubleshoot.md).
- W przypadku powrotu po awarii nie działa, sprawdź, czy problem jest wyświetlany w [w tym artykule](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Kolejne kroki

Za pomocą replikacji teraz w miejscu, należy [uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md) aby upewnić się, tryb failover działa zgodnie z oczekiwaniami. 
