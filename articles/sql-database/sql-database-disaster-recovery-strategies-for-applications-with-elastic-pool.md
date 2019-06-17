---
title: Projektowanie rozwiązania do odzyskiwania po awarii — Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak projektować rozwiązania do odzyskiwania po awarii w chmurze, wybierając wzorzec prawo trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6a332ce265a4bb41a9ad3c0c3a29683187a0f0d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098409"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategie odzyskiwania po awarii dla aplikacji wykorzystujących pule elastyczne bazy danych SQL

W ciągu lat nauczyliśmy się, że usługi w chmurze są niezawodne, a zdarzenia krytycznego się tak zdarzyć. SQL Database oferuje kilka możliwości do zapewnienia ciągłość prowadzenia działalności biznesowej w aplikacji, gdy wystąpią następujące zdarzenia. [Pule elastyczne](sql-database-elastic-pool.md) i pojedynczych baz danych obsługują tego samego rodzaju możliwości odzyskiwania po awarii. W tym artykule opisano kilka strategii odzyskiwania po awarii dla pul elastycznych wykorzystać te funkcje zachowywania ciągłości biznesowej bazy danych SQL.

W tym artykule wykorzystano następujące canonical wzorca aplikacji SaaS niezależnego dostawcy oprogramowania:

Aplikacja nowoczesne rozwiązania sieci web, które są oparte na chmurze aprowizację jednej bazy danych SQL dla każdego użytkownika końcowego. Niezależny dostawca oprogramowania ma wielu klientów i w związku z tym korzysta z wielu baz danych, nazywane baz danych dzierżaw. Ponieważ baz danych dzierżawy mają zwykle działanie nieprzewidywalne wzorce, niezależny dostawca oprogramowania używa puli elastycznej, aby baza danych kosztów przewidywalnego przez dłuższy czas. Puli elastycznej też upraszcza zarządzanie wydajności, jeśli gwałtowne wzrosty aktywności użytkownika. Oprócz bazy danych dzierżaw aplikacja używa kilka baz danych do zarządzania profile użytkowników, zabezpieczeń, zbieraj wzorce użycia itd. Dostępność poszczególnych dzierżaw nie ma wpływu na dostępność aplikacji jako całości. Jednak dostępność i wydajność bazy danych zarządzania jest krytyczny dla aplikacji funkcji i w przypadku baz danych zarządzania w trybie offline całej aplikacji jest w trybie offline.

W tym artykule omówiono strategii odzyskiwania po awarii, obejmujących szereg scenariuszy z koszt uruchamiania poufnych aplikacji do takich, które mają wymagania w zakresie dostępności rygorystyczne.

> [!NOTE]
> Jeśli używasz wersji Premium lub krytyczne dla działania firmy baz danych i pul elastycznych, możesz zwiększyć ich odporne na błędy regionalnych przestojów przez konwertowania go na potrzeby konfiguracji wdrożenia nadmiarowe strefy. Zobacz [strefowo nadmiarowe bazy danych](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenariusz 1. Koszt uruchamiania poufnych

Jestem firm uruchamiania i jestem bardzo koszt poufnych.  Chcę, aby uprościć wdrażanie i zarządzanie nimi aplikacji i dla poszczególnych klientów można utworzyć ograniczone umowy SLA. Ale chcę, aby upewnić się, aplikacja jako całość nigdy nie jest w trybie offline.

Do spełnienia wymagań prostotę, wdrażanie wszystkich baz danych dzierżaw w jednej elastycznej puli w wybranym przez siebie w regionie platformy Azure i wdrażanie baz danych zarządzania replikacją geograficzną pojedynczych baz danych. Do odzyskiwania po awarii dzierżaw służy funkcja przywracania geograficznego, które pojawia się bez ponoszenia dodatkowych kosztów. Aby zapewnić dostępność bazy danych zarządzania, replikacja geograficzna je do innego regionu za pomocą automatycznej pracy awaryjnej grupy (krok 1). Trwającą koszt konfiguracji odzyskiwania po awarii, w tym scenariuszu jest równy łączny koszt korzystania z dodatkowej bazy danych. Ta konfiguracja została przedstawiona na następny diagram.

![Rysunek 1.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Jeśli wystąpi awaria w regionie głównym, proces odzyskiwania, aby wyświetlić aplikację w trybie online są zilustrowane następny diagram.

* Grupy trybu failover inicjuje automatycznej pracy awaryjnej bazy danych zarządzania w regionie odzyskiwania po awarii. Aplikacja jest automatycznie ponownie łączona do nowych podstawowych i wszystkich nowych kont i baz danych dzierżawy są tworzone w regionie odzyskiwania po awarii. Istniejący klienci przeglądać swoje dane, które są tymczasowo niedostępne.
* Tworzenie puli elastycznej z taką samą konfigurację jak oryginalne puli (2).
* Funkcja przywracania geograficznego umożliwia utworzenie kopii dzierżawy baz danych (3). Można wziąć pod uwagę wyzwalanie przywracania poszczególnych przez połączenia użytkowników końcowych lub używają innego schematu priorytet specyficzne dla aplikacji.

Na tym etapie aplikacja jest wróci do trybu online w regionie odzyskiwania po awarii, ale niektórzy klienci opóźnienie przy uzyskiwaniu dostępu do swoich danych.

![Rysunek 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Jeśli tymczasowe awarii, istnieje możliwość, że przez platformę Azure odzyskać regionu podstawowego, zanim wszystkie operacje przywracania bazy danych są kompletne w regionie odzyskiwania po awarii. W takim przypadku możesz odpowiednio organizować przenoszenie aplikacji do regionu podstawowego. Zajmie to przedstawione na diagramie dalej kroki.

* Anuluj wszystkie żądania oczekujące Przywracanie geograficzne.
* Tryb failover bazy danych zarządzania do regionu podstawowego (5). Po odzyskaniu regionu stare kolory podstawowe automatycznie stały się pomocnicze bazy danych. Teraz są ponownie przełączyć się role.
* Zmień parametry połączenia aplikacji, aby wskazać region podstawowy. Teraz wszystkich nowych kont i baz danych dzierżawy są tworzone w regionie podstawowym. Niektórzy klienci istniejących przeglądać swoje dane, które są tymczasowo niedostępne.
* Ustaw wszystkie bazy danych w puli odzyskiwania po awarii, aby tylko do odczytu do upewnij się, że nie można modyfikować w regionie odzyskiwania po awarii (6).
* Dla każdej bazy danych w puli odzyskiwania po awarii, które uległy zmianie od czasu odzyskiwania Zmień nazwę lub usuń odpowiednie baz danych w puli podstawowej (7).
* Skopiuj zaktualizowany baz danych z puli odzyskiwania po awarii do głównej puli (8).
* Usuń pulę odzyskiwania po awarii (9)

Na tym etapie aplikacja jest w trybie online w regionie podstawowym, przy użyciu wszystkich baz danych dzierżaw dostępne w puli podstawowej.

![Rysunek 3.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Klucz **korzyści** ta strategia jest niskie koszty bieżące nadmiarowości warstwy danych. Kopie zapasowe są automatycznie pobierane przez usługę SQL Database przy użyciu nie ponownego zapisywania aplikacji i bez ponoszenia dodatkowych kosztów.  Koszt jest naliczany, tylko wtedy, gdy zostaną przywrócone elastycznych baz danych. **Kompromis** jest, że kompletny odzyskiwania wszystkich baz danych dzierżaw zajmuje znaczną ilość czasu. Czas zależy od całkowitej liczby przywraca zainicjowana przez Ciebie w regionie odzyskiwania po awarii i całkowity rozmiar baz danych dzierżaw. Nawet wtedy, gdy można priorytety przywraca kilka dzierżaw przez inne osoby, rywalizuje o wszystkich przywracania, które są inicjowane w tym samym regionie, usługa rozstrzyga o kolejności przetwarzania i ogranicza ogólnego wpływu na istniejących klientów baz danych. Ponadto odzyskiwania baz danych dzierżawy, nie można uruchomić dopiero po utworzeniu nowej puli elastycznej w regionie odzyskiwania po awarii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenariusz 2. Dojrzała aplikacji z usługą warstwowych

Jestem dojrzała aplikację SaaS z oferty usługi warstwowych i różnymi umowami SLA dla klientów wersji próbnej i płacąc klientów. Dla klientów wersji próbnej mam obniżyć koszty możliwie. Wersja próbna klienci mogą korzystać Przestój, ale chcę zmniejszyć prawdopodobieństwo jego. W przypadku płacących klientów żadnego przestoju jest ryzyko lotu. Chcę upewnić się, że płacenia klienci mogą zawsze uzyskują dostęp do danych.

Aby obsługiwać ten scenariusz, należy oddzielić próbne z płatnych dzierżaw przez umieszczenie ich w oddzielnych pul elastycznych. Wersji próbnej klienci mają niższa wartość eDTU lub rdzeni wirtualnych na dzierżawę i niższy poziom umowy SLA dłuższych odzyskiwania. Płacących klientów znajdują się w puli z wyższą wartość eDTU lub rdzeni wirtualnych na dzierżawę i wyższy poziom umowy SLA. Aby zagwarantować najniższy czas odzyskiwania, baz danych dzierżaw płacących klientów są replikacją geograficzną. Ta konfiguracja została przedstawiona na następny diagram.

![Rysunek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Jak pierwszego scenariusza bazy danych zarządzania są bardzo aktywne, więc użyć pojedynczej bazy danych replikowanej geograficznie dla niego (1). Dzięki temu przewidywalną wydajność dla subskrypcji nowych klientów, aktualizacje profilu i innych operacji zarządzania. Region, w którym znajdują się kolory podstawowe zarządzanie baz danych jest regionem podstawowym i region, w którym znajdują się serwery baz danych zarządzania jest w regionie odzyskiwania po awarii.

Baz danych dzierżaw płacących klientów ma aktywnych baz danych w puli "płatnych" aprowizowane w regionie podstawowym. Aprowizacja dodatkowych puli o tej samej nazwie w regionie odzyskiwania po awarii. Każda dzierżawa jest replikowana geograficznie pomocniczej puli (2). Dzięki temu szybkiego odzyskiwania wszystkich baz danych dzierżaw przy użyciu trybu failover.

Jeśli wystąpi awaria w regionie głównym, proces odzyskiwania, aby wyświetlić aplikację w trybie online zostały przedstawione na następny diagram:

![Rysunek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Natychmiast pracy awaryjnej bazy danych zarządzania w regionie odzyskiwania po awarii (3).
* Zmień parametry połączenia aplikacji, aby wskazywał w regionie odzyskiwania po awarii. Teraz wszystkich nowych kont i baz danych dzierżawy są tworzone w regionie odzyskiwania po awarii. Istniejący klienci wersji próbnej, zobacz swoje dane, które są tymczasowo niedostępne.
* Tryb failover baz danych dzierżawy płatnych puli w regionie odzyskiwania po awarii, aby natychmiast przywrócić ich dostępność (4). Ponieważ przełączenie w tryb failover jest szybka zmiana poziomu, należy wziąć pod uwagę optymalizacji, gdzie poszczególne przejścia w tryb failover są wyzwalane na żądanie przez użytkownika końcowego połączenia.
* Jeśli liczby jednostek eDTU puli dodatkowej wartość rozmiaru lub generacji — rdzeń wirtualny był mniejszy niż podstawowy, ponieważ pomocniczych baz danych wymagane tylko wydajności, aby przetwarzać dzienniki zmian, chociaż były one pomocnicze bazy danych, od razu zwiększyć pojemność puli teraz obsłużyć pełnego obciążenia od wszystkich dzierżaw (5).
* Tworzenie nowej puli elastycznej przy użyciu tej samej nazwie i taką samą konfigurację w regionie odzyskiwania po awarii dla baz danych w wersji próbnej klienci (6).
* Po utworzeniu puli wersji próbnej klienci umożliwia przywracanie geograficzne przywracanie baz danych dzierżawy wersji próbnej do nowej puli (7). Należy wziąć pod uwagę wyzwalanie przywracania poszczególnych przez połączenia użytkowników końcowych lub użyj innego systemu priorytet specyficzne dla aplikacji.

Na tym etapie aplikacja jest wróci do trybu online w regionie odzyskiwania po awarii. Wszystkie płacących klientów ma dostęp do swoich danych, gdy wersji próbnej klienci opóźnienie przy uzyskiwaniu dostępu do swoich danych.

Gdy region podstawowy jest przywracany przez platformę Azure *po* została przywrócona do aplikacji w regionie odzyskiwania po awarii, możesz kontynuować uruchamianie aplikacji w danym regionie lub możesz zdecydować się na powrót po awarii do regionu podstawowego. Jeśli region podstawowy jest przywracany *przed* ukończeniu procesu pracy awaryjnej należy wziąć pod uwagę powrotem po awarii już teraz. Powrót po awarii przyjmuje przedstawione na diagramie dalej kroki:

![Rysunek 6.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Anuluj wszystkie żądania oczekujące Przywracanie geograficzne.
* Tryb failover bazy danych zarządzania (8). Po odzyskaniu regionu stary serwer podstawowy staje się automatycznie pomocniczej. Teraz ponownie staje się podstawowym.  
* Tryb failover bazy danych dzierżaw płatne (9). Podobnie po odzyskaniu regionu stare kolory podstawowe automatycznie stają się one pomocnicze bazy danych. Teraz ponownie staną się kolory podstawowe.
* Ustaw przywrócona wersja próbna bazy danych, które zostały zmienione w regionie odzyskiwania po awarii do tylko do odczytu (10).
* Dla każdej bazy danych w puli klienci próbnego odzyskiwania po awarii, które zmieniło odzyskiwania Zmień nazwę lub usuń odpowiednie bazy danych w puli podstawowej w wersji próbnej klienci (11).
* Skopiuj zaktualizowany baz danych z puli odzyskiwania po awarii do głównej puli (12).
* Usuwanie puli odzyskiwania po awarii (13).

> [!NOTE]
> Operacja trybu failover jest asynchroniczna. Aby zminimalizować czas odzyskiwania jest ważne, wykonaj polecenie pracy awaryjnej baz danych dzierżaw w partiach, co najmniej 20 baz danych.

Klucz **korzyści** tej strategii jest najwyższym umowy SLA dla płacących klientów. Gwarantuje również, że nowe wersje próbne są odblokowane, zaraz po utworzeniu puli próbnego odzyskiwania po awarii. **Kompromis** czy płatna czy ta konfiguracja zwiększa łączny koszt korzystania z bazy danych dzierżaw przez koszt puli odzyskiwania po awarii pomocniczej dla klientów. Ponadto jeśli dodatkowej Pula ma inny rozmiar, płacących klientów wystąpić obniżenie wydajności po włączeniu trybu failover, do czasu ukończenia uaktualniania puli w regionie odzyskiwania po awarii.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenariusz 3. Rozproszone geograficznie aplikacji przy użyciu usługi warstwowej

Masz dojrzała aplikacji SaaS w przypadku ofert z warstwowego usługi. Chcę, aby zaoferować bardzo atrakcyjnych umowy SLA płatnych klientów i zminimalizować ryzyko wpływ w przypadku wystąpienia awarii, ponieważ nawet krótką przerwę w działaniu może spowodować, że głównym powodem niezadowolenia klienta. Koniecznie czy płacących klientów zawsze mają dostęp do swoich danych. Wersje próbne są bezpłatne i umowy SLA nie jest dostępna w trakcie okresu próbnego.

Aby obsługiwać ten scenariusz, należy użyć trzech oddzielnych pul elastycznych. Aprowizuj dwie pule taki sam rozmiar Wysoka liczba jednostek Edtu lub rdzeni wirtualnych na bazę danych w dwóch różnych regionach, które ma zawierać baz danych dzierżaw klientów płatną. Trzeci puli zawierającej próbne może mieć niższy Edtu lub rdzeni wirtualnych na bazę danych i udostępniane w jednym z dwóch regionach.

W celu zagwarantowania najniższy czas odzyskiwania podczas awarii, baz danych dzierżaw płacących klientów są replikowane geograficznie z 50% podstawowych baz danych w dwóch regionach. Podobnie w każdym regionie istnieje 50% pomocniczych baz danych. W ten sposób Jeśli region jest w trybie offline tylko przez 50% klientów płatnych baz danych ma wpływ i trzeba w trybie Failover. Inne bazy danych pozostają bez zmian. Ta konfiguracja jest zilustrowany na poniższym diagramie:

![Rysunek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

W poprzednich scenariuszach zarządzania bazy danych są bardzo aktywnych więc je skonfigurować jako pojedyncze replikacji geograficznej bazy danych (1). Zapewnia przewidywalną wydajność nowego klienta, subskrypcji, aktualizacje profilu i innych operacji zarządzania. Region A jest regionem podstawowym dla baz danych zarządzania i region B zostanie użyty do odzyskiwania bazy danych zarządzania.

Baz danych dzierżaw płacących klientów są także replikowane geograficznie, ale z kolory podstawowe i pomocnicze bazy danych współdzielić region A i region B (2). W ten sposób podstawowy baz danych dzierżaw wpływ awarii można Failover do innego regionu i stają się dostępne. Druga połowa baz danych dzierżawy są nie wpływać na wszystkich.

Następny diagram przedstawia czynności odzyskiwania i w razie wystąpienia awarii w regionie A.

![Rysunek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Od razu pracę awaryjną zarządzania bazy danych do regionu B (3).
* Zmień parametry połączenia aplikacji, aby wskazywał zarządzania bazy danych w regionie B. zmodyfikować bazy danych zarządzania, aby upewnić się, że nowe konta i baz danych dzierżawy są tworzone w regionie B istniejących baz danych dzierżawy znajdują się tam również. Istniejący klienci wersji próbnej, zobacz swoje dane, które są tymczasowo niedostępne.
* Tryb failover baz danych dzierżawy płatną, do puli 2 w regionie B, aby natychmiast przywrócić ich dostępność (4). Ponieważ przełączenie w tryb failover jest szybka zmiana poziomu, można rozważyć optymalizacji, gdzie poszczególne przejścia w tryb failover są wyzwalane na żądanie przez użytkownika końcowego połączenia.
* Od teraz puli 2 zawiera tylko podstawowe bazy danych, łączna liczba obciążenia w tej puli zwiększa i natychmiast zwiększyć jego rozmiaru w jednostkach eDTU (5) lub liczba rdzeni wirtualnych.
* Tworzenie nowej puli elastycznej przy użyciu tej samej nazwie i taką samą konfigurację w regionie B dla baz danych w wersji próbnej klienci (6).
* Po utworzeniu puli należy użyć przywracania geograficznego, aby przywrócić bazę pojedynczego dzierżawcy wersji próbnej do puli (7). Można wziąć pod uwagę wyzwalanie przywracania poszczególnych przez połączenia użytkowników końcowych lub używają innego schematu priorytet specyficzne dla aplikacji.

> [!NOTE]
> Operacja trybu failover jest asynchroniczna. Aby zminimalizować czas odzyskiwania, należy wykonać polecenia trybu failover baz danych dzierżaw w partiach, co najmniej 20 baz danych.

Na tym etapie aplikacja jest wróci do trybu online w regionie B. Wszystkie płacących klientów ma dostęp do swoich danych, gdy wersji próbnej klienci opóźnienie przy uzyskiwaniu dostępu do swoich danych.

Po odzyskaniu region A należy zdecydować, jeśli chcesz użyć regionu B dla klientów wersji próbnej lub powrotu po awarii do korzystania z wersji próbnej klienci puli w regionie A. Jedno kryterium może być % baz danych dzierżawy w wersji próbnej zmodyfikowany od czasu odzyskiwania. Niezależnie od tej decyzji musisz ponownie zrównoważyć płatnych dzierżawy między dwoma pulami. Następny diagram przedstawia proces, gdy baz danych dzierżawy w wersji próbnej powracać po awarii do regionu A.  

![Rysunek 6.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Anuluj wszystkie żądania oczekujące Przywracanie geograficzne próbnego odzyskiwania po awarii puli.
* Tryb failover bazy danych zarządzania (8). Po odzyskaniu regionu stary serwer podstawowy stanie automatycznie się pomocniczej. Teraz ponownie staje się podstawowym.  
* Wybierz której płatnych dzierżawy baz danych powracać po awarii do puli 1 i zainicjuj tryb failover, aby ich pomocnicze bazy danych (9). Po odzyskaniu regionu wszystkich baz danych w puli 1 została automatycznie pomocnicze bazy danych. Teraz 50% z nich stanie się ponownie kolory podstawowe.
* Zmniejsz rozmiar puli 2 do oryginalnego liczby jednostek eDTU (10) lub liczba rdzeni wirtualnych.
* Zestaw wszystkich przywrócona wersja próbna bazy danych w regionie B do tylko do odczytu (11).
* Dla każdej bazy danych w wersji próbnej puli odzyskiwania po awarii, które uległy zmianie od czasu odzyskiwania Zmień nazwę lub usuń odpowiednie bazy danych w puli podstawowej wersji próbnej (12).
* Skopiuj zaktualizowany baz danych z puli odzyskiwania po awarii do głównej puli (13).
* Usuwanie puli odzyskiwania po awarii (14).

Klucz **korzyści** tej strategii są:

* Obsługuje ona najbardziej agresywne umowy SLA dla klientów płacących ponieważ zapewnia, że awaria nie może mieć wpływ na więcej niż 50% baz danych dzierżaw.
* Gwarantuje on, nowe wersje próbne są odblokowane, jak tylko dziennik odzyskiwania po awarii puli jest tworzona podczas odzyskiwania.
* Umożliwia efektywniejsze wykorzystanie pojemności puli 50% pomocniczych baz danych w puli 1 i 2 puli zapewniona jest mniej aktywne niż podstawowych baz danych.

Głównym **wad i zalet** są:

* Operacji CRUD względem bazy danych zarządzania ma mniejsze opóźnienie dla użytkowników końcowych, połączony element niż region dla użytkowników końcowych podłączone do regionu B, ponieważ są one wykonywane względem podstawowej bazy danych zarządzania.
* Wymaga to bardziej złożone projektu bazy danych zarządzania. Na przykład każdy rekord dzierżawy ma tag lokalizacji, która musi zostać zmienione podczas trybu failover i powrotu po awarii.  
* Do czasu ukończenia uaktualniania puli w regionie B, płacących klientów może wystąpić obniżenie wydajności niż zwykle.

## <a name="summary"></a>Podsumowanie

Ten artykuł koncentruje się na Strategie odzyskiwania po awarii w warstwie bazy danych używane przez aplikację wielodostępną SaaS niezależnego dostawcy oprogramowania. Strategii, którą wybierzesz opiera się na potrzeby aplikacji, na przykład model biznesowy umowy SLA, aby zaoferować klientom, budżetu, ograniczenia itd. Każdej strategii opisane przedstawia korzyści i kompromis, dzięki czemu można podjąć świadomą decyzję. Ponadto konkretnej aplikacji prawdopodobnie zawiera innymi składnikami platformy Azure. Aby przejrzeć ich wskazówki ciągłości biznesowej i zorganizować odzyskiwanie warstwy bazy danych z nimi. Aby dowiedzieć się więcej o zarządzaniu odzyskiwanie bazy danych aplikacji na platformie Azure, zapoznaj się [projektowania rozwiązań w chmurze do odzyskiwania po awarii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md).
* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do odzyskania, zobacz [przywrócić bazę danych z kopii zapasowych zainicjowanych przez usługę](sql-database-recovery-using-backups.md).
* Aby dowiedzieć się o szybsze opcje odzyskiwania, zobacz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).
* Aby dowiedzieć się więcej o używaniu automatycznych kopii zapasowych, archiwizowania, zobacz [kopiowanie bazy danych](sql-database-copy.md).
