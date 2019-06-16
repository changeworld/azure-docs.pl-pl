---
title: Stopniowe uaktualnienia aplikacji — Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą replikacji geograficznej usługi Azure SQL Database obsługuje uaktualnień aplikacji w chmurze w trybie online.
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
ms.date: 02/13/2019
ms.openlocfilehash: 47fd6c1e2bb342bc1a31fb16a45a5ebc749dca69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702677"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Zarządzanie przeprowadzania uaktualnienia równoległych aplikacji w chmurze przy użyciu aktywnej replikacji geograficznej bazy danych SQL

Dowiedz się, jak używać [aktywnej replikacji geograficznej](sql-database-auto-failover-group.md) w bazie danych SQL Azure w celu umożliwienia przeprowadzania uaktualnienia aplikacji w chmurze. Ponieważ uaktualnienia są szkodliwe działania, powinny one być częścią Twojego ciągłość prowadzenia działalności biznesowej planowania i projektowania. W tym artykule możemy przyjrzeć się dwa różne sposoby organizowania procesu uaktualniania i omówiono zalety i wady każdej opcji. Na potrzeby tego artykułu nazywamy do aplikacji, która składa się z witryny sieci Web, która jest podłączony do pojedynczej bazy danych jako swoją warstwę danych. Naszym celem jest uaktualnienie wersji 1 (V1) aplikacji do wersji 2 (V2) bez żadnych znaczący wpływ na wrażenia użytkownika.

Podczas obliczania opcje uaktualniania, należy wziąć pod uwagę następujące czynniki:

* Wpływ na dostępność aplikacji, podczas uaktualniania, takich jak jak długo może być ograniczony lub negatywny wpływ na dostępność aplikacji funkcji.
* Zdolność do wdrożenia w przypadku nieudanego uaktualnienia.
* Luki w zabezpieczeniach aplikacji, jeśli niepowiązanych, poważnej awarii podczas uaktualniania.
* Dolar łączny koszt. Ten współczynnik obejmuje nadmiarowość dodatkowej bazy danych i dodatkowe koszty tymczasowe składniki używane przez proces uaktualniania.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uaktualnianie aplikacji, które polegają na kopie zapasowe bazy danych na potrzeby odzyskiwania po awarii

Jeśli aplikacja opiera się na automatycznych kopiach zapasowych i używa funkcja przywracania geograficznego odzyskiwania po awarii, aplikacja jest wdrożona pojedynczym regionem platformy Azure. Aby zminimalizować zakłócenia użytkownika, należy utworzyć środowisko przejściowe w danym regionie, ze wszystkimi składnikami aplikacji związane z uaktualnienia. Na pierwszym diagramie przedstawiono środowisko operacyjne przed procesu uaktualniania. Punkt końcowy `contoso.azurewebsites.net` reprezentuje środowiska produkcyjnego, aplikacji sieci web. Aby można wycofać uaktualnienie, należy utworzyć środowisko przejściowe z w pełni zsynchronizowane kopię bazy danych. Wykonaj następujące kroki, aby utworzyć środowisko przejściowe dla uaktualnienie:

1. Tworzenie pomocniczej bazy danych, w tym samym regionie platformy Azure. Monitoruj pomocniczego, aby zobaczyć, jeśli proces rozmieszczania jest ukończone (1).
2. Utwórz nowe środowisko dla aplikacji sieci web i wywołać go "Przemieszczania". Zostanie zarejestrowana w usłudze Azure DNS przy użyciu adresu URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Te kroki przygotowania nie będzie mieć wpływ na środowisko produkcyjne, w którym może działać w trybie pełnego dostępu.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po zakończeniu kroków przygotowania aplikacja jest gotowa do rzeczywistego uaktualnienia. Następny diagram przedstawia kroki wykonywane w procesie uaktualniania:

1. Ustaw podstawowej bazy danych do trybu tylko do odczytu (3). W tym trybie gwarantuje, że środowiska produkcyjnego, aplikacji sieci web (wersja 1) pozostaje tylko do odczytu podczas uaktualniania, aby zapobiec wystąpieniu rozbieżności danych między wystąpieniami bazy danych w wersji 1 i 2.
2. Odłącz pomocniczej bazy danych przy użyciu trybu planowane zakończenie (4). Ta akcja tworzy kopię w pełni zsynchronizowane, niezależnie od głównej bazy danych. Ta baza danych zostanie uaktualniony.
3. Włącz pomocniczej bazy danych w trybie odczytu i zapisu, a następnie uruchom skrypt uaktualnienia (5).

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Jeśli uaktualnienie zakończy się pomyślnie, możesz teraz przystąpić do przełączanie użytkowników do uaktualnionych kopii aplikacji, która staje się do środowiska produkcyjnego. Przełączanie obejmuje kilka kroków, jak pokazano na diagramie dalej:

1. Aktywuj operację zamiany między środowiskami produkcyjnym i środowisk przejściowych w aplikacji sieci web (6). Ta operacja jest przełączany adresy URL dwa środowiska. Teraz `contoso.azurewebsites.net` wskazuje V2 wersję witryny sieci web i bazy danych (środowisko produkcyjne). 
2. Jeśli nie potrzebujesz już wersji V1 stało się tymczasowej kopii po wymiany, można zlikwidować środowisko przejściowe (7).

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Jeśli proces uaktualniania się niepowodzeniem (na przykład ze względu na błąd w skrypcie uaktualniania), należy wziąć pod uwagę środowisko przejściowe naruszenia. Aby przywrócić aplikację do stanu sprzed uaktualnienia, przywrócenie aplikacji w środowisku produkcyjnym w celu pełnego dostępu. Następny diagram pokazuje kroków operacja cofania:

1. Ustaw kopii bazy danych w trybie odczytu i zapisu (8). Ta akcja przywraca pełną funkcjonalność V1 kopii w środowisku produkcyjnym.
2. Analiza głównej przyczyny problemu i zlikwidować środowisko przejściowe (9).

Na tym etapie aplikacja jest w pełni funkcjonalne i możesz powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian systemu DNS, ponieważ nie został jeszcze wykonany operacji zamiany.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Zaletą tej opcji jest możliwość uaktualnienia aplikacji w jednym regionie, postępując zbiór prostych krokach. Koszt Dolar uaktualnienia jest stosunkowo niska. 

Główne kosztem jest, że w przypadku poważnej awarii podczas uaktualniania, przywracanie do stanu sprzed uaktualnienia obejmuje ponownego wdrożenia aplikacji w różnych regionach i przywracanie bazy danych z kopii zapasowej przy użyciu przywracania geograficznego. W wyniku tego procesu znaczących przestojów.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uaktualnianie aplikacji, które zależą od bazy danych replikacji geograficznej dla odzyskiwania po awarii

Jeśli aplikacja korzysta z aktywnej replikacji geograficznej lub grup automatyczny tryb failover w celu zachowania ciągłości, wdrożono co najmniej dwóch różnych regionach. Jest aktywne, podstawowej bazy danych w regionie podstawowym oraz tylko do odczytu, pomocnicze bazy danych w regionie kopii zapasowej. Wraz z czynników, o których wspomniano na początku tego artykułu proces uaktualniania należy również zagwarantować, że:

* Aplikacja pozostaje chroniona przed katastrofalnych awarii przez cały czas podczas procesu uaktualniania.
* Geograficznie nadmiarowy składniki aplikacji są uaktualniane równolegle ze składnikami active.

Aby osiągnąć te cele, oprócz używania środowisk aplikacji sieci Web, należy wykonać korzystać z usługi Azure Traffic Manager przy użyciu profilu trybu failover jeden aktywny punkt końcowy i jeden punkt końcowy z kopii zapasowej. Następny diagram przedstawia środowisko operacyjne przed procesu uaktualniania. Witryny sieci web `contoso-1.azurewebsites.net` i `contoso-dr.azurewebsites.net` reprezentują środowiska produkcyjnego w aplikacji przy użyciu pełnej geograficznej nadmiarowości. W środowisku produkcyjnym obejmuje następujące składniki:

* Środowiska produkcyjnego, aplikacji sieci web `contoso-1.azurewebsites.net` w regionie podstawowym (1)
* Podstawowej bazy danych w regionie podstawowym, (2)
* Rezerwowe wystąpienie aplikacji sieci web w regionie kopii zapasowej (3)
* Replikowanej geograficznie pomocniczej bazy danych w regionie kopii zapasowej (4)
* Profil wydajności usługi Traffic Manager z punktem końcowym usługi online o nazwie `contoso-1.azurewebsites.net` i wywołać punkt końcowy w trybie offline `contoso-dr.azurewebsites.net`

Aby umożliwić wycofać uaktualnienie, należy utworzyć środowisko przejściowe z w pełni zsynchronizowane kopię tej aplikacji. Ponieważ należy upewnić się, że aplikację można szybko odzyskać, w przypadku wystąpienia poważnej awarii w procesie uaktualnienia, środowisko przejściowe musi być magazynu geograficznie nadmiarowego również. Poniższe kroki są wymagane do utworzenia w środowisku przejściowym do uaktualnienia:

1. Wdróż środowisko przejściowe aplikacji sieci web w regionie podstawowym (6).
2. Tworzenie pomocniczej bazy danych w regionie głównym platformy Azure (7). Skonfiguruj środowisko przejściowe aplikacji sieci web, aby nawiązać z nim. 
3. Utwórz innego magazynu geograficznie nadmiarowego pomocniczej bazy danych w regionie kopii zapasowej, replikując pomocniczej bazy danych w regionie podstawowym. (Ta metoda jest wywoływana *łańcuchowa geografickou replikaci*.) (8).
4. Wdróż środowisko przejściowe wystąpienia aplikacji sieci web w regionie kopii zapasowej (9) i skonfigurować go do łączenia z magazynu geograficznie nadmiarowego pomocniczej bazy danych utworzono (8).

> [!NOTE]
> Te kroki przygotowania nie będzie mieć wpływ na aplikację w środowisku produkcyjnym. Pozostanie w pełni funkcjonalne w trybie odczytu i zapisu.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po zakończeniu kroków przygotowania środowisko tymczasowe jest gotowy do uaktualnienia. Następny diagram ilustruje te kroki uaktualniania:

1. Ustaw podstawowej bazy danych w środowisku produkcyjnym do trybu tylko do odczytu (10). W tym trybie gwarantuje, że produkcyjnej bazy danych (wersja 1) nie zmienią się podczas uaktualniania, aby zapobiec wystąpieniu rozbieżności danych między wystąpieniami bazy danych w wersji 1 i 2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Zakończenie replikacji geograficznej przez odłączenie pomocniczy (11). Ta akcja tworzy niezależne, ale w pełni zsynchronizowane kopię produkcyjnej bazy danych. Ta baza danych zostanie uaktualniony. W poniższym przykładzie użyto instrukcji języka Transact-SQL, ale [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) jest również dostępna. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Uruchom skrypt uaktualnienia skryptu przed `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`i przemieszczania podstawowej bazy danych (12). Zmiany w bazie danych będą automatycznie replikowane do dodatkowej przemieszczania.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Jeśli uaktualnienie zakończy się pomyślnie, możesz teraz przełączać użytkowników do aplikacji w wersji V2. Następny diagram ilustruje etapy:

1. Aktywuj operację zamiany między środowiskami produkcyjnym i środowisk przejściowych w aplikacji sieci web w regionie podstawowym (13) i region kopii zapasowej (14). V2 teraz aplikacja staje się środowisku produkcyjnym, przy użyciu kopii w regionie kopii zapasowej.
2. Jeśli nie potrzebujesz już aplikacji (15 i 16) w wersji 1, można zlikwidować środowisko przejściowe.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Jeśli proces uaktualniania się niepowodzeniem (na przykład ze względu na błąd w skrypcie uaktualniania), należy wziąć pod uwagę środowisko przejściowe, aby być w stanie niespójnym. Aby przywrócić aplikację do stanu sprzed uaktualnienia, przywrócić za pomocą aplikacji w wersji 1 w środowisku produkcyjnym. Wymagane czynności są wyświetlane na diagramie dalej:

1. Ustaw kopia podstawowej bazy danych w środowisku produkcyjnym w trybie odczytu i zapisu (17). Ta akcja przywraca pełną funkcjonalność w wersji 1 w środowisku produkcyjnym.
2. Wykonaj analizę głównej przyczyny i napraw lub usuń środowisko przejściowe (18 i 19).

Na tym etapie aplikacja jest w pełni funkcjonalne i możesz powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga, że DNS zmienia się, ponieważ nie można wykonać operację zamiany.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Zaletą tej opcji jest możliwość uaktualnienia aplikacji i jej geograficznie nadmiarowych kopii równolegle w bez uszczerbku dla ciągłości działania usługi podczas uaktualniania.

Główne kosztem jest wymaga podwójna nadmiarowość każdego składnika aplikacji i w związku z tym spowoduje naliczenie wyższych kosztów dolara. Obejmuje to również bardziej skomplikowanych przepływów pracy.

## <a name="summary"></a>Podsumowanie

Te dwie metody uaktualniania opisany w artykule różnią się w złożoność i niższe koszty dolara, ale obie skupiają się na minimalizowanie, jak długo użytkownik jest ograniczona do operacji tylko do odczytu. Ten czas bezpośrednio jest zdefiniowana przez czas trwania uaktualniania skryptów. Go nie są zależne od rozmiaru bazy danych, warstwy usług, który został wybrany, konfigurację witryny sieci Web lub innych czynników, które nie mogą łatwo zarządzać. Wszystkie kroki przygotowania są całkowicie niezależni od kroki uaktualnienia, a nie miały wpływu na aplikację w środowisku produkcyjnym. Wydajność skrypt uaktualnienia skryptu jest kluczowym czynnikiem, który określa środowisko użytkownika podczas uaktualniania. Dlatego najlepszym sposobem środowiska jest skup się na tworzenie skryptów uaktualniania jak najbardziej efektywne.

## <a name="next-steps"></a>Kolejne kroki

* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat aktywnej replikacji geograficznej usługi Azure SQL Database, zobacz [odczytu pomocniczych baz danych przy użyciu aktywnej replikacji geograficznej Utwórz](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatyczny tryb failover w usłudze Azure SQL Database, zobacz [umożliwiają automatyczny tryb failover grupy przejrzyste i skoordynowany pracy w trybie failover wielu baz danych](sql-database-auto-failover-group.md).
* Aby dowiedzieć się więcej na temat środowisk przejściowych w usłudze Azure App Service, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../app-service/deploy-staging-slots.md).
* Aby dowiedzieć się więcej na temat profilów usługi Azure Traffic Manager, zobacz [Zarządzanie profilem usługi Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).
