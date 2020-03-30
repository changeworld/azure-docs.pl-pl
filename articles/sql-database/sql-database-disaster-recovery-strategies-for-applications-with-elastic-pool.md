---
title: Projektowanie rozwiązań do odzyskiwania po awarii
description: Dowiedz się, jak zaprojektować rozwiązanie chmurowe do odzyskiwania po awarii, wybierając odpowiedni wzorzec pracy awaryjnej.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 4eeaa187142a6d0d97b12f685ebc455f3844606f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825870"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategie odzyskiwania po awarii dla aplikacji korzystających z elastycznych pul bazy danych SQL

Z biegiem lat dowiedzieliśmy się, że usługi w chmurze nie są niezawodne i zdarzają się katastrofalne incydenty. Baza danych SQL zapewnia kilka możliwości zapewnienia ciągłości biznesowej aplikacji w przypadku wystąpienia tych zdarzeń. [Pule elastyczne](sql-database-elastic-pool.md) i pojedyncze bazy danych obsługują ten sam rodzaj funkcji odzyskiwania po awarii (DR). W tym artykule opisano kilka strategii odzyskiwania po awarii dla pul elastycznych, które wykorzystują te funkcje ciągłości biznesowej bazy danych SQL.

W tym artykule użyto następującego kanonicznego wzorca aplikacji isv SaaS:

Nowoczesna aplikacja sieci web oparta na chmurze udostępnia jedną bazę danych SQL dla każdego użytkownika końcowego. Isv ma wielu klientów i dlatego używa wielu baz danych, znany jako dzierżawy baz danych. Ponieważ bazy danych dzierżawy zazwyczaj mają wzorce działań nieprzewidywalne, niezależna firma isv używa puli elastycznej, aby koszt bazy danych bardzo przewidywalne przez dłuższy czas. Pula elastyczna upraszcza również zarządzanie wydajnością, gdy wzrost aktywności użytkownika. Oprócz baz danych dzierżawy aplikacja używa również kilku baz danych do zarządzania profilami użytkowników, zabezpieczeniami, zbieraniem wzorców użycia itp. Dostępność poszczególnych dzierżaw nie ma wpływu na dostępność aplikacji jako całości. Jednak dostępność i wydajność baz danych zarządzania ma kluczowe znaczenie dla funkcji aplikacji i jeśli bazy danych zarządzania są w trybie offline, cała aplikacja jest w trybie offline.

W tym artykule omówiono strategie odzyskiwania po awarii obejmujące szereg scenariuszy, od aplikacji startowych wrażliwych na koszty do tych z rygorystycznymi wymaganiami dotyczącymi dostępności.

> [!NOTE]
> Jeśli używasz baz danych Premium lub Business Critical i pul elastycznych, możesz je odporne na awarie regionalne, konwertując je na konfigurację wdrożenia nadmiarowego strefy. Zobacz [bazy danych nadmiarowe strefowe](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenariusz 1. Uruchamianie z uwzględnieniem kosztów

Jestem firmą start-up i jestem bardzo wrażliwy na koszty.  Chcę uprościć wdrażanie i zarządzanie aplikacją i mogę mieć ograniczoną umowy SLA dla klientów indywidualnych. Ale chcę upewnić się, że aplikacja jako całość nigdy nie jest w trybie offline.

Aby spełnić wymagania dotyczące prostoty, należy wdrożyć wszystkie bazy danych dzierżawy w jednej puli elastycznej w wybranym regionie platformy Azure i wdrożyć bazy danych zarządzania jako pojedyncze bazy danych replikowane geograficznie. W przypadku odzyskiwania po awarii dzierżawców należy użyć funkcji przywracania geograficznego, co wiąże się z bez dodatkowych kosztów. Aby zapewnić dostępność baz danych zarządzania, replikuj je geograficznie do innego regionu przy użyciu grupy automatycznego trybu failover (krok 1). Bieżący koszt konfiguracji odzyskiwania po awarii w tym scenariuszu jest równy całkowitemu kosztowi pomocniczych baz danych. Ta konfiguracja jest zilustrowana na następnym diagramie.

![Rysunek 1.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Jeśli wystąpi awaria w regionie podstawowym, kroki odzyskiwania, aby przenieść aplikację do trybu online są zilustrowane przez następny diagram.

* Grupa trybu failover inicjuje automatyczną tryb failover bazy danych zarządzania do regionu odzyskiwania po awarii. Aplikacja jest automatycznie ponownie połączony z nowym podstawowym i wszystkie nowe konta i dzierżawy baz danych są tworzone w regionie odzyskiwania po awarii. Obecni klienci widzą swoje dane tymczasowo niedostępne.
* Utwórz pulę elastyczną z taką samą konfiguracją jak oryginalna pula (2).
* Użyj przywracania geograficznego, aby utworzyć kopie baz danych dzierżawy (3). Można rozważyć wyzwolenie poszczególnych przywraca przez połączenia użytkownika końcowego lub użyć innego schematu priorytetu specyficzne dla aplikacji.

W tym momencie aplikacja jest z powrotem w trybie online w regionie odzyskiwania po awarii, ale niektórzy klienci doświadczają opóźnienia podczas uzyskiwania dostępu do swoich danych.

![Rysunek 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Jeśli awaria była tymczasowa, jest możliwe, że region podstawowy jest odzyskiwany przez platformę Azure przed zakończeniem wszystkich przywracania bazy danych w regionie odzyskiwania po awarii. W takim przypadku zaaranżować przenoszenie aplikacji z powrotem do regionu podstawowego. Proces wykonuje kroki przedstawione na następnym diagramie.

* Anuluj wszystkie zaległe żądania przywracania geograficznego.
* Przejmuje się w błąd bazy danych zarządzania w regionie podstawowym (5). Po odzyskaniu regionu stare prawybory automatycznie stały się drugortarłonami. Teraz ponownie zmieniają role.
* Zmień ciąg połączenia aplikacji, aby wskazywał z powrotem do regionu podstawowego. Teraz wszystkie nowe konta i dzierżawy baz danych są tworzone w regionie podstawowym. Niektórzy istniejący klienci widzą swoje dane tymczasowo niedostępne.
* Ustaw wszystkie bazy danych w puli odzyskiwania po awarii jako tylko do odczytu, aby upewnić się, że nie można ich zmodyfikować w regionie odzyskiwania po awarii (6).
* Dla każdej bazy danych w puli odzyskiwania po awarii, która uległa zmianie od czasu odzyskania, zmień nazwę lub usuń odpowiednie bazy danych w puli podstawowej (7).
* Skopiuj zaktualizowane bazy danych z puli odzyskiwania po awarii do puli podstawowej (8).
* Usuwanie puli odzyskiwania po awarii (9)

W tym momencie aplikacja jest w trybie online w regionie podstawowym ze wszystkimi bazami danych dzierżawy dostępnych w puli podstawowej.

![Rysunek 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Kluczową **zaletą** tej strategii jest niski koszt bieżący nadmiarowości warstwy danych. Kopie zapasowe są pobierane automatycznie przez usługę bazy danych SQL bez ponownego zapisywania aplikacji i bez dodatkowych kosztów.  Koszt jest ponoszony tylko wtedy, gdy elastyczne bazy danych są przywracane. Kompromis **polega** na tym, że całkowite odzyskanie wszystkich baz danych dzierżawy zajmuje dużo czasu. Czas zależy od całkowitej liczby przywracania zainicjowanych w regionie odzyskiwania po awarii i ogólnego rozmiaru baz danych dzierżawy. Nawet jeśli priorytet przywraca niektórych dzierżawców nad innymi, konkurują z wszystkich innych przywraca, które są inicjowane w tym samym regionie, co usługa rozstrzyga i ogranicza, aby zminimalizować ogólny wpływ na istniejące bazy danych klientów. Ponadto odzyskiwanie baz danych dzierżawy nie można uruchomić, dopóki nie zostanie utworzona nowa pula elastyczna w regionie odzyskiwania po awarii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenariusz 2. Dojrzała aplikacja z usługą warstwową

Jestem dojrzałą aplikacją SaaS z wielopoziomowymi ofertami usług i różnymi opłatami SLA dla klientów testowych i płacących klientów. Dla klientów testowych muszę obniżyć koszty w jak największej wysokości. Klienci testowi mogą zdejmować przestoje, ale chcę zmniejszyć jego prawdopodobieństwo. Dla płacących klientów każdy przestój jest ryzykiem lotu. Dlatego chcę mieć pewność, że płacący klienci są zawsze w stanie uzyskać dostęp do swoich danych.

Aby obsługiwać ten scenariusz, oddzielić dzierżawy wersji próbnej od płatnych dzierżaw, umieszczając je w oddzielnych pulach elastycznych. Klienci wersji próbnej mają niższe eDTU lub wirtualne na dzierżawę i niższą umowy SLA z dłuższym czasem odzyskiwania. Płacący klienci znajdują się w puli z wyższymi eDTU lub wirtualnymi na dzierżawcę i wyższą opłatą SLA. Aby zagwarantować najniższy czas odzyskiwania, bazy danych dzierżawców klientów płacących są replikowane geograficznie. Ta konfiguracja jest zilustrowana na następnym diagramie.

![Rysunek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Podobnie jak w pierwszym scenariuszu, bazy danych zarządzania są dość aktywne, więc można użyć jednej bazy danych replikowanej geograficznie dla niego (1). Zapewnia to przewidywalną wydajność dla nowych subskrypcji klientów, aktualizacji profilu i innych operacji zarządzania. Region, w którym znajdują się prawybory baz danych zarządzania jest regionem podstawowym, a region, w którym znajdują się pomocnicze bazy danych zarządzania, jest regionem odzyskiwania po awarii.

Bazy danych dzierżawców płacących klientów mają aktywne bazy danych w puli "płatnej" aprowizowana w regionie podstawowym. Aprowizuj pulę pomocniczą o tej samej nazwie w regionie odzyskiwania po awarii. Każdy dzierżawca jest replikowany geograficznie do puli pomocniczej (2). Umożliwia to szybkie odzyskiwanie wszystkich baz danych dzierżawy przy użyciu trybu failover.

Jeśli wystąpi awaria w regionie podstawowym, kroki odzyskiwania, aby przenieść aplikację do trybu online są zilustrowane na następnym diagramie:

![Rysunek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Natychmiast przeładuj w błąd bazy danych zarządzania do regionu odzyskiwania po awarii (3).
* Zmień parametry połączenia aplikacji, aby wskazywał region odzyskiwania po awarii. Teraz wszystkie nowe konta i dzierżawy baz danych są tworzone w regionie odzyskiwania po awarii. Istniejący klienci wersji próbnej widzą swoje dane tymczasowo niedostępne.
* Przełącz się w stan fail over płatnych dzierżawców baz danych do puli w regionie odzyskiwania po awarii, aby natychmiast przywrócić ich dostępność (4). Ponieważ tryb failover jest szybką zmianą poziomu metadanych, należy wziąć pod uwagę optymalizację, w której poszczególne tryby failover są wyzwalane na żądanie przez połączenia użytkownika końcowego.
* Jeśli rozmiar eDTU puli dodatkowej lub wartość typu vCore była niższa niż podstawowa, ponieważ pomocnicze bazy danych wymagały tylko pojemności do przetwarzania dzienników zmian, gdy były pomocniczymi elementami, natychmiast zwiększ pojemność puli, aby pomieścić pełne obciążenie wszystkich najemców (5).
* Utwórz nową pulę elastyczną o tej samej nazwie i tej samej konfiguracji w regionie odzyskiwania po awarii dla baz danych klientów wersji próbnej (6).
* Po utworzeniu puli klientów wersji próbnej użyj przywracania geograficznego, aby przywrócić bazy danych poszczególnych dzierżawców wersji próbnej do nowej puli (7). Należy rozważyć wyzwolenie poszczególnych przywraca przez połączenia użytkownika końcowego lub użyć innego schematu priorytetu specyficzne dla aplikacji.

W tym momencie aplikacja jest z powrotem w trybie online w regionie odzyskiwania po awarii. Wszyscy płacący klienci mają dostęp do swoich danych, podczas gdy klienci wersji próbnej doświadczają opóźnienia podczas uzyskiwania dostępu do swoich danych.

Po odzyskaniu regionu podstawowego przez platformę Azure *po* przywróceniu aplikacji w regionie odzyskiwania po awarii można kontynuować uruchamianie aplikacji w tym regionie lub można zdecydować się na powrót po awarii do regionu podstawowego. Jeśli region podstawowy jest odzyskiwany *przed* zakończeniem procesu pracy awaryjnej, należy rozważyć niepowodzenie z powrotem od razu. Powrót po awarii wykonuje kroki opisane na następnym diagramie:

![Rysunek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Anuluj wszystkie zaległe żądania przywracania geograficznego.
* Przejmuje się awaryjnie w bazach danych zarządzania (8). Po odzyskaniu regionu stary podstawowy automatycznie staje się drugorzędny. Teraz znowu staje się podstawowym.  
* Przejmuje się awaryjnie w bazach danych dzierżawy płatnej (9). Podobnie, po ożywieniu regionu, stare prawybory automatycznie stają się pomocniczymi. Teraz znowu stają się prawyborami.
* Ustaw przywrócone bazy danych wersji próbnej, które uległy zmianie w regionie odzyskiwania po awarii, na tylko do odczytu (10).
* Dla każdej bazy danych w puli odzyskiwania po awarii klientów wersji próbnej, która uległa zmianie od czasu odzyskania, zmień nazwę lub usuń odpowiednią bazę danych w puli podstawowej klientów próbnej (11).
* Skopiuj zaktualizowane bazy danych z puli odzyskiwania po awarii do puli podstawowej (12).
* Usuń pulę odzyskiwania po awarii (13).

> [!NOTE]
> Operacja pracy awaryjnej jest asynchroniza. Aby zminimalizować czas odzyskiwania, ważne jest, aby wykonać polecenie trybu failover bazy danych dzierżawy w partiach co najmniej 20 baz danych.

Kluczową **zaletą** tej strategii jest to, że zapewnia najwyższą umowy SLA dla płacących klientów. Gwarantuje również, że nowe wersje próbne są odblokowane zaraz po utworzeniu puli odzyskiwania po awarii. Kompromis **polega** na tym, że ta konfiguracja zwiększa całkowity koszt baz danych dzierżawy o koszt dodatkowej puli odzyskiwania po awarii dla płatnych klientów. Ponadto jeśli pula pomocnicza ma inny rozmiar, płacący klienci doświadczają niższej wydajności po przełączeniu w pracę awaryjną, dopóki uaktualnienie puli w regionie odzyskiwania po awarii nie zostanie zakończone.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenariusz 3. Aplikacja rozproszona geograficznie z usługą warstwową

Mam dojrzałą aplikację SaaS z warstwowymi ofertami usług. Chcę zaoferować bardzo agresywną umowy SLA moim płatnym klientom i zminimalizować ryzyko wystąpienia awarii, ponieważ nawet krótka przerwa może spowodować niezadowolenie klientów. Bardzo ważne jest, aby płacący klienci zawsze mogli uzyskać dostęp do swoich danych. Wersje próbne są bezpłatne, a umowy SLA nie są oferowane w okresie próbnym.

Aby obsługiwać ten scenariusz, należy użyć trzech oddzielnych pul elastycznych. Aprowizuj dwie pule o równym rozmiarze z wysokimi eDTU lub vCorami na bazę danych w dwóch różnych regionach, aby zawierały bazy danych dzierżawy płatnych klientów. Trzecia pula zawierająca dzierżawców wersji próbnej może mieć niższe eDTU lub vCore na bazę danych i być aprowizowana w jednym z dwóch regionów.

Aby zagwarantować najniższy czas odzyskiwania podczas awarii, bazy danych dzierżawców klientów płacących są replikowane geograficznie z 50% podstawowych baz danych w każdym z dwóch regionów. Podobnie każdy region ma 50% pomocniczych baz danych. W ten sposób, jeśli region jest w trybie offline, tylko 50% płatnych baz danych klientów mają wpływ i muszą awaryjnie. Inne bazy danych pozostają nienaruszone. Ta konfiguracja jest zilustrowana na poniższym diagramie:

![Rysunek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Podobnie jak w poprzednich scenariuszach, bazy danych zarządzania są dość aktywne, więc skonfiguruj je jako pojedyncze bazy danych replikowane geograficznie (1). Zapewnia to przewidywalną wydajność nowych subskrypcji klientów, aktualizacji profili i innych operacji zarządzania. Region A jest regionem podstawowym dla baz danych zarządzania i region B jest używany do odzyskiwania baz danych zarządzania.

Bazy danych dzierżawców płacących klientów są również replikowane geograficznie, ale z głównymi i pomocniczymi bazami danych podzielonymi między regionY A i region B (2). W ten sposób dzierżawy podstawowych baz danych dotkniętych awarią może awaryjnie do innego regionu i stają się dostępne. Nie ma to żadnego wpływu na drugą połowę baz danych dzierżawy.

Następny diagram ilustruje kroki odzyskiwania do podjęcia, jeśli wystąpi awaria w regionie A.

![Rysunek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Natychmiast w pracy awaryjnej baz danych zarządzania do regionu B (3).
* Zmień parametry połączenia aplikacji, aby wskazywał bazy danych zarządzania w regionie B. Zmodyfikuj bazy danych zarządzania, aby upewnić się, że nowe konta i bazy danych dzierżawy są tworzone w regionie B, a tam również znajdują się istniejące bazy danych dzierżawy. Istniejący klienci wersji próbnej widzą swoje dane tymczasowo niedostępne.
* Przełącz się w stan fail over do bazy danych płatnego dzierżawcy do puli 2 w regionie B, aby natychmiast przywrócić ich dostępność (4). Ponieważ tryb failover jest szybką zmianą poziomu metadanych, można rozważyć optymalizację, w której poszczególne tryb failover są wyzwalane na żądanie przez połączenia użytkownika końcowego.
* Ponieważ teraz pula 2 zawiera tylko podstawowe bazy danych, całkowite obciążenie w puli wzrasta i może natychmiast zwiększyć jego rozmiar eDTU (5) lub liczbę podstawowych.
* Utwórz nową pulę elastyczną o tej samej nazwie i tej samej konfiguracji w regionie B dla baz danych klientów wersji próbnej (6).
* Po utworzeniu puli użyj przywracania geograficznego, aby przywrócić indywidualną próbną bazę danych dzierżawy do puli (7). Można rozważyć wyzwolenie poszczególnych przywraca przez połączenia użytkownika końcowego lub użyć innego schematu priorytetu specyficzne dla aplikacji.

> [!NOTE]
> Operacja pracy awaryjnej jest asynchroniza. Aby zminimalizować czas odzyskiwania, ważne jest, aby wykonać polecenie trybu failover bazy danych dzierżawy w partiach co najmniej 20 baz danych.

W tym momencie aplikacja jest z powrotem w trybie online w regionie B. Wszyscy płacący klienci mają dostęp do swoich danych, podczas gdy klienci wersji próbnej doświadczają opóźnienia podczas uzyskiwania dostępu do swoich danych.

Po odzyskaniu regionu A należy zdecydować, czy chcesz używać regionu B dla klientów próbnych, czy powrotu po awarii do korzystania z puli klientów próbnych w regionie A. Jednym z kryteriów może być % próbnych baz danych dzierżawy zmodyfikowanych od czasu odzyskania. Niezależnie od tej decyzji, należy ponownie zrównoważyć płatnych najemców między dwoma pulami. następny diagram ilustruje proces, gdy baza danych dzierżawy wersji próbnej powróci do regionu A.  

![Rysunek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Anuluj wszystkie zaległe żądania przywracania geograficznego do puli próbnej pamięci dr.
* Przejmuje się awaryjnie w bazie danych zarządzania (8). Po odzyskaniu regionu stary podstawowy automatycznie stał się drugorzędny. Teraz znowu staje się podstawowym.  
* Wybierz, które płatne bazy danych dzierżawy powiodą się z powrotem do puli 1 i inicjują opcję pracy awaryjnej do ich pomocniczych (9). Po odzyskaniu regionu wszystkie bazy danych w puli 1 automatycznie stały się pomocniczymi elementami. Teraz 50% z nich ponownie staje się prawyborami.
* Zmniejsz rozmiar puli 2 do oryginalnego eDTU (10) lub liczby punów wirtualnych.
* Ustaw wszystkie przywrócone bazy danych próbnych w regionie B na tylko do odczytu (11).
* Dla każdej bazy danych w puli odzyskiwania po awarii, która uległa zmianie od czasu odzyskania, zmień nazwę lub usuń odpowiednią bazę danych w puli podstawowej wersji próbnej (12).
* Skopiuj zaktualizowane bazy danych z puli odzyskiwania po awarii do puli podstawowej (13).
* Usuń pulę odzyskiwania po awarii (14).

Główne **zalety** tej strategii to:

* Obsługuje najbardziej agresywną umowę SLA dla płacących klientów, ponieważ zapewnia, że awaria nie może mieć wpływu na więcej niż 50% baz danych dzierżawy.
* Gwarantuje, że nowe wersje próbne są odblokowane, gdy tylko pula odzyskiwania po awarii ścieżki zostanie utworzona podczas odzyskiwania.
* Umożliwia bardziej efektywne wykorzystanie pojemności puli, ponieważ 50% pomocniczych baz danych w puli 1 i puli 2 są gwarantowane być mniej aktywne niż podstawowe bazy danych.

Główne **kompromisy** to:

* Operacje CRUD względem baz danych zarządzania mają mniejsze opóźnienia dla użytkowników końcowych połączonych z regionem A niż dla użytkowników końcowych połączonych z regionem B, ponieważ są one wykonywane względem podstawowych baz danych zarządzania.
* Wymaga bardziej złożonego projektu bazy danych zarządzania. Na przykład każdy rekord dzierżawy ma tag lokalizacji, który musi zostać zmieniony podczas pracy awaryjnej i powrotu po awarii.  
* Klienci płacący mogą mieć niższą wydajność niż zwykle, dopóki uaktualnienie puli w regionie B nie zostanie zakończone.

## <a name="summary"></a>Podsumowanie

W tym artykule koncentruje się na strategii odzyskiwania po awarii dla warstwy bazy danych używane przez SaaS isv aplikacji wielu dzierżawców. Wybrana strategia jest oparta na potrzebach aplikacji, takich jak model biznesowy, umowa SLA, którą chcesz zaoferować klientom, ograniczenie budżetu itp. Każda opisana strategia określa korzyści i kompromis, dzięki czemu można podjąć świadomą decyzję. Ponadto określonej aplikacji prawdopodobnie zawiera inne składniki platformy Azure. Dlatego można przejrzeć ich wskazówki dotyczące ciągłości biznesowej i zorganizować odzyskiwanie warstwy bazy danych z nimi. Aby dowiedzieć się więcej na temat zarządzania odzyskiwaniem aplikacji bazy danych na platformie Azure, zobacz [Projektowanie rozwiązań w chmurze do odzyskiwania po awarii.](sql-database-designing-cloud-solutions-for-disaster-recovery.md)  

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o automatycznych kopiach zapasowych usługi Azure SQL Database, zobacz [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).
* Aby zapoznać się z omówieniem ciągłości biznesowej i scenariuszami, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat używania automatycznych kopii zapasowych do odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](sql-database-recovery-using-backups.md).
* Aby dowiedzieć się więcej o szybszych opcjach odzyskiwania, zobacz [Aktywne grupy replikacji geograficznej](sql-database-active-geo-replication.md) i [Auto-failover](sql-database-auto-failover-group.md).
* Aby dowiedzieć się więcej na temat używania automatycznych kopii zapasowych do archiwizacji, zobacz [kopiowanie bazy danych](sql-database-copy.md).
