---
title: Stopniowe uaktualnienia aplikacji — Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą replikacji geograficznej usługi Azure SQL Database obsługuje uaktualnień aplikacji w chmurze w trybie online.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 08/23/2018
ms.openlocfilehash: 4d518c959943184c38ed78cc8eb5449b306c8b04
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162308"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Zarządzanie przeprowadzania uaktualnienia równoległych aplikacji w chmurze przy użyciu aktywnej replikacji geograficznej bazy danych SQL
> [!NOTE]
> [Aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) jest teraz dostępna dla wszystkich baz danych we wszystkich warstwach.
> 

Dowiedz się, jak używać [geografickou replikaci](sql-database-geo-replication-overview.md) w bazie danych SQL w celu umożliwienia przeprowadzania uaktualnienia aplikacji w chmurze. Ponieważ uaktualnienie jest operacją uciążliwe, należy go część projektowania i planowania ciągłości biznesowej. W tym artykule możemy przyjrzeć się dwa różne sposoby organizowania procesu uaktualniania i omówić zalet i wad poszczególnych opcji. Do celów w tym artykule użyjemy prostej aplikacji, która składa się z witryną sieci web podłączony do pojedynczej bazy danych jako swoją warstwę danych. Naszym celem jest, aby uaktualnić wersję 1 aplikacji do wersji 2, bez żadnych znaczny wpływ na środowisko pracy użytkownika końcowego. 

Podczas obliczania opcji uaktualnienia należy uwzględnić następujące czynniki:

* Wpływ na dostępność aplikacji, podczas uaktualniania. Jak długo funkcja aplikacji może być ograniczony lub obniżoną wydajność.
* Możliwość przywracać stanu sprzed na wypadek niepowodzenia uaktualnienia.
* Luki w zabezpieczeniach aplikacji, w przypadku niepowiązanych poważnej awarii podczas uaktualniania.
* Dolar łączny koszt.  Obejmuje to dodatkowa Redundancja i dodatkowe koszty tymczasowe składniki używane przez proces uaktualniania. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uaktualnianie aplikacji, które polegają na kopie zapasowe bazy danych na potrzeby odzyskiwania po awarii
Jeśli aplikacja opiera się na automatycznych kopiach zapasowych i używa funkcja przywracania geograficznego odzyskiwania po awarii, zwykle jest wdrażany w pojedynczym regionie platformy Azure. W tym przypadku proces uaktualnienia obejmuje utworzenie kopii zapasowej wdrożenia wszystkich składników aplikacji zajmujące się uaktualnienia. Aby zminimalizować zakłócenia przez użytkownika końcowego będzie korzystać z usługi Azure Traffic Manager (ATM), za pomocą profilu trybu failover.  Na poniższym diagramie przedstawiono środowisko operacyjne przed procesu uaktualniania. Punkt końcowy <i>contoso 1.azurewebsites.net</i> reprezentuje miejscem produkcyjnym, aplikacji, która musi zostać uaktualniony. Aby włączyć możliwość wycofać uaktualnienie, ma potrzeby tworzenia miejsca etapu z w pełni zsynchronizowane kopię tej aplikacji. Poniższe kroki są wymagane w celu przygotowania aplikacji do uaktualnienia:

1. Utwórz miejsce etapu dla uaktualnienie. Aby czy utworzyć pomocniczą bazę danych (1) i wdrażanie identycznych witryny sieci web, w tym samym regionie platformy Azure. Monitoruj pomocniczego, aby zobaczyć, jeśli proces rozmieszczania kończy się.
2. Utwórz profil pracy awaryjnej w ATM z <i>contoso 1.azurewebsites.net</i> jako punkt końcowy w trybie online i <i>contoso 2.azurewebsites.net</i> w trybie offline. 

> [!NOTE]
> Należy pamiętać, procedura przygotowująca nie ma wpływu na aplikacji w gnieździe produkcyjnym i może działać w trybie pełnego dostępu.
>  

![Konfiguracja z rzeczywistym użyciem — Replikacja bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Po wykonaniu kroków przygotowywania aplikacji jest gotowy do rzeczywistego uaktualnienia. Na poniższym diagramie przedstawiono kroki do wykonania w procesie uaktualniania. 

1. Ustaw podstawowej bazy danych w gnieździe produkcyjnym do trybu tylko do odczytu (3). Gwarantuje to, że wystąpienie produkcyjnych aplikacji (wersja 1) pozostanie tylko do odczytu podczas uaktualniania, aby zapobiec wystąpieniu rozbieżności danych między wystąpieniami bazy danych w wersji 1 i 2.  
2. Odłącz pomocniczej bazy danych przy użyciu trybu planowane zakończenie (4). Zostanie utworzony w pełni zsynchronizowane kopię niezależnie od podstawowej bazy danych. Ta baza danych zostanie uaktualniony.
3. Włącz podstawowej bazy danych w trybie odczytu i zapisu, a następnie uruchom skrypt uaktualnienia w miejscu etapu (5).     

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Jeśli uaktualnianie zakończone pomyślnie możesz teraz przystąpić do przełączania użytkowników końcowych do kopiowania przejściowego aplikacji. Staną się teraz z miejscem produkcyjnym aplikacji.  Ten proces obejmuje kilka kroków pokazanych na poniższym diagramie.

1. Przełącz online punktu końcowego z profilu ATM <i>contoso 2.azurewebsites.net</i>, który wskazuje na witrynie sieci web (6) w wersji V2. Staje się teraz z miejscem produkcyjnym przy użyciu aplikacji w wersji 2 i do niego jest przekierowywany ruch użytkowników końcowych.  
2. Jeśli nie potrzebujesz już składniki aplikacji w wersji 1, dzięki czemu możesz bezpiecznie usunąć je (7).   

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem, na przykład ze względu na błąd w skrypcie uaktualniania miejsca etapie należy uwzględnić naruszenia zabezpieczeń. Aby wycofać aplikacji do stanu sprzed uaktualnienia po prostu przywrócić aplikacji w gnieździe produkcyjnym pełny dostęp. Etapy są wyświetlane na diagramie dalej.    

1. Ustaw kopii bazy danych w trybie odczytu i zapisu (8). Spowoduje to przywrócenie pełne V1 funkcjonalnie w gnieździe produkcyjnym.
2. Analiza głównych przyczyn i usunąć składniki, których bezpieczeństwo zostało naruszone w gnieździe etapu (9). 

Na tym etapie aplikacja jest w pełni funkcjonalne i można powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian w profilu ATM, jak już wskazuje <i>contoso 1.azurewebsites.net</i> jako aktywny punkt końcowy.
> 
> 

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Klucz **korzyści** tej opcji jest możliwość uaktualnienia aplikacji w jednym regionie przy użyciu zestawu prostych krokach. Koszt Dolar uaktualnienia jest stosunkowo niska. Głównym **kosztem** jest, że w przypadku poważnej awarii podczas uaktualniania Przywracanie do stanu sprzed uaktualnienia będzie obejmować ponownego wdrażania aplikacji w innym regionie i przywracanie bazy danych z kopii zapasowej przy użyciu Funkcja przywracania geograficznego. Ten proces spowoduje znaczących przestojów.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uaktualnianie aplikacji, które zależą od bazy danych replikacji geograficznej dla odzyskiwania po awarii
Jeśli aplikacja korzysta z replikacji geograficznej dla ciągłości działania, wdrożono co najmniej dwóch różnych regionach, aktywne wdrożenie w regionie podstawowym i wstrzymania wdrożenia w regionie kopii zapasowej. Oprócz czynników, o których wspomniano wcześniej proces uaktualniania należy zagwarantować, że:

* Aplikacja pozostaje chroniona przed katastrofalnych awarii przez cały czas podczas procesu uaktualniania
* Geograficznie nadmiarowy składniki aplikacji są uaktualniane równolegle ze składnikami active

Aby osiągnąć te cele będzie korzystać przy użyciu profilu trybu failover z jednym aktywnym i trzech kopii zapasowych punktów końcowych Azure Traffic Manager (ATM).  Na poniższym diagramie przedstawiono środowisko operacyjne przed procesu uaktualniania. Witryny sieci web <i>contoso 1.azurewebsites.net</i> i <i>contoso dr.azurewebsites.net</i> reprezentują miejscem produkcyjnym w aplikacji przy użyciu pełnej geograficznej nadmiarowości. Aby włączyć możliwość wycofać uaktualnienie, ma potrzeby tworzenia miejsca etapu z w pełni zsynchronizowane kopię tej aplikacji. Ponieważ musisz upewnić się, że aplikację można szybko odzyskać, w przypadku wystąpienia poważnej awarii w procesie uaktualnienia miejsca etapu musi być magazynu geograficznie nadmiarowego w także. Poniższe kroki są wymagane w celu przygotowania aplikacji do uaktualnienia:

1. Utwórz miejsce etapu dla uaktualnienie. Aby czy utworzyć pomocniczą bazę danych (1), a następnie wdrożona identyczną kopię witryny sieci web, w tym samym regionie platformy Azure. Monitoruj pomocniczego, aby zobaczyć, jeśli proces rozmieszczania kończy się.
2. Tworzenie nadmiarowej geograficznie pomocniczej bazy danych w gnieździe etapu dzięki replikacji geograficznej pomocniczej bazy danych w regionie kopii zapasowej (jest to nazywane "połączonymi w łańcuch replikacji geograficznej"). Monitorowanie kopii zapasowych dodatkowej można sprawdzić, czy Proces rozmieszczania jest ukończone (3).
3. Utwórz kopię rezerwy dynamicznej witryny sieci web w regionie kopii zapasowej i połączyć go z pomocniczego magazynu geograficznie nadmiarowego (4).  
4. Dodaj dodatkowe punkty końcowe <i>contoso 2.azurewebsites.net</i> i <i>contoso 3.azurewebsites.net</i> do profilu pracy awaryjnej w ATM jako offline punktów końcowych (5). 

> [!NOTE]
> Należy pamiętać, procedura przygotowująca nie ma wpływu na aplikacji w gnieździe produkcyjnym i może działać w trybie pełnego dostępu.
> 
> 

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Po wykonaniu czynności przygotowujące miejsce etap jest gotowy do uaktualnienia. Na poniższym diagramie przedstawiono kroki uaktualnienia.

1. Ustaw podstawowej bazy danych w gnieździe produkcyjnym do trybu tylko do odczytu (6). Gwarantuje to, że wystąpienie produkcyjnych aplikacji (wersja 1) pozostanie tylko do odczytu podczas uaktualniania, aby zapobiec wystąpieniu rozbieżności danych między wystąpieniami bazy danych w wersji 1 i 2.  
2. Odłącz pomocniczej bazy danych w tym samym regionie przy użyciu trybu planowane zakończenie (7). Niezależnie od w pełni zsynchronizowane kopię podstawowej bazy danych, które będą automatycznie stają się one głównej po zakończeniu zostanie utworzony. Ta baza danych zostanie uaktualniony.
3. Włącz podstawowej bazy danych w gnieździe etapu w trybie odczytu i zapisu, a następnie uruchom skrypt uaktualnienia (8).    

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Jeśli uaktualnianie zostało ukończone pomyślnie teraz można przystąpić do przełączania użytkowników końcowych do aplikacji w wersji V2. Na poniższym diagramie przedstawiono kroki do wykonania.

1. Przełącz aktywny punkt końcowy z profilu ATM <i>contoso 2.azurewebsites.net</i>, które jest teraz wskazuje na witrynie sieci web (9) w wersji V2. Staje się teraz miejscem produkcyjnym, przy użyciu aplikacji w wersji 2 i użytkowników końcowych ruch będzie kierowany do niego. 
2. Jeśli nie potrzebujesz już aplikacji w wersji 1, dzięki czemu możesz bezpiecznie usunąć to (10 i 11).  

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem, na przykład ze względu na błąd w skrypcie uaktualniania miejsca etapie należy uwzględnić naruszenia zabezpieczeń. Aby wycofać aplikacji do stanu sprzed uaktualnienia po prostu wracasz do korzystania z aplikacji w gnieździe produkcyjnym z pełnym dostępem. Etapy są wyświetlane na diagramie dalej.    

1. Ustaw kopia podstawowej bazy danych w miejscu produkcyjnym w trybie odczytu i zapisu (12). Spowoduje to przywrócenie pełne V1 funkcjonalnie w gnieździe produkcyjnym.
2. Analiza głównych przyczyn i usunąć składniki, których bezpieczeństwo zostało naruszone w gnieździe etapu (13 i 14). 

Na tym etapie aplikacja jest w pełni funkcjonalne i można powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian w profilu ATM, jak już wskazuje <i>contoso 1.azurewebsites.net</i> jako aktywny punkt końcowy.
> 
> 

![Konfiguracja replikacji geograficznej bazy danych SQL. Odzyskiwanie po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Klucz **korzyści** tej opcji jest możliwość uaktualnienia aplikacji i jej geograficznie nadmiarowych kopii równolegle w bez uszczerbku dla ciągłości działania usługi podczas uaktualniania. Głównym **kosztem** wymaga podwójna nadmiarowość każdego składnika aplikacji i w związku z tym spowoduje naliczenie wyższych kosztów dolara. Obejmuje to również bardziej skomplikowanych przepływów pracy. 

## <a name="summary"></a>Podsumowanie
Te dwie metody uaktualniania opisany w artykule różnią się w złożoność i Dolar kosztów, ale ich obu skupić się na skrócenie czasu, gdy użytkownik końcowy jest ograniczona do operacji tylko do odczytu. Ten czas bezpośrednio jest zdefiniowana przez czas trwania uaktualniania skryptów. Nie są zależne od rozmiaru bazy danych, warstwy usług, który został wybrany, konfiguracja witryny sieci web i inne czynniki, które nie mogą łatwo zarządzać. Jest to spowodowane wszystkich kroków przygotowania są całkowicie niezależni od kroki uaktualnienia i może odbywać się bez wywierania wpływu na aplikację w środowisku produkcyjnym. Wydajność skrypt uaktualnienia skryptu jest kluczowym czynnikiem, który określa środowisko użytkownika końcowego podczas uaktualniania. Najlepszym sposobem ją ulepszyć, możesz więc, koncentrując się prace nad jak najbardziej efektywne skrypt uaktualnienia skryptu.  

## <a name="next-steps"></a>Kolejne kroki
* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md).
* Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do odzyskania, zobacz [przywrócić bazę danych z automatycznych kopii zapasowych](sql-database-recovery-using-backups.md).
* Aby dowiedzieć się o szybsze opcje odzyskiwania, zobacz [aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md).


