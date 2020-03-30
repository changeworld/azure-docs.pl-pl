---
title: Odzyskiwanie po awarii
description: Dowiedz się, jak odzyskać bazę danych z regionalnej awarii lub awarii centrum danych za pomocą aktywnej replikacji geograficznej usługi Azure SQL Database i funkcji przywracania geograficznego.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: d28edd28dcbe31bfe63c2d0a9c3e975967efef04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256382"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Przywracanie bazy danych SQL platformy Azure lub pracy awaryjnej do pomocniczego

Usługa Azure SQL Database oferuje następujące możliwości odzyskiwania po awarii:

- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md)
- [Grupy automatycznego trybu failover](sql-database-auto-failover-group.md)
- [Przywracanie geograficzne](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Bazy danych nadmiarowe stref](sql-database-high-availability.md)

Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej i funkcjach obsługujących te scenariusze, zobacz [Ciągłość działania](sql-database-business-continuity.md).

> [!NOTE]
> Jeśli używasz strefowych baz danych lub pul Premium lub Business Critical, proces odzyskiwania jest zautomatyzowany, a pozostała część tego materiału nie ma zastosowania.

> [!NOTE]
> Bazy danych podstawowych i pomocniczych muszą mieć tę samą warstwę usług. Zdecydowanie zaleca się również utworzenie pomocniczej bazy danych o tym samym rozmiarze obliczeniowym (DTU lub vCores) co podstawowa. Aby uzyskać więcej informacji, zobacz [Uaktualnianie lub obniżanie wersji jako podstawowej bazy danych](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database).

> [!NOTE]
> Użyj jednej lub kilku grup trybu failover, aby zarządzać funkcją failover wielu baz danych.
> Jeśli dodasz istniejącą relację replikacji geograficznej do grupy trybu failover, upewnij się, że geo-pomocniczy jest skonfigurowany z tą samą warstwą usług i rozmiarem obliczeń co podstawowy. Aby uzyskać więcej informacji, zobacz [Używanie grup automatycznego trybu failover w celu włączenia przezroczystego i skoordynowanego trybu failover wielu baz danych](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Przygotuj się na awarię

Aby zakończyć się powodzeniem w przypadku odzyskiwania danych do innego regionu danych przy użyciu grup trybu failover lub geob nadmiarowych kopii zapasowych, należy przygotować serwer w innej awarii centrum danych, aby stać się nowym serwerem podstawowym, jeśli zajdzie taka potrzeba, a także mieć dobrze zdefiniowane kroki udokumentowane i w celu zapewnienia sprawnego powrotu do zdrowia. Te kroki przygotowania obejmują:

- Zidentyfikuj serwer bazy danych SQL w innym regionie, aby stać się nowym serwerem podstawowym. W przypadku przywracania geograficznego jest to zazwyczaj serwer w [regionie sparowanym](../best-practices-availability-paired-regions.md) dla regionu, w którym znajduje się baza danych. Eliminuje to dodatkowe koszty ruchu podczas operacji przywracania geograficznego.
- Identyfikowanie i opcjonalnie definiowanie reguł zapory IP na poziomie serwera wymaganych dla użytkowników do uzyskania dostępu do nowej podstawowej bazy danych.
- Określ, w jaki sposób chcesz przekierować użytkowników na nowy serwer podstawowy, na przykład zmieniając parametry połączenia lub zmieniając wpisy DNS.
- Identyfikować i opcjonalnie tworzyć, logowania, które muszą być obecne w głównej bazie danych na nowym serwerze podstawowym i upewnij się, że te logowania mają odpowiednie uprawnienia w głównej bazie danych, jeśli istnieje. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia bazy danych SQL po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md)
- Identyfikowanie reguł alertów, które należy zaktualizować, aby zamapować do nowej podstawowej bazy danych.
- Dokumentowanie konfiguracji inspekcji w bieżącej podstawowej bazie danych
- Wykonaj [ćwiczenie odzyskiwania po awarii](sql-database-disaster-recovery-drills.md). Aby symulować awarię przywracania geograficznego, można usunąć lub zmienić nazwę źródłowej bazy danych, aby spowodować błąd łączności aplikacji. Aby symulować awarię przy użyciu grup trybu failover, można wyłączyć aplikację sieci web lub maszynę wirtualną połączoną z bazą danych lub przełączyć ją w tryb failover w celu spowodowania awarii łączności aplikacji.

## <a name="when-to-initiate-recovery"></a>Kiedy zainicjować odzyskiwanie

Operacja odzyskiwania wpływa na aplikację. Wymaga zmiany ciągu połączenia SQL lub przekierowania przy użyciu systemu DNS i może spowodować trwałą utratę danych. W związku z tym należy to zrobić tylko wtedy, gdy awaria może trwać dłużej niż cel czasu odzyskiwania aplikacji. Gdy aplikacja jest wdrażana w produkcji należy wykonać regularne monitorowanie kondycji aplikacji i użyć następujących punktów danych, aby potwierdzić, że odzyskiwanie jest uzasadnione:

1. Błąd stałej łączności z warstwy aplikacji do bazy danych.
2. Portal platformy Azure zawiera alert o incydencie w regionie o szerokim wpływie.

> [!NOTE]
> Jeśli używasz grup trybu failover i wybierz automatyczne trybu failover, proces odzyskiwania jest zautomatyzowany i przejrzysty dla aplikacji.

W zależności od tolerancji aplikacji na przestoje i ewentualnej odpowiedzialności biznesowej można rozważyć następujące opcje odzyskiwania.

Użyj [get recoverable database](https://msdn.microsoft.com/library/dn800985.aspx) *(LastAvailableBackupDate*), aby uzyskać najnowszy punkt przywracania replikowane geograficznie.

## <a name="wait-for-service-recovery"></a>Poczekaj na odzysk usługi

Zespoły platformy Azure pracują pilnie, aby przywrócić dostępność usługi tak szybko, jak to możliwe, ale w zależności od głównej przyczyny może to potrwać wiele godzin lub dni.  Jeśli aplikacja może tolerować znaczne przestoje można po prostu poczekać na zakończenie odzyskiwania. W takim przypadku nie jest wymagane żadne działanie z Twojej strony. Bieżący stan usługi można zobaczyć na naszym [pulpicie nawigacyjnym usługi Azure Service Health.](https://azure.microsoft.com/status/) Po odzyskaniu regionu dostępność aplikacji zostanie przywrócona.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Praca awaryjna na serwerze pomocniczym replikowanym geograficznie w grupie trybu failover

Jeśli przestoje aplikacji może spowodować odpowiedzialność biznesową, należy używać grup trybu failover. Umożliwia aplikacji szybko przywrócić dostępność w innym regionie w przypadku awarii. Aby zapoznać się z samouczkiem, zobacz [Implementowanie geograficznie rozproszonej bazy danych](sql-database-implement-geo-distributed-database.md).

Aby przywrócić dostępność baz danych, należy zainicjować od pracy awaryjnej na serwerze pomocniczym przy użyciu jednej z obsługiwanych metod.

Użyj jednego z następujących przewodników, aby przejść w stan fail over do pomocniczej bazy danych replikowanej geograficznie:

- [Przeładuj awaryjnie serwer pomocniczy replikowany geograficznie za pomocą portalu Azure](sql-database-geo-replication-portal.md)
- [Przeładuj awaryjnie na serwerze pomocniczym przy użyciu programu PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Przeładuj awaryjnie na serwerze pomocniczym przy użyciu usługi Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Odzyskiwanie przy użyciu przywracania geograficznego

Jeśli przestój aplikacji nie powoduje odpowiedzialności biznesowej, można użyć [przywracania geograficznego](sql-database-recovery-using-backups.md) jako metody odzyskiwania baz danych aplikacji. Tworzy kopię bazy danych z najnowszej kopii zapasowej geob nadmiarowej.

## <a name="configure-your-database-after-recovery"></a>Konfigurowanie bazy danych po odzyskaniu

Jeśli używasz przywracania geograficznego do odzyskania po awarii, należy upewnić się, że łączność z nowymi bazami danych jest poprawnie skonfigurowana, aby można było wznowić normalną funkcję aplikacji. Jest to lista kontrolna zadań, aby przygotować produkcję odzyskanej bazy danych.

### <a name="update-connection-strings"></a>Aktualizowanie ciągów połączeń

Ponieważ odzyskana baza danych znajduje się na innym serwerze, należy zaktualizować parametry połączenia aplikacji, aby wskazywały ten serwer.

Aby uzyskać więcej informacji na temat zmieniania ciągów połączeń, zobacz odpowiedni język programowania dla [biblioteki połączeń](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory

Należy upewnić się, że reguły zapory skonfigurowane na serwerze i w bazie danych są zgodne z regułami skonfigurowanych na serwerze podstawowym i podstawowej bazie danych. Aby uzyskać więcej informacji, zobacz [Jak: Konfigurowanie ustawień zapory (azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurowanie loginów i użytkowników bazy danych

Należy upewnić się, że wszystkie logowania używane przez aplikację istnieją na serwerze, który jest gospodarzem odzyskanej bazy danych. Aby uzyskać więcej informacji, zobacz [Konfiguracja zabezpieczeń dla replikacji geograficznej](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Podczas ćwiczenia odzyskiwania po awarii należy skonfigurować i przetestować reguły zapory serwera i loginy (i ich uprawnienia). Te obiekty na poziomie serwera i ich konfiguracja mogą nie być dostępne podczas awarii.

### <a name="setup-telemetry-alerts"></a>Konfigurowanie alertów telemetrycznych

Należy upewnić się, że istniejące ustawienia reguły alertów są aktualizowane w celu mapowania do odzyskanej bazy danych i innego serwera.

Aby uzyskać więcej informacji na temat reguł alertów bazy danych, zobacz [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) i [śledzenie kondycji usługi](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Włącz inspekcję

Jeśli inspekcja jest wymagana do uzyskania dostępu do bazy danych, należy włączyć inspekcję po odzyskaniu bazy danych. Aby uzyskać więcej informacji, zobacz [Inspekcja bazy danych](sql-database-auditing.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych usługi Azure SQL Database, zobacz [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o scenariuszach projektowania i odzyskiwania ciągłości biznesowej, zobacz [Scenariusze ciągłości](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej o używaniu automatycznych kopii zapasowych do odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych usługą](sql-database-recovery-using-backups.md)
