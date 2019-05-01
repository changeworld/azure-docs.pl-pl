---
title: Konfigurowanie odzyskiwania po awarii dla programu SQL Server przy użyciu programu SQL Server i usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla programu SQL Server przy użyciu programu SQL Server i usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 7725563a80182be8f8c02d94ef1e6cfa382c04d3
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924850"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Konfigurowanie odzyskiwania po awarii dla programu SQL Server

W tym artykule opisano, jak chronić programu SQL Server zaplecza aplikacji przy użyciu kombinacji ciągłości programu SQL Server i technologii recovery (BCDR) po awarii, i [usługi Azure Site Recovery](site-recovery-overview.md).

Przed rozpoczęciem upewnij się, że rozumiesz możliwości odzyskiwania po awarii programu SQL Server, takich jak klaster pracy awaryjnej, zawsze włączonych grup dostępności, dublowania bazy danych i wysyłania dziennika.


## <a name="sql-server-deployments"></a>Wdrożenia programu SQL Server

Wiele obciążeń używania programu SQL Server jako podstawa i można zintegrować z aplikacji, takich jak SharePoint, Dynamics i SAP, wdrożenie usług danych.  Program SQL Server można wdrożyć na wiele sposobów:

* **Autonomiczny program SQL Server**: Program SQL Server i wszystkimi bazami danych są hostowane na jednym komputerze (fizyczny lub wirtualny). Podczas wirtualizacji, hostów klastra jest używany dla lokalnej wysokiej dostępności. Poziomie gościa o wysokiej dostępności nie jest zaimplementowana.
* **SQL Server awaryjnej wystąpienia (zawsze włączone FCI)**: Dwa lub więcej węzłów z uruchomionym programem SQL Server wystąpienia z dyskami udostępnionymi są skonfigurowane w klastrze pracy awaryjnej Windows. Jeśli węzeł jest wyłączony, klaster może pracy awaryjnej programu SQL Server do innego wystąpienia. Aby zaimplementować wysoką dostępność w lokacjach głównych, zazwyczaj służy tej konfiguracji. To wdrożenie nie chroni przed awarią lub awaria w warstwie magazynu udostępnionego. Udostępniony dysk może być implementowany przy użyciu iSCSI, fiber channel lub udostępnionego dysku vhdx.
* **Zawsze włączone grupy dostępności programu SQL Server**: Co najmniej dwa węzły są konfigurowane w udostępnionym klastra nic za pomocą programu SQL Server baz danych skonfigurowanych w grupie dostępności, przy użyciu replikacji synchronicznej i automatycznej pracy awaryjnej.

  W tym artykule wykorzystuje następujące natywnych SQL awaryjnego odzyskiwania technologie do odzyskiwania baz danych do lokacji zdalnej:

* SQL zawsze włączone grupy dostępności, zapewnienie odzyskiwania po awarii dla programu SQL Server 2012 lub 2014 w wersji Enterprise.
* Funkcja dublowania bazy danych SQL w trybie wysokiego bezpieczeństwa, SQL Server Standard edition (dowolna wersja) lub SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

### <a name="supported-scenarios"></a>Obsługiwane scenariusze
Usługa Site Recovery chroni program SQL Server zgodnie z opisem w tabeli.

**Scenariusz** | **Lokacja dodatkowa** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Serwer fizyczny** | Yes | Yes
**Azure** |Nie dotyczy| Yes

### <a name="supported-sql-server-versions"></a>Obsługiwane wersje programu SQL Server
Te wersje programu SQL Server są obsługiwane w przypadku obsługiwanych scenariuszy:

* SQL Server 2016 Enterprise i Standard
* SQL Server 2014 Enterprise i Standard
* SQL Server 2012 Enterprise i Standard
* SQL Server 2008 R2 Enterprise i Standard

### <a name="supported-sql-server-integration"></a>Integracja programu SQL Server obsługiwane

Usługa Site Recovery może zostać zintegrowany z natywnych technologiami BCDR serwera SQL, podsumowane w tabeli, aby zapewnić rozwiązanie odzyskiwania po awarii.

**Funkcja** | **Szczegóły** | **SQL Server** |
--- | --- | ---
**Konfigurowanie zawsze włączonej grupy dostępności** | Autonomiczny uruchomić wiele wystąpień programu SQL Server w klastrze trybu failover, który ma wiele węzłów.<br/><br/>Bazy danych, można podzielić na grupy trybu failover, które można skopiować (dublowanych) w wystąpieniach programu SQL Server tak, aby Brak udostępnionego magazynu jest wymagana.<br/><br/>Zapewnia odzyskiwanie po awarii między lokacją główną a przynajmniej jednej lokacji dodatkowej. Dwa węzły można skonfigurować pod kątem w udostępnionej nic klastra przy użyciu bazy danych programu SQL Server jest skonfigurowany w grupie dostępności przy użyciu replikacji synchronicznej i automatycznej pracy awaryjnej. | SQL Server 2016, SQL Server 2014 i SQL Server 2012 Enterprise edition
**(Zawsze na FCI) do klastra trybu failover** | Program SQL Server korzysta z Windows klastra trybu failover wysokiej dostępności obciążeń programu SQL Server w środowisku lokalnym.<br/><br/>Węzły uruchomione wystąpienia programu SQL Server z dyskami udostępnionymi są skonfigurowane w klastrze trybu failover. Jeśli wystąpienie jest wyłączony klastra kończy się niepowodzeniem przez inny.<br/><br/>Klaster nie chroni przed awarią lub przerw w magazynie udostępnionym. Udostępniony dysk może być implementowany przy użyciu iSCSI, fiber channel, lub udostępnione Vhdx. | SQL Server Enterprise editions<br/><br/>SQL Server Standard edition (ograniczoną do tylko dwa węzły)
**(Wysokie bezpieczeństwo tryb) dublowania bazy danych** | Chroni pojedynczej bazy danych do pojedynczej kopii dodatkowej. Dostępne w obu wysokie bezpieczeństwo (synchroniczne) i trybach replikacji (asynchroniczny) o wysokiej wydajności. Nie wymaga klastra trybu failover. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise wszystkie wersje
**Autonomiczny program SQL Server** | SQL Server i bazy danych są hostowane na jednym serwerze (fizyczny lub wirtualny). Klaster hosta jest używana do wysokiej dostępności, jeśli serwer znajduje się wirtualny. Nie wysokiej dostępności poziomie gościa. | Enterprise lub Standard

## <a name="deployment-recommendations"></a>Zalecenia dotyczące wdrożenia

Ta tabela zawiera podsumowanie Nasze zalecenia dotyczące integracji technologiami BCDR serwera SQL z usługą Site Recovery.

| **Wersja** | **Wersja** | **Wdrożenie** | **Na infrastrukturę lokalną w środowisku lokalnym** | **Środowiska lokalnego do platformy Azure** |
| --- | --- | --- | --- | --- |
| Program SQL Server 2016, 2014 lub 2012 |Enterprise |Wystąpienia klastra trybu failover |Zawsze włączone grupy dostępności |Zawsze włączone grupy dostępności |
|| Enterprise |Zawsze włączone grupy dostępności w celu zapewnienia wysokiej dostępności |Zawsze włączone grupy dostępności |Zawsze włączone grupy dostępności |
|| Standardowa (Standard) |Wystąpienia klastra trybu failover (FCI) |Replikacja usługi Site Recovery dublowaniem lokalne |Replikacja usługi Site Recovery dublowaniem lokalne |
|| Enterprise lub Standard |Autonomiczna |Replikacja usługi Site Recovery |Replikacja usługi Site Recovery |
| SQL Server 2008 R2 or 2008 |Enterprise lub Standard |Wystąpienia klastra trybu failover (FCI) |Replikacja usługi Site Recovery dublowaniem lokalne |Replikacja usługi Site Recovery dublowaniem lokalne |
|| Enterprise lub Standard |Autonomiczna |Replikacja usługi Site Recovery |Replikacja usługi Site Recovery |
| Program SQL Server (dowolna wersja) |Enterprise lub Standard |Wystąpienia klastra trybu failover — aplikacja usługi DTC |Replikacja usługi Site Recovery |Nieobsługiwane |

## <a name="deployment-prerequisites"></a>Wymagania wstępne dotyczące wdrożenia

* Lokalne wdrożenie programu SQL Server, z obsługiwaną wersją programu SQL Server. Zazwyczaj należy również usługi Active Directory dla programu SQL server.
* Wymagania dotyczące tego scenariusza, którą chcesz wdrożyć. Dowiedz się więcej na temat pomocy technicznej dotyczące [replikacji do platformy Azure](site-recovery-support-matrix-to-azure.md) i [lokalnych](site-recovery-support-matrix.md), i [wymagania wstępne dotyczące wdrażania](site-recovery-prereq.md).

## <a name="set-up-active-directory"></a>Konfigurowanie usługi Active Directory

Konfigurowanie usługi Active Directory, w lokacji dodatkowej odzyskiwania dla programu SQL Server do prawidłowego działania.

* **Małych przedsiębiorstw**— z małą liczbą aplikacji i jednego kontrolera domeny dla witryny lokalnej, aby w trybie Failover całej lokacji, zalecamy użycie replikacji usługi Site Recovery do replikacji kontrolera domeny do regionu pomocniczego Centrum danych, lub na platformie Azure.
* **Średnich i dużych przedsiębiorstw**— Jeśli masz dużą liczbę aplikacji, w lesie usługi Active Directory i chcesz przejść w tryb failover aplikacją lub obciążeniem, zaleca się Konfigurowanie dodatkowego kontrolera domeny w dodatkowym centrum danych lub na platformie Azure. Jeśli używasz zawsze włączonych grup dostępności do odzyskania z lokacją zdalną, zalecamy innego dodatkowego kontrolera domeny w lokacji dodatkowej lub na platformie Azure jest skonfigurowany do używania odzyskane wystąpienia programu SQL Server.

Instrukcje w tym artykule zakładają, że kontroler domeny jest dostępny w lokalizacji dodatkowej. [Dowiedz się więcej](site-recovery-active-directory.md) o ochronie usługi Active Directory z usługą Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integracja z programu SQL Server Always On replikacji do platformy Azure

Oto, co należy zrobić:

1. Importuj skrypty na koncie usługi Azure Automation. Zawiera skrypty do trybu failover grupy dostępności SQL w [maszyny wirtualnej usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) i [klasycznej maszyny wirtualnej](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Dodaj usługi ASR-SQL-FailoverAG jako akcję wstępnie pierwszej grupy planu odzyskiwania.

1. Wykonaj instrukcje dostępne w skrypcie tworzenia zmienna usługi automation, aby podać nazwę grupy dostępności.

### <a name="steps-to-do-a-test-failover"></a>Kroki testu trybu failover

SQL Always On nie obsługuje natywnie testowy tryb failover. Dlatego zalecamy wykonanie poniższych czynności:

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

1. Wykonaj test trybu failover planu odzyskiwania.

### <a name="steps-to-do-a-failover"></a>Kroki, aby przejść w tryb failover

Po dodaniu skryptu w ramach planu odzyskiwania i sprawdzana podczas testowania trybu failover planu odzyskiwania, możesz przejść w tryb failover planu odzyskiwania.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integracja z programu SQL Server Always On replikacji do lokacji dodatkowej w środowisku lokalnym

Program SQL Server jest używane grupy dostępności, wysokiej dostępności (lub infrastruktury klasyfikacji plików), zaleca się używanie grup dostępności do lokalizacji odzyskiwania. Należy pamiętać, że dotyczy to aplikacji, które nie używają transakcje rozproszone.

1. [Konfigurowanie bazy danych](https://msdn.microsoft.com/library/hh213078.aspx) do grupy dostępności.
1. Tworzenie sieci wirtualnej w lokacji dodatkowej.
1. Skonfiguruj połączenie sieci VPN lokacja lokacja między siecią wirtualną i lokacji głównej.
1. Utwórz maszynę wirtualną do lokalizacji odzyskiwania, a na nim zainstalować program SQL Server.
1. Rozszerzanie istniejących zawsze włączonej grupy dostępności na nową maszynę Wirtualną programu SQL Server. To wystąpienie programu SQL Server należy skonfigurować jako kopia repliki asynchronicznego.
1. Utwórz odbiornik grupy dostępności, lub zaktualizuj istniejący odbiornik, aby uwzględnić asynchronicznego replika maszyny wirtualnej.
1. Upewnij się, że farma aplikacji jest skonfigurowany przy użyciu odbiornika. Jeśli jest instalacji przy użyciu nazwy serwera bazy danych, należy zaktualizować je, aby użyć odbiornika, więc nie trzeba go ponownie skonfigurować po przełączeniu w tryb failover.

W przypadku aplikacji korzystających z transakcji rozproszonych, zaleca się wdrożenia usługi Site Recovery przy użyciu [replikacji site-to-site server serwer fizyczny/VMware](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Zagadnienia dotyczące planu odzyskiwania
1. Dodaj ten przykładowy skrypt do biblioteki programu VMM w lokacjach głównych i dodatkowych.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Po utworzeniu planu odzyskiwania w przypadku aplikacji, należy dodać akcję wstępnie do kroku grupy-1 inicjowanych przez skrypty, które wywołuje skrypt do trybu failover grupy dostępności.

## <a name="protect-a-standalone-sql-server"></a>Ochrona autonomiczne wystąpienie programu SQL Server

W tym scenariuszu firma Microsoft zaleca użycie replikacji usługi Site Recovery do ochrony komputera programu SQL Server. Konkretne kroki zależy, czy jest SQL Server na maszynie Wirtualnej lub serwer fizyczny i czy ma zostać zreplikowana na platformę Azure lub dodatkowej lokacji lokalnej. Dowiedz się więcej o [scenariuszy odzyskiwania lokacji](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Ochrona klastra programu SQL Server (standard edition/SQL Server 2008 R2)

W przypadku klastra z programem SQL Server Standard edition lub SQL Server 2008 R2 zalecane jest użycie replikacji usługi Site Recovery do ochrony programu SQL Server.

### <a name="on-premises-to-on-premises"></a>Ze środowiska lokalnego do środowiska lokalnego

* Jeśli aplikacja korzysta z transakcji rozproszonych firma Microsoft zaleca wdrożeniem [Site Recovery dzięki replikacji sieci SAN](site-recovery-vmm-san.md) środowisku funkcji Hyper-V lub [serwer fizyczny/VMware do programu VMware](site-recovery-vmware-to-vmware.md) w środowisku VMware.
* W przypadku aplikacji niewymagające usługi DTC powyżej metody należy użyć do odzyskania klastra jako autonomiczny serwer przy użyciu lokalnego wysokie bezpieczeństwo dublowania bazy danych.

### <a name="on-premises-to-azure"></a>Z serwera lokalnego do platformy Azure

Usługa Site Recovery nie zapewnia gościa Obsługa klastrów, podczas replikacji do platformy Azure. Dla wersji Standard programu SQL Server także nie zapewnia rozwiązanie odzyskiwania po awarii niskie koszty. W tym scenariuszu firma Microsoft zaleca ochrony klastra programu SQL Server w środowisku lokalnym, do autonomicznego programu SQL Server i odzyskać ją na platformie Azure.

1. Skonfiguruj dodatkowe autonomiczne wystąpienie programu SQL Server w lokacji lokalnej.
1. Skonfiguruj wystąpienie ma pełnić rolę dublowania baz danych, które chcesz chronić. Konfigurowanie funkcji dublowania w trybie wysokiego bezpieczeństwa.
1. Konfigurowanie odzyskiwania lokacji w lokacji lokalnej dla ([funkcji Hyper-V](site-recovery-hyper-v-site-to-azure.md) lub [serwerów fizycznych i maszyn wirtualnych programu VMware)](site-recovery-vmware-to-azure-classic.md).
1. Replikacja usługi Site Recovery umożliwia replikowanie nowego wystąpienia programu SQL Server na platformę Azure. Ponieważ jest kopii duplikatu wysokiego bezpieczeństwa, zostaną zsynchronizowane przy użyciu klastra podstawowego, ale będą replikowane do platformy Azure przy użyciu replikacji usługi Site Recovery.


![Klaster w warstwie Standardowa](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Zagadnienia dotyczące powrotu po awarii

W przypadku klastrów programu SQL Server Standard powrotu po awarii po nieplanowany tryb failover wymaga kopii zapasowej programu SQL server i przywracania z wystąpienia duplikatu do oryginalnego klastra, za pomocą reestablishment dublowania.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](site-recovery-components.md) o architekturze usługi Site Recovery.
