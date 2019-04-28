---
title: Odzyskiwanie po awarii bazy danych SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odzyskać bazę danych po awarii regionalnego centrum danych lub błędu przy użyciu aktywnej replikacji geograficznej usługi Azure SQL Database, a także funkcje przywracania geograficznego.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1e1bc92c684bf6ddbb7dc4ff0f882ad61ddeb27e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075816"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Przywracanie bazy danych SQL Azure lub trybu failover do dodatkowej

Usługa Azure SQL Database oferuje następujące możliwości odzyskiwania sprawności po awarii:

- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md)
- [Grupy automatycznego trybu failover](sql-database-auto-failover-group.md)
- [Funkcja przywracania geograficznego](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Strefowo nadmiarowe bazy danych](sql-database-high-availability.md)

Aby dowiedzieć się więcej na temat ciągłość działania — scenariusze i funkcje obsługi tych scenariuszy, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md).

> [!NOTE]
> Jeśli używasz strefowo nadmiarowych baz danych Premium lub krytyczne dla działania firmy lub pul jest zautomatyzowany proces odzyskiwania, a pozostała część tego materiału nie ma zastosowania.

> [!NOTE]
> Podstawowych i pomocniczych baz danych muszą mieć taką samą warstwę usług. Zalecane jest również zdecydowanie, czy pomocniczej bazy danych został utworzony przy użyciu tych samych obliczeń rozmiaru (jednostki Dtu lub rdzeni wirtualnych) jako podstawowy. Aby uzyskać więcej informacji, zobacz [uaktualnianie, lub obniżenie jako podstawowej bazy danych](sql-database-active-geo-replication.md#upgrading-or-downgrading-a-primary-database).

> [!NOTE]
> Użyj jednej lub kilku grup trybu failover można zarządzać trybem failover wielu baz danych.
> Jeśli dodasz istniejącej relacji replikacji geograficznej do grupy trybu failover upewnij się, że pomocniczej geograficznej jest skonfigurowany z tej samej warstwy usług i rozmiaru obliczeń jako podstawowy. Aby uzyskać więcej informacji, zobacz [umożliwiają automatyczny tryb failover grupy przejrzyste i skoordynowany pracy w trybie failover wielu baz danych](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Przygotuj się na wypadek wystąpienia awarii

Do poprawnego działania odzyskiwania do innego regionu danych przy użyciu grupy trybu failover lub geograficznie nadmiarowych kopii zapasowych, należy przygotować serwer w innym centrum danych awarię w stanie się nowym serwerem podstawowym należy wystąpić również mają dobrze zdefiniowane czynności opisanych i należy przetestować, aby zapewnić sprawne odzyskiwanie. Te kroki przygotowania obejmują:

- Określ serwer bazy danych SQL w innym regionie, aby stać się nowym serwerem podstawowym. Dla przywracania geograficznego, zwykle jest to serwer w [sparowanym regionie](../best-practices-availability-paired-regions.md) dla regionu, w którym znajdują się bazy danych. Pozwala to wyeliminować koszty dodatkowy ruch podczas operacji przywracania geograficznego.
- Identyfikowanie i opcjonalnie zdefiniowanie, reguły zapory poziomu serwera IP potrzebne na użytkownikom dostęp do nowej podstawowej bazy danych.
- Określ, jak chcesz przekierować użytkowników do nowym serwerem podstawowym, takie jak, zmieniając parametry połączenia lub zmieniając wpisy DNS.
- Identyfikowanie i opcjonalnie utworzyć, dane logowania, które muszą znajdować się w bazie danych master w nowym serwerem podstawowym i upewnij się, że te logowania do odpowiednich uprawnień w bazie danych master, jeśli istnieje. Aby uzyskać więcej informacji, zobacz [zabezpieczeń bazy danych SQL Database po awarii](sql-database-geo-replication-security-config.md)
- Określ reguły alertów, które muszą zostać zaktualizowane, aby mapować do nowej podstawowej bazy danych.
- Dokument inspekcji konfiguracji podstawowej bazy danych
- Wykonaj [próbnego odzyskiwania po awarii](sql-database-disaster-recovery-drills.md). Symulowanie awarii przywracania geograficznego, można usunąć lub zmienić nazwę źródłowej bazy danych, aby spowodować, że błąd łączności z aplikacji. Symulowanie awarii przy użyciu grupy trybu failover, można wyłączyć aplikacji sieci web lub maszyny wirtualnej podłączone do bazy danych lub pracy awaryjnej bazy danych, aby spowodować błędów łączności aplikacji.

## <a name="when-to-initiate-recovery"></a>Kiedy należy rozpocząć odzyskiwania

Operacja odzyskiwania ma wpływ na aplikację. Ona konieczna jest zmiana parametrów połączenia SQL lub przekierowania przy użyciu systemu DNS i może spowodować utratę danych trwałych. W związku z tym należy to zrobić tylko w przypadku, gdy awaria zostanie prawdopodobnie wystarczą na dłużej, niż cel czasu odzyskiwania aplikacji. Gdy aplikacja jest wdrażana w środowisku produkcyjnym należy wykonać regularnego monitorowania kondycji aplikacji i użyć następujących punktów danych do potwierdzenia, że jest to uzasadnione odzyskiwania:

1. Błąd trwały łączności z warstwy aplikacji do bazy danych.
2. Witryna Azure portal pokazuje alert o zdarzenia w regionie wywierają duży wpływ.

> [!NOTE]
> Jeśli są używane grupy trybu failover, a wybrana automatycznej pracy awaryjnej, proces odzyskiwania jest zautomatyzowanych i niewidoczna dla aplikacji.

W zależności od aplikacji tolerancji przestojów i możliwych odpowiedzialności biznesowych należy rozważyć następujące opcje odzyskiwania.

Użyj [uzyskać odzyskiwalne bazy danych](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) można pobrać najnowszy punkt przywracania z replikacją geograficzną.

## <a name="wait-for-service-recovery"></a>Poczekaj, aż odzyskiwanie

Pracy zespoły Azure przerwami, aby przywrócić dostępność usługi, jak szybko jak to możliwe, ale w zależności od głównego to spowodowane może zająć godziny lub dni.  Jeśli Twoja aplikacja może tolerować znaczących przestojów, można po prostu poczekaj odzyskiwania zakończyć. W takim wypadku żadna akcja ze strony użytkownika jest wymagana. Można wyświetlić bieżący stan usługi na naszych [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Po odzyskaniu regionu zostanie przywrócony dostępność aplikacji.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Awaryjnie na serwer pomocniczy w grupie trybu failover replikacji geograficznej

W przypadku przerwy w działaniu Twojej aplikacji może spowodować odpowiedzialność firmy, należy używać grupy trybu failover. Umożliwia aplikacji szybkie przywracanie dostępności w innym regionie na wypadek awarii. Aby zapoznać się z samouczkiem, zobacz [Implementowanie rozproszonej geograficznie bazy danych](sql-database-implement-geo-distributed-database.md).

Aby przywrócić dostępność bazy danych, musisz zainicjować trybu failover na serwer pomocniczy przy użyciu jednej z obsługiwanych metod.

Do trybu failover do pomocniczej bazy danych replikowanej geograficznie, użyj jednej z następujących przewodników:

- [Awaryjnie na serwer pomocniczy replikacją geograficzną za pomocą witryny Azure portal](sql-database-geo-replication-portal.md)
- [Awaryjnie na serwer pomocniczy przy użyciu programu PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Awaryjnie na serwer pomocniczy przy użyciu języka Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Odzyskiwanie przy użyciu przywracania geograficznego

Jeśli nie powoduje przestojów aplikacji odpowiedzialność firmy możesz użyć [przywracania geograficznego](sql-database-recovery-using-backups.md) jako metody odzyskiwania baz danych aplikacji. Tworzy kopię bazy danych z magazynu geograficznie nadmiarowego najnowszej kopii zapasowej.

## <a name="configure-your-database-after-recovery"></a>Konfigurowanie bazy danych po odzyskaniu

Jeśli używasz przywracania geograficznego odzyskiwania sprawności po awarii, upewnij się, że łączność z nowych baz danych jest odpowiednio skonfigurowana tak, aby funkcja normalne aplikacji może być wznowione. Jest to lista kontrolna zadań w celu przygotowania odzyskanej bazy danych w środowisku produkcyjnym.

### <a name="update-connection-strings"></a>Zaktualizuj parametry połączenia

Ponieważ odzyskanej bazy danych znajduje się na innym serwerze, należy zaktualizować parametry połączenia aplikacji, aby wskazywała na tym serwerze.

Aby uzyskać więcej informacji na temat zmiany parametrów połączenia, zobacz język programowania odpowiednie dla Twojego [biblioteki połączeń](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory

Należy się upewnić, że reguły zapory skonfigurowane na serwerze i w bazie danych są zgodne te, które zostały skonfigurowane na serwerze podstawowym i podstawowej bazy danych. Aby uzyskać więcej informacji, zobacz [Instrukcje: Konfigurowanie ustawień zapory (usługa Azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurowanie logowania i użytkowników bazy danych

Należy się upewnić, że wszystkie nazwy logowania używanych przez aplikację istnieje na serwerze, który jest hostem odzyskanej bazy danych. Aby uzyskać więcej informacji, zobacz [konfigurację zabezpieczeń dla replikacji geograficznej](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Należy skonfigurować i przetestować reguły zapory serwera i nazwy logowania (i ich uprawnienia) podczas odzyskiwania po awarii. Te obiekty na poziomie serwera i ich konfiguracja może być dostępny podczas awarii.

### <a name="setup-telemetry-alerts"></a>Skonfigurować alerty dotyczące danych telemetrycznych

Należy się upewnić, że Twoje istniejące ustawienia reguły alertu są aktualizowane do mapowania odzyskanej bazy danych i inny serwer.

Aby uzyskać więcej informacji na temat reguł alertów bazy danych, zobacz [odbieranie powiadomień Alert](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) i [Track Service Health](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Włączanie inspekcji

Jeśli inspekcja jest wymagany dostęp do bazy danych, musisz włączyć inspekcję po odzyskaniu bazy danych. Aby uzyskać więcej informacji, zobacz [inspekcja bazy danych](sql-database-auditing.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o scenariuszach projektu i odzyskiwania ciągłości biznesowej, zobacz [ciągłość działania — scenariusze](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do odzyskania, zobacz [przywrócić bazę danych z kopii zapasowych zainicjowanych przez usługę](sql-database-recovery-using-backups.md)
