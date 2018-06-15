---
title: Replikowanie aplikacji za pomocą programu SQL Server i usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób replikowania SQL Server przy użyciu usługi Azure Site Recovery dla funkcji po awarii programu SQL Server.
services: site-recovery
documentationcenter: ''
author: prateek9us
manager: gauravd
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: pratshar
ms.openlocfilehash: 7afa05b53186ceac13bef3294c7a139f77193110
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896235"
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Ochrona programu SQL Server przy użyciu odzyskiwanie po awarii programu SQL Server i usługi Azure Site Recovery

W tym artykule opisano, jak chronić wewnętrznej programu SQL Server przy użyciu kombinacji ciągłość prowadzenia działalności biznesowej programu SQL Server i technologii (BCDR) odzyskiwania po awarii, aplikacji i [usługi Azure Site Recovery](site-recovery-overview.md).

Przed rozpoczęciem upewnij się, że rozumiesz możliwości odzyskiwania po awarii programu SQL Server, łącznie z klastra trybu failover, zawsze włączonych grup dostępności funkcji dublowania baz danych i wysyłania dziennika.


## <a name="sql-server-deployments"></a>Wdrożenie programu SQL Server

Wiele obciążeń Użyj programu SQL Server jako podstawę i może być zintegrowana z aplikacji, takich jak SharePoint, Dynamics i SAP, wdrożenie usługi danych.  SQL Server może być wdrożony na kilka sposobów:

* **Autonomicznego serwera SQL**: SQL Server i wszystkie bazy danych znajdują się na pojedynczym komputerze (fizycznej lub wirtualnej). Podczas wirtualizacji, klaster hosta jest używana do lokalnego wysokiej dostępności. Wysoka dostępność poziomie gościa nie jest zaimplementowana.
* **Tryb Failover Clustering wystąpienia programu SQL Server (zawsze na FCI)**: co najmniej dwa węzły z uruchomionym programem SQL Server instancji z udostępnionych dysków są skonfigurowane w klastrze pracy awaryjnej systemu Windows. Jeśli węzeł jest wyłączony, klaster może przełączyć programu SQL Server do innego wystąpienia. Ta konfiguracja jest zwykle używane do implementacji wysokiej dostępności w lokacji głównej. To wdrożenie nie chroni przed uszkodzenie lub awaria w warstwie magazynu udostępnionego. Udostępniony dysk może być zaimplementowany przy użyciu udostępnionego dysku vhdx, fiber channel lub iSCSI.
* **SQL zawsze włączonych grup dostępności**: co najmniej dwa węzły są konfigurowane w udostępnionym klastra nic z baz danych serwera SQL skonfigurowany w grupie dostępności, Replikacja synchroniczna i automatycznej pracy awaryjnej.

 W tym artykule wykorzystuje następujące natywnego SQL awaryjnego odzyskiwania technologie do odzyskiwania baz danych do zdalnej witryny:

* SQL zawsze włączonych grup dostępności, zapewnia odzyskiwania po awarii dla programu SQL Server 2012 lub 2014 Enterprise Edition.
* Funkcja dublowania bazy danych SQL w trybie wysokiego bezpieczeństwa, SQL Server Standard edition (dowolna wersja) lub SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji

### <a name="supported-scenarios"></a>Obsługiwane scenariusze
Usługa Site Recovery może chronić programu SQL Server, zgodnie z opisem w tabeli.

**Scenariusz** | **Do lokacji dodatkowej** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Serwer fizyczny** | Yes | Yes
**Azure**|Nie dotyczy| Yes

### <a name="supported-sql-server-versions"></a>Obsługiwane wersje programu SQL Server
Te wersje programu SQL Server są obsługiwane dla obsługiwanych scenariuszach:

* SQL Server 2016 Enterprise i Standard
* SQL Server 2014 Enterprise i Standard
* SQL Server 2012 Enterprise i Standard
* SQL Server 2008 R2 Enterprise i Standard

### <a name="supported-sql-server-integration"></a>Obsługiwaną integracji programu SQL Server

Usługa Site Recovery można zintegrować z natywnego technologiami BCDR serwera SQL podsumowane w tabeli, aby zapewnić rozwiązanie odzyskiwania po awarii.

**Funkcja** | **Szczegóły** | **SQL Server** |
--- | --- | ---
**Konfigurowanie zawsze włączonej grupy dostępności** | Uruchamianie wielu wystąpień autonomicznych programu SQL Server w klastrze trybu failover, który ma wiele węzłów.<br/><br/>Bazy danych można grupować w grupy trybu failover, które mogą zostać skopiowane (dublowany) w wystąpieniach programu SQL Server, aby niezbędne jest brak udostępnionego magazynu.<br/><br/>Zapewnia odzyskiwanie po awarii między lokacją główną i co najmniej jednej lokacji dodatkowej. Dwa węzły można skonfigurować w udostępnionej nic skonfigurowany klaster z baz danych programu SQL Server w grupie dostępności Replikacja synchroniczna i automatycznej pracy awaryjnej. | SQL Server 2016, programu SQL Server 2014 i SQL Server 2012 Enterprise edition
**(Zawsze na FCI dla) klastra trybu failover** | SQL Server korzysta z systemu Windows klastra trybu failover dla wysokiej dostępności lokalnego obciążeń programu SQL Server.<br/><br/>Węzły uruchomione wystąpienia programu SQL Server z udostępnionych dysków są skonfigurowane w klastrze pracy awaryjnej. Jeśli wystąpienie jest wyłączony klastra nie powiedzie się na inny.<br/><br/>Klaster nie chroni przed awarią lub awarie w magazynie udostępnionym. Udostępniony dysk może być zaimplementowany z interfejsu iSCSI, fiber channel, lub udostępnionego Vhdx. | SQL Server Enterprise Edition<br/><br/>SQL Server Standard edition (maksymalnie dwa węzły tylko)
**(Tryb wysokiego bezpieczeństwa) dublowania bazy danych** | Chroni pojedynczej bazy danych do pojedynczej kopii dodatkowej. Dostępne w obu wysokiego bezpieczeństwa (synchroniczne) i wysokiej wydajności replikacji (asynchronicznej) trybów. Nie wymaga klastra pracy awaryjnej. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise wszystkie wersje
**Autonomiczny program SQL Server** | SQL Server i bazy danych znajdują się na jednym serwerze (fizycznych lub wirtualnych). Klaster hostów jest używana wysokiej dostępności, gdy serwer jest wirtualnego. Nie wysokiej dostępności poziomie gościa. | Enterprise lub Standard edition

## <a name="deployment-recommendations"></a>Zalecenia dotyczące wdrożenia

Ta tabela zawiera podsumowanie Nasze zalecenia dotyczące integracji z usługą Site Recovery technologiami BCDR serwera SQL.

| **Wersja** | **Wersja** | **Wdrożenie** | **Wersja Premium do lokalnego** | **Wersja Premium na platformie Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 lub 2012 |Enterprise |Wystąpienie klastra pracy awaryjnej |Zawsze włączone grupy dostępności |Zawsze włączone grupy dostępności |
|| Enterprise |Zawsze włączone grupy dostępności wysokiej dostępności |Zawsze włączone grupy dostępności |Zawsze włączone grupy dostępności | |
|| Standardowa (Standard) |Wystąpienia klastra trybu failover (FCI) |Replikacja z lokacji odzyskiwania dublowaniem lokalnego |Replikacja z lokacji odzyskiwania dublowaniem lokalnego | |
|| Enterprise lub Standard |Autonomiczna |Replikacja odzyskiwania lokacji |Replikacja odzyskiwania lokacji | |
| SQL Server 2008 R2 lub 2008 |Enterprise lub Standard |Wystąpienia klastra trybu failover (FCI) |Replikacja z lokacji odzyskiwania dublowaniem lokalnego |Replikacja z lokacji odzyskiwania dublowaniem lokalnego |
|| Enterprise lub Standard |Autonomiczna |Replikacja odzyskiwania lokacji |Replikacja odzyskiwania lokacji | |
| SQL Server (dowolna wersja) |Enterprise lub Standard |Wystąpienie klastra pracy awaryjnej — aplikacji usługi DTC |Replikacja odzyskiwania lokacji |Nieobsługiwane |

## <a name="deployment-prerequisites"></a>Wymagania wstępne dotyczące wdrożenia

* Lokalnego wdrożenia serwera SQL, zainstalowana obsługiwana wersja programu SQL Server. Zazwyczaj należy również usługi Active Directory dla programu SQL server.
* Wymagania dotyczące tego scenariusza, którą chcesz wdrożyć. Dowiedz się więcej o wymaganiach dotyczących pomocy technicznej dla [replikacji do platformy Azure](site-recovery-support-matrix-to-azure.md) i [lokalnymi](site-recovery-support-matrix.md), i [wymagania wstępne dotyczące wdrażania](site-recovery-prereq.md).
* Aby skonfigurować odzyskiwania na platformie Azure, uruchom [oceny gotowości maszyny wirtualnej Azure](http://www.microsoft.com/download/details.aspx?id=40898) narzędzia na maszynach wirtualnych programu SQL Server, aby się upewnić, że są one zgodne z platformy Azure i usługi Site Recovery.

## <a name="set-up-active-directory"></a>Konfigurowanie usługi Active Directory

Konfigurowanie usługi Active Directory, w lokacji dodatkowej odzyskiwania dla programu SQL Server do prawidłowego działania.

* **Małe przedsiębiorstwa**— z mniejszą liczbą aplikacji i jednego kontrolera domeny do lokalnej lokacji, aby przełączyć w całej lokacji, zalecane jest użycie usługi Site Recovery replikacji replikacji kontrolera domeny na serwerze pomocniczym Centrum danych, lub na platformie Azure.
* **Średnich i dużych przedsiębiorstw**— Jeśli masz wiele aplikacji w lesie usługi Active Directory i chcesz zakończyć się niepowodzeniem przez aplikację lub obciążenia, zaleca się Konfigurowanie dodatkowego kontrolera domeny w dodatkowego centrum danych lub na platformie Azure. Jeśli używasz zawsze włączonych grup dostępności do odzyskania z lokacją zdalną, zaleca się Konfigurowanie innego dodatkowego kontrolera domeny w lokacji dodatkowej lub na platformie Azure do użycia na potrzeby odzyskanego wystąpienia programu SQL Server.

Instrukcje w tym artykule zakładają, że kontroler domeny jest dostępny w lokalizacji dodatkowej. [Dowiedz się więcej](site-recovery-active-directory.md) o ochronie usługi Active Directory z usługą Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrowanie z programu SQL Server AlwaysOn dla replikacji do platformy Azure

Oto, co należy zrobić:

1. Importuj skryptów do konta usługi Automatyzacja Azure. Zawiera skrypty do trybu failover grupy dostępności SQL w [maszyny wirtualnej usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) i [maszynę wirtualną w klasycznym](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Dodaj ASR-SQL-FailoverAG jako akcją przed pierwszą grupą planu odzyskiwania.

1. Wykonaj instrukcje dostępne w skrypcie tworzenia zmiennej automatyzacji, aby podać nazwę grupy dostępności.

### <a name="steps-to-do-a-test-failover"></a>Kroki, aby wykonać test trybu failover

Program SQL AlwaysOn nie obsługuje natywnie testowy tryb failover. Dlatego zaleca się następujące czynności:

1. Konfigurowanie [kopia zapasowa Azure](../backup/backup-azure-arm-vms.md) na maszynie wirtualnej, który jest hostem repliki grupy dostępności na platformie Azure.

1. Aby mogło nastąpić wyzwolenie testowy tryb failover planu odzyskiwania, należy odzyskać maszynę wirtualną z kopii zapasowej w poprzednim kroku.

    ![Przywracanie z kopii zapasowej systemu Azure ](./media/site-recovery-sql/restore-from-backup.png)

1. [Wymuszenie kworum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na maszynie wirtualnej przywróconej z kopii zapasowej.

1. Zaktualizuj IP odbiornika do adresu IP, które są dostępne w testowej sieci trybu failover.

    ![Zaktualizuj odbiornik IP](./media/site-recovery-sql/update-listener-ip.png)

1. Przełącz odbiornika w trybie online.

    ![Przełącz do trybu Online odbiornika](./media/site-recovery-sql/bring-listener-online.png)

1. Tworzenie modułu równoważenia obciążenia z jednego adresu IP utworzone w puli adresów IP frontonu odpowiadającej każdego odbiornika grupy dostępności i maszyny wirtualnej SQL dodane w puli zaplecza.

     ![Tworzenie usługi równoważenia obciążenia - puli adresów IP frontonu ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Tworzenie usługi równoważenia obciążenia - puli wewnętrznej bazy danych ](./media/site-recovery-sql/create-load-balancer2.png)

1. Wykonaj test trybu failover planu odzyskiwania.

### <a name="steps-to-do-a-failover"></a>Kroki, aby tryb failover

Po dodaniu skrypt w planie odzyskiwania i zweryfikowany, wykonując test trybu failover planu odzyskiwania, można wykonywać trybu failover planu odzyskiwania.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integrowanie z programu SQL Server AlwaysOn dla replikacji do lokacji dodatkowej lokalnymi

Jeśli program SQL Server używa grup dostępności dla wysokiej dostępności (lub FCI), zaleca się przy użyciu grup dostępności w lokacji odzyskiwania również. Należy zwrócić uwagę, to ma zastosowanie do aplikacji, które nie używają transakcji rozproszonych.

1. [Skonfuguruj](https://msdn.microsoft.com/library/hh213078.aspx) do grupy dostępności.
1. Tworzenie sieci wirtualnej w lokacji dodatkowej.
1. Konfigurowanie połączenia sieci VPN lokacja lokacja między sieci wirtualnej i lokacji głównej.
1. Utwórz maszynę wirtualną w lokacji odzyskiwania, a SQL Server należy zainstalować na nim.
1. Rozszerzyć istniejącą zawsze włączonych grup dostępności nową maszynę Wirtualną programu SQL Server. Konfigurowania tego wystąpienia programu SQL Server jako kopia repliki asynchronicznego.
1. Utwórz odbiornik grupy dostępności, lub zaktualizować istniejący odbiornik, aby dołączyć maszynę wirtualną repliki asynchronicznego.
1. Upewnij się, że farmy aplikacji jest skonfigurowany przy użyciu odbiornika. Jeśli ustawianie przy użyciu nazwy serwera bazy danych, zaktualizuj go do użycia odbiornika, więc nie trzeba go ponownie skonfigurować po przejściu w tryb failover.

W przypadku aplikacji korzystających z transakcji rozproszonych, zaleca się wdrażania usługi Site Recovery z [replikacji do lokacji serwera VMware/fizyczne](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Zagadnienia dotyczące planu odzyskiwania
1. Dodaj ten przykładowy skrypt do biblioteki programu VMM w lokacjach głównych i dodatkowych.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Po utworzeniu planu odzyskiwania dla aplikacji, należy dodać akcję sprzed do kroku inicjowanych przez skrypty grupy-1, który wywołuje skrypt do trybu failover grupy dostępności.

## <a name="protect-a-standalone-sql-server"></a>Ochrona autonomicznego serwera SQL

W tym scenariuszu zalecane jest użycie replikacji usługi Site Recovery do ochrony komputera programu SQL Server. Dokładne kroki zależy, czy serwer SQL jest maszyny Wirtualnej lub serwerze fizycznym i określa, czy chcesz replikować do platformy Azure lub dodatkowej lokacji lokalnymi. Dowiedz się więcej o [scenariuszy odzyskiwania lokacji](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Ochrona klastra programu SQL Server (standard edition/Windows Server 2008 R2)

W przypadku klastra z programem SQL Server Standard edition lub SQL Server 2008 R2, firma Microsoft zaleca się, że używasz replikacji usługi Site Recovery do ochrony programu SQL Server.

### <a name="on-premises-to-on-premises"></a>Ze środowiska lokalnego do środowiska lokalnego

* Jeśli aplikacja korzysta z transakcji rozproszonych zalecamy wdrożeniem [odzyskiwania lokacji za pomocą replikacji SAN](site-recovery-vmm-san.md) w środowisku funkcji Hyper-V lub [serwera VMware/fizyczne do programu VMware](site-recovery-vmware-to-vmware.md) w środowisku VMware.
* Dla aplikacji z systemem innym niż DTC należy użyć metody powyżej, aby odzyskać klaster jako serwer autonomiczny, wykorzystując lokalnego wysokiego bezpieczeństwa dublowania bazy danych.

### <a name="on-premises-to-azure"></a>Dane lokalne na platformie Azure

Usługa Site Recovery nie zapewnia gościa Obsługa klastrów, podczas replikacji do platformy Azure. Dla wersji Standard programu SQL Server również nie zapewnia rozwiązanie odzyskiwania po awarii niskich kosztach. W tym scenariuszu Zalecamy ochronę lokalnego klastra programu SQL Server do autonomicznego serwera SQL i odzyskać ją na platformie Azure.

1. Skonfiguruj dodatkowe autonomiczne wystąpienie programu SQL Server w lokacji lokalnej.
1. Skonfiguruj wystąpienie jako dublowania baz danych, które chcesz chronić. Konfigurowanie funkcji dublowania w trybie wysokiego bezpieczeństwa.
1. Konfigurowanie usługi Site Recovery w lokacji lokalnej dla ([funkcji Hyper-V](site-recovery-hyper-v-site-to-azure.md) lub [VMware maszyny wirtualne/serwery fizyczne)](site-recovery-vmware-to-azure-classic.md).
1. Replikacja usługi Site Recovery umożliwia replikowanie nowe wystąpienie programu SQL Server na platformę Azure. Ponieważ jest kopii duplikatu wysokiego bezpieczeństwa, zostanie on zsynchronizowany z klastra głównego, ale będą replikowane do platformy Azure przy użyciu replikacji usługi Site Recovery.


![Klaster w warstwie Standardowa](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Zagadnienia dotyczące powrotu po awarii

W przypadku klastrów programu SQL Server Standard powrotu po awarii po nieplanowany tryb failover wymaga kopii zapasowej serwera SQL i przywracania z wystąpienia duplikatu do oryginalnego klastra reestablishment duplikatu.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](site-recovery-components.md) o architekturze usługi Site Recovery.
