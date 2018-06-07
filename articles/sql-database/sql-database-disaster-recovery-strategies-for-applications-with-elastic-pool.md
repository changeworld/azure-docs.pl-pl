---
title: Projektowanie rozwiązania w zakresie odzyskiwania po awarii — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaprojektować rozwiązanie chmury odzyskiwania po awarii, wybierając wzorzec prawo trybu failover.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 5de8aebb6ffc5763dd7f0b8852c31923914e4c55
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645535"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategie odzyskiwania po awarii dla aplikacji wykorzystujących pule elastyczne bazy danych SQL
Całościowo ma dowiedzieliśmy się, że usługi w chmurze nie są niezawodne i stanie krytycznego zdarzenia. Baza danych SQL oferuje kilka możliwości do zapewnienia ciągłości biznesowej aplikacji, gdy zdarzenia te występują. [Pule elastyczne](sql-database-elastic-pool.md) i pojedynczych baz danych obsługują tego samego rodzaju możliwości odzyskiwania po awarii. W tym artykule opisano kilka strategii odzyskiwania po awarii dla pul elastyczna korzystać z tych funkcjach ciągłości biznesowej bazy danych SQL.

W tym artykule korzysta ze wzorca następujących aplikacji SaaS niezależnego dostawcy oprogramowania kanoniczny:

<i>Aplikacja sieci web opartej na chmurze nowoczesnych inicjuje jednej bazy danych SQL dla każdego użytkownika końcowego. Niezależny dostawca oprogramowania ma wielu klientów i w związku z tym używa wielu baz danych, nazywane dzierżawy baz danych. Ponieważ baz danych dzierżawy mają zwykle działania nieprzewidywalnych wzorców, niezależnego dostawcy oprogramowania używa puli elastycznej, aby baza danych, kosztów bardzo przewidywalną przez dłuższy czas. Puli elastycznej także upraszcza zarządzanie wydajności, gdy wzrósł aktywności użytkownika. Oprócz baz danych dzierżawy aplikacja używa wielu baz danych do zarządzania profilami użytkowników, zabezpieczeń, zbieranie wzorców użycia itd. Dostępność poszczególnych dzierżawców nie wpływa na dostępność aplikacji jako całość. Jednak dostępności i wydajności bazy danych zarządzania jest szczególnie ważne dla funkcji aplikacji i jeśli bazy danych zarządzania znajdują się w trybie offline całej aplikacji jest w trybie offline.</i>  

W tym artykule omówiono strategii odzyskiwania po awarii, obejmujących szereg scenariuszy z koszt poufnych uruchamiania aplikacji, aby te wymagania rygorystyczne dostępności.

> [!NOTE]
> Jeśli używasz Premium lub baz danych biznesowych krytyczne (wersja zapoznawcza) i pule elastyczne, umożliwia elastyczne regionalnej awarii konwertując je do konfiguracji wdrożenia nadmiarowe strefy (obecnie w wersji zapoznawczej). Zobacz [Strefowo nadmiarowy baz danych](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenariusz 1. Koszt poufnych uruchamiania
<i>I am firm uruchamiania i bardzo jestem koszt poufnych.  Chcę, aby uprościć wdrażanie i zarządzanie aplikacji i I ma ograniczone umowy SLA dla poszczególnych odbiorców. Ale chcę upewnić się, aplikacja jako całość nigdy nie jest w trybie offline.</i>

Do zaspokojenia wymogu prostotę, wdrażanie wszystkich dzierżawy baz danych w jednej elastycznej puli w regionie Azure wybranym i wdrażanie baz danych zarządzania jako replikacją geograficzną pojedynczych baz danych. W przypadku odzyskiwania po awarii dzierżawcy Użyj przywracaniem geograficznym, które pojawia się bez ponoszenia dodatkowych kosztów. Aby zapewnić dostępność bazy danych zarządzania, replikacja geograficzna je do innego regionu przy użyciu automatycznej pracy awaryjnej grupy (w — wersja zapoznawcza) (krok 1). Koszt trwającej konfiguracji odzyskiwania po awarii, w tym scenariuszu jest równa całkowity koszt dodatkowej bazy danych. Ta konfiguracja została przedstawiona na diagramie dalej.

![Rysunek 1.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

W przypadku wystąpienia awarii w regionie podstawowym, kroki odzyskiwania, aby przełączyć aplikację w trybie online są zilustrowane dalej diagramu.

* Grupa trybu failover inicjuje automatycznej pracy awaryjnej bazy danych zarządzania w regionie odzyskiwania po awarii. Aplikacja jest automatycznie ponownie podłączane do nowych kont głównych i wszystkich nowych i baz danych dzierżawy są tworzone w regionie odzyskiwania po awarii. Istniejących klientów, zobacz swoje dane, które są tymczasowo niedostępne.
* Tworzenie puli elastycznej z taką samą konfigurację jak oryginalny puli (2).
* Geograficzne umożliwia utworzenie kopii dzierżawcy baz danych [3]. Można wziąć pod uwagę wyzwalania poszczególnych przywraca przez użytkownika końcowego połączenia lub użyj innego schematu priorytet specyficzne dla aplikacji.


W tym momencie w regionie odzyskiwania po awarii aplikacji jest znowu w trybie online, ale niektórzy klienci opóźnienie podczas uzyskiwania dostępu do swoich danych.

![Rysunek 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Jeśli awarii tymczasowe, jest możliwe, że regionu podstawowego zostaną odzyskane przy Azure przed przywracania bazy danych zostaną ukończone w regionie odzyskiwania po awarii. W takim przypadku organizowania przenoszenie aplikacji do regionu podstawowego. Ten proces trwa czynności przedstawione na diagramie dalej.

* Anulowanie wszystkich żądań oczekujących przywracaniem geograficznym.   
* Tryb failover baz danych zarządzania w regionie podstawowym (5). Po odzyskaniu regionu starego kolory podstawowe automatycznie stały się pomocnicze bazy danych. Teraz one ponownie przełączyć się ról. 
* Zmień parametry połączenia aplikacji, aby wskazywały regionu podstawowego. Teraz wszystkich nowych kont i baz danych dzierżawy są tworzone w regionie podstawowym. Niektóre istniejących klientów, zobacz swoje dane, które są tymczasowo niedostępne.   
* Ustaw wszystkie bazy danych w puli DR tylko do odczytu do upewnij się, że nie można zmodyfikować w regionie DR (6). 
* Dla każdej bazy danych w puli DR, które uległy zmianie od czasu odzyskiwania Zmień lub usuń odpowiednie baz danych w puli głównej (7). 
* Skopiuj zaktualizowany baz danych z puli odzyskiwania po awarii do głównej puli (8). 
* Usuwanie puli DR (9)

W tym momencie aplikacji jest w trybie online w regionie podstawowym z wszystkich dzierżawy baz danych dostępnych w puli głównej.

![Rysunek 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Klucz **korzystać** ta strategia jest niski koszt trwającą nadmiarowości warstwy danych. Kopie zapasowe są pobierane automatycznie przez usługę SQL Database z nie aplikacji ponownego zapisywania i bez ponoszenia dodatkowych kosztów.  Koszt poniesienia tylko wtedy, gdy zostaną przywrócone elastycznych baz danych. **Zależność** jest, że pełne odzyskiwania wszystkich baz danych dzierżawy ma długiego czasu. Czas zależy od całkowitej liczby operacji przywracania inicjowania w regionie odzyskiwania po awarii i całkowity rozmiar baz danych dzierżawy. Nawet jeśli niektórzy dzierżawcy przywraca priorytetem przez innych użytkowników, możesz konkurencyjnych wszystkich pozycji, które są inicjowane w tym samym regionie, jak usługa rozstrzyga o kolejności przetwarzania i ogranicza, aby zminimalizować ogólnego wpływu na istniejących klientów baz danych. Ponadto odzyskiwania baz danych dzierżawy nie można uruchomić, dopóki nie zostanie utworzona nowa pula elastyczna w regionie odzyskiwania po awarii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenariusz 2. Dojrzała aplikacji z usługą warstwowych
<i>Jestem dojrzałe aplikacji SaaS oferty usługi warstwowych i innej umowy SLA dla klientów z wersji próbnej i płatności klientów. W przypadku wersji próbnej klientów muszę możliwie kosztów. Klienci wersji próbnej może zająć Przestój, ale chcę, aby zmniejszyć prawdopodobieństwo jej. Płatniczych klientom przestój stanowi zagrożenie transmitowane. Tak, chcę upewnić się, że płatności klienci zawsze będą mogli uzyskiwać dostępu do danych.</i> 

Do obsługi tego scenariusza, należy oddzielić próbne z dzierżawcami płatnych przez umieszczenie ich w osobnych pulach elastycznej. Wersja próbna klienci mają niższe eDTU lub vCores dla każdej dzierżawy i dolnym SLA dłuższego czasu odzyskiwania. Płatniczych klienci znajdują się w puli z większej liczby jednostek eDTU lub vCores dla każdej dzierżawy i wyższe umowy SLA. Zagwarantowanie najniższy czasu odzyskiwania baz danych dzierżawy rzeczywistych klientów są replikacją geograficzną. Ta konfiguracja została przedstawiona na diagramie dalej. 

![Rysunek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Jak pierwszego scenariusza bazy danych zarządzania są bardzo aktywny, aby używać jednego replikacją geograficzną bazy danych dla niego (1). Dzięki temu przewidywalną wydajność nowe subskrypcje klienta, aktualizacji profilu i innych operacji zarządzania. Region, w którym znajdują się kolory podstawowe baz danych zarządzania jest regionie podstawowym i regionu, w którym znajdują się serwery baz danych zarządzania to region odzyskiwania po awarii.

Rzeczywistych klientów dzierżawcy z bazy danych ma aktywne baz danych w puli "płatną" udostępniane w regionie podstawowym. Udostępnić dodatkowej puli o takiej samej nazwie w regionie odzyskiwania po awarii. Poszczególni dzierżawcy jest replikacją geograficzną do dodatkowej puli (2). Umożliwia to szybkie odzyskiwanie wszystkie dzierżawy bazy danych przy użyciu trybu failover. 

W przypadku wystąpienia awarii w regionie podstawowym, na dalej diagram przedstawiono kroki odzyskiwania można wyświetlić aplikacji w tryb online:

![Rysunek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Natychmiast przełączyć bazy danych zarządzania do regionu DR (3).
* Zmień parametry połączenia aplikacji wskaż region odzyskiwania po awarii. Teraz wszystkich nowych kont i baz danych dzierżawy są tworzone w regionie odzyskiwania po awarii. Istniejących klientów wersji próbnej, zobacz swoje dane, które są tymczasowo niedostępne.
* Tryb failover płatną dzierżawy baz danych w puli w regionie odzyskiwania po awarii do przywrócenia natychmiast ich dostępności (4). Ponieważ przejście w tryb failover jest szybkie metadanych zmiany poziomu, należy wziąć pod uwagę optymalizacji, gdzie poszczególne pracy w trybie Failover są wyzwalane na żądanie przez użytkownika końcowego połączenia. 
* Jeśli wartość rozmiaru lub vCore eDTU puli dodatkowej został niższa niż podstawowy, ponieważ pomocniczej bazy danych tylko wymagane możliwości przetwarzania dzienniki zmian podczas były pomocnicze bazy danych, natychmiast zwiększyć pojemność puli do teraz w celu uwzględnienia pełnego obciążenia dla wszystkich dzierżawców (5). 
* Utwórz nową pulę elastyczną z tej samej nazwie i taką samą konfigurację w regionie odzyskiwania po awarii dla baz danych klientów wersji próbnej (6). 
* Po utworzeniu puli klientów wersji próbnej umożliwia geograficzne Przywróć poszczególne dzierżawy w wersji próbnej bazy danych do nowej puli (7). Należy wziąć pod uwagę wyzwalania poszczególnych przywraca przez użytkownika końcowego połączenia lub użyj innego schematu priorytet specyficzne dla aplikacji.

W tym momencie aplikacja jest znowu w trybie online w regionie odzyskiwania po awarii. Wszyscy klienci płatniczych mają dostęp do ich danych, gdy klienci wersji próbnej opóźnienie podczas uzyskiwania dostępu do swoich danych.

Gdy regionu podstawowego zostaną odzyskane przy Azure *po* została przywrócona do aplikacji w regionie odzyskiwania po awarii, można kontynuować działania aplikacji w tym regionie lub można wybrać powrót po awarii do regionu podstawowego. Jeśli jest odzyskiwana regionu podstawowego *przed* zakończenie procesu pracy awaryjnej, należy wziąć pod uwagę w przypadku braku wstecz od razu. Powrót po awarii wykonuje czynności przedstawione na diagramie dalej: 

![Rysunek 6.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Anulowanie wszystkich żądań oczekujących przywracaniem geograficznym.   
* Tryb failover baz danych zarządzania (8). Po odzyskaniu regionu stary serwer podstawowy automatycznie stanie się pomocniczej. Teraz ponownie staje się serwerem podstawowym.  
* Tryb failover płatną dzierżawy baz danych (9). Podobnie po odzyskaniu regionu starego kolory podstawowe automatycznie stanie się pomocnicze bazy danych. Teraz stają się one kolory podstawowe ponownie. 
* Ustaw przywróconej wersji próbnej baz danych, które zostały zmienione w regionie DR tylko do odczytu (10).
* Dla każdej bazy danych z puli klientów wersji próbnej DR zmienione od czasu odzyskiwania Zmień lub Usuń odpowiednią bazę danych w puli głównej wersji próbnej klientów (11). 
* Skopiuj zaktualizowany baz danych z puli odzyskiwania po awarii do głównej puli (12). 
* Usuwanie puli DR [13] 

> [!NOTE]
> Operacja trybu failover jest asynchroniczne. Aby zminimalizować czas odzyskiwania należy wykonać polecenie pracy awaryjnej bazy dzierżawy w partiach co najmniej 20 baz danych. 
> 
> 

Klucz **korzystać** tej strategii jest najwyższym umowy SLA dla klientów płatniczych. Gwarantuje również to nowe wersje próbne są odblokowane, natychmiast po utworzeniu puli DR wersji próbnej. **Zależność** otrzymuje czy taka konfiguracja zwiększa całkowity koszt baz danych dzierżawy przez koszt dodatkowej puli odzyskiwania po awarii dla klientów. Ponadto jeśli pula dodatkowej ma inny rozmiar, rzeczywistych klientów wydajność niższe po w tryb failover do czasu ukończenia uaktualniania puli w regionie odzyskiwania po awarii. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenariusz 3. Geograficznie rozproszonych aplikacji przy użyciu usługi warstwowej
<i>Masz dojrzałe aplikacji SaaS z ofertami warstwowych usługi. Chcę, aby oferować bardzo agresywnie SLA płatną klientów i ograniczyć ryzyko wpływ w przypadku wystąpienia awarii, ponieważ nawet krótki przerwania może spowodować niezadowolenie klienta. Bardzo ważne, czy płatniczych klienci mają zawsze dostęp do swoich danych jest. Wersje próbne są wolne i umowa SLA nie jest oferowany podczas okresu próbnego. </i> 

Do obsługi tego scenariusza, należy użyć trzech oddzielnych elastyczne pule. Udostępnić dwie pule taki sam rozmiar wysokiej Edtu lub vCores na bazę danych w dwóch różnych regionach zawiera klientów płatną dzierżawcy z bazy danych. Trzeci puli zawierającej próbne może mieć niższy Edtu lub vCores na bazę danych i udostępniane w jednym z dwóch regionach.

W celu zagwarantowania najniższy czasu odzyskiwania podczas awarii, są bazy danych dzierżawy płacących odbiorców replikacją geograficzną z głównej bazy danych w dwóch regionach 50%. Podobnie każdy region ma 50% dodatkowej bazy danych. W ten sposób Jeśli region jest w trybie offline, tylko 50% klientów płatną bazy danych ma wpływ i mieć do pracy awaryjnej. Innych baz danych pozostaną nienaruszone. Na poniższym diagramie przedstawiono tę konfigurację:

![Rysunek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Jak w poprzednich scenariuszach bazy danych zarządzania są dość active tak skonfigurowane jako pojedyncze replikacją geograficzną bazy danych (1). Zapewnia przewidywalną wydajność nowego klienta, subskrypcje, aktualizacji profilu i innych operacji zarządzania. Region A jest regionu podstawowego dla baz danych zarządzania, a region B służy do odzyskiwania bazy danych zarządzania.

Bazy danych dzierżawy rzeczywistych klientów są również replikacją geograficzną, ale z kolory podstawowe i pomocnicze podzielony między region A i region B (2). Bazy danych podstawowego dzierżawy wpływ awarii w ten sposób można przełączyć do innego regionu i stają się dostępne. Druga połowa dzierżawy baz danych nie można wpływ na cały. 

Następny diagramie przedstawiono kroki odzyskiwania, aby podjąć w przypadku wystąpienia awarii w regionie A.

![Rysunek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Bezpośrednio w trybie Failover zarządzania bazy danych do regionu B (3).
* Zmień parametry połączenia aplikacji do punktu z bazami danych zarządzania w regionie B. zmodyfikować bazy danych zarządzania, aby upewnić się, że nowe konta i baz danych dzierżawy są tworzone w regionie B i istniejących baz danych dzierżawy znajdują się tam również. Istniejących klientów wersji próbnej, zobacz swoje dane, które są tymczasowo niedostępne.
* Tryb failover płatną dzierżawy baz danych, do puli 2 w regionie B natychmiast przywrócić ich dostępności (4). Ponieważ przejście w tryb failover jest szybkie metadanych zmiany poziomu, można rozważyć optymalizacji, gdzie poszczególne pracy w trybie Failover są wyzwalane na żądanie przez użytkownika końcowego połączenia. 
* Od teraz puli 2 zawiera tylko głównej bazy danych, całkowita obciążenie wzrost puli i natychmiast może zwiększyć jego rozmiaru eDTU (5) lub liczby vCores. 
* Utwórz nową pulę elastyczną z tej samej nazwie i taką samą konfigurację w regionie B dla baz danych klientów wersji próbnej (6). 
* Po utworzeniu puli umożliwia geograficzne przywrócenia poszczególne dzierżawy w wersji próbnej w puli (7). Można wziąć pod uwagę wyzwalania poszczególnych przywraca przez użytkownika końcowego połączenia lub użyj innego schematu priorytet specyficzne dla aplikacji.

> [!NOTE]
> Operacja trybu failover jest asynchroniczne. Aby zminimalizować czas odzyskiwania, należy wykonać polecenie pracy awaryjnej bazy dzierżawy w partiach co najmniej 20 baz danych. 
> 

W tym momencie aplikacja jest znowu w trybie online w regionie B. Wszyscy klienci płatniczych mają dostęp do ich danych, gdy klienci wersji próbnej opóźnienie podczas uzyskiwania dostępu do swoich danych.

Po odzyskaniu region A należy zdecydować, jeśli chcesz użyć region B dla klientów z wersji próbnej lub powrotu po awarii przy użyciu puli klientów wersji próbnej w regionie A. Jedno kryterium można % baz danych dzierżawy w wersji próbnej modyfikowany od czasu odzyskiwania. Niezależnie od tej decyzji musisz ponownie zrównoważyć płatną dzierżawcy między dwoma zestawami. Następny diagramie został pokazany proces w przypadku baz danych dzierżawy w wersji próbnej powrót po awarii do regionu A.  

![Rysunek 6.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Anulowanie wszystkich żądań oczekujących przywracaniem geograficznym do wersji próbnej puli odzyskiwania po awarii.   
* Tryb failover bazie danych zarządzania (8). Po odzyskaniu regionu stary serwer podstawowy automatycznie aktywowano pomocniczej. Teraz ponownie staje się serwerem podstawowym.  
* Wybierz, które płatną dzierżawy baz danych powrót po awarii do puli 1 i zainicjuj tryb failover ich pomocnicze bazy danych (9). Po odzyskaniu regionu wszystkie bazy danych w puli 1 została automatycznie pomocnicze bazy danych. Teraz 50% ich stają się ponownie kolory podstawowe. 
* Zmniejsz rozmiar puli 2 eDTU oryginalnego (10) lub liczby vCores.
* Ustaw wszystkie przywrócone wersji próbnej baz danych w regionie B tylko do odczytu (11).
* Dla każdej bazy danych w wersji próbnej puli odzyskiwania po awarii, który zmienił się od czasu odzyskiwania Zmień lub Usuń odpowiednią bazę danych w puli głównej wersji próbnej (12). 
* Skopiuj zaktualizowany baz danych z puli odzyskiwania po awarii do głównej puli [13]. 
* Usuwanie puli DR (14) 

Klucz **korzyści** tej strategii są:

* Obsługuje ona najbardziej agresywne SLA płatniczych klientom ponieważ zapewnia, że awarii nie może mieć wpływ na więcej niż 50% dzierżawy baz danych. 
* Gwarantuje on, nowe wersje próbne są odblokowane, jak dziennik DR puli jest tworzona podczas odzyskiwania. 
* Umożliwia efektywniejsze wykorzystanie pojemności puli 50% dodatkowej bazy danych w puli 1 i 2 puli zapewniona jest mniej aktywne niż głównej bazy danych.

Głównym **kompromisy** są:

* Operacje CRUD baz danych zarządzania mają mniejsze opóźnienia dla użytkowników końcowych podłączone do regionu A niż dla użytkowników końcowych podłączone do regionu B, ponieważ są one wykonywane względem podstawowej bazy danych zarządzania.
* Wymaga to bardziej złożonych projektu bazy danych zarządzania. Na przykład każdy rekord dzierżawy ma tag lokalizacji, która musi zostać zmienione w tryb failover i powrotu po awarii.  
* Płatniczych klientów może zgłaszać obniżenia wydajności niż zwykle, aż do zakończenia uaktualnienia puli w regionie B. 

## <a name="summary"></a>Podsumowanie
Ten artykuł skupia się na strategii odzyskiwania po awarii dla warstwy bazy danych używana przez aplikację wielodostępne SaaS niezależnego dostawcy oprogramowania. Strategia wybrane opiera się na potrzeby aplikacji, takich jak model biznesowy, umowy dotyczącej poziomu usług, aby oferować klientom, budżetu ograniczenia itp. Każdej strategii opisane przedstawiono zalety i zależność, można podjąć świadomej decyzji. Określonych aplikacji może także inne składniki platformy Azure. Aby przejrzeć ich wskazówki ciągłości biznesowej i organizowania odzyskiwania warstwy bazy danych z nimi. Aby dowiedzieć się więcej o zarządzaniu odzyskiwania bazy danych aplikacji na platformie Azure, zobacz [projektowania rozwiązań chmury odzyskiwania po awarii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat usługi Azure SQL bazy danych automatycznego tworzenia kopii zapasowych, zobacz [bazy danych SQL automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md).
* Omówienie ciągłości działalności biznesowej i scenariuszy, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej o używaniu kopie zapasowe automatycznego odzyskiwania, zobacz [przywrócić bazę danych z kopii zapasowych inicjowane przez usługę](sql-database-recovery-using-backups.md).
* Informacje na temat opcji odzyskiwania szybsze, zobacz [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md).
* Aby dowiedzieć się, za pomocą automatycznych kopii zapasowej do archiwizacji, zobacz [kopiowanie bazy danych](sql-database-copy.md).

