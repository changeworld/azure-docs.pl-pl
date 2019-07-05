---
title: Konfigurowanie odzyskiwania po awarii dla programu SQL Server przy użyciu programu SQL Server i usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla programu SQL Server przy użyciu programu SQL Server i usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491774"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Konfigurowanie odzyskiwania po awarii dla programu SQL Server

W tym artykule opisano, jak chronić programu SQL Server zaplecza aplikacji przy użyciu kombinacji ciągłości programu SQL Server i technologii recovery (BCDR) po awarii, i [usługi Azure Site Recovery](site-recovery-overview.md).

Przed rozpoczęciem upewnij się, że rozumiesz, że funkcje odzyskiwania po awarii programu SQL Server, łącznie z klastra trybu failover, zawsze włączonych grup dostępności, dublowania, zaloguj się wysyłki, aktywna replikacja geograficzna i grupy automatyczny tryb failover.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Zalecenie odzyskiwania po awarii dla integracji technologiami BCDR serwera SQL w usłudze Site Recovery

Wybór technologii bcdr można wykorzystać do serwerów SQL odzyskiwania powinna być oparta na Twoje potrzeby RTO i RPO zgodnie pod tabelą. Po nawiązaniu wybór Site Recovery można zintegrować z operacji trybu failover z tymi technologiami, aby zorganizować odzyskiwanie całej aplikacji.

**Typ wdrożenia** | **Technologia BCDR** | **Oczekiwany cel czasu odzyskiwania dla programu SQL** | **Oczekiwany cel punktu odzyskiwania dla programu SQL** |
--- | --- | --- | ---
Program SQL Server na maszynie Wirtualnej IaaS platformy Azure lub w lokalnych| **[Zawsze włączona grupa dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Wartość równoważna czas się replika pomocnicza jako podstawowy | Replikacja jest asynchroniczny do repliki pomocniczej, dlatego jest utrata danych.
Program SQL Server na maszynie Wirtualnej IaaS platformy Azure lub w lokalnych| **[(Zawsze na FCI) do klastra trybu failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Wartość równoważna czas potrzebny do trybu failover między węzłami | Używa magazynu udostępnionego, dlatego tego samego widoku wystąpienia magazynu jest dostępny w trybie failover.
Program SQL Server na maszynie Wirtualnej IaaS platformy Azure lub w lokalnych| **[(W trybie wysokiej wydajności) dublowania bazy danych](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Wartość równoważna czas poświęcony na Wymuszanie usługi, którego używa serwer duplikatu jako serwer rezerwy aktywnej. | Replikacja jest asynchroniczne. Duplikat bazy danych mogą być opóźnione w pewnym stopniu za dublowanej bazy danych. Przerwa jest zazwyczaj małych howvever, może stać się istotne, jeśli server podmiotowi zabezpieczeń lub dublowania system jest mocno obciążony.<br></br>Wysyłanie dziennika może być uzupełnieniem dublowania bazy danych i jest to preferowana alternatywa dla funkcji dublowania baz danych asynchroniczne
SQL jako usługi PaaS na platformie Azure<br></br>(Pule elastyczne, serwerów baz danych SQL) | **Aktywna replikacja geograficzna** | 30 sekund, po jego wyzwoleniu<br></br>Po aktywowaniu trybu failover do jednej z pomocniczych baz danych, wszystkie inne pomocnicze bazy danych są automatycznie łączeni ze nową podstawową. | Cel punktu odzyskiwania przez 5 sekund<br></br>Aktywna replikacja geograficzna korzysta z technologii Always On programu SQL Server, aby informacje o asynchronicznym replikowaniu przekazane transakcje na podstawowej bazy danych do pomocniczej bazy danych przy użyciu izolacji migawki. <br></br>Gwarantuje, że pomocnicze dane nigdy nie miała transakcji częściowych.
SQL jako usługi PaaS skonfigurowane przy użyciu aktywnej replikacji geograficznej na platformie Azure<br></br>(Wystąpienie zarządzane SQL Database, pul elastycznych, serwerów baz danych SQL) | **Grupy automatycznego trybu failover** | RTO 1 godzina | Cel punktu odzyskiwania przez 5 sekund<br></br>Automatyczny tryb failover grupy zapewniają semantykę grupy na podstawie aktywnej replikacji geograficznej, ale jest używany ten sam mechanizm replikacji asynchronicznej.
Program SQL Server na maszynie Wirtualnej IaaS platformy Azure lub w lokalnych| **Replikacja za pomocą usługi Azure Site Recovery** | Zazwyczaj mniej niż 15 minut. [Dowiedz się więcej](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) Aby dowiedzieć się więcej o umowie SLA czas RTO, dostarczone przez usługę Azure Site Recovery. | 1 godzinę w celu zachowania spójności aplikacji i 5 minut w celu zachowania spójności awarii. 

> [!NOTE]
> Kilka istotne zagadnienia dotyczące ochrony obciążeń SQL przy użyciu usługi Azure Site Recovery:
> * Usługa Azure Site Recovery jest niezależny od aplikacji, a dzięki temu dowolna wersja programu SQL server, które zostało wdrożone w obsługiwanym systemie operacyjnym mogą być chronione przez usługę Azure Site Recovery. [Dowiedz się więcej](vmware-physical-azure-support-matrix.md#replicated-machines).
> * Można użyć usługi Site Recovery dla każdego wdrożenia na platformie Azure, funkcji Hyper-V, VMware lub infrastruktury fizycznej. Postępuj zgodnie z [wskazówki](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) na końcu dokumentu na temat ochrony klastra programu SQL Server za pomocą usługi Azure Site Recovery.
> * Upewnij się, zmian szybkość (Bajty zapisu na sekundę) na maszynie w danych mieści się w [limity usługi Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). W przypadku maszyn z systemem windows można wyświetlić to karcie wydajności w Menedżerze zadań. Obserwuj zapisu szybkość dla każdego dysku.
> * Usługa Azure Site Recovery obsługuje replikację wystąpienia klastra trybu Failover na bezpośrednimi miejscami do magazynowania. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-s2d-vms.md).
 

## <a name="disaster-recovery-of-application"></a>Odzyskiwanie po awarii aplikacji

**Usługa Azure Site Recovery organizuje testowy tryb failover i trybu failover w całej aplikacji za pomocą planów odzyskiwania.** 

Istnieją pewne wymagania wstępne, aby upewnić się, że planu odzyskiwania jest w pełni dostosowane zgodnie z Twoimi wymaganiami. Każdego wdrożenia programu SQL Server wymaga zazwyczaj usługi Active Directory. Wymaga ona również łączność w warstwie aplikacji.

### <a name="step-1-set-up-active-directory"></a>Krok 1: Konfigurowanie usługi Active Directory

Konfigurowanie usługi Active Directory, w lokacji dodatkowej odzyskiwania dla programu SQL Server do prawidłowego działania.

* **Małych przedsiębiorstw**— z małą liczbą aplikacji i jednego kontrolera domeny dla witryny lokalnej, aby w trybie Failover całej lokacji, zalecamy użycie replikacji usługi Site Recovery do replikacji kontrolera domeny do regionu pomocniczego Centrum danych, lub na platformie Azure.
* **Średnich i dużych przedsiębiorstw**— Jeśli masz dużą liczbę aplikacji, w lesie usługi Active Directory i chcesz przejść w tryb failover aplikacją lub obciążeniem, zaleca się Konfigurowanie dodatkowego kontrolera domeny w dodatkowym centrum danych lub na platformie Azure. Jeśli używasz zawsze włączonych grup dostępności do odzyskania z lokacją zdalną, zalecamy innego dodatkowego kontrolera domeny w lokacji dodatkowej lub na platformie Azure jest skonfigurowany do używania odzyskane wystąpienia programu SQL Server.

Instrukcje w tym artykule zakładają, że kontroler domeny jest dostępny w lokalizacji dodatkowej. [Dowiedz się więcej](site-recovery-active-directory.md) o ochronie usługi Active Directory z usługą Site Recovery.

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>Krok 2: Upewnij się, łączność z innymi warstwy aplikacji i warstwa sieci web

Upewnij się, że gdy warstwa bazy danych jest uruchomiona w miejscu docelowym regionem świadczenia usługi Azure, łączność z aplikacją i warstwa sieci web. Niezbędne kroki należy podjąć wcześniej do weryfikowania łączności z testowania trybu failover.

Zrozumienie, jak można zaprojektować aplikacje, aby uzyskać informacje o łączności z kilka przykładów:
* [Projektowanie aplikacji do odzyskiwania po awarii w chmurze](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie odzyskiwania po awarii w puli elastycznej](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>Krok 3: Integracja z zawsze włączone, aktywna replikacja geograficzna lub grup automatyczny tryb failover dla aplikacji w tryb failover

Technologiami BCDR zawsze włączone, mają grupy replikacji i trybu failover automatycznie Active Geo repliki pomocnicze programu SQL server działającego w miejscu docelowym regionem świadczenia usługi Azure. W związku z tym pierwszym krokiem do trybu failover Twojej aplikacji jest zapewnienie tej repliki jako podstawowy (przy założeniu, że masz już kontrolerem domeny w pomocniczym). Ten krok nie może być konieczne, Jeśli postanowisz to zrobić automatyczny tryb failover. Tylko po zakończeniu pracy w trybie failover bazy danych należy trybu failover warstwy sieci web lub aplikacji.

> [!NOTE] 
> W przypadku ochrony maszyn SQL za pomocą usługi Azure Site Recovery, wystarczy utworzyć grupę odzyskiwania tych maszyn i dodawanie ich trybu failover w planie odzyskiwania.

[Tworzenie planu odzyskiwania](site-recovery-create-recovery-plans.md) za pomocą aplikacji i maszyn wirtualnych w warstwie sieci web. Wykonaj poniższe kroki, aby dodać pracy w trybie failover warstwy bazy danych:

1. Importuj skrypty na koncie usługi Azure Automation. Zawiera skrypty do trybu failover grupy dostępności SQL w [maszyny wirtualnej usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) i [klasycznej maszyny wirtualnej](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Dodaj usługi ASR-SQL-FailoverAG jako akcję wstępnie pierwszej grupy planu odzyskiwania.

1. Wykonaj instrukcje dostępne w skrypcie tworzenia zmienna usługi automation, aby podać nazwę grupy dostępności.

### <a name="step-4-conduct-a-test-failover"></a>Krok 4: Należy przeprowadzić test trybu failover

Niektóre technologie BCDR, takich jak SQL Always On natywnie nie obsługują testowanie trybu failover. Dlatego zalecamy następujące podejście **tylko wtedy, gdy integracji z usługą takich technologii**:

1. Konfigurowanie [kopia zapasowa Azure](../backup/backup-azure-arm-vms.md) na maszynie wirtualnej, który jest hostem repliki grupy dostępności na platformie Azure.

1. Przed wyzwoleniem testowy tryb failover planu odzyskiwania, należy odzyskać maszynę wirtualną z kopii zapasowej wykonywanej w poprzednim kroku.

    ![Przywracanie z kopii zapasowej platformy Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Wymuszenie kworum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na maszynie wirtualnej przywróconej z kopii zapasowej.

1. Zaktualizuj IP odbiornika do adresu IP, które są dostępne w testowej sieci trybu failover.

    ![Aktualizuj odbiornik IP](./media/site-recovery-sql/update-listener-ip.png)

1. Przenieś odbiornika w trybie online.

    ![Przełącz do trybu Online odbiornika](./media/site-recovery-sql/bring-listener-online.png)

1. Utwórz moduł równoważenia obciążenia z jeden z adresów IP utworzonych w ramach puli adresów IP frontonu, odpowiadający każdej odbiornika grupy dostępności i z maszyny wirtualnej SQL, które są dodawane do puli zaplecza.

     ![Tworzenie modułu równoważenia obciążenia — pula adresów IP frontonu](./media/site-recovery-sql/create-load-balancer1.png)

    ![Tworzenie modułu równoważenia obciążenia — puli zaplecza](./media/site-recovery-sql/create-load-balancer2.png)

1. Dodaj trybu failover z poziomu aplikacji, następuje warstwa sieci web, w tym planie odzyskiwania w grupach kolejnych odzyskiwania. 
1. Wykonaj test trybu failover planu odzyskiwania, aby przetestować tryb failover end-to-end w aplikacji.

## <a name="steps-to-do-a-failover"></a>Kroki, aby przejść w tryb failover

Po dodaniu skryptu w ramach planu odzyskiwania, w kroku 3 i zweryfikować ją, wykonując test trybu failover przy użyciu wyspecjalizowanego podejście w kroku 4, możesz przejść w tryb failover planu odzyskiwania utworzonego w kroku 3.

Należy pamiętać, że kroki trybu failover dla aplikacji i warstwy sieci web powinna być taka sama, zarówno w przypadku testowego trybu failover, jak i planów odzyskiwania trybu failover.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Jak zabezpieczyć klaster programu SQL Server (standard edition/SQL Server 2008 R2)

W przypadku klastra z programem SQL Server Standard edition lub SQL Server 2008 R2 zalecane jest użycie replikacji usługi Site Recovery do ochrony programu SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure na platformę Azure i środowiska lokalnego do platformy Azure

Usługa Site Recovery nie zapewnia gościa Obsługa klastrów, podczas replikacji do regionu platformy Azure. Dla wersji Standard programu SQL Server także nie zapewnia rozwiązanie odzyskiwania po awarii niskie koszty. W tym scenariuszu firma Microsoft zaleca ochrony klastra programu SQL Server na autonomicznym serwerze SQL Server w lokalizacji głównej i odzyskać ją w lokacji dodatkowej.

1. Na podstawowym regionem platformy Azure lub w lokacji lokalnej, należy skonfigurować dodatkowe autonomiczne wystąpienie programu SQL Server.
1. Skonfiguruj wystąpienie ma pełnić rolę dublowania baz danych, które chcesz chronić. Konfigurowanie funkcji dublowania w trybie wysokiego bezpieczeństwa.
1. Konfigurowanie odzyskiwania lokacji w lokacji głównej ([Azure](azure-to-azure-tutorial-enable-replication.md), [funkcji Hyper-V](site-recovery-hyper-v-site-to-azure.md) lub [serwerów fizycznych i maszyn wirtualnych programu VMware)](site-recovery-vmware-to-azure-classic.md).
1. Użyj replikacji usługi Site Recovery, aby nowe wystąpienie programu SQL Server są replikowane do lokacji dodatkowej. Ponieważ jest kopii duplikatu wysokiego bezpieczeństwa, zostaną zsynchronizowane przy użyciu klastra podstawowego, ale będą replikowane, przy użyciu replikacji usługi Site Recovery.


![Klaster w warstwie Standardowa](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Zagadnienia dotyczące powrotu po awarii

W przypadku klastrów programu SQL Server Standard powrotu po awarii po nieplanowany tryb failover wymaga kopii zapasowej programu SQL server i przywracania z wystąpienia duplikatu do oryginalnego klastra, za pomocą reestablishment dublowania.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Jak SQL uzyskać licencje, gdy chroniony za pomocą usługi Azure Site Recovery?
Replikacja za pomocą usługi Azure Site Recovery dla programu SQL Server jest objęta korzyścią odzyskiwania po awarii w ramach programu Software Assurance dla wszystkich scenariuszy usługi Azure Site Recovery (lokalnego odzyskiwania po awarii na platformę Azure lub odzyskiwania po awarii między regionami infrastruktury IaaS platformy Azure). [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Czy usługi Azure Site Recovery będzie obsługiwać Moja wersja SQL?
Usługa Azure Site Recovery jest niezależny od aplikacji. Dzięki temu dowolnej wersji programu SQL server, która została wdrożona w obsługiwanym systemie operacyjnym, mogą być chronione przez usługę Azure Site Recovery. [Dowiedz się więcej](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej](site-recovery-components.md) o architekturze usługi Site Recovery.
* Serwery SQL na platformie Azure, aby uzyskać więcej informacji na temat [rozwiązania wysokiej dostępności](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) dla odzyskiwania w regionie pomocniczym platformy Azure.
* Bazy danych SQL na platformie Azure, aby uzyskać więcej informacji na temat [ciągłość prowadzenia działalności biznesowej](../sql-database/sql-database-business-continuity.md) i [wysokiej dostępności](../sql-database/sql-database-high-availability.md) opcji odzyskiwania w regionie pomocniczym platformy Azure.
* Dla maszyn z programem SQL server w środowisku lokalnym [więcej](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) o opcjach wysokiej dostępności, odzyskiwania w usłudze Azure Virtual Machines.
