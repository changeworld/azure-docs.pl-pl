---
title: Migracja maszyn wirtualnych systemu Windows do magazynu Azure Premium z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Migracji istniejących maszyn wirtualnych do usługi Azure Premium Storage za pomocą usługi Site Recovery. Magazyn w warstwie Premium oferuje obsługę dysków o wysokiej wydajności i małych opóźnieniach/O wykonujących obciążeń uruchomionych na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
cloud: Azure
documentationcenter: na
author: luywang
manager: jeconnoc
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.openlocfilehash: 171b0f938e44218d11cfb001e3f58ebd0feb35fd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migracja do magazynu Premium za pomocą usługi Azure Site Recovery

[Usługa Azure Premium Storage](premium-storage.md) zapewnia obsługę wysokiej wydajności i małych opóźnień dysków dla maszyny wirtualnej (VM), które są uruchomione/O wykonujących obciążeń. Ten przewodnik pomaga migracji dysków maszyny Wirtualnej z konta magazynu w warstwie standardowa do konta magazynu premium za pomocą [usługi Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Usługa Site Recovery jest usługą platformy Azure, która wspiera strategię ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii poprzez organizowanie replikacji lokalnych serwerów fizycznych i maszyn wirtualnych do chmury (Azure) lub dodatkowego centrum danych. W przypadku wystąpienia awarii w lokalizacji głównej, należy przełączyć lokalizacji dodatkowej, aby aplikacje i obciążenia, które są dostępne. Możesz powrót po awarii do lokalizacji głównej gdy powróci ona do normalnego działania. 

Usługa Site Recovery zapewnia testowy tryb failover do obsługi testowanie odzyskiwania po awarii bez wywierania wpływu na środowiska produkcyjne. Można uruchomić tryb failover minimalna utrata danych (w zależności od częstotliwości replikacji) dla nieoczekiwanych awarii. W scenariuszu migracji do magazyn w warstwie Premium, można użyć [pracy awaryjnej w usłudze Site Recovery](../../site-recovery/site-recovery-failover.md) migracji dysków docelowych do konta magazynu w warstwie premium.

Zaleca się migrowanie do magazynu Premium za pomocą usługi Site Recovery, ponieważ ta opcja zapewnia minimalnym czasem przestojów. Ta opcja pozwala również uniknąć wykonywania ręczne kopiowanie dysków i tworzenie nowych maszyn wirtualnych. Usługa Site Recovery systematycznie skopiuje dysków i tworzenie nowych maszyn wirtualnych w trybie failover. 

Usługa Site Recovery obsługuje kilka typów trybu failover z minimalnym lub bez przestojów. Zaplanowaniu czasu na przestoje i ocenić utraty danych, zobacz [typach trybu failover w usłudze Site Recovery](../../site-recovery/site-recovery-failover.md). Jeśli użytkownik [przygotowanie do połączenia z maszynami wirtualnymi Azure po pracy awaryjnej](../../site-recovery/vmware-walkthrough-overview.md), powinno być możliwe do połączenie z maszyną Wirtualną Azure za pomocą protokołu RDP po pracy awaryjnej.

![Diagram odzyskiwania po awarii][1]

## <a name="azure-site-recovery-components"></a>Składniki usługi Azure Site Recovery

Składniki usługi Site Recovery mają zastosowanie w tym scenariuszu migracji:

* **Serwer konfiguracji** jest maszyny Wirtualnej platformy Azure, koordynowania komunikacji, który zarządza procesami replikacji i odzyskiwania danych. Na tej maszynie Wirtualnej, należy uruchomić plik konfiguracji pojedynczego, aby zainstalować serwer konfiguracji i dodatkowych składników, nazywany serwerem procesu, jako brama replikacji. Przeczytaj informacje o [wymagania wstępne dotyczące konfiguracji serwera](../../site-recovery/vmware-walkthrough-overview.md). Możesz skonfigurować serwer konfiguracji tylko raz i używać go do wszystkich migracji do tego samego regionu.

* **Serwer przetwarzania** jest brama replikacji który: 

  1. Odbiera dane replikacji ze źródła maszyn wirtualnych.
  2. Optymalizuje dane z pamięci podręcznej, kompresji i szyfrowania.
  3. Wysyła dane do konta magazynu. 

  Również obsługę instalacji wypychanej usługi mobilności ze źródłem maszyn wirtualnych i przeprowadza automatyczne odnajdywanie źródłowe maszyny wirtualne. Domyślny serwer przetwarzania jest zainstalowany na serwerze konfiguracji. Można wdrożyć dodatkowe autonomicznych serwerów procesu skalowania wdrożenia. Przeczytaj informacje o [najlepszych rozwiązań dotyczących wdrażania serwera procesu](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) i [wdrażanie serwerów dodatkowych procesów](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Możesz skonfigurować serwer przetwarzania tylko raz i używać go do wszystkich migracji do tego samego regionu.

* **Usługa mobilności** jest składnikiem, który jest wdrażany w każdej standardowe maszynę Wirtualną, która ma zostać zreplikowana. Go przechwytywania zapisów danych na Maszynie wirtualnej standardowe i przekazuje je do serwera przetwarzania. Przeczytaj informacje o [replikowane wymagania wstępne dotyczące maszyny](../../site-recovery/vmware-walkthrough-overview.md).

Ten rysunek przedstawia interakcje między tymi składnikami:

![Interakcja składników usługi Site Recovery][15]

> [!NOTE]
> Usługa Site Recovery nie obsługuje migracji dyski funkcji miejsca do magazynowania.

Dla dodatkowych składników dla innych scenariuszy, zobacz [architektura scenariusza](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Poniżej przedstawiono wymagania dotyczące usługi Azure w tym scenariuszu migracji:

* Subskrypcja platformy Azure.
* Konto magazynu Azure premium do przechowywania replikowanych danych.
* Azure sieci wirtualnej, z którym nawiążą połączenie maszyny wirtualne, gdy są tworzone w trybie failover. Sieć wirtualna platformy Azure musi być w tym samym regionie, w którym działa usługa Site Recovery.
* Standardowe konto magazynu platformy Azure do przechowywania dzienników replikacji. Może to być to samo konto magazynu dla dysków maszyny Wirtualnej, które są migrowane.

## <a name="prerequisites"></a>Wymagania wstępne

* W tym artykule opisano składniki scenariusza migracji odpowiednich w poprzedniej sekcji.
* Zaplanowaniu czasu na przestoje przez naukę [pracy awaryjnej w usłudze Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Kroki instalacji i migracji

Usługa Site Recovery można użyć do przeprowadzenia migracji maszyn wirtualnych IaaS platformy Azure, między regionami lub w tym samym regionie. Poniższe instrukcje są przystosowane w tym scenariuszu migracji, w artykule [replikowanie maszyn wirtualnych VMware lub serwerów fizycznych do platformy Azure](../../site-recovery/vmware-walkthrough-overview.md). Wykonaj łącza, aby uzyskać szczegółowe instrukcje oprócz instrukcje w tym artykule.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: Tworzenie magazynu usług odzyskiwania

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz **Utwórz zasób** > **zarządzania** > **tworzenia kopii zapasowych i odzyskiwania lokacji (OMS)**. Alternatywnie można wybrać **Przeglądaj** > **magazyn usług odzyskiwania** > **Dodaj**. 
3. Określ region, który maszyny wirtualne będą replikowane do. Na potrzeby migracji w tym samym regionie wybierz region, w których źródła maszyn wirtualnych i kont magazynu źródłowego. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: Wybranie celów ochrony 

1. Na maszynie Wirtualnej, której chcesz zainstalować serwer konfiguracji, otwórz [portalu Azure](https://portal.azure.com).
2. Przejdź do **Magazyny usług odzyskiwania** > **ustawienia** > **usługi Site Recovery** > **krok 1: przygotowanie Infrastruktura** > **cel ochrony**.

   ![Przeglądanie do okienka cel ochrony][2]

3. W obszarze **cel ochrony**, w pierwszej listy rozwijanej wybierz **do platformy Azure**. Na drugiej liście rozwijanej wybierz **nie zwirtualizowanych / inne**, a następnie wybierz **OK**.

   ![Okienko cel ochrony wypełnionego pola][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: Konfigurowanie środowiska źródłowego (serwer konfiguracji)

1. Pobierz **Unified instalacja usługi Azure Site Recovery** i klucz rejestracji w magazynie, przechodząc do **przygotowanie infrastruktury** > **Przygotuj źródło**  >  **Dodaj serwer** okienka. 
 
   Konieczne będzie klucza rejestracji magazynu, aby uruchomić Instalatora ujednoliconego. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Przeglądanie do okienka Dodawanie serwera][4]

2. W **Dodaj serwer** okienka, Dodaj serwer konfiguracji.

   ![Dodawanie serwera okienka wybranego serwera konfiguracji][5]

3. Uruchom Unified Instalatora, aby zainstalować serwer konfiguracji i serwer przetwarzania na używanym jako serwer konfiguracji maszyny Wirtualnej. Możesz [przeprowadzenie zrzuty ekranu](../../site-recovery/vmware-walkthrough-overview.md) do ukończenia instalacji. Mogą odwoływać się do następujących zrzuty ekranu dla kroki określone w tym scenariuszu migracji.

   1. W **przed rozpoczęciem**, wybierz pozycję **zainstalować serwer konfiguracji i serwera przetwarzania**.

      ![Przed rozpoczęciem strony][6]

   2. W **rejestracji**, wyszukaj i wybierz klucz rejestracji, który został pobrany z magazynu.

      ![Strony rejestracji][7]

   3. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. W tym scenariuszu migracji, wybierz **nr**.

      ![Strona szczegółów środowiska][8]

4. Po zakończeniu instalacji, wykonaj następujące czynności **serwera konfiguracji odzyskiwania Microsoft Azure lokacji** okno:
 
   1. Użyj **Zarządzanie kontami** kartę, aby utworzyć konto tej usługi Site Recovery można użyć automatycznego wykrywania. (W tym scenariuszu o ochronie komputerów fizycznych, konfigurowania konta nie jest istotne, ale należy co najmniej jedno konto, aby włączyć jedno z następujących czynności. W takim przypadku można określić nazwę konta i hasła wszystkich.) 
   2. Użyj **rejestracji magazynu** kartę, aby przekazać plik poświadczeń magazynu.

      ![Karta rejestracji magazynu][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: Konfigurowanie środowiska docelowego

Wybierz **przygotowanie infrastruktury** > **docelowego**i określ model wdrożenia, który ma być używany dla maszyn wirtualnych po pracy awaryjnej. Możesz wybrać **klasycznego** lub **Resource Manager**, w zależności od danego scenariusza.

![Okienko docelowy][10]

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. 

> [!NOTE]
> Jeśli używasz konta magazynu w warstwie premium dla replikowanych danych, należy skonfigurować dodatkowe konto magazynu do przechowywania dzienników replikacji.

### <a name="step-5-set-up-replication-settings"></a>Krok 5: Konfigurowanie ustawień replikacji

Aby sprawdzić, czy serwer konfiguracji jest pomyślnie skojarzone z zasadami replikacji, które tworzysz, wykonaj [Konfigurowanie ustawień replikacji](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Krok 6: Planowanie pojemności

1. Użyj [planowania pojemności](../../site-recovery/site-recovery-capacity-planner.md) dokładnie oszacować przepustowość sieci, magazynu i inne wymagania zgodnie z replikacji z potrzeb. 
2. Gdy wszystko będzie gotowe, wybierz **tak, już to zostało zrobione** w **czy ukończono Planowanie wydajności?**.

   ![Pole potwierdzenia ukończono Planowanie pojemności][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: Zainstaluj usługę mobilności i włączyć replikację

1. Istnieje możliwość [instalacji wypychanej](../../site-recovery/vmware-walkthrough-overview.md) do maszyn wirtualnych źródła lub do [ręcznie zainstalować usługi mobilności](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) na źródłowe maszyny wirtualne. Można znaleźć wymóg wypychanie instalacji i ścieżkę ręczne Instalatora dostarczonego łącza. Podczas wykonywania instalacji ręcznej, konieczne może użyć wewnętrznego adresu IP można znaleźć serwera konfiguracji.

   ![Strona szczegółów serwera konfiguracji][12]

   Przełączona w tryb failover maszyny Wirtualnej będzie mieć dwa tymczasowego dyski: jeden z podstawowej maszyny Wirtualnej, a druga utworzone podczas inicjowania obsługi maszyny wirtualnej w regionie odzyskiwania. Aby wykluczyć dysku tymczasowym przed replikacji, zainstalować usługi mobilności, przed włączeniem replikacji. Aby dowiedzieć się więcej o tym, jak można wykluczyć dysku tymczasowy, zobacz [wykluczyć z replikacji dyski](../../site-recovery/vmware-walkthrough-overview.md).

2. Aby włączyć replikację:
   1. Wybierz **Replikowanie aplikacji** > **źródła**. Po włączeniu replikacji po raz pierwszy, wybierz **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn.
   2. W kroku 1 — Konfiguracja **źródła** jako serwera przetwarzania.
   3. W kroku 2 Określ model wdrożenia trybu failover post, konto magazynu premium, aby przeprowadzić migrację do konta magazynu w warstwie standardowa, aby zapisać dzienników i sieci wirtualnej nie.
   4. W kroku 3 należy dodać chronionych maszyn wirtualnych za pomocą adresu IP. (Wewnętrzny adres IP, można je znaleźć, może być konieczne).
   5. W kroku 4 należy skonfigurować właściwości, wybierając konta, które należy wcześniej skonfigurować na serwerze przetwarzania.
   6. W kroku 5, wybierz zasady replikacji, utworzony wcześniej w "krok 5: Konfigurowanie ustawień replikacji."
   7. Kliknij przycisk **OK**.

   > [!NOTE]
   > Cofnięciu przydziału maszyny Wirtualnej platformy Azure i ponownego uruchomienia, nie ma żadnej gwarancji, że otrzyma ten sam adres IP. Jeśli zmieni adres IP serwera konfiguracji serwera/procesu lub chronionych maszynach wirtualnych platformy Azure, replikacji, w tym scenariuszu może nie działać poprawnie.

   ![Włączanie replikacji okienka ze źródłem wybrane][13]

Podczas projektowania środowiska usługi Azure Storage, zalecane jest użycie kont magazynu osobne dla każdej maszyny Wirtualnej w zestawie dostępności. Firma Microsoft zaleca, należy wykonać najlepszym rozwiązaniem w warstwie magazynu do [użycia wielu kont magazynu dla każdego zestawu dostępności](../linux/manage-availability.md). Dystrybucja dysków maszyny Wirtualnej do wielu kont magazynu pomaga zwiększyć dostępność magazynu i rozpowszechnia we/wy w ramach infrastruktury magazynu Azure.

W przypadku maszyn wirtualnych w zestawie dostępności, zamiast replikować dysków wszystkich maszyn wirtualnych na jedno konto magazynu, zdecydowanie zaleca się migracja wielu maszyn wirtualnych wiele razy. Dzięki temu maszyny wirtualne w tym samym zestawie dostępności nie mają konta magazynu jednego. Użyj **włączania replikacji** okienko, aby skonfigurować konto magazynu docelowego dla każdej maszyny Wirtualnej, jeden z nich.
 
Model wdrożenia trybu failover post można wybrać w zależności od potrzeb. Jeśli usługi Azure Resource Manager jako model wdrożenia trybu failover post, możesz w trybie Failover maszyny Wirtualnej (Resource Manager) do maszyny Wirtualnej (Resource Manager), lub możesz w trybie Failover maszyny Wirtualnej (wdrożenia klasyczne) do maszyny Wirtualnej (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Krok 8: Uruchom test trybu failover

Aby sprawdzić, czy z replikacji została ukończona, wybierz wystąpienie usługi Site Recovery, a następnie wybierz **ustawienia** > **elementy replikowane**. Pojawi się stan i procent procesu replikacji. 

Po początkowej replikacji zakończeniu testować tryb failover do sprawdzania poprawności strategii replikacji. Aby uzyskać szczegółowe instrukcje test trybu failover, zobacz [uruchomić test trybu failover w usłudze Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Przed uruchomieniem żadnych trybu failover upewnij się, że maszyn wirtualnych i strategii replikacji spełniają wymagania. Aby uzyskać więcej informacji o uruchamianiu test trybu failover, zobacz [testowanie trybu failover na platformie Azure w usłudze Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Można wyświetlić stan użytkownika testowego trybu failover w **ustawienia** > **zadania** > *YOUR_FAILOVER_PLAN_NAME*. W okienku widać podziału kroków i wyniki powodzeń/niepowodzeń. Jeśli testowy tryb failover nie powiedzie się na dowolnym etapie, zaznacz krok, aby Sprawdź komunikat o błędzie. 

### <a name="step-9-run-a-failover"></a>Krok 9: Uruchomienie trybu failover

Po przeprowadzeniu testu jest zakończyć pracy awaryjnej, tryb failover do migracji dysków do magazyn w warstwie Premium i replikowania wystąpień maszyn wirtualnych. Postępuj zgodnie z instrukcjami szczegółowe [tryb failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Należy wybrać **Zamknij maszyny wirtualne i zsynchronizuj najnowsze dane**. Ta opcja określa, czy usługa Site Recovery należy spróbować zamknąć chronionych maszyn wirtualnych i synchronizacji danych, dzięki czemu będzie można przełączyć najnowszej wersji danych. Jeśli nie zaznaczysz tej opcji lub próba nie powiedzie się, tryb failover będzie przesyłany z najnowszego punktu odzyskiwania dla maszyny Wirtualnej. 

Usługa Site Recovery spowoduje utworzenie wystąpienia maszyny Wirtualnej, którego typ jest taka sama jak lub podobne do obsługującą magazyn maszyny Wirtualnej — wersja Premium. Możesz sprawdzić wydajność i cen różnych wystąpień maszyny Wirtualnej, przechodząc do [cennik maszyn wirtualnych systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [cennik maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Czynności wykonywane po migracji

1. **Skonfiguruj replikowanych maszyn wirtualnych do zestawu, jeśli ma to zastosowanie dostępności**. Usługa Site Recovery nie obsługuje migracji maszyn wirtualnych wraz z zestawu dostępności. W zależności od wdrożenia zreplikowanej maszyny Wirtualnej wykonaj jedną z następujących czynności:
   * Dla maszyny Wirtualnej utworzonej przy użyciu klasycznego modelu wdrażania: Dodaj maszynę Wirtualną do zestawu w portalu Azure dostępności. Aby uzyskać szczegółowy opis kroków, przejdź do [Dodaj istniejącą maszynę wirtualną do zestawu dostępności](../linux/classic/configure-availability-classic.md).
   * Dla maszyny Wirtualnej utworzonej przy użyciu modelu wdrażania usługi Resource Manager: Zapisz konfigurację maszyny wirtualnej, a następnie usunięcie i ponowne utworzenie maszyn wirtualnych w zestawie dostępności. Aby to zrobić, należy użyć skryptu w [ustawić Azure Resource Manager maszyny Wirtualnej zestawu dostępności](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Przed uruchomieniem tego skryptu sprawdź jego ograniczenia i zaplanowaniu czasu na przestoje.

2. **Usuń stare maszyn wirtualnych i dysków**. Upewnij się, że dyski Premium są zgodne z dysków źródłowych i że nowych maszyn wirtualnych wykonać taką samą funkcję jak źródłowe maszyny wirtualne. Usuń maszynę Wirtualną i Usuń dyski ze źródła kont magazynu w portalu Azure. Jeśli występuje problem, której ten dysk nie jest usunięte, mimo że można usunąć maszyny Wirtualnej, zobacz [Rozwiązywanie problemów podczas usuwania wirtualne dyski twarde](../../storage/common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

3. **Wyczyść infrastruktury usługi Azure Site Recovery**. Jeśli usługi Site Recovery nie jest już potrzebne, można wyczyścić swoją infrastrukturę. Usuń elementy replikowane, czy serwer konfiguracji i zasady odzyskiwania, a następnie usuń magazyn Azure Site Recovery.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Monitorowanie i rozwiązywanie problemów z ochroną maszyn wirtualnych i serwerów fizycznych](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum usługi Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Kolejne kroki

W określonych scenariuszach migracji maszyn wirtualnych zobacz następujące zasoby:

* [Migrowanie maszyn wirtualnych platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Tworzenie i przekazywanie wirtualnego dysku twardego serwera Windows Azure](upload-generalized-managed.md)
* [Migrowanie maszyn wirtualnych z Amazon usług AWS na platformie Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zobacz też następujące zasoby, aby dowiedzieć się więcej na temat usługi Azure Storage i maszyn wirtualnych platformy Azure:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure](premium-storage.md)

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
