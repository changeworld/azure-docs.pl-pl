---
title: Odzyskiwanie po awarii bazy danych SQL | Dokumentacja firmy Microsoft
description: Jak odzyskać bazę danych z datacenter regionalnej awarii lub niepowodzenia aktywna replikacja geograficzna bazy danych SQL Azure i możliwości przywracania geo.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 6ac4b26e1b014da792791ca657c9f51230a135b5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Przywracanie bazy danych SQL Azure lub trybu failover do dodatkowej
Baza danych SQL Azure oferuje następujące możliwości odzyskiwania po awarii:

* [Replikacja geograficzna i trybu failover grupy aktywne](sql-database-geo-replication-overview.md)
* [geograficzne](sql-database-recovery-using-backups.md#point-in-time-restore)
* [Strefowo nadmiarowy baz danych](sql-database-high-availability.md)

Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej i funkcje obsługi tych scenariuszy, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md).

> [!NOTE]
> Jeśli używasz strefowo nadmiarowy bazy danych Premium lub biznesowe krytyczne lub pule (wersja zapoznawcza) jest zautomatyzowany proces odzyskiwania i pozostałej części tego materiału nie ma zastosowania. 

### <a name="prepare-for-the-event-of-an-outage"></a>Przygotowanie na wypadek wystąpienia awarii
Do poprawnego działania odzyskiwania do innego obszaru danych przy użyciu grup pracy awaryjnej lub geograficznie nadmiarowego kopii zapasowych, które trzeba przygotować serwer w innym centrum danych awarii, aby stać się nowym serwerem podstawowym należy wystąpić również mają dobrze zdefiniowanego czynności opisanych i przetestowane w celu zapewnienia sprawnego odzyskiwania. Te kroki przygotowania obejmują:

* Zidentyfikuj serwer logiczny w innym regionie, aby stać się nowym serwerem podstawowym. Do przywrócenia geograficznie, zazwyczaj jest to serwer w [sparowanego region](../best-practices-availability-paired-regions.md) dla regionu, w którym znajduje się baza danych. Eliminuje to koszt dodatkowy ruch podczas operacji przywracania geo.
* Identyfikowanie i opcjonalnie zdefiniować, reguły zapory poziomu serwera potrzebne użytkownikom dostęp do nowych głównej bazy danych.
* Określ, jak zamierzasz przekierować użytkowników do nowym serwerem podstawowym, takich jak, zmieniając parametry połączenia lub zmieniając wpisy DNS.
* Identyfikowanie i opcjonalnie utworzyć, logowania, które musi znajdować się w bazie danych master w nowym serwerem podstawowym i upewnij się, że te logowania do odpowiednich uprawnień w bazie danych master, jeśli istnieje. Aby uzyskać więcej informacji, zobacz [zabezpieczeń bazy danych SQL po awarii](sql-database-geo-replication-security-config.md)
* Określ reguły alertów, które muszą zostać zaktualizowane do mapowania na nowe podstawowej bazy danych.
* Dokument inspekcji konfiguracji podstawowej bazy danych
* Wykonaj [wyszczególniania odzyskiwania po awarii](sql-database-disaster-recovery-drills.md). Do symulacji awarii do przywrócenia geograficznie, można usunąć lub zmienić nazwy źródłowej bazy danych, aby spowodować błąd łączności aplikacji. Aby symulować awarii przy użyciu grup trybu failover, można wyłączyć aplikacji sieci web lub połączonej z bazy danych lub bazy danych w tryb failover maszyny wirtualnej powodować awarie połączenia z aplikacji.

## <a name="when-to-initiate-recovery"></a>Kiedy należy rozpocząć odzyskiwania
Operacja odzyskiwania ma wpływ na aplikację. Konieczna jest zmiana parametrów połączenia SQL lub przekierowania przy użyciu serwera DNS, a może spowodować utratę danych trwałych. W związku z tym należy to zrobić tylko w przypadku awarii prawdopodobnie może trwać dłużej niż celu czasu odzyskiwania aplikacji. Gdy aplikacja jest wdrażana w środowisku produkcyjnym należy wykonać regularnego monitorowania kondycji aplikacji i użyć następujących punktów danych do potwierdzenia, że jest to uzasadnione odzyskiwania:

1. Trwałe połączenie nie powiodło się z warstwy aplikacji bazy danych.
2. Azure portal zawiera alert o zdarzenia w regionie szerokie wpływu.

> [!NOTE]
> Jeśli używasz trybu failover grupy i wybrać automatycznej pracy awaryjnej, proces odzyskiwania jest automatyczne i niewidoczne dla aplikacji. 

W zależności od ustawiona tolerancja aplikacji czasem przestoju oraz możliwości biznesowe odpowiedzialności należy wziąć pod uwagę następujące opcje odzyskiwania.

Użyj [pobrać możliwych do odzyskania bazy danych](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) można pobrać najnowszy punkt przywracania z replikacją geograficzną.

## <a name="wait-for-service-recovery"></a>Poczekaj, aż usługi odzyskiwania
Pracy Azure zespoły dokładnie, aby przywrócić dostępność usługi, jak szybko jak to możliwe, ale w zależności od głównego spowodować ich może zająć godziny i dni.  Jeśli aplikacja może tolerować znaczących przestoju możesz po prostu poczekać odzyskiwania zakończyć. W takim przypadku jest wymagana żadna akcja ze strony użytkownika. Możesz wyświetlać bieżący stan usługi na naszych [pulpit nawigacyjny kondycji usługi Azure](https://azure.microsoft.com/status/). Po odzyskaniu region dostępność aplikacji został przywrócony.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Awaryjnie na serwer pomocniczy replikacją geograficzną, w grupie trybu failover
Jeśli przestój aplikacji może spowodować odpowiedzialności firm, należy używać grup trybu failover. Umożliwia aplikacji do szybkiego przywrócenia dostępności w innym regionie, w razie awarii. Dowiedz się, jak [Konfigurowanie trybu failover grup](sql-database-geo-replication-portal.md).

Aby przywrócić dostępności baz danych, należy zainicjować trybu failover na serwer pomocniczy przy użyciu jednej z obsługiwanych metod.

Do pracy awaryjnej pomocniczej bazy danych replikacją geograficzną, użyj jednej z następujących przewodnikach:

* [Awaryjnie na serwer pomocniczy replikacją geograficzną przy użyciu portalu Azure](sql-database-geo-replication-portal.md)
* [Awaryjnie na serwer pomocniczy przy użyciu programu PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)

## <a name="recover-using-geo-restore"></a>Odzyskać, używając funkcji przywracania geo
Jeśli przestój aplikacji nie powoduje odpowiedzialności firm można użyć [geograficzne](sql-database-recovery-using-backups.md) jako metoda odzyskiwania baz danych z aplikacji. Tworzy kopię bazy danych z jego najnowszej kopii zapasowej geograficznie nadmiarowy.

## <a name="configure-your-database-after-recovery"></a>Konfigurowanie bazy danych po odzyskaniu
Jeśli korzystasz z przywracaniem geograficznym odzyskiwania po awarii, należy się upewnić, że łączność nowych baz danych jest skonfigurowany prawidłowo, dzięki czemu można wznowić funkcja normalne aplikacji. Jest to lista kontrolna zadań w celu przygotowania produkcyjnego odzyskanej bazy danych.

### <a name="update-connection-strings"></a>Zaktualizuj parametry połączenia
Ponieważ odzyskanej bazy danych znajduje się w innym serwerze, należy zaktualizować parametry połączenia aplikacji, aby wskazywały na tym serwerze.

Aby uzyskać więcej informacji na temat zmiany parametrów połączenia, zobacz język programowanie odpowiednie dla Twojej [biblioteki połączeń](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory
Należy się upewnić, że reguły zapory skonfigurowane na serwerze i w bazie danych są zgodne te, które zostały skonfigurowane na serwerze podstawowym i podstawowej bazy danych. Aby uzyskać więcej informacji, zobacz [porady: Konfigurowanie ustawień zapory (baza danych SQL Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurowanie logowania i bazy danych użytkowników
Należy się upewnić, że wszystkie dane logowania używany przez aplikację istnieje na serwerze, który jest hostem odzyskanej bazy danych. Aby uzyskać więcej informacji, zobacz [konfiguracji zabezpieczeń — replikacja geograficzna](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Skonfigurowanie i testowanie reguł zapory serwera i logowań (i ich uprawnienia) podczas wyszczególniania odzyskiwania po awarii. Te obiekty z poziomu serwera i ich konfiguracji może nie być dostępne podczas awarii.
> 
> 

### <a name="setup-telemetry-alerts"></a>Ustaw alerty telemetrii
Należy upewnić się, że istniejące ustawienia reguły alertu zostaną zaktualizowane do mapowania odzyskanej bazy danych i inny serwer.

Aby uzyskać więcej informacji o regułach alertów bazy danych, zobacz [odbieranie powiadomień Alert](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) i [kondycja usługi Śledź](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Włączanie inspekcji
Jeśli inspekcja jest wymagany dostęp do bazy danych, musisz włączyć inspekcję po odzyskaniu bazy danych. Aby uzyskać więcej informacji, zobacz [inspekcji bazy danych](sql-database-auditing.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat usługi Azure SQL bazy danych automatycznego tworzenia kopii zapasowych, zobacz [bazy danych SQL automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md)
* Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej projektu i odzyskiwania, zobacz [ciągłości scenariuszy](sql-database-business-continuity.md)
* Aby dowiedzieć się więcej o używaniu kopie zapasowe automatycznego odzyskiwania, zobacz [przywrócić bazę danych z kopii zapasowych inicjowane przez usługę](sql-database-recovery-using-backups.md)

