---
title: Migrowanie maszyn wirtualnych z systemem Windows do usługi Azure Premium Storage przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Migrowanie istniejących maszyn wirtualnych do usługi Azure Premium Storage przy użyciu usługi Site Recovery. Usługa Premium Storage oferuje obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla wyjścia — dużych obciążeń wejścia/uruchomione na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 5744ee76d36b1cea256cd2594bcbc07c954f38f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60849889"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrowanie do usługi Premium Storage przy użyciu usługi Azure Site Recovery

[Usługa Azure premium SSD](disks-types.md) dostarczać obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych (VM), które są uruchomione I O dużych obciążeń wejścia /. Ten przewodnik ułatwi Ci migracji dysków maszyn wirtualnych z konta magazynu w warstwie standardowa do konta magazynu premium storage za pomocą [usługi Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Usługa Site Recovery jest usługą platformy Azure, którzy przyczyniają się do strategii dotyczącej ciągłości biznesowej i odzyskiwania po awarii poprzez organizowanie replikacji lokalnych serwerów fizycznych i maszyn wirtualnych do chmury (Azure) lub do dodatkowego centrum danych. W przypadku wystąpienia awarii w lokalizacji podstawowej, przełączysz się lokalizacji dodatkowej, aby aplikacje i obciążenia, które są dostępne. Powrotu po awarii do lokalizacji głównej gdy powraca do normalnego działania. 

Usługa Site Recovery zapewnia testowych trybów Failover odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Możesz uruchomić tryb failover z minimalną utratą danych (w zależności od częstotliwości replikacji) w razie nieoczekiwanych awarii. W scenariuszu migracji do usługi Premium Storage można używać [trybu failover w usłudze Site Recovery](../../site-recovery/site-recovery-failover.md) migrację dyski docelowe do konta magazynu premium storage.

Zalecamy przeprowadzenie migracji do usługi Premium Storage przy użyciu Site Recovery, ponieważ ta opcja zapewnia minimalnych przestojach. Ta opcja pozwala uniknąć również wykonywanie ręczne kopiowanie dysków i tworzenie nowych maszyn wirtualnych. Usługa Site Recovery systematycznie skopiuje dyski i tworzyć nowe maszyny wirtualne podczas pracy awaryjnej. 

Usługa Site Recovery obsługuje wiele typów przejścia w tryb failover przy minimalnym lub bez przestojów. Aby zaplanować przestój usługi i Oszacuj utraty danych, zobacz [typach trybu failover w usłudze Site Recovery](../../site-recovery/site-recovery-failover.md). Jeśli użytkownik [przygotowanie do połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover](../../site-recovery/vmware-walkthrough-overview.md), należy nawiązać połączenie z maszyną wirtualną Azure przy użyciu protokołu RDP po włączeniu trybu failover.

![Diagram odzyskiwania po awarii][1]

## <a name="azure-site-recovery-components"></a>Składniki usługi Azure Site Recovery

Te składniki usługi Site Recovery mają zastosowanie do danego scenariusza migracji:

* **Serwer konfiguracji** jest Maszyną wirtualną platformy Azure służy do koordynowania komunikacji, który zarządza procesami replikację i odzyskiwanie danych. Na tej maszynie Wirtualnej, należy uruchomić plik konfiguracji pojedynczego, aby zainstalować serwer konfiguracji i dodatkowych składników, o nazwie serwera przetwarzania jako brama replikacji. Przeczytaj o [wymagania wstępne serwera konfiguracji](../../site-recovery/vmware-walkthrough-overview.md). Możesz skonfigurować serwer konfiguracji tylko jeden raz i używać go do wszystkich migracji do tego samego regionu.

* **Serwer przetwarzania** jest brama replikacji który: 

  1. Odbiera dane replikacji z źródłowe maszyny wirtualne.
  2. Optymalizuje dane z pamięci podręcznej, kompresji i szyfrowania.
  3. Wysyła dane do konta magazynu. 

  Ponadto obsługuje instalację wypychaną usługi mobility na źródłowe maszyny wirtualne i przeprowadza automatyczne odnajdywanie źródłowe maszyny wirtualne. Domyślny serwer przetwarzania jest zainstalowany na serwerze konfiguracji. Można wdrażać autonomiczne dodatkowych serwerów przetwarzania skalowania wdrożenia. Przeczytaj o [najlepsze rozwiązania dotyczące wdrażania serwera przetwarzania](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) i [wdrażanie dodatkowych serwerów przetwarzania](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Możesz skonfigurować serwer przetwarzania tylko raz i używać go do wszystkich migracji do tego samego regionu.

* **Usługa mobilności** to składnik, który jest wdrażany na każdej maszynie Wirtualnej standardowych, które mają być replikowane. Jego służy do przechwytywania zapisów danych na maszyna wirtualna w warstwie standardowa i przekazuje je do serwera przetwarzania. Przeczytaj o [replikowane wymagania wstępne dotyczące maszyny](../../site-recovery/vmware-walkthrough-overview.md).

Ten rysunek przedstawia interakcje tych składników:

![Interakcja składników usługi Site Recovery][15]

> [!NOTE]
> Usługa Site Recovery nie obsługuje migracji dyski funkcji miejsca do magazynowania.

Dla dodatkowych składników w innych sytuacjach [architekturze scenariusza](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Poniżej przedstawiono wymagania platformy Azure, w tym scenariuszu migracji:

* Subskrypcja platformy Azure.
* Konto magazynu premium platformy Azure do przechowywania replikowanych danych.
* Usługa Azure sieci wirtualnej, do którego będą łączyć z maszyn wirtualnych, w przypadku ich utworzenia w trybie failover. Sieć wirtualna platformy Azure musi być w tym samym regionie, w którym działa usługa Site Recovery.
* Konto usługi Azure standard storage do przechowywania dzienników replikacji. Może to być tego samego konta magazynu dla dysków maszyn wirtualnych, do których należą migrowane.

## <a name="prerequisites"></a>Wymagania wstępne

* Opis składników scenariusza migracji istotne w poprzedniej sekcji.
* Plan usługi Czas przestoju, zapoznając [trybu failover w usłudze Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Kroki konfiguracji i migracji

Usługa Site Recovery umożliwia migrowanie maszyn wirtualnych IaaS platformy Azure między regionami lub w ramach tego samego regionu. Poniższe instrukcje są dostosowane w tym scenariuszu migracji z artykułu [replikować maszyny wirtualne VMware lub serwerów fizycznych na platformę Azure](../../site-recovery/vmware-walkthrough-overview.md). Wykonaj poniższe łącza, aby uzyskać szczegółowy opis kroków, oprócz instrukcje w tym artykule.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: Tworzenie magazynu usługi Recovery Services

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz **Utwórz zasób** > **zarządzania** > **Backup i Site Recovery (OMS)** . Alternatywnie, można wybrać **Przeglądaj** > **magazyn usługi Recovery Services** > **Dodaj**.
   >[!NOTE]
   >Backup i Site Recovery była wcześniej częścią ![pakiet OMS](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand).
1. Określ region, który zostanie zreplikowana maszyn wirtualnych. Na potrzeby migracji, w tym samym regionie wybierz region, w których są Twoje źródłowe maszyny wirtualne i konta magazynu źródłowej. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: Wybranie celów ochrony 

1. Na maszynie Wirtualnej, którym chcesz zainstalować serwer konfiguracji, otwórz [witryny Azure portal](https://portal.azure.com).
2. Przejdź do **Magazyny usługi Recovery Services** > **ustawienia** > **Site Recovery** > **krok 1: Przygotowanie infrastruktury** > **Cel ochrony**.

   ![Przejście do okienka cel ochrony][2]

3. W obszarze **cel ochrony**, w pierwszej listy rozwijanej wybierz **na platformę Azure**. Na drugiej liście rozwijanej wybierz **bez wirtualizacji / inne**, a następnie wybierz pozycję **OK**.

   ![Okienko cel ochrony z wypełnione pola][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: Konfigurowanie środowiska źródłowego (serwer konfiguracji)

1. Pobierz **usługi Azure Site Recovery Unified Setup** i klucz rejestracji magazynu, przechodząc do **przygotowanie infrastruktury** > **przygotowywanie źródła**  >  **Dodaj serwer** okienka. 
 
   Konieczne będzie klucz rejestracji magazynu, aby uruchomić ujednoliconego Instalatora. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Przejście do okienka Dodaj serwer][4]

2. W **Dodaj serwer** okienko, Dodaj serwer konfiguracji.

   ![Dodawanie serwera okienka wybranego serwera konfiguracji][5]

3. Na maszynie Wirtualnej, którego używasz jako serwera konfiguracji Uruchom ujednoliconego Instalatora, aby zainstalować serwer konfiguracji i serwerem przetwarzania. Możesz [przeprowadzenie zrzuty ekranu](../../site-recovery/vmware-walkthrough-overview.md) do ukończenia instalacji. Mogą odwoływać się do poniższych zrzutach ekranu kroki określone w tym scenariuszu migracji.

   1. W **przed rozpoczęciem**, wybierz opcję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.

      ![Zanim rozpoczniesz][6]

   2. W **rejestracji**Wyszukaj i wybierz klucz rejestracji pobrany z magazynu.

      ![Strona rejestracji][7]

   3. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. W tym scenariuszu migracji wybierz **nie**.

      ![Środowisko strony szczegółów][8]

4. Po zakończeniu instalacji, wykonaj następujące czynności **serwera usługi Microsoft Azure Site Recovery konfiguracji** okna:
 
   1. Użyj **Zarządzanie kontami** kartę, aby utworzyć konto tego usługa Site Recovery można użyć do automatycznego odnajdowania. (W tym scenariuszu o ochronie komputerów fizycznych, konfigurowania konta nie ma znaczenia, ale potrzebujesz co najmniej jedno konto, aby włączyć jedną z następujących czynności. W tym przypadku można nazwę konta i hasło jako.) 
   2. Użyj **rejestracja w magazynie** kartę, aby przekazać plik poświadczeń magazynu.

      ![Kartę rejestracja w magazynie][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: Konfigurowanie środowiska docelowego

Wybierz **przygotowanie infrastruktury** > **docelowej**i określ modelu wdrażania, których chcesz użyć dla maszyn wirtualnych po pracy awaryjnej. Możesz wybrać **klasycznego** lub **usługi Resource Manager**, w zależności od danego scenariusza.

![W okienku docelowego][10]

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. 

> [!NOTE]
> Jeśli używasz konta usługi premium storage dla replikowanych danych, musisz skonfigurować dodatkowe standardowe konto magazynu do przechowywania dzienników replikacji.

### <a name="step-5-set-up-replication-settings"></a>Krok 5. Konfigurowanie ustawień replikacji

Aby sprawdzić, czy serwer konfiguracji jest pomyślnie skojarzone z zasadami replikacji, które tworzysz, wykonaj [Konfigurowanie ustawień replikacji](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Krok 6: Planowanie pojemności

1. Użyj [planowania pojemności](../../site-recovery/site-recovery-capacity-planner.md) Aby dokładnie oszacować przepustowość sieci, magazynu i innych wymagań, aby spełnić swoje replikacji. 
2. Gdy wszystko będzie gotowe, wybierz pozycję **tak, ma to zostało zrobione** w **czy ukończono Planowanie pojemności?** .

   ![Pole potwierdzenia ukończono Planowanie pojemności][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: Instalowanie usługi mobilności i włączanie replikacji

1. Istnieje możliwość [instalacja wypychana](../../site-recovery/vmware-walkthrough-overview.md) źródłowych maszyn wirtualnych lub do [ręcznie zainstalować usługę mobilności](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) na źródłowym maszynom wirtualnym. Możesz znaleźć wymaganie wypychanie instalacji i ścieżkę ręczne Instalatora w podany link. Jeśli wykonujesz ręcznej instalacji może być konieczne użyć wewnętrznego adresu IP można znaleźć serwera konfiguracji.

   ![Strona Szczegóły serwera konfiguracji][12]

   Maszyna wirtualna trybie failed-over będzie mieć dwa dyski tymczasowe: jeden z podstawowej maszyny Wirtualnej, a druga utworzony podczas aprowizowania maszyny Wirtualnej w regionie odzyskiwania. Aby wykluczyć dysk tymczasowy przed włączeniem replikacji, należy zainstalować usługi mobilności, przed włączeniem replikacji. Aby dowiedzieć się więcej na temat wykluczania dysku tymczasowego, zobacz [wykluczanie dysków z replikacji](../../site-recovery/vmware-walkthrough-overview.md).

2. Aby włączyć replikację:
   1. Wybierz **Replikowanie aplikacji** > **źródła**. Po włączeniu replikacji po raz pierwszy, wybierz **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn.
   2. W kroku 1, skonfiguruj **źródła** jako serwer przetwarzania.
   3. W kroku 2 Określ model wdrażania po przełączeniu w tryb failover, konto magazynu premium storage do migracji do konta magazynu w warstwie standardowa, aby zapisać dzienniki i sieć wirtualna nie.
   4. W kroku 3 należy dodać chronionych maszyn wirtualnych przy użyciu adresu IP. (Może być konieczne wewnętrzny adres IP, aby je znaleźć).
   5. W kroku 4 należy skonfigurować właściwości, wybierając pozycję konta, które należy wcześniej skonfigurować na serwerze przetwarzania.
   6. W kroku 5 wybierz zasady replikacji, który został wcześniej utworzony "krok 5: Konfigurowanie ustawień replikacji."
   7. Kliknij przycisk **OK**.

   > [!NOTE]
   > Gdy w Maszynie wirtualnej platformy Azure jest bez alokacji, a następnie uruchomić go ponownie, nie ma żadnej gwarancji, że otrzyma ten sam adres IP. Jeśli zmieni adres IP serwera konfiguracji serwera i przetwarzanie lub chronionych maszyn wirtualnych platformy Azure, replikacji, w tym scenariuszu, mogą nie działać poprawnie.

   ![Włącz replikację okienko ze źródłem wybrane][13]

Podczas projektowania środowiska usługi Azure Storage, zaleca się używać oddzielne konta usługi storage dla każdej maszyny Wirtualnej w zestawie dostępności. Firma Microsoft zaleca, postępuj zgodnie z najlepszym rozwiązaniem w warstwie magazynu do [używanie wielu kont magazynu dla każdego zestawu dostępności](../linux/manage-availability.md). Dystrybucja dysków maszyn wirtualnych na wielu kontach magazynu pomaga zwiększyć dostępność magazynu i dystrybuuje we/wy w infrastrukturze usługi Azure storage.

W przypadku maszyn wirtualnych w zestawie dostępności, zamiast replikować dysków wszystkich maszyn wirtualnych na jedno konto magazynu, zdecydowanie zaleca się migracja wielu maszyn wirtualnych, wiele razy. Dzięki temu maszyny wirtualne w tym samym zestawie dostępności nie mają jednego konta magazynu. Użyj **włączania replikacji** okienko, aby skonfigurować docelowe konto magazynu dla każdej maszyny Wirtualnej po jednej naraz.
 
Można wybrać model wdrożenia po przejściu do trybu failover zgodnie z Twoimi wymaganiami. Jeśli wybierzesz usługi Azure Resource Manager jako model wdrażania po przełączeniu w tryb failover możesz przełączać awaryjnie maszyny Wirtualnej (Resource Manager) do maszyny Wirtualnej (Resource Manager), lub możesz przełączać awaryjnie maszyny Wirtualnej (model klasyczny) do maszyny Wirtualnej (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Krok 8: Wykonywanie próby przejścia w tryb failover

Aby sprawdzić, czy usługi replikacji jest pełna, wybierz wystąpienie usługi Site Recovery, a następnie wybierz pozycję **ustawienia** > **zreplikowane elementy**. Zostanie wyświetlony stan i procent proces replikacji. 

Po replikacji początkowej zakończeniu uruchomić testowy tryb failover, aby zweryfikować działanie strategii replikacji. Aby uzyskać szczegółowy opis kroków testu trybu failover, zobacz [uruchomić test trybu failover w usłudze Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Przed uruchomieniem dowolnego trybu failover upewnij się, że maszyny wirtualne i strategii replikacji spełniają wymagania. Aby uzyskać więcej informacji na temat uruchamiania testu trybu failover, zobacz [testowanie trybu failover na platformie Azure w usłudze Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Można wyświetlić stan usługi testowania trybu failover w **ustawienia** > **zadania** > *YOUR_FAILOVER_PLAN_NAME*. W okienku można zobaczyć podział kroków i Powodzenie/niepowodzenie wyników. Jeśli testowy tryb failover nie powiedzie się na dowolnym etapie, zaznacz krok, sprawdź komunikat o błędzie. 

### <a name="step-9-run-a-failover"></a>Krok 9: Uruchamianie trybu failover

Po przeprowadzeniu testu trybu failover zostanie zakończona, należy uruchomić tryb failover do migracji dysków do usługi Premium Storage, a następnie do replikowania wystąpień maszyn wirtualnych. Wykonaj szczegółowe instrukcje zostały podane w [przejściu w tryb failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Pamiętaj o wybraniu **zamykają maszyny wirtualne i zsynchronizuj najnowsze dane**. Ta opcja określa, czy usługa Site Recovery należy spróbować zamknąć chronionych maszyn wirtualnych i synchronizacji danych, dzięki czemu najnowsza wersja danych zostaną przełączone w tryb failover. Jeśli nie zaznaczysz tej opcji lub próba nie powiedzie, pracy w trybie failover będzie z najnowszego dostępnego punktu odzyskiwania dla maszyny Wirtualnej. 

Usługa Site Recovery utworzy wystąpienia maszyny Wirtualnej, którego typem jest taka sama jak lub podobne do maszyny Wirtualnej na obsługującą magazyn Premium. Wydajność i ceny różnych wystąpień maszyn wirtualnych można sprawdzić, przechodząc do [cennik maszyn wirtualnych Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [cennik maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Czynności wykonywane po migracji

1. **Konfigurowanie replikowane maszyny wirtualne do zestawu, jeśli ma to zastosowanie dostępności**. Usługa Site Recovery nie obsługuje migracji maszyn wirtualnych wraz z zestawem dostępności. W zależności od wdrożenia zreplikowanej maszyny Wirtualnej wykonaj jedną z następujących czynności:
   * Dla maszyny Wirtualnej utworzone za pomocą klasycznego modelu wdrażania: Dodaj maszynę Wirtualną do zestaw dostępności w portalu Azure. Aby uzyskać szczegółowy opis kroków, przejdź do [Dodaj istniejącą maszynę wirtualną do zestawu dostępności](../linux/classic/configure-availability-classic.md).
   * Dla maszyny Wirtualnej utworzone za pomocą modelu wdrażania usługi Resource Manager: Zapisz konfigurację maszyny Wirtualnej i następnie usunięcie i ponowne utworzenie maszyn wirtualnych w zestawie dostępności. Aby to zrobić, należy użyć skryptu w [zestawu usługi Azure Resource Manager zestawu dostępności maszyny Wirtualnej](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Przed uruchomieniem tego skryptu Sprawdź swoje ograniczenia, a następnie zaplanować przestój usługi.

2. **Usuń stare maszyny wirtualne i dyski**. Upewnij się, że dyski w warstwie Premium są spójne z dysków źródłowych i że nowych maszyn wirtualnych wykonaj taką samą funkcję jak źródłowe maszyny wirtualne. Usuń maszynę Wirtualną, a następnie usuń dyski z kont magazynu źródła w witrynie Azure portal. Jeśli występuje problem, które dysk nie jest usunięte, mimo że można usunąć maszyny Wirtualnej, zobacz [Rozwiązywanie problemów z błędami usuwania zasobów magazynu](storage-resource-deletion-errors.md).

3. **Wyczyść infrastruktury usługi Azure Site Recovery**. Jeśli usługa Site Recovery nie jest już potrzebny, można wyczyścić swoją infrastrukturę. Usuń zreplikowane elementy, serwer konfiguracji i zasady odzyskiwania, a następnie usuń magazyn usługi Azure Site Recovery.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Monitorowanie i rozwiązywanie problemów z ochroną maszyn wirtualnych i serwerów fizycznych](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum usługi Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Kolejne kroki

W określonych scenariuszach Migrowanie maszyn wirtualnych zobacz następujące zasoby:

* [Migrowanie maszyn wirtualnych platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Windows Server na platformie Azure](upload-generalized-managed.md)
* [Migrowanie maszyn wirtualnych ze środowiska Amazon AWS na platformę Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zobacz też następujące zasoby, aby dowiedzieć się więcej na temat usługi Azure Storage i Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Usługa Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
