---
title: Konfigurowanie odzyskiwania po awarii dla programu SQL Server za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla programu SQL Server przy użyciu programu SQL Server i usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084738"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Konfigurowanie odzyskiwania po awarii dla programu SQL Server

W tym artykule opisano, jak pomóc w ochronie zaplecza programu SQL Server aplikacji. Można to zrobić przy użyciu kombinacji technologii ciągłości biznesowej programu SQL Server i odzyskiwania po awarii (BCDR) oraz [usługi Azure Site Recovery.](site-recovery-overview.md)

Przed rozpoczęciem upewnij się, że znasz możliwości odzyskiwania po awarii programu SQL Server. Te funkcje obejmują:

* Klaster trybu failover
* Grupy dostępności Always On
* Dublowanie bazy danych
* Wysyłanie dziennika
* Aktywna replikacja geograficzna
* Grupy automatycznego trybu failover

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Połączenie technologii BCDR z odzyskiem witryny

Wybór technologii BCDR do odzyskiwania wystąpień programu SQL Server powinien opierać się na celu czasu odzyskiwania (RTO) i celu punktu odzyskiwania (RPO), zgodnie z opisem w poniższej tabeli. Połącz odzyskiwanie witryny z operacją pracy awaryjnej wybranej technologii, aby zorganizować odzyskiwanie całej aplikacji.

Typ wdrożenia | Technologia BCDR | Oczekiwany rto dla programu SQL Server | Oczekiwany rpo dla programu SQL Server |
--- | --- | --- | ---
SQL Server na platformie Azure infrastruktury jako usługa (IaaS) maszyny wirtualnej (VM) lub w środowisku lokalnym.| [Zawsze włączona grupa dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Czas, który ma sprawić, że replika pomocnicza stanie się podstawowa. | Ponieważ replikacja do repliki pomocniczej jest asynchroniza, istnieje utrata danych.
SQL Server na maszynie wirtualnej usługi Azure IaaS lub w środowisku lokalnym.| [Klastrowanie trybu failover (zawsze włączone FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Czas pracy awaryjnej między węzłami. | Ponieważ always on FCI używa udostępnionego magazynu, ten sam widok wystąpienia magazynu jest dostępny w przypadku pracy awaryjnej.
SQL Server na maszynie wirtualnej usługi Azure IaaS lub w środowisku lokalnym.| [Dublowanie bazy danych (tryb o wysokiej wydajności)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Czas wymuszania usługi, która używa serwera dublowania jako ciepłego serwera rezerwowego. | Replikacja jest asynchroniza. Dublowana baza danych może pozostawać w tyle za główną bazą danych. Opóźnienie jest zazwyczaj małe. Ale może stać się duży, jeśli system serwera dublowania lub dublowania jest pod dużym obciążeniem.<br/><br/>Wysyłka dziennika może być uzupełnieniem dublowania bazy danych. Jest to korzystna alternatywa dla dublowania asynchroniiowej bazy danych.
SQL jako platforma jako usługa (PaaS) na platformie Azure.<br/><br/>Ten typ wdrożenia obejmuje pule elastyczne i serwery bazy danych SQL Azure. | Aktywna replikacja geograficzna | 30 sekund po wyzwoleniu trybu failover.<br/><br/>Po uaktywnieniu pracy awaryjnej dla jednej z pomocniczych baz danych, wszystkie inne pomocnicze są automatycznie połączone z nowym podstawowym. | RPO na pięć sekund.<br/><br/>Aktywna replikacja geograficzna korzysta z technologii Zawsze włączone programu SQL Server. Asynchronicznie replikuje zatwierdzone transakcje w podstawowej bazie danych do pomocniczej bazy danych przy użyciu izolacji migawki.<br/><br/>Dane pomocnicze jest gwarantowane nigdy nie mają częściowych transakcji.
SQL jako PaaS skonfigurowany z aktywną replikacją geograficzną na platformie Azure.<br/><br/>Ten typ wdrożenia obejmuje wystąpienie zarządzane bazy danych SQL, pule elastyczne i serwery bazy danych SQL. | Grupy automatycznego trybu failover | RTO jednej godziny. | RPO na pięć sekund.<br/><br/>Grupy automatycznego trybu failover zapewniają semantytykę grupy oprócz aktywnej replikacji geograficznej. Ale ten sam mechanizm replikacji asynchroniiowej jest używany.
SQL Server na maszynie wirtualnej usługi Azure IaaS lub w środowisku lokalnym.| Replikacja za pomocą usługi Azure Site Recovery | RTO jest zazwyczaj mniej niż 15 minut. Aby dowiedzieć się więcej, przeczytaj [SLA RTO dostarczoną przez site recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Jedna godzina dla spójności aplikacji i pięć minut dla spójności awarii. Jeśli szukasz niższego RPO, użyj innych technologii BCDR.

> [!NOTE]
> Kilka ważnych zagadnień podczas pomocy w ochronie obciążeń SQL za pomocą usługi Site Recovery:
> * Site Recovery jest niezależny od aplikacji. Usługa Site Recovery może pomóc w ochronie dowolnej wersji programu SQL Server wdrożonej w obsługiwanym systemie operacyjnym. Aby dowiedzieć się więcej, zobacz [macierz obsługi odzyskiwania](vmware-physical-azure-support-matrix.md#replicated-machines) zreplikowanych maszyn.
> * Można użyć usługi Site Recovery dla dowolnego wdrożenia na platformie Azure, Hyper-V, VMware lub infrastruktury fizycznej. Na końcu tego artykułu postępuj zgodnie ze wskazówkami dotyczącymi [ochrony klastra programu SQL Server](#how-to-help-protect-a-sql-server-cluster) za pomocą narzędzia Site Recovery.
> * Upewnij się, że szybkość zmiany danych obserwowana na komputerze mieści się w [granicach odzyskiwania witryny.](vmware-physical-azure-support-matrix.md#churn-limits) Szybkość zmiany jest mierzona w bajtach zapisu na sekundę. W przypadku komputerów z systemem Windows można wyświetlić tę szybkość zmiany, wybierając kartę **Wydajność** w Menedżerze zadań. Obserwuj szybkość zapisu dla każdego dysku.
> * Usługa Site Recovery obsługuje replikację wystąpień klastra trybu failover w bezpośrednich miejscach do magazynowania. Aby dowiedzieć się więcej, [zobacz, jak włączyć replikację bezpośrednią miejsca do magazynowania](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Odzyskiwanie po awarii aplikacji

Usługa Site Recovery organizuje test pracy awaryjnej i pracy awaryjnej całej aplikacji za pomocą planów odzyskiwania.

Istnieją pewne wymagania wstępne, aby upewnić się, że plan odzyskiwania jest w pełni dostosowany do Twoich potrzeb. Każde wdrożenie programu SQL Server zazwyczaj wymaga wdrożenia usługi Active Directory. Wymaga również łączności dla warstwy aplikacji.

### <a name="step-1-set-up-active-directory"></a>Krok 1: Konfigurowanie usługi Active Directory

Skonfiguruj usługę Active Directory w dodatkowej lokacji odzyskiwania dla programu SQL Server, aby działała poprawnie.

* **Małe przedsiębiorstwo:** Masz niewielką liczbę aplikacji i jeden kontroler domeny dla lokacji lokalnej. Jeśli chcesz przejść w stan fail over całej lokacji, użyj replikacji odzyskiwania witryny. Ta usługa replikuje kontroler domeny do pomocniczego centrum danych lub platformy Azure.
* **Średnie i duże przedsiębiorstwo:** może być konieczne skonfigurowanie dodatkowych kontrolerów domeny.
  - Jeśli masz dużą liczbę aplikacji, masz las usługi Active Directory i chcesz przejść w tryb fail over przez aplikację lub obciążenie, skonfiguruj inny kontroler domeny w pomocniczym centrum danych lub na platformie Azure.
  -  Jeśli używasz grup dostępności zawsze włączone do odzyskiwania do lokacji zdalnej, skonfiguruj inny kontroler domeny w lokacji dodatkowej lub na platformie Azure. Ten kontroler domeny jest używany dla odzyskanego wystąpienia programu SQL Server.

Instrukcje w tym artykule zakładają, że kontroler domeny jest dostępny w lokalizacji dodatkowej. Aby dowiedzieć się więcej, zobacz procedury [pomagające chronić usługi Active Directory za pomocą funkcji Odzysk lokacji](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Krok 2: Zapewnienie łączności z innymi warstwami

Po uruchomieniu warstwy bazy danych w docelowym regionie platformy Azure upewnij się, że masz łączność z warstwami aplikacji i sieci web. Podejmij niezbędne kroki z wyprzedzeniem, aby sprawdzić poprawność łączności przy testowym uchyłkaniem w pracy awaryjnej.

Aby dowiedzieć się, jak projektować aplikacje pod kątem zagadnień dotyczących łączności, zobacz następujące przykłady:

* [Projektowanie aplikacji do odzyskiwania po awarii w chmurze](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie odzyskiwania po awarii w puli elastycznej](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Krok 3: Współdziałanie z zawsze włączonymi, aktywną replikacją geograficzną i grupami automatycznego trybu failover

Technologie BCDR zawsze włączone, aktywna replikacja geograficzna i grupy automatycznego trybu failover mają repliki pomocnicze programu SQL Server uruchomione w docelowym regionie platformy Azure. Pierwszym krokiem dla pracy awaryjnej aplikacji jest określenie tej repliki jako podstawowej. Ten krok zakłada, że masz już kontroler domeny w pomocniczej. Krok może nie być konieczne, jeśli zdecydujesz się zrobić auto-failover. Przepełnienie awaryjne warstw sieci web i aplikacji dopiero po zakończeniu pracy awaryjnej bazy danych.

> [!NOTE]
> Jeśli pomogłeś chronić maszyny SQL za pomocą usługi Site Recovery, wystarczy utworzyć grupę odzyskiwania tych maszyn i dodać ich praca awaryjna w planie odzyskiwania.

[Tworzenie planu odzyskiwania](site-recovery-create-recovery-plans.md) z maszynami wirtualnymi warstwy sieci web aplikacji i sieci web. Poniższe kroki pokazują, jak dodać pracy awaryjnej warstwy bazy danych:

1. Zaimportuj skrypty do pracy w trybie fail-1 grupa dostępności SQL zarówno na [maszynie wirtualnej Menedżera zasobów,](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) jak i [klasycznej maszynie wirtualnej](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Zaimportuj skrypty do konta usługi Azure Automation.

    [![Obraz logo "Wdrażanie na platformie Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt ASR-SQL-FailoverAG jako działanie wstępne pierwszej grupy planu odzyskiwania.

1. Postępuj zgodnie z instrukcjami dostępnymi w skrypcie, aby utworzyć zmienną automatyzacji. Ta zmienna zawiera nazwę grup dostępności.

### <a name="step-4-conduct-a-test-failover"></a>Krok 4: Przeprowadzenie testu po awarii

Niektóre technologie BCDR, takie jak SQL Always On, nie obsługują natywnie pracy awaryjnej testu. Zalecamy następujące podejście *tylko w przypadku korzystania z takich technologii*.

1. Konfigurowanie [usługi Azure Backup](../backup/backup-azure-arm-vms.md) na maszynie wirtualnej, która obsługuje replikę grupy dostępności na platformie Azure.

1. Przed wyzwoleniem testowego trybu failover planu odzyskiwania, odzyskać maszynę wirtualną z kopii zapasowej wykonanej w poprzednim kroku.

    ![Zrzut ekranu przedstawiający okno przywracania konfiguracji z usługi Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Wymuś kworum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na maszynie wirtualnej, która została przywrócona z kopii zapasowej.

1. Zaktualizuj adres IP odbiornika, aby był adresem dostępnym w testowej sieci trybu failover.

    ![Zrzut ekranu przedstawiający okno reguł i okno dialogowe właściwości adresu IP](./media/site-recovery-sql/update-listener-ip.png)

1. Przewiń słuchacza do trybu online.

    ![Zrzut ekranu przedstawiający okno oznaczone Content_AG z nazwami i stanami serwerów](./media/site-recovery-sql/bring-listener-online.png)

1. Upewnij się, że moduł równoważenia obciążenia w sieci trybu failover ma jeden adres IP, z puli adresów IP frontu, który odpowiada każdemu odbiornikowi grupy dostępności, oraz z maszyną wirtualną programu SQL Server w puli zaplecza.

     ![Zrzut ekranu przedstawiający okno zatytułowane "SQL-AlwaysOn-LB - Frontend IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Zrzut ekranu przedstawiający okno zatytułowane "SQL-AlwaysOn-LB — pula adresów IP wewnętrznej bazy danych](./media/site-recovery-sql/create-load-balancer2.png)

1. W późniejszych grupach odzyskiwania dodaj pracy awaryjnej warstwy aplikacji, a następnie warstwy sieci web dla tego planu odzyskiwania.

1. Wykonaj testową przekład wdrówkę planu odzyskiwania, aby przetestować end-to-end pracy awaryjnej aplikacji.

## <a name="steps-to-do-a-failover"></a>Kroki, aby wykonać pracy awaryjnej

Po dodaniu skryptu w kroku 3 i sprawdź poprawność go w kroku 4, można wykonać pracy awaryjnej planu odzyskiwania utworzonego w kroku 3.

Kroki pracy awaryjnej dla warstw aplikacji i sieci web powinny być takie same w testowych planach odzyskiwania w ywodzącej po awarii i pracy awaryjnej.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Jak chronić klaster programu SQL Server

W przypadku klastra z programem SQL Server Standard edition lub SQL Server 2008 R2 zaleca się użycie replikacji usługi Site Recovery w celu ochrony programu SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Platforma Azure na platformie Azure i lokalnie na platformie Azure

Usługa Site Recovery nie zapewnia obsługi klastra gościa podczas replikowania do regionu platformy Azure. SQL Server Standard edition również nie zapewnia taniego rozwiązania do odzyskiwania po awarii. W tym scenariuszu zaleca się chronić klaster programu SQL Server do autonomicznego wystąpienia programu SQL Server w lokalizacji podstawowej i odzyskać go w pomocniczej.

1. Skonfiguruj dodatkowe autonomiczne wystąpienie programu SQL Server w podstawowym regionie platformy Azure lub w lokacji lokalnej.

1. Skonfiguruj wystąpienie tak, aby służyło jako dublowanie baz danych, które mają być chronione. Konfigurowanie dublowania w trybie wysokiego bezpieczeństwa.

1. Konfigurowanie odzyskiwania lokacji w lokacji głównej dla maszyn wirtualnych [platformy Azure,](azure-to-azure-tutorial-enable-replication.md) [funkcji Hyper-V](site-recovery-hyper-v-site-to-azure.md)lub [VMware i serwerów fizycznych.](site-recovery-vmware-to-azure-classic.md)

1. Replikacja usługi Site Recovery umożliwia replikację nowego wystąpienia programu SQL Server do lokacji dodatkowej. Ponieważ jest to kopia dublowana o wysokim poziomie bezpieczeństwa, zostanie zsynchronizowana z klasterem podstawowym, ale zreplikowana przy użyciu replikacji odzyskiwania lokacji.

   ![Obraz standardowego klastra, który pokazuje relację i przepływ między lokacją główną, odzyskiem lokacji i platformą Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Zagadnienia dotyczące powrotu po awarii

W przypadku klastrów SQL Server Standard powrót po awarii po nieplanowanym trybie failover wymaga kopii zapasowej i przywracania programu SQL Server. Ta operacja jest wykonywana z wystąpienia dublowania do oryginalnego klastra z ponownego ustanowienia dublowania.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>W jaki sposób program SQL Server otrzymuje licencję, gdy jest używany z usługą Site Recovery?

Replikacja odzyskiwania lokacji dla programu SQL Server jest objęta korzyścią odzyskiwania po awarii pakietu Software Assurance. Ten zakres dotyczy wszystkich scenariuszy odzyskiwania lokacji: lokalnie do odzyskiwania po awarii platformy Azure i odzyskiwania po awarii między regionami usługi Azure IaaS. Więcej informacji można znaleźć w [cennikach usługi Azure Site Recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-site-recovery-support-my-sql-server-version"></a>Czy usługa Site Recovery będzie obsługiwać moją wersję programu SQL Server?

Site Recovery jest niezależny od aplikacji. Usługa Site Recovery może pomóc w ochronie dowolnej wersji programu SQL Server wdrożonej w obsługiwanym systemie operacyjnym. Aby uzyskać więcej informacji, zobacz [macierz obsługi odzyskiwania](vmware-physical-azure-support-matrix.md#replicated-machines) zreplikowanych maszyn.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [architekturze odzyskiwania witryny](site-recovery-components.md).
* W przypadku programu SQL Server na platformie Azure dowiedz się więcej o [rozwiązaniach o wysokiej dostępności](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) do odzyskiwania w pomocniczym regionie platformy Azure.
* W przypadku bazy danych SQL dowiedz się więcej o [opcjach ciągłości biznesowej](../sql-database/sql-database-business-continuity.md) i [wysokiej dostępności](../sql-database/sql-database-high-availability.md) odzyskiwania w pomocniczym regionie platformy Azure.
* W przypadku komputerów z programem SQL Server w środowisku lokalnym dowiedz się więcej o [opcjach wysokiej dostępności](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) odzyskiwania w maszynach wirtualnych platformy Azure.
