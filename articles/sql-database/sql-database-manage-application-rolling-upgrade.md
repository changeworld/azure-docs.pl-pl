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
ms.date: 01/29/2019
ms.openlocfilehash: 1aa3960e3a974703cfecec2bd28fc41f74f7df96
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238411"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Zarządzanie przeprowadzania uaktualnienia równoległych aplikacji w chmurze przy użyciu aktywnej replikacji geograficznej bazy danych SQL

Dowiedz się, jak używać [aktywnej replikacji geograficznej](sql-database-auto-failover-group.md) w bazie danych SQL w celu umożliwienia przeprowadzania uaktualnienia aplikacji w chmurze. Ponieważ uaktualnienie jest operacją uciążliwe, należy go część projektowania i planowania ciągłości biznesowej. W tym artykule możemy przyjrzeć się dwa różne sposoby organizowania procesu uaktualniania i omówić zalet i wad poszczególnych opcji. Na potrzeby tego artykułu będziemy używać aplikacji, która składa się z witryną sieci web podłączony do pojedynczej bazy danych jako swoją warstwę danych. Naszym celem jest, aby uaktualnić wersję 1 aplikacji do wersji 2, bez żadnych znaczny wpływ na środowisko pracy użytkownika końcowego.

Podczas obliczania opcje uaktualniania, należy rozważyć następujące czynniki:

* Wpływ na dostępność aplikacji, podczas uaktualniania. Jak długo funkcja aplikacji może być ograniczony lub obniżoną wydajność.
* Możliwość wycofywania, jeśli uaktualnienie nie powiedzie się.
* Luki w zabezpieczeniach aplikacji, w przypadku niepowiązanych poważnej awarii podczas uaktualniania.
* Dolar łączny koszt.  Obejmuje to dodatkowa Redundancja i dodatkowe koszty tymczasowe składniki używane przez proces uaktualniania.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uaktualnianie aplikacji, które polegają na kopie zapasowe bazy danych na potrzeby odzyskiwania po awarii

Jeśli aplikacja opiera się na automatycznych kopiach zapasowych i używa funkcja przywracania geograficznego odzyskiwania po awarii, aplikacja jest wdrożona pojedynczym regionem platformy Azure. Aby zminimalizować zakłócenia przez użytkownika końcowego, utworzysz środowisko przejściowe w danym regionie, ze wszystkimi składnikami aplikacji związane z uaktualnienia. Na poniższym diagramie przedstawiono środowisko operacyjne przed procesu uaktualniania. Punkt końcowy `contoso.azurewebsites.net` reprezentuje miejscem produkcyjnym, aplikacji sieci web. Aby włączyć możliwość wycofać uaktualnienie, musisz utworzyć miejsca przejściowego, za pomocą w pełni zsynchronizowane kopię bazy danych. Poniższe kroki utworzy środowisko przejściowe dla uaktualnienie:

1. Tworzenie pomocniczej bazy danych, w tym samym regionie platformy Azure. Monitoruj pomocniczego, aby zobaczyć, jeśli proces rozmieszczania jest ukończone (1).
2. Utwórz nowe miejsce wdrożenia aplikacji sieci web o nazwie "Przemieszczania". Zostanie zarejestrowana w systemie DNS za pomocą adresu URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Należy pamiętać, procedura przygotowująca nie ma wpływu na miejsca produkcyjne i może działać w trybie pełnego dostępu.
>  

![Konfiguracja z rzeczywistym użyciem — Replikacja bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po wykonaniu kroków przygotowania, aplikacja jest gotowa do rzeczywistego uaktualnienia. Na poniższym diagramie przedstawiono kroki do wykonania w procesie uaktualniania.

1. Ustaw podstawowej bazy danych do trybu tylko do odczytu (3). W tym trybie gwarantuje, że miejscem produkcyjnym aplikacji sieci web (wersja 1) pozostanie tylko do odczytu podczas uaktualniania, aby zapobiec wystąpieniu rozbieżności danych między wystąpieniami bazy danych w wersji 1 i 2.  
2. Odłącz pomocniczej bazy danych przy użyciu trybu planowane zakończenie (4). Zostanie utworzony w pełni zsynchronizowane kopię niezależnie od podstawowej bazy danych. Ta baza danych zostanie uaktualniony.
3. Włącz podstawowej bazy danych w trybie odczytu i zapisu, a następnie uruchom skrypt uaktualnienia (5).

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Jeśli uaktualnianie zakończone pomyślnie, możesz teraz przystąpić do przełączania użytkowników końcowych w uaktualnionym kopii aplikacji. Staną się teraz miejscem produkcyjnym.  Przełączanie obejmuje kilka kroków pokazanych na poniższym diagramie.

1. Aktywuj operację zamiany między środowiskami produkcyjnym i przejściowym miejsc aplikacji sieci web (6). Przełączą się adresy URL dwa gniazda. Teraz `contoso.azurewebsites.net` wskaże witryny sieci web i bazy danych (środowisko produkcyjne) w wersji V2.  
2. Jeśli nie potrzebujesz już wersji V1 stało się tymczasowej kopii po wymiany, można zlikwidować envoronment przemieszczania (7).

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem, na przykład ze względu na błąd w skrypcie uaktualniania miejsca etapie należy uwzględnić naruszenia zabezpieczeń. Można wycofać aplikacji do stanu sprzed uaktualnienia należy przywrócić aplikacji w gnieździe produkcyjnym pełny dostęp. Etapy są wyświetlane na diagramie dalej.

1. Ustaw kopii bazy danych w trybie odczytu i zapisu (8). Go spowoduje przywrócenie pełne V1 funkcjonalnie kopia produkcji.
2. Analiza głównych przyczyn i zlikwidować środowisko przejściowe (9).

Na tym etapie aplikacja jest w pełni funkcjonalne i można powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian systemu DNS, ponieważ operacja zamiany nie został jeszcze wykonany.

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Klucz **korzyści** tej opcji jest możliwość uaktualnienia aplikacji w jednym regionie przy użyciu zestawu prostych krokach. Koszt Dolar uaktualnienia jest stosunkowo niska. Głównym **kosztem** jest, że w przypadku poważnej awarii podczas uaktualniania Przywracanie do stanu sprzed uaktualnienia będzie obejmować ponowne wdrożenie aplikacji w innym regionie i przywracanie bazy danych z kopii zapasowej przy użyciu Funkcja przywracania geograficznego. Ten proces spowoduje znaczących przestojów.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uaktualnianie aplikacji, które zależą od bazy danych replikacji geograficznej dla odzyskiwania po awarii

Jeśli aplikacja korzysta z aktywnej replikacji geograficznej lub grupy trybu Failover na potrzeby zachowania ciągłości biznesowej, wdrożono co najmniej dwóch różnych regionach, z podstawowej bazy danych dla aktywnej w regionie podstawowym oraz tylko do odczytu pomocniczej bazy danych w regionie kopii zapasowej. Oprócz czynników, o których wspomniano wcześniej proces uaktualniania należy zagwarantować, że:

* Aplikacja pozostaje chroniona przed katastrofalnych awarii przez cały czas podczas procesu uaktualniania
* Geograficznie nadmiarowy składniki aplikacji są uaktualniane równolegle ze składnikami active

Aby osiągnąć te cele, oprócz używania miejsc wdrożenia aplikacji sieci Web, będzie korzystać przy użyciu profilu trybu failover z jednym aktywnym i jednym kopii zapasowych punktów końcowych Azure Traffic Manager (ATM).  Na poniższym diagramie przedstawiono środowisko operacyjne przed procesu uaktualniania. Witryny sieci web `contoso-1.azurewebsites.net` i `contoso-dr.azurewebsites.net` reprezentują środowiska produkcyjnego w aplikacji przy użyciu pełnej geograficznej nadmiarowości. W środowisku produkcyjnym obejmuje następujące składniki:

1. Miejscem produkcyjnym aplikacji sieci web `contoso-1.azurewebsites.net` w regionie podstawowym (1)
2. Podstawowa baza danych w regionie podstawowym, (2) 
3. Wystąpienie zapasowego aplikacji sieci web w regionie kopii zapasowej (3)
4. Replikowanej geograficznie pomocniczej bazy danych w regionie kopii zapasowej (4)
5. Profilu wydajności usługi Azure traffic manager z punktem końcowym online `contoso-1.azurewebsites.net` i punktu końcowego w trybie offline `contoso-dr.azurewebsites.net`

Aby włączyć możliwość wycofać uaktualnienie, musisz utworzyć środowisko przejściowe, za pomocą w pełni zsynchronizowane kopię tej aplikacji. Ponieważ musisz upewnić się, że aplikację można szybko odzyskać, w przypadku wystąpienia poważnej awarii w procesie uaktualnienia środowisko przejściowe musi również być objęty nadmiarowością geograficzną. Poniższe kroki są wymagane do utworzenia w środowisku przejściowym do uaktualnienia:

1. Wdrażanie miejsca przejściowego, aplikacji sieci web w regionie podstawowym (6)
2. Tworzenie pomocniczej bazy danych w regionie głównym platformy Azure (7). Skonfiguruj miejsce na tymczasową aplikacji sieci web, aby nawiązać z nim. 
3. Utwórz innego magazynu geograficznie nadmiarowego pomocniczej bazy danych w regionie kopii zapasowej, replikując pomocniczej bazy danych w regionie podstawowym (jest to nazywane "połączonymi w łańcuch replikacji geograficznej") (8).
3. Wdrażanie miejsca przejściowego wystąpienia aplikacji sieci web, w tym regionie kopii zapasowej (9) i skonfiguruj ją, aby połączyć pomocnicza geograficzna utworzonego w kroku (9).


> [!NOTE]
> Należy pamiętać, procedura przygotowująca nie ma wpływu na aplikacji w gnieździe produkcyjnym i pozostaną w pełni funkcjonalne w trybie odczytu i zapisu.

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po wykonaniu kroków przygotowania, środowisko przejściowe jest gotowy do uaktualnienia. Na poniższym diagramie przedstawiono kroki uaktualnienia.

1. Ustaw podstawowej bazy danych w gnieździe produkcyjnym do trybu tylko do odczytu (10). W tym trybie gwarantuje, że produkcyjnej bazy danych (wersja 1) nie zmieni się podczas uaktualniania, aby zapobiec wystąpieniu rozbieżności danych między wystąpieniami bazy danych w wersji 1 i 2.  
2. Odłącz pomocniczej bazy danych w tym samym regionie przy użyciu trybu planowane zakończenie (11). Utworzy kopię niezależne, ale w pełni zsynchronizowane z produkcyjnej bazy danych. Ta baza danych zostanie uaktualniony.
3. Uruchom skrypt uaktualnienia skryptu przed `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` i przemieszczania podstawowej bazy danych (12). Zmiany w bazie danych będą automatycznie replikowane do dodatkowej przemieszczania 

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Jeśli uaktualnianie zakończone pomyślnie, możesz teraz przystąpić do użytkowników końcowych przełączyć się do aplikacji w wersji V2. Na poniższym diagramie przedstawiono kroki do wykonania.

1. Aktywuj operację zamiany między środowiskami produkcyjnym i przejściowym miejsc aplikacji sieci web w regionie podstawowym (13) i region kopii zapasowej (14). V2 teraz aplikacja staje się miejscem produkcyjnym, przy użyciu kopii w regionie kopii zapasowej.
2. Można zlikwidować środowisko przejściowe, jeśli nie potrzebujesz już aplikacji (15 i 16) w wersji 1.  

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem, na przykład ze względu na błąd w skrypcie uaktualnienia, środowisko przejściowe uznaje się w stanie niespójnym. Można wycofać aplikacji do stanu sprzed uaktualnienia przywrócić za pomocą aplikacji w wersji 1 w środowisku produkcyjnym. Wymagane czynności są wyświetlane na diagramie dalej.

1. Ustaw kopia podstawowej bazy danych w miejscu produkcyjnym w trybie odczytu i zapisu (17). Go spowoduje przywrócenie pełne V1 funkcjonalnie w gnieździe produkcyjnym.
2. Wykonywanie analizy głównych przyczyn i napraw lub usuń środowisko przejściowe (18 i 19).

Na tym etapie aplikacja jest w pełni funkcjonalne i można powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian systemu DNS, ponieważ nie wykonał operację zamiany.

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Klucz **korzyści** tej opcji jest możliwość uaktualnienia aplikacji i jej geograficznie nadmiarowych kopii równolegle w bez uszczerbku dla ciągłości działania usługi podczas uaktualniania. Głównym **kosztem** wymaga podwójna nadmiarowość każdego składnika aplikacji i w związku z tym spowoduje naliczenie wyższych kosztów dolara. Obejmuje to również bardziej skomplikowanych przepływów pracy.

## <a name="summary"></a>Podsumowanie

Te dwie metody uaktualniania opisany w artykule różnią się w złożoność i Dolar kosztów, ale ich obu skupić się na skrócenie czasu, gdy użytkownik końcowy jest ograniczona do operacji tylko do odczytu. Ten czas bezpośrednio jest zdefiniowana przez czas trwania uaktualniania skryptów. Nie są zależne od rozmiaru bazy danych, warstwy usług, który został wybrany, konfiguracja witryny sieci web i inne czynniki, które nie mogą łatwo zarządzać. Wszystkie kroki przygotowania są całkowicie niezależni od kroki uaktualnienia, a nie wpływają one na produkcyjnej aplikacji. Wydajność skrypt uaktualnienia skryptu jest kluczowym czynnikiem, który określa środowisko użytkownika końcowego podczas uaktualniania. Najlepszym sposobem ją ulepszyć, możesz więc, koncentrując się prace nad jak najbardziej efektywne skrypt uaktualnienia skryptu.  

## <a name="next-steps"></a>Kolejne kroki

* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL Database aktywnej replikacji geograficznej, zobacz [odczytu pomocniczych baz danych przy użyciu aktywnej replikacji geograficznej Utwórz](sql-database-active-geo-replication.md).
* Aby uzyskać informacje dotyczące grupy trybu Failover bazy danych SQL Azure, zobacz [umożliwiają automatyczny tryb failover grupy przejrzyste i skoordynowany pracy w trybie failover wielu baz danych](sql-database-auto-failover-group.md).
* Aby dowiedzieć się więcej na temat miejsc wdrożenia i środowisko przejściowe w usłudze Azure App Service, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../app-service/deploy-staging-slots.md).  
* Profile usługi Azure traffic manager można znaleźć [Zarządzanie profilem usługi Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).  


