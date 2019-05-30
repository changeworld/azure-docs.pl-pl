---
title: Konfigurowanie odzyskiwania po awarii do platformy Azure dla dużej liczby maszyn wirtualnych VMware lub serwery fizyczne z usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii do platformy Azure dla dużej liczby lokalnych maszyn wirtualnych z programu VMware lub serwery fizyczne z usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: 593a82221ec94e43d50b7382ba89053d5f68864a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805924"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Konfigurowanie odzyskiwania po awarii na dużą skalę dla serwerów fizycznych i maszyn wirtualnych VMware

W tym artykule opisano sposób konfigurowania odzyskiwania po awarii na platformie Azure w przypadku dużej liczby (> 1000) lokalnych maszyn wirtualnych z programu VMware lub serwerów fizycznych w środowisku produkcyjnym przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.


## <a name="define-your-bcdr-strategy"></a>Definiowanie strategii BCDR

W ramach ciągłości działania i strategia odzyskiwania (BCDR) zdefiniuj cele punktu odzyskiwania (RPO) i cele czasu odzyskiwania (RTO) dla aplikacji biznesowych i obciążeń. Cel czasu odzyskiwania mierzy czas trwania czasu i usługa poziomu, w którym aplikacja biznesowa lub proces musi być przywrócona i dostępne, aby uniknąć problemów ciągłość działalności biznesowej.
- Usługa Site Recovery umożliwia replikację ciągłą w przypadku maszyn wirtualnych VMware i serwerów fizycznych i [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) dla cel czasu odzyskiwania.
- Podczas planowania odzyskiwania po awarii na dużą skalę dla maszyn wirtualnych VMware i ustalenie zasobów platformy Azure, które są potrzebne, można określić wartości RTO, który będzie używany w obliczeniach pojemności.


## <a name="best-practices"></a>Najlepsze rozwiązania

Ogólne najlepsze rozwiązania dotyczące odzyskiwania po awarii na dużą skalę. Następujące najlepsze rozwiązania zostały omówione bardziej szczegółowo w następnej sekcji dokumentu.

- **Określenie wymagań dotyczących docelowego**: Oszacuj się w zakresie pojemności i zasobów na platformie Azure, przed rozpoczęciem konfigurowania odzyskiwania po awarii.
- **Planowanie składniki usługi Site Recovery**: Zorientować się, jakie składniki usługi Site Recovery (serwer konfiguracji, serwerów przetwarzania) konieczną do spełnienia szacowany pojemności.
- **Skonfiguruj co najmniej jednego serwera przetwarzania skalowalnego w poziomie**: Nie należy używać serwera przetwarzania, który działa domyślnie na serwerze konfiguracji. 
- **Uruchom najnowsze aktualizacje**: Zespół usługi Site Recovery wydaje nowe wersje składników usługi Site Recovery w regularnych odstępach czasu i upewnij się, że używasz najnowszej wersji. Aby ułatwić, korzystając z niego, śledzić [what's new](site-recovery-whats-new.md) aktualizacji, a [Włącz i zainstaluj aktualizacje](service-updates-how-to.md) zwalniania.
- **Aktywne monitorowanie**: Podczas odzyskiwania po awarii działanie, należy aktywnie monitorować stan i kondycję replikowanych maszyn i zasoby infrastruktury.
- **Odzyskiwanie po awarii**: Próbne operacje odzyskiwania po awarii należy uruchamiać w regularnych odstępach czasu. To nie wpływa na środowisko produkcyjne, ale zapewnić tryb failover na platformie Azure będzie działać zgodnie z oczekiwaniami w razie potrzeby.



## <a name="gather-capacity-planning-information"></a>Zbierz informacje dotyczące planowania pojemności

Zbierz informacje o środowisku w środowisku lokalnym w celu oceny i Oszacuj, że wymaga pojemności target (Azure).
- Dla oprogramowania VMware uruchamianie Planisty wdrożenia dla maszyn wirtualnych VMware to zrobić.
- Dla serwerów fizycznych Zbierz ręcznie informacje.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Uruchamianie Planisty wdrożenia dla maszyn wirtualnych VMware

Planista wdrożenia służy do zbierania informacji na temat środowiska lokalnego VMware.

- Uruchamianie Planisty wdrożenia podczas okresu, który reprezentuje Typowy współczynnik zmian dla maszyn wirtualnych. Spowoduje to wygenerowanie bardziej dokładne szacunki i zalecenia.
- Firma Microsoft zaleca uruchamianie Planisty wdrożenia na komputerze z serwerem konfiguracji, ponieważ planista oblicza przepływność między serwerem, na którym jest uruchomiony. [Dowiedz się więcej](site-recovery-vmware-deployment-planner-run.md#get-throughput) temat pomiaru przepływności.
- Jeśli nie masz jeszcze skonfigurować serwer konfiguracji:
    - [Zapoznaj się z omówieniem](vmware-physical-azure-config-process-server-overview.md) z składniki usługi Site Recovery.
    - [Konfigurowanie serwera konfiguracji](vmware-azure-deploy-configuration-server.md), aby można było uruchomić planista wdrażania na nim.

Następnie uruchom Planistę w następujący sposób:

1. [Dowiedz się więcej o](site-recovery-deployment-planner.md) Planisty wdrożenia. Możesz pobrać najnowszą wersję z portalu lub [pobrać go bezpośrednio](https://aka.ms/asr-deployment-planner).
2. Przegląd [wymagania wstępne](site-recovery-deployment-planner.md#prerequisites) i [najnowsze aktualizacje](site-recovery-deployment-planner-history.md) dla Planisty wdrożenia i [pobieranie i wyodrębnianie](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) narzędzie.
3. [Uruchamianie Planisty wdrożenia](site-recovery-vmware-deployment-planner-run.md) na serwerze konfiguracji.
4. [Generowanie raportu](site-recovery-vmware-deployment-planner-run.md#generate-report) Podsumowując szacunki i zalecenia.
5. Analizowanie [raport zalecenia](site-recovery-vmware-deployment-planner-analyze-report.md) i [koszt szacunki](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Domyślnie narzędzie jest skonfigurowana do profilowania i generuje raport dla maksymalnie 1000 maszyn wirtualnych. Aby zmienić limit, należy zwiększyć wartość klucza MaxVMsSupported w pliku ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planowanie wymagań docelowego (Azure) i pojemności

Przy użyciu szacunki zebrane i zalecenia, możesz zaplanować dla docelowych zasobów i pojemności. Po przeprowadzeniu Planisty wdrożenia dla maszyn wirtualnych VMware można użyć liczby [raport zalecenia](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) ułatwiające.

- **Zgodne maszyny wirtualne**: Liczba ta umożliwia określenie liczby maszyn wirtualnych, które są gotowe do odzyskiwania po awarii na platformie Azure. Zalecenia dotyczące przepustowości sieci i rdzeni platformy Azure są oparte na ten numer.
- **Wymagana przepustowość sieci**: Należy pamiętać, przepustowości, czego potrzebujesz do replikacji różnicowej zgodnych maszyn wirtualnych. 
    - Podczas uruchamiania Planisty należy określić żądany cel punktu odzyskiwania w ciągu kilku minut. Zalecenia dotyczące pokazują przepustowości wymaganej do osiągnięcia tego celu punktu odzyskiwania przez 100% do 90% czasu. 
    - Zalecenia dotyczące przepustowości sieci wziąć pod uwagę przepustowość potrzebna do łączna liczba serwerów konfiguracji i serwerów przetwarzania, zaleca się w aplikacji Planner.
- **Wymagana liczba rdzeni platformy Azure**: Uwaga liczba rdzeni, których potrzebujesz w docelowym regionie platformy Azure, na podstawie liczba zgodnych maszyn wirtualnych. Jeśli nie masz wystarczającej liczby rdzeni w trybie failover Usługa Site Recovery nie można utworzyć wymagane maszyn wirtualnych platformy Azure.
- **Zalecany rozmiar partii maszyn wirtualnych**: Zalecany rozmiar partii jest oparty na zdolność do ukończenia replikacji początkowej dla partii w ciągu 72 godzin domyślnie, jednocześnie spełniając wymagania dotyczące celu punktu odzyskiwania przez 100%. Wartość godziny może być modyfikowana.

Te zalecenia pozwalają umożliwia planowanie zasobów platformy Azure, przepustowości sieci i przetwarzanie wsadowe maszyny Wirtualnej.

## <a name="plan-azure-subscriptions-and-quotas"></a>Plan subskrypcji platformy Azure i przydziały

Chcemy upewnić się, że dostępnych limitów przydziału w subskrypcji docelowej są wystarczające do obsługi trybu failover.

**Zadanie** | **Szczegóły** | **Akcja**
--- | --- | ---
**Sprawdź rdzeni** | Dostępny limit przydziału rdzeni nie być równa lub przekracza liczby całkowitej docelowych w momencie przejścia w tryb failover, pracy w trybie Failover zakończy się niepowodzeniem. | Dla maszyn wirtualnych VMware Sprawdź, czy masz wystarczającej liczby rdzeni w subskrypcji docelowej, aby spełnić zalecenie core planista wdrażania usługi.<br/><br/> Dla serwerów fizycznych Sprawdź, czy rdzeni platformy Azure spełniają usługi ręczna szacunki.<br/><br/> Aby sprawdzić przydziały w witrynie Azure portal > **subskrypcji**, kliknij przycisk **użycie i przydziały**.<br/><br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) o zwiększenie limitów przydziału.
**Sprawdzanie limitów pracy awaryjnej** | Liczba przejścia w tryb failover nie może przekroczyć limitów trybu failover Usługa Site Recovery. |  W przypadku przejścia w tryb failover zostaną przekroczone, możesz dodać subskrypcji i awaryjnie do wielu subskrypcji lub zwiększyć limit przydziału dla subskrypcji. 


### <a name="failover-limits"></a>Limity pracy awaryjnej

Limity określa liczbę trybu failover, które są obsługiwane przez usługę Site Recovery w ciągu jednej godziny, zakładając, że dla trzech dysków na maszynę.

Co to jest zgodne średniej? Aby uruchomić Maszynę wirtualną platformy Azure, Azure wymaga niektóre sterowniki, które będzie w stanie start rozruchu i usługi takie jak DHCP, należy ustawić do automatycznego uruchamiania.
- Maszyny zgodne już mieć tych ustawień w miejscu.
- Dla maszyn z systemem Windows można proaktywnie sprawdzanie zgodności i zapewnienia ich zgodności, jeśli to konieczne. [Dowiedz się więcej](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Maszyny z systemem Linux są tylko w celu zapewnienia zgodności w momencie przejścia w tryb failover.

**Maszyna jest zgodna z platformą Azure?** | **Limity maszyn wirtualnych platformy Azure (tryb failover dysków zarządzanych)**
--- | --- 
Tak | 2000
Nie | 1000

- Limity przyjęto założenie, to minimalny, inne zadania są wykonywane w regionie docelowym dla subskrypcji.
- Niektóre regiony platformy Azure są mniejsze i może być nieco niższe limity.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planowanie infrastruktury i łączność maszyn wirtualnych

Po włączeniu trybu failover na platformie Azure należy obciążeń działają tak samo, jak w środowisku lokalnym i umożliwić użytkownikom dostęp do obciążenia uruchomione na maszynach wirtualnych platformy Azure.

- [Dowiedz się więcej](site-recovery-active-directory.md#test-failover-considerations) około przechodzenia w tryb failover z infrastruktury lokalnej usługi Active Directory i DNS na platformie Azure.
- [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) dotyczących przygotowywania nawiązać połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Planowanie pojemności źródła i wymagania

Jest ważne, czy masz wystarczające serwerów konfiguracji i serwerów przetwarzania skalowalnego w poziomie w celu spełnienia wymagań w zakresie pojemności. Po rozpoczęciu na dużą skalę wdrożenia początek jeden serwer konfiguracji i serwer pojedynczego procesu skalowania w poziomie. W razie osiągnięcia określonych granicach, Dodaj dodatkowe serwery.

>[!NOTE]
> W przypadku maszyn wirtualnych VMware planista wdrażania sprawia, że niektóre zalecenia dotyczące serwerów konfiguracji i przetwarzania, których potrzebujesz. Firma Microsoft zaleca korzystanie z tabel, o których zawarte w poniższych procedurach, zamiast z zaleceniem planista wdrażania usługi. 


## <a name="set-up-a-configuration-server"></a>Konfigurowanie serwera konfiguracji
 
Pojemność serwera konfiguracji jest zależna od liczby replikowanych maszyn, a nie na podstawie danych współczynnik zmian danych. Aby ustalić, czy wymagany jest serwer dodatkowe czynności konfiguracyjne, użyj tych zdefiniowanych limitów maszyny Wirtualnej.

**CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Replikowane maszyny limit**
 --- | --- | --- | ---
8 wirtualnych procesorów CPU<br> gniazda 2 * 4 rdzenie @ 2,5 Ghz | 16 GB | 600 TB | Do 550 maszyn<br> Przyjęto założenie, że każda maszyna ma trzy dyski 100 GB.

- Limity te są oparte na serwerze konfiguracji, przy użyciu szablonu OVF.
- Limity przyjęto założenie, że nie używasz serwera przetwarzania, który działa domyślnie na serwerze konfiguracji.

Jeśli potrzebujesz dodać nowy serwer konfiguracji, wykonaj następujące instrukcje:

- [Konfigurowanie serwera konfiguracji](vmware-azure-deploy-configuration-server.md) odzyskiwania po awarii maszyny Wirtualnej VMware, przy użyciu szablonu OVF.
- [Konfigurowanie serwera konfiguracji](physical-azure-set-up-source.md) ręcznie dla serwerów fizycznych lub w przypadku wdrożeń programu VMware, które nie mogą używać szablonu OVF.

Po skonfigurowaniu serwera konfiguracji należy pamiętać, że:

- Po skonfigurowaniu serwera konfiguracji należy wziąć pod uwagę subskrypcji i magazynu, w którym znajduje się on, ponieważ nie można zmienić po zakończeniu instalacji. Jeśli trzeba zmienić magazyn, należy usunąć skojarzenie serwera konfiguracji z magazynu i ponownie je zarejestrować. Spowoduje to zatrzymanie replikacji maszyn wirtualnych w magazynie.
- Jeśli chcesz skonfigurować serwer konfiguracji z wieloma kartami sieciowymi, należy to zrobić podczas. Nie możesz tego zrobić po zarejestrowaniu serwera konfiguracji w magazynie.

## <a name="set-up-a-process-server"></a>Konfigurowanie serwera przetwarzania

Proces serwera jest pojemność przez szybkości współczynnika zmian danych, a nie przez liczbę maszyn włączona replikacja.

- W przypadku dużych wdrożeń powinien zawsze mieć co najmniej jednego serwera przetwarzania skalowalnego w poziomie.
- Aby ustalić, czy potrzebujesz dodatkowych serwerów, skorzystaj z poniższej tabeli.
- Zaleca się dodawania serwera z najwyższą specyfikacji. 


**CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Współczynnik zmian danych**
 --- | --- | --- | --- 
12 procesorów wirtualnych Vcpu<br> gniazda 2 * 6 rdzeni @ 2,5 Ghz | 24 GB | 1 GB | Do 2 TB dziennie

Skonfiguruj serwer przetwarzania w następujący sposób:

1. Przegląd [wymagania wstępne](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Zainstaluj serwer w [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), lub z [wiersza polecenia](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Konfigurowanie maszyn replikowanych do użycia nowego serwera. Jeśli masz już maszyn replikowanych:
    - Możesz [przenieść](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) cały proces obciążenie serwera do nowego serwera przetwarzania.
    - Możesz też [przenieść](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) określonych maszyn wirtualnych do nowego serwera przetwarzania.



## <a name="enable-large-scale-replication"></a>Włącz replikację na dużą skalę

Po planowanie pojemności i wdrażanie wymaganych składników i infrastruktury, należy włączyć replikację dla dużej liczby maszyn wirtualnych.

1. Posortuj maszyn w partie. Włączanie replikacji maszyn wirtualnych w partii, a następnie przejść do następnej partii.

    - W przypadku maszyn wirtualnych VMware można użyć [zalecany rozmiar partii maszyn wirtualnych](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) raportu planisty wdrożenia usługi.
    - Dla komputerów fizycznych, firma Microsoft zaleca zidentyfikować partie na podstawie maszyn, które mają podobne rozmiar i ilości danych, a dostępnej przepływności sieci. Celem jest maszyny usługi batch, które są prawdopodobnie na zakończenie początkowej replikacji w całym tyle samo czasu.
    
2. Współczynnik zmian na dysku dla maszyny jest duża lub przekracza limit w thePlanner wdrożenia, można przenieść niekrytyczne plików, które nie ma potrzeby zreplikowania maszyny (na przykład zrzuty dzienników lub pliki tymczasowe). W przypadku maszyn wirtualnych programu VMware mogą przenosić tych plików na osobny dysk, a następnie [Wyklucz ten dysk](vmware-azure-exclude-disk.md) z replikacji.
3. Przed włączeniem replikacji Sprawdź maszyn, które spełniają wymagania [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Skonfiguruj zasady replikacji dla [maszyny wirtualne VMware](vmware-azure-set-up-replication.md#create-a-policy) lub [serwerów fizycznych](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Włączanie replikacji na potrzeby [maszyny wirtualne VMware](vmware-azure-enable-replication.md) lub [serwerów fizycznych](physical-azure-disaster-recovery.md#enable-replication). Dotyczącego replikacji początkowej dla wybranych maszyn.

## <a name="monitor-your-deployment"></a>Monitorowanie wdrożenia

Należy rozpocząć replikację dla pierwszej partii maszyn wirtualnych, aby rozpocząć, monitorowanie wdrożenia w następujący sposób:  

1. Przypisywanie administratora odzyskiwania po awarii, do monitorowania stanu kondycji replikowanych maszyn.
2. [Monitorowanie zdarzeń dotyczących](site-recovery-monitor-and-troubleshoot.md) zreplikowane elementy i infrastruktury.
3. [Monitorowanie kondycji](vmware-physical-azure-monitor-process-server.md) serwerów proces skalowania w poziomie.
4. Zarejestruj się uzyskać [wiadomości e-mail z powiadomieniami](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) dla zdarzeń, ułatwiają monitorowanie.
5. Należy przeprowadzić zwykłe [odzyskiwanie po awarii](site-recovery-test-failover-to-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="plan-for-large-scale-failovers"></a>Planowanie przejścia w tryb failover na dużą skalę

W przypadku awarii może być konieczne w trybie Failover dużej liczby maszyn/obciążeń na platformie Azure. Przygotowanie do tego typu zdarzenia w następujący sposób.

Można przygotować wcześniej do trybu failover w następujący sposób:

- [Przygotowywanie infrastruktury i maszyny wirtualne](#plan-infrastructure-and-vm-connectivity) tak, aby obciążenia będą dostępne po włączeniu trybu failover i dlatego, że użytkownicy mogą uzyskiwać dostęp do maszyn wirtualnych platformy Azure.
- Uwaga [limity pracy awaryjnej](#failover-limits) we wcześniejszej części tego dokumentu. Upewnij się, że Twoja praca awaryjna będzie mieszczą się w tych limitów.
- Uruchom zwykłe [odzyskiwanie po awarii](site-recovery-test-failover-to-azure.md). Ćwiczenia ułatwiają:
    - Luki w danym wdrożeniu, przed włączeniem trybu failover.
    - Szacowany czas RTO end-to-end dla aplikacji.
    - Oszacuj end-to-end cel punktu odzyskiwania dla obciążeń.
    - Określ zakres adresów IP powoduje konflikt.
    - Jeśli uruchomisz awarii, firma Microsoft zaleca, nie używaj sieciach produkcyjnych dla awarii, unikać używania tej samej nazwy podsieci w sieciach produkcyjnych i testowych i czyszczenie testu pracy w trybie Failover, po każdym testowania odzyskiwania po awarii.

Aby uruchomić tryb failover na dużą skalę, zalecamy wykonanie poniższych czynności:

1. Tworzenie planów odzyskiwania dla trybu failover obciążenia.
    - Każdy plan odzyskiwania można wyzwolić tryb failover, maksymalnie 50 maszyn.
    - [Dowiedz się więcej](recovery-plan-overview.md) informacje o planach odzyskiwania.
2. Dodaj skrypty elementu runbook usługi Azure Automation do planów odzyskiwania, aby zautomatyzować dowolne zadania ręcznie na platformie Azure. Typowe zadania obejmują, konfigurowanie równoważenia obciążenia, aktualizowanie DNS itp. [Dowiedz się więcej](site-recovery-runbook-automation.md)
2. Przed włączeniem trybu failover należy przygotować maszyn Windows, aby były zgodne ze środowiskiem usługi Azure. [Limity pracy awaryjnej](#plan-azure-subscriptions-and-quotas) są wyższe dla maszyn, które są zgodne. [Dowiedz się więcej](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) o elementach runbook.
4.  Wyzwolić tryb failover przy użyciu [Start AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) polecenia cmdlet programu PowerShell, wraz z planu odzyskiwania.



## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Monitor Usługa Site Recovery](site-recovery-monitor-and-troubleshoot.md)
