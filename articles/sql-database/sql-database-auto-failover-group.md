---
title: Grupy trybu failover
description: Grupy automatycznego trybu failover są funkcją SQL Database, która umożliwia zarządzanie replikacją i automatycznym/koordynowanym trybem failover grupy baz danych na serwerze SQL Database lub wszystkich bazach danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 10/23/2019
ms.openlocfilehash: 88bcee1cbb23bf298c5ad3920a7744d8da6ce3fb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821957"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Używanie grup z obsługą trybu failover w celu zapewnienia przezroczystej i skoordynowanej pracy w trybie failover wielu baz danych

Grupy autotrybu failover są funkcją SQL Database, która umożliwia zarządzanie replikacją i trybem failover grupy baz danych na serwerze SQL Database lub wszystkich baz danych w wystąpieniu zarządzanym w innym regionie. Jest to deklaratywne streszczenie na istniejącej funkcji [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) , zaprojektowane w celu uproszczenia wdrażania geograficznie replikowanych baz danych i zarządzania nimi na dużą skalę. Możesz zainicjować tryb failover ręcznie lub można delegować go do usługi SQL Database na podstawie zasad zdefiniowanych przez użytkownika. Ta ostatnia opcja umożliwia automatyczne odzyskanie wielu pokrewnych baz danych w regionie pomocniczym po katastrofalnym błędzie lub innym nieplanowanym zdarzeniu, które powoduje całkowite lub częściowe utratę dostępności usługi SQL Database w regionie podstawowym. Grupa trybu failover może zawierać jedną lub wiele baz danych, zwykle używanych przez tę samą aplikację. Ponadto można użyć dodatkowych baz danych z możliwością odczytu, aby odciążać obciążenia zapytań służących tylko do odczytywania. Ponieważ grupy autotrybu failover obejmują wiele baz danych, te bazy danych muszą być skonfigurowane na serwerze podstawowym. Grupy autotrybu failover obsługują replikację wszystkich baz danych w grupie tylko do jednego serwera pomocniczego w innym regionie.

> [!NOTE]
> Podczas pracy z pojedynczymi lub w puli baz danych na serwerze SQL Database i potrzebujesz wielu serwerów pomocniczych w tym samym lub różnych regionach, użyj [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md). 

W przypadku korzystania z grup automatycznych trybu failover z automatycznymi zasadami trybu failover każda awaria wpływająca na jedną lub kilka baz danych w grupie powoduje automatyczne przejście w tryb failover. Zazwyczaj są to zdarzenia, które nie mogą być nieznacznie ograniczane przez wbudowane operacje automatycznej wysokiej dostępności. Przykładowe wyzwalacze trybu failover obejmują zdarzenie spowodowane przez pierścień dzierżawy lub sterowanie, które nie uległy awarii z powodu wycieku pamięci jądra systemu operacyjnego w kilku węzłach obliczeniowych lub zdarzenia spowodowane przez co najmniej jeden pierścień dzierżawy, ponieważ niewłaściwy kabel sieciowy został wycięty podczas przetwarzania typu ro likwidowanie sprzętu utine.  Aby uzyskać więcej informacji, zobacz [SQL Database wysoka dostępność](sql-database-high-availability.md).

Ponadto grupy autotrybu failover udostępniają punkty końcowe odbiornika do odczytu i zapisu oraz tylko do odczytu, które pozostaną bez zmian podczas pracy w trybie failover. Bez względu na to, czy jest używana ręczna czy automatyczna aktywacja trybu failover, przełączanie do trybu failover wszystkie pomocnicze bazy danych w grupie do podstawowego. Po zakończeniu pracy w trybie failover bazy danych rekord DNS zostanie automatycznie zaktualizowany, aby przekierować punkty końcowe do nowego regionu. Aby uzyskać informacje na temat określonych elementów RPO i RTO, zobacz [Omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).

W przypadku korzystania z grup automatycznych trybu failover z automatycznymi zasadami trybu failover każda awaria wpływająca na bazy danych na serwerze SQL Database lub wystąpieniu zarządzanym powoduje automatyczne przejście w tryb failover. Grupa autotrybu failover można zarządzać przy użyciu:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Grupa trybu failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [Interfejs API REST: Grupa trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Po przejściu w tryb failover upewnij się, że wymagania dotyczące uwierzytelniania dla serwera i bazy danych są skonfigurowane na nowym serwerze podstawowym. Aby uzyskać szczegółowe informacje, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md).

Aby osiągnąć prawdziwą ciągłość biznesową, Dodawanie nadmiarowości bazy danych między centrami elementów jest tylko częścią rozwiązania. Odzyskiwanie aplikacji (usługi) od końca do końca po katastrofalnym błędzie wymaga odzyskania wszystkich składników wchodzących w skład usługi i usług zależnych. Przykładowe składniki obejmują oprogramowanie klienta (na przykład przeglądarkę z niestandardowym językiem JavaScript), frontony sieci Web, magazyn i system DNS. Należy pamiętać, że wszystkie składniki są odporne na te same awarie i stają się dostępne w ramach celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkie usługi zależne i zrozumieć gwarancje i funkcje, które zapewnia. Następnie należy wykonać odpowiednie czynności, aby upewnić się, że usługa działa w trybie failover usług, na których jest ona zależna. Aby uzyskać więcej informacji na temat projektowania rozwiązań na potrzeby odzyskiwania po awarii, zobacz [projektowanie rozwiązań w chmurze na potrzeby odzyskiwania po awarii przy użyciu aktywnej replikacji geograficznej](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologia i możliwości grupy z obsługą trybu failover

- **Grupa trybu failover (przeciwmgielne)**

  Grupa trybu failover to nazwana grupa baz danych zarządzana przez pojedynczy serwer SQL Database lub w jednym zarządzanym wystąpieniu, która może być przełączana w tryb failover jako jednostka do innego regionu w przypadku, gdy wszystkie lub niektóre podstawowe bazy danych staną się niedostępne z powodu awarii w regionie podstawowym. Po utworzeniu dla wystąpień zarządzanych Grupa trybu failover zawiera wszystkie bazy danych użytkownika w tym wystąpieniu, w związku z czym można skonfigurować tylko jedną grupę trybu failover w wystąpieniu.
  
  > [!IMPORTANT]
  > Nazwa grupy trybu failover musi być globalnie unikatowa w ramach domeny `.database.windows.net`.

- **Serwery SQL Database**

     W przypadku serwerów SQL Database niektóre lub wszystkie bazy danych użytkownika na pojedynczym serwerze SQL Database można umieścić w grupie trybu failover. Ponadto serwer SQL Database obsługuje wiele grup trybu failover na jednym serwerze SQL Database.

- **Głównym**

  Serwer SQL Database lub wystąpienie zarządzane, które hostuje podstawowe bazy danych w grupie trybu failover.

- **Dodatkowych**

  Serwer SQL Database lub wystąpienie zarządzane, które hostuje pomocnicze bazy danych w grupie trybu failover. Pomocnicza nie może znajdować się w tym samym regionie co podstawowy.

- **Dodawanie pojedynczych baz danych do grupy trybu failover**

  Można umieścić kilka pojedynczych baz danych na tym samym serwerze SQL Database w tej samej grupie trybu failover. W przypadku dodania pojedynczej bazy danych do grupy trybu failover automatycznie zostanie utworzona pomocnicza baza danych z tą samą wersją i rozmiarem obliczeniowym na serwerze pomocniczym.  Ten serwer został określony podczas tworzenia grupy trybu failover. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze pomocniczym, łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, na serwerze pomocniczym zostanie utworzony nowy element pomocniczy.
  
  > [!IMPORTANT]
  > Upewnij się, że serwer pomocniczy nie ma bazy danych o tej samej nazwie, chyba że jest to istniejąca pomocnicza baza danych. W grupach trybu failover dla wystąpienia zarządzanego są replikowane wszystkie bazy danych użytkowników. Nie można wybrać podzestawu baz danych użytkowników na potrzeby replikacji w grupie trybu failover.

- **Dodawanie baz danych w puli elastycznej do grupy trybu failover**

  Wszystkie lub kilka baz danych w puli elastycznej można umieścić w tej samej grupie trybu failover. Jeśli podstawowa baza danych znajduje się w puli elastycznej, zostanie ona automatycznie utworzona w puli elastycznej o tej samej nazwie (puli pomocniczej). Musisz się upewnić, że serwer pomocniczy zawiera pulę elastyczną o tej samej dokładnej nazwie i wystarczającej pojemności do hostowania pomocniczych baz danych, które zostaną utworzone przez grupę trybu failover. W przypadku dodania bazy danych w puli, która ma już pomocniczą bazę danych w puli pomocniczej, to łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, w dodatkowej puli zostanie utworzony nowy element pomocniczy.
  
- **Strefa DNS**

  Unikatowy identyfikator, który jest generowany automatycznie podczas tworzenia nowego wystąpienia. Obsługa certyfikatu wielodomenowego (SAN) dla tego wystąpienia jest obsługiwana w celu uwierzytelnienia połączeń klientów z dowolnym wystąpieniem w tej samej strefie DNS. Dwa wystąpienia zarządzane w tej samej grupie trybu failover muszą współdzielić strefę DNS. 
  
  > [!NOTE]
  > Identyfikator strefy DNS nie jest wymagany dla grup trybu failover utworzonych dla serwerów SQL Database.

- **Odbiornik odczytu i zapisu grupy trybu failover**

  Rekord CNAME systemu DNS wskazujący na adres URL bieżącego elementu podstawowego. Jest tworzony automatycznie podczas tworzenia grupy trybu failover i umożliwia obciążenia SQL do odczytu i zapisu, aby w sposób przezroczysty ponownie połączyć się z podstawową bazą danych podczas pracy w trybie failover. Po utworzeniu grupy trybu failover na serwerze SQL Database rekord CNAME DNS dla adresu URL odbiornika zostanie utworzony jako `<fog-name>.database.windows.net`. Po utworzeniu grupy trybu failover w wystąpieniu zarządzanym rekord CNAME DNS dla adresu URL odbiornika zostanie utworzony jako `<fog-name>.zone_id.database.windows.net`.

- **Odbiornik tylko do odczytu grupy trybu failover**

  Utworzony rekord CNAME systemu DNS wskazujący odbiornik tylko do odczytu wskazujący na adres URL pomocniczy. Jest tworzony automatycznie podczas tworzenia grupy trybu failover i zezwala na nieprzezroczyste połączenie z serwerem pomocniczym tylko do odczytu z usługą przy użyciu określonych reguł równoważenia obciążenia. Po utworzeniu grupy trybu failover na serwerze SQL Database rekord CNAME DNS dla adresu URL odbiornika zostanie utworzony jako `<fog-name>.secondary.database.windows.net`. Po utworzeniu grupy trybu failover w wystąpieniu zarządzanym rekord CNAME DNS dla adresu URL odbiornika zostanie utworzony jako `<fog-name>.zone_id.secondary.database.windows.net`.

- **Zasady automatycznego trybu failover**

  Domyślnie grupa trybu failover jest konfigurowana z użyciem zasad automatycznego trybu failover. Usługa SQL Database wyzwala tryb failover po wykryciu awarii i wygaśnięciu okresu prolongaty. System musi sprawdzić, czy nie można zmniejszyć przestoju dzięki wbudowanej [infrastrukturze wysokiej dostępności usługi SQL Database](sql-database-high-availability.md) ze względu na skalę wpływu. Jeśli chcesz kontrolować przepływ pracy trybu failover z poziomu aplikacji, możesz wyłączyć automatyczne przełączanie do trybu failover.
  
  > [!NOTE]
  > Ze względu na to, że weryfikacja skali przestoju i szybkość, z jaką można ją ograniczyć, wiąże się z działaniami ludzkimi przez zespół operacyjny, okres prolongaty nie może być ustawiony poniżej jednej godziny.  To ograniczenie ma zastosowanie do wszystkich baz danych w grupie trybu failover niezależnie od ich stanu synchronizacji danych. 

- **Zasady trybu failover tylko do odczytu**

  Domyślnie tryb failover odbiornika tylko do odczytu jest wyłączony. Gwarantuje to, że nie ma to wpływu na wydajność podstawowego, gdy pomocnicza jest w trybie offline. Oznacza to jednak również, że sesje tylko do odczytu nie będą mogły nawiązywać połączenia do momentu odzyskania pomocniczego. Jeśli nie można tolerować przestojów w przypadku sesji tylko do odczytu i są one prawidłowe do tymczasowego użycia podstawowego dla ruchu tylko do odczytu i zapisu do odczytu z kosztem potencjalnego obniżenia wydajności podstawowego, można włączyć tryb failover dla odbiornika tylko do odczytu Konfigurując Właściwość `AllowReadOnlyFailoverToPrimary`. W takim przypadku ruch tylko do odczytu zostanie automatycznie przekierowany do podstawowego, jeśli pomocnicza nie jest dostępna.

- **Planowana praca w trybie failover**

   Planowana praca w trybie failover wykonuje pełną synchronizację między podstawowymi a pomocniczymi bazami danych przed przełączeniem do roli podstawowej. Gwarantuje to brak utraty danych. Planowana praca w trybie failover jest używana w następujących scenariuszach:

  - Przeprowadzaj ćwiczenia odzyskiwania po awarii (DR) w środowisku produkcyjnym, gdy utrata danych nie jest akceptowalna
  - Przeniesienie baz danych do innego regionu
  - Zwróć bazy danych do regionu podstawowego po usunięciu awarii (powrót po awarii).

- **Nieplanowany tryb failover**

   Nieplanowane lub wymuszone przejście w tryb failover natychmiast przełącza pomocnicze do roli podstawowej bez synchronizacji z serwerem podstawowym. Ta operacja spowoduje utratę danych. Nieplanowana praca w trybie failover jest używana jako metoda odzyskiwania w trakcie awarii, gdy podstawowa jest niedostępna. Gdy pierwotny element podstawowy jest w trybie online, zostanie automatycznie ponownie nawiązać połączenie bez synchronizacji i stanie się nowym pomocniczym.

- **Ręczna praca awaryjna**

  Tryb failover można zainicjować ręcznie w dowolnym momencie, niezależnie od konfiguracji automatycznej pracy awaryjnej. Jeśli zasady automatycznej pracy awaryjnej nie są skonfigurowane, do odzyskania baz danych w grupie trybu failover jest wymagane ręczne przełączenie w tryb failover. Możesz inicjować wymuszone lub przyjazne przejście w tryb failover (z pełną synchronizacją danych). Ten drugi może służyć do przemieszczenie podstawowego do regionu pomocniczego. Po zakończeniu pracy w trybie failover rekordy DNS są automatycznie aktualizowane, aby zapewnić łączność z nowym podstawowym

- **Okres prolongaty z utratą danych**

  Ponieważ podstawowe i pomocnicze bazy danych są synchronizowane przy użyciu replikacji asynchronicznej, przełączenie w tryb failover może spowodować utratę danych. Można dostosować zasady automatycznego trybu failover w celu odzwierciedlenia tolerancji aplikacji do utraty danych. Konfigurując `GracePeriodWithDataLossHours`, można kontrolować czas oczekiwania systemu przed zainicjowaniem trybu failover, który prawdopodobnie spowoduje utratę danych.

- **Wiele grup trybu failover**

  Można skonfigurować wiele grup trybu failover dla tej samej pary serwerów, aby sterować skalą trybu failover. Każda grupa przejdzie w tryb failover niezależnie. Jeśli aplikacja wielodostępna korzysta z pul elastycznych, można użyć tej funkcji do mieszania podstawowych i pomocniczych baz danych w każdej puli. W ten sposób można zmniejszyć wpływ przestoju tylko na połowę dzierżawców.

  > [!NOTE]
  > Wystąpienie zarządzane nie obsługuje wielu grup trybu failover.
  
## <a name="permissions"></a>Uprawnienia
Uprawnienia dla grupy trybu failover są zarządzane za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md). Rola [współautor SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) ma wszystkie uprawnienia niezbędne do zarządzania grupami trybu failover. 

### <a name="create-failover-group"></a>Utwórz grupę trybu failover
Aby utworzyć grupę trybu failover, wymagany jest dostęp do zapisu RBAC zarówno do serwera podstawowego, jak i pomocniczego, oraz do wszystkich baz danych w grupie trybu failover. W przypadku wystąpienia zarządzanego wymagany jest dostęp do zapisu RBAC zarówno do podstawowego, jak i pomocniczego wystąpienia zarządzanego, ale uprawnienia do poszczególnych baz danych nie są istotne, ponieważ nie można dodać do grupy trybu failover ani usunąć z niej poszczególnych baz danych wystąpienia zarządzanego. 

### <a name="update-a-failover-group"></a>Aktualizowanie grupy trybu failover
Aby zaktualizować grupę trybu failover, wymagany jest dostęp do zapisu RBAC do grupy trybu failover oraz wszystkich baz danych na bieżącym serwerze podstawowym lub zarządzanym wystąpieniu.  

### <a name="failover-a-failover-group"></a>Przełączanie do trybu failover grupy trybu failover
Aby można było przełączyć grupę trybu failover, należy uzyskać dostęp do zapisu RBAC do grupy trybu failover na nowym serwerze podstawowym lub zarządzanym wystąpieniu. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Najlepsze rozwiązania dotyczące korzystania z grup trybu failover z pojedynczymi bazami danych i pulami elastycznymi

Grupa autotrybu failover musi być skonfigurowana na serwerze głównym SQL Database i będzie łączyć ją z serwerem pomocniczym SQL Database w innym regionie świadczenia usługi Azure. Grupy mogą obejmować wszystkie lub niektóre bazy danych na tych serwerach. Na poniższym diagramie przedstawiono typową konfigurację aplikacji w chmurze nadmiarowej geograficznie przy użyciu wielu baz danych i grupy automatycznej pracy awaryjnej.

![Tryb failover](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Aby zapoznać się z szczegółowym samouczkiem krok po kroku dodawania pojedynczej bazy danych do grupy trybu failover, zobacz [Dodawanie pojedynczej bazy danych do grupy trybu failover](sql-database-single-database-failover-group-tutorial.md) . 


Podczas projektowania usługi z zachowaniem ciągłości działania postępuj zgodnie z następującymi ogólnymi wskazówkami:

- **Korzystanie z jednej lub kilku grup trybu failover w celu zarządzania trybem failover wielu baz danych**

  Można utworzyć co najmniej jedną grupę trybu failover między dwoma serwerami w różnych regionach (serwery podstawowe i pomocnicze). Każda grupa może zawierać jedną lub kilka baz danych, które są odzyskiwane jako jednostki na wypadek, a niektóre podstawowe bazy danych staną się niedostępne z powodu awarii w regionie podstawowym. Grupa trybu failover tworzy geograficzną i pomocniczą bazę danych z tym samym celem usługi co podstawowy. W przypadku dodania istniejącej relacji replikacji geograficznej do grupy trybu failover upewnij się, że dla elementu podrzędnego jest skonfigurowana ta sama warstwa usługi i rozmiar obliczeń jako podstawowa.
  
  > [!IMPORTANT]
  > Tworzenie grup trybu failover między dwoma serwerami w różnych subskrypcjach nie jest obecnie obsługiwane w przypadku pojedynczych baz danych i pul elastycznych. Przeniesienie podstawowego lub pomocniczego serwera do innej subskrypcji po utworzeniu grupy trybu failover może spowodować błędy żądań trybu failover i innych operacji.

- **Używanie odbiornika do odczytu i zapisu w obciążeniu OLTP**

  Podczas wykonywania operacji OLTP Użyj `<fog-name>.database.windows.net` jako adresu URL serwera, a połączenia są automatycznie kierowane do podstawowego. Ten adres URL nie zmienia się po przełączeniu w tryb failover. Należy zauważyć, że tryb failover obejmuje aktualizację rekordu DNS, dlatego połączenia klientów są przekierowywane do nowego podstawowego tylko po odświeżeniu pamięci podręcznej DNS klienta.

- **Używanie odbiornika tylko do odczytu dla obciążenia przeznaczonego tylko do odczytu**

  Jeśli istnieje logicznie izolowane obciążenie przeznaczone tylko do odczytu, które jest odporne na określoną nieaktualność danych, możesz użyć pomocniczej bazy danych w aplikacji. W przypadku sesji tylko do odczytu Użyj `<fog-name>.secondary.database.windows.net` jako adresu URL serwera, a połączenie jest automatycznie przekierowywane do pomocniczego. Zaleca się również, aby wskazać w polu cel odczytu parametrów połączenia przy użyciu `ApplicationIntent=ReadOnly`. Jeśli chcesz mieć pewność, że obciążenie tylko do odczytu może być ponownie nawiązywane po przejściu w tryb failover lub jeśli serwer pomocniczy przejdzie w tryb offline, upewnij się, że skonfigurowano Właściwość `AllowReadOnlyFailoverToPrimary` zasad trybu failover. 

- **Przygotowanie do obniżenia wydajności**

  Decyzja o przejściu w tryb failover SQL jest niezależna od pozostałej części aplikacji lub innych używanych usług. Aplikacja może być "mieszana" z niektórymi składnikami w jednym regionie i innymi. Aby uniknąć obniżenia wydajności, należy upewnić się, że wdrożenie nadmiarowe aplikacji znajduje się w regionie odzyskiwania po awarii, a następnie przestrzegać tych [wytycznych dotyczących zabezpieczeń sieci](#failover-groups-and-network-security).

  > [!NOTE]
  > Aplikacja w regionie DR nie musi używać innych parametrów połączenia.  

- **Przygotowanie do utraty danych**

  Jeśli zostanie wykryta awaria, program SQL czeka na okres określony przez `GracePeriodWithDataLossHours`. Wartość domyślna to 1 godzina. Jeśli nie możesz zapewnić utraty danych, pamiętaj, aby ustawić `GracePeriodWithDataLossHours` na wystarczająco dużą liczbę, na przykład 24 godziny. Użyj ręcznej pracy awaryjnej grupy, aby wrócić z poziomu pomocniczego do podstawowego.

  > [!IMPORTANT]
  > Pule elastyczne z 800 lub mniej DTU i więcej niż 250 baz danych korzystających z replikacji geograficznej mogą napotkać problemy, w tym dłuższe planowane przełączanie w tryb failover i wydajność o obniżonej wydajności.  Te problemy mogą wystąpić w przypadku obciążeń intensywnie korzystających z zapisu, gdy punkty końcowe replikacji geograficznej są szeroko oddzielane przez geografię lub gdy dla każdej bazy danych są używane wiele pomocniczych punktów końcowych.  Objawy tych problemów są wskazywane, gdy opóźnienie replikacji geograficznej rośnie wraz z upływem czasu.  Te opóźnienia można monitorować przy użyciu wykazu [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Jeśli te problemy wystąpią, środki zaradcze obejmują zwiększenie liczby DTU puli lub zmniejszenie liczby replikowanych geograficznie baz danych w tej samej puli.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Najlepsze rozwiązania dotyczące korzystania z grup trybu failover z wystąpieniami zarządzanymi

Grupa autotrybu failover musi być skonfigurowana w wystąpieniu podstawowym i nawiązać połączenie z wystąpieniem pomocniczym w innym regionie świadczenia usługi Azure.  Wszystkie bazy danych w wystąpieniu zostaną zreplikowane do wystąpienia pomocniczego. 

Na poniższym diagramie przedstawiono typową konfigurację aplikacji w chmurze nadmiarowej geograficznie przy użyciu wystąpienia zarządzanego i grupy autotrybu failover.

![Tryb failover](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Zobacz [Dodawanie wystąpienia zarządzanego do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md) , aby uzyskać szczegółowy samouczek krok po kroku dodawania wystąpienia zarządzanego do korzystania z grupy trybu failover. 

Jeśli aplikacja używa wystąpienia zarządzanego jako warstwy danych, postępuj zgodnie z ogólnymi wskazówkami podczas projektowania pod kątem ciągłości działania:

- **Utwórz wystąpienie pomocnicze w tej samej strefie DNS co wystąpienie podstawowe**

  Aby zapewnić nieprzerwane połączenie z wystąpieniem podstawowym po przejściu w tryb failover, oba wystąpienia muszą znajdować się w tej samej strefie DNS. Gwarantuje to, że ten sam certyfikat wielodomenowy (SAN) może służyć do uwierzytelniania połączeń klientów z jednym z dwóch wystąpień w grupie trybu failover. Gdy aplikacja jest gotowa do wdrożenia produkcyjnego, Utwórz wystąpienie pomocnicze w innym regionie i upewnij się, że współużytkuje strefę DNS z wystąpieniem podstawowym. Można to zrobić, określając `DNS Zone Partner` opcjonalny parametr przy użyciu Azure Portal, programu PowerShell lub interfejsu API REST. 

> [!IMPORTANT]
> Pierwsze wystąpienie utworzone w podsieci określa strefę DNS dla wszystkich kolejnych wystąpień w tej samej podsieci. Oznacza to, że dwa wystąpienia z tej samej podsieci nie mogą należeć do różnych stref DNS.   

  Aby uzyskać więcej informacji o tworzeniu wystąpienia pomocniczego w tej samej strefie DNS co wystąpienie podstawowe, zobacz [Tworzenie pomocniczego wystąpienia zarządzanego](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Włącz ruch związany z replikacją między dwoma wystąpieniami**

  Ze względu na to, że każde wystąpienie jest izolowane w własnej sieci wirtualnej, należy zezwolić na ruch dwukierunkowy między tymi sieci wirtualnychami. Zobacz [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Tworzenie grupy trybu failover między wystąpieniami zarządzanymi w różnych subskrypcjach**

  Można utworzyć grupę trybu failover między wystąpieniami zarządzanymi w dwóch różnych subskrypcjach. Korzystając z interfejsu API programu PowerShell, można to zrobić, określając parametr `PartnerSubscriptionId` wystąpienia pomocniczego. W przypadku korzystania z interfejsu API REST każdy identyfikator wystąpienia uwzględniony w parametrze `properties.managedInstancePairs` może mieć własną wartość identyfikatora subskrypcji. 
  
  > [!IMPORTANT]
  > Witryna Azure Portal nie obsługuje grup trybu failover w różnych subskrypcjach.

  
- **Skonfiguruj grupę trybu failover, aby zarządzać trybem failover całego wystąpienia**

  Grupa trybu failover będzie zarządzać trybem failover wszystkich baz danych w wystąpieniu. Gdy grupa zostanie utworzona, każda baza danych w wystąpieniu zostanie automatycznie zreplikowana geograficznie do wystąpienia pomocniczego. Nie można użyć grup trybu failover w celu zainicjowania częściowej pracy awaryjnej podzestawu baz danych.

  > [!IMPORTANT]
  > Jeśli baza danych zostanie usunięta z wystąpienia podstawowego, zostanie ona również porzucona automatycznie w wystąpieniu pomocniczej lokalizacji geograficznej.

- **Używanie odbiornika do odczytu i zapisu w obciążeniu OLTP**

  Podczas wykonywania operacji OLTP Użyj `<fog-name>.zone_id.database.windows.net` jako adresu URL serwera, a połączenia są automatycznie kierowane do podstawowego. Ten adres URL nie zmienia się po przełączeniu w tryb failover. Tryb failover obejmuje aktualizację rekordu DNS, dlatego połączenia klienckie są przekierowywane do nowego serwera podstawowego dopiero po odświeżeniu pamięci podręcznej DNS klienta. Ponieważ wystąpienie pomocnicze współużytkuje strefę DNS z serwerem podstawowym, aplikacja kliencka będzie mogła ponownie połączyć się z nim przy użyciu tego samego certyfikatu sieci SAN.

- **Bezpośrednie łączenie z replikacją geograficzną na potrzeby zapytań tylko do odczytu**

  Jeśli istnieje logicznie izolowane obciążenie przeznaczone tylko do odczytu, które jest odporne na określoną nieaktualność danych, możesz użyć pomocniczej bazy danych w aplikacji. Aby połączyć się bezpośrednio z replikacją geograficzną, użyj `server.secondary.zone_id.database.windows.net` jako adresu URL serwera, a połączenie jest nawiązywane bezpośrednio do pomocniczej replikacji geograficznej.

  > [!NOTE]
  > W niektórych warstwach usług Azure SQL Database obsługuje korzystanie z [replik tylko do odczytu](sql-database-read-scale-out.md) w celu równoważenia obciążenia obciążeń zapytań tylko do odczytu przy użyciu pojemności jednej repliki tylko do odczytu i przy użyciu parametru `ApplicationIntent=ReadOnly` w parametrach połączenia. Jeśli skonfigurowano pomocniczą replikację geograficzną, można użyć tej funkcji do łączenia się z repliką tylko do odczytu w lokalizacji podstawowej lub w lokalizacji zreplikowanej geograficznie.
  > - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji podstawowej, użyj `<fog-name>.zone_id.database.windows.net`.
  > - Aby nawiązać połączenie z repliką tylko do odczytu w lokalizacji pomocniczej, użyj `<fog-name>.secondary.zone_id.database.windows.net`.

- **Przygotowanie do obniżenia wydajności**

  Decyzja o przejściu w tryb failover SQL jest niezależna od pozostałej części aplikacji lub innych używanych usług. Aplikacja może być "mieszana" z niektórymi składnikami w jednym regionie i innymi. Aby uniknąć obniżenia wydajności, należy upewnić się, że wdrożenie nadmiarowe aplikacji znajduje się w regionie odzyskiwania po awarii, a następnie przestrzegać tych [wytycznych dotyczących zabezpieczeń sieci](#failover-groups-and-network-security).

- **Przygotowanie do utraty danych**

  Jeśli zostanie wykryta awaria, program SQL automatycznie wyzwala tryb failover do odczytu i zapisu, jeśli nie ma utraty danych w najlepszej wiedzy. W przeciwnym razie czeka na okres określony przez `GracePeriodWithDataLossHours`. Jeśli określono `GracePeriodWithDataLossHours`, przygotuj się na utratę danych. Ogólnie rzecz biorąc, platforma Azure preferuje dostępność. Jeśli nie możesz zapewnić utraty danych, pamiętaj, aby ustawić GracePeriodWithDataLossHours na wystarczająco dużą liczbę, na przykład 24 godziny.

  Aktualizacja systemu DNS odbiornika do odczytu i zapisu stanie się natychmiast po zainicjowaniu trybu failover. Ta operacja nie powoduje utraty danych. Jednak proces przełączania ról bazy danych może potrwać do 5 minut w normalnych warunkach. Dopóki nie zostanie ukończona, niektóre bazy danych w nowym wystąpieniu podstawowym nadal będą tylko do odczytu. Jeśli tryb failover jest inicjowany przy użyciu programu PowerShell, cała operacja jest synchroniczna. Jeśli zostanie zainicjowany przy użyciu Azure Portal, interfejs użytkownika będzie wskazywać stan ukończenia. Jeśli zostanie zainicjowany przy użyciu interfejsu API REST, należy użyć mechanizmu sondowania standardowej Azure Resource Manager, aby monitorować ukończenie.

  > [!IMPORTANT]
  > Aby przenieść Primaries z powrotem do oryginalnej lokalizacji, Użyj ręcznego trybu failover grupy. Gdy awaria, która spowodowała przejście w tryb failover, jest zmniejszana, można przenieść podstawowe bazy danych do oryginalnej lokalizacji. W tym celu należy zainicjować ręczną pracę awaryjną grupy.

- **Potwierdź znane ograniczenia grup trybu failover**

  Zmiana nazwy i rozmiaru wystąpienia bazy danych nie jest obsługiwana w przypadku wystąpień w grupie trybu failover. Należy tymczasowo usunąć grupę trybu failover, aby móc przeprowadzić te akcje.

## <a name="failover-groups-and-network-security"></a>Grupy trybu failover i zabezpieczenia sieci

W przypadku niektórych aplikacji reguły zabezpieczeń wymagają, aby dostęp sieciowy do warstwy danych był ograniczony do określonego składnika lub składników, takich jak maszyna wirtualna, usługa sieci Web itd. To wymaganie wiąże się z pewnymi wyzwaniami związanymi z projektowaniem ciągłości biznesowej i użyciem grup trybu failover. Podczas wdrażania takiego ograniczonego dostępu należy wziąć pod uwagę następujące opcje.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Korzystanie z grup trybu failover i reguł sieci wirtualnej

Jeśli używasz [punktów końcowych usługi Virtual Network i reguł](sql-database-vnet-service-endpoint-rule-overview.md) , aby ograniczyć dostęp do bazy danych SQL, pamiętaj, że każdy punkt końcowy usługi Virtual Network ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci. W związku z tym tylko aplikacje klienckie wdrożone w tym samym regionie mogą łączyć się z podstawową bazą danych. Ponieważ w trybie failover wyniki sesji klienta SQL są przekierowywane do serwera w innym (pomocniczym) regionie, te sesje zakończą się niepowodzeniem, jeśli pochodzą z klienta spoza tego regionu. Z tego powodu nie można włączyć zasad automatycznych trybu failover, jeśli serwery uczestniczące zostaną uwzględnione w regułach Virtual Network. Aby zapewnić obsługę ręcznego przełączania do trybu failover, wykonaj następujące kroki:

1. Udostępnianie nadmiarowych kopii składników frontonu aplikacji (usługi sieci Web, maszyn wirtualnych itp.) w regionie pomocniczym
2. Skonfiguruj osobno [reguły sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) dla serwera głównego i pomocniczego
3. Włączanie [trybu failover frontonu przy użyciu konfiguracji usługi Traffic Manager](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Zainicjuj ręczną pracę awaryjną po wykryciu awarii. Ta opcja jest zoptymalizowana pod kątem aplikacji, które wymagają spójnego opóźnienia między frontonem a warstwą danych i obsługuje odzyskiwanie w przypadku, gdy awaria występuje zarówno na frontonie, na warstwie danych, jak i w obu tych przypadkach.

> [!NOTE]
> Jeśli używasz **odbiornika z tylko odczytem** , aby zrównoważyć obciążenie tylko do odczytu, upewnij się, że to obciążenie jest wykonywane na maszynie wirtualnej lub innym zasobie w regionie pomocniczym, dzięki czemu można nawiązać połączenie z pomocniczą bazą danych.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Korzystanie z grup trybu failover i reguł zapory usługi SQL Database

Jeśli plan ciągłości działalności biznesowej wymaga przejścia w tryb failover przy użyciu grup z automatyczną obsługą trybu failover, możesz ograniczyć dostęp do bazy danych SQL przy użyciu tradycyjnych reguł zapory.  Aby zapewnić obsługę automatycznego trybu failover, wykonaj następujące kroki:

1. [Tworzenie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Utwórz publiczny moduł równoważenia obciążenia](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) i przypisz do niego publiczny adres IP.
3. [Tworzenie sieci wirtualnej i maszyn wirtualnych](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) dla składników frontonu
4. [Utwórz sieciową grupę zabezpieczeń](../virtual-network/security-overview.md) i skonfiguruj połączenia przychodzące.
5. Upewnij się, że połączenia wychodzące są otwarte w usłudze Azure SQL Database przy użyciu [tagu usługi](../virtual-network/security-overview.md#service-tags)"SQL".
6. Utwórz [regułę zapory bazy danych SQL](sql-database-firewall-configure.md) , aby zezwolić na ruch przychodzący z publicznego adresu IP utworzonego w kroku 1.

Aby uzyskać więcej informacji na temat sposobu konfigurowania dostępu wychodzącego i adresu IP do użycia w regułach zapory, zobacz [połączenia wychodzące modułu równoważenia obciążenia](../load-balancer/load-balancer-outbound-connections.md).

Powyższa konfiguracja gwarantuje, że automatyczna praca awaryjna nie będzie blokować połączeń z składników frontonu i zakłada, że aplikacja może tolerować dłuższy czas oczekiwania między frontonem a warstwą danych.

> [!IMPORTANT]
> Aby zagwarantować ciągłość działania w regionie awarii, należy zapewnić geograficzną nadmiarowość dla składników frontonu i baz danych.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Włączanie replikacji geograficznej między wystąpieniami zarządzanymi i ich sieci wirtualnych

Po skonfigurowaniu grupy trybu failover między podstawowym i pomocniczym wystąpieniem zarządzanym w dwóch różnych regionach każde wystąpienie jest izolowane przy użyciu niezależnej sieci wirtualnej. Aby zezwolić na ruch związany z replikacją między tymi sieci wirtualnychami, upewnij się, że spełniono następujące wymagania wstępne:

1. Dwa zarządzane wystąpienia muszą znajdować się w różnych regionach świadczenia usługi Azure.
1. Dwa zarządzane wystąpienia muszą mieć tę samą warstwę usług i mieć ten sam rozmiar magazynu. 
1. Dodatkowe wystąpienie zarządzane musi być puste (bez baz danych użytkowników).
1. Sieci wirtualne używane przez zarządzane wystąpienia muszą być połączone za pomocą [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub Express Route. Gdy dwie sieci wirtualne nawiązują połączenie za poorednictwem sieci lokalnej, upewnij się, że nie ma portów blokowania reguły zapory 5022 i 11000-11999. Globalna komunikacja równorzędna sieci wirtualnych nie jest obsługiwana.
1. Dwa wystąpienia zarządzane sieci wirtualnych nie mogą mieć nakładających się adresów IP.
1. Należy skonfigurować sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń), takie jak porty 5022 i zakres 11000 ~ 12 000 są otwarte i wychodzące dla połączeń z innej podsieci zarządzanego wystąpienia. Jest to dozwolone dla ruchu replikacji między wystąpieniami

   > [!IMPORTANT]
   > Nieprawidłowo skonfigurowane reguły zabezpieczeń sieciowej grupy zabezpieczeń prowadzą do zablokowanych operacji kopiowania bazy danych.

7. Wystąpienie pomocnicze jest skonfigurowane z prawidłowym IDENTYFIKATORem strefy DNS. Strefa DNS jest właściwością wystąpienia zarządzanego, a jego identyfikator jest uwzględniony w adresie nazwy hosta. Identyfikator strefy jest generowany jako ciąg losowy, gdy pierwsze wystąpienie zarządzane jest tworzone w każdej sieci wirtualnej, a ten sam identyfikator jest przypisany do wszystkich innych wystąpień w tej samej podsieci. Po przypisaniu strefa DNS nie może być modyfikowana. Wystąpienia zarządzane zawarte w tej samej grupie trybu failover muszą współdzielić strefę DNS. W tym celu należy przekazać Identyfikator strefy wystąpienia podstawowego jako wartość parametru DnsZonePartner podczas tworzenia wystąpienia pomocniczego. 

   > [!NOTE]
   > Aby uzyskać szczegółowy samouczek dotyczący konfigurowania grup trybu failover z wystąpieniem zarządzanym, zobacz [Dodawanie wystąpienia zarządzanego do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uaktualnianie lub obniżanie poziomu podstawowej bazy danych

Możesz uaktualnić lub obniżyć podstawową bazę danych do innego rozmiaru obliczeniowego (w ramach tej samej warstwy usług, a nie między Ogólnego przeznaczenia i Krytyczne dla działania firmy) bez rozłączania pomocniczych baz danych. Podczas uaktualniania zalecamy najpierw uaktualnić wszystkie pomocnicze bazy danych, a następnie uaktualnić podstawową. W przypadku obniżenia poziomu należy odwrócić kolejność: najpierw Obniż poziom podstawowego, a następnie obniżyć wszystkie pomocnicze bazy danych. W przypadku uaktualnienia lub obniżenia poziomu bazy danych do innej warstwy usług to zalecenie jest wymuszane.

Ta sekwencja jest zalecana, aby uniknąć problemu polegającego na tym, że pomocnicza w mniejszej jednostce SKU jest przeciążona i należy ją ponownie umieścić w procesie uaktualnienia lub obniżenia poziomu. Można również uniknąć problemu, tworząc podstawowy tylko do odczytu, przy kosztach, które mają wpływ na wszystkie obciążenia odczytu i zapisu do podstawowego. 

> [!NOTE]
> W przypadku utworzenia pomocniczej bazy danych w ramach konfiguracji grupy trybu failover nie zaleca się obniżania poziomu pomocniczej bazy danych. Ma to na celu zapewnienie wystarczającej wydajności warstwy danych do przetwarzania zwykłego obciążenia po aktywowaniu trybu failover.

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie danych o kluczowym znaczeniu

Ze względu na duże opóźnienie sieci rozległej, ciągła kopia używa mechanizmu replikacji asynchronicznej. Replikacja asynchroniczna powoduje nieuniknięcie utraty danych w przypadku wystąpienia błędu. Jednak niektóre aplikacje mogą nie wymagać utraty danych. Aby chronić te aktualizacje krytyczne, Deweloper aplikacji może wywoływać procedurę systemu [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) natychmiast po zatwierdzeniu transakcji. Wywołanie `sp_wait_for_database_copy_sync` blokuje wątek wywołujący do momentu przekazania ostatniej zatwierdzonej transakcji do pomocniczej bazy danych. Jednak nie czeka na odtworzenie i zatwierdzenie wysłanych transakcji na serwerze pomocniczym. `sp_wait_for_database_copy_sync` jest objęty zakresem do określonego linku ciągłego kopiowania. Każdy użytkownik z uprawnieniami do nawiązywania połączenia z podstawową bazą danych może wywoływać tę procedurę.

> [!NOTE]
> `sp_wait_for_database_copy_sync` zapobiega utracie danych po przejściu w tryb failover, ale nie gwarantuje pełnej synchronizacji dostępu do odczytu. Opóźnienie spowodowane przez `sp_wait_for_database_copy_sync` wywołanie procedury może być istotne i zależy od rozmiaru dziennika transakcji w czasie wywołania.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupy trybu failover i przywracanie do punktu w czasie

Informacje o korzystaniu z funkcji przywracania do punktu w czasie z grupami trybu failover można znaleźć [w temacie odzyskiwanie do punktu w czasie (kopie)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Programowe zarządzanie grupami trybu failover

Jak wspomniano wcześniej, grupy autotrybu failover i aktywnej replikacji geograficznej mogą być również zarządzane programowo przy użyciu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń. Aktywna replikacja geograficzna obejmuje zestaw Azure Resource Manager interfejsów API do zarządzania, w tym [Azure SQL Database interfejsu API REST](https://docs.microsoft.com/rest/api/sql/) i [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Te interfejsy API wymagają użycia grup zasobów i obsługują zabezpieczenia oparte na rolach (RBAC). Aby uzyskać więcej informacji na temat implementowania ról dostępu, zobacz [Access Control oparte na rolach platformy Azure](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: zarządzanie trybem failover bazy danych SQL przy użyciu pojedynczych baz danych i pul elastycznych

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją na serwerze podstawowym i pomocniczym|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Usuwa grupę trybu failover z serwera i usuwa wszystkie pomocnicze bazy danych uwzględnione w grupie |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera konfigurację grupy trybu failover |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modyfikuje konfigurację grupy trybu failover |
| [Przełącznik-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Wyzwala tryb failover grupy trybu failover na serwerze pomocniczym |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Dodaje co najmniej jedną bazę danych do grupy trybu failover Azure SQL Database|
|  | |

> [!IMPORTANT]
> Przykładowy skrypt można znaleźć w temacie [Konfigurowanie i przełączanie w tryb failover grupy trybu failover dla pojedynczej bazy danych](scripts/sql-database-add-single-db-to-failover-group-powershell.md).
>

### <a name="powershell-managing-sql-database-failover-groups-with-managed-instances"></a>PowerShell: Zarządzanie grupami trybu failover bazy danych SQL z wystąpieniami zarządzanymi 

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją na serwerze podstawowym i pomocniczym|
| [Set-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modyfikuje konfigurację grupy trybu failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Pobiera konfigurację grupy trybu failover|
| [Przełącznik-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Wyzwala tryb failover grupy trybu failover na serwerze pomocniczym|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Usuwa grupę trybu failover|
|  | |

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>Interfejs API REST: Zarządzanie grupami trybu failover usługi SQL Database przy użyciu jednej i puli baz danych

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub Zaktualizuj grupę trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuń grupę trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Tryb failover (zaplanowany)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Przełączenie w tryb failover z bieżącego serwera podstawowego na ten serwer. |
| [Wymuś utratę danych w trybie failover](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails z bieżącego serwera podstawowego na ten serwer. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Pobiera grupę trybu failover. |
| [Wyświetl listę grup trybu failover według serwera](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Wyświetla listę grup trybu failover na serwerze. |
| [Aktualizuj grupę trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizuje grupę trybu failover. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>Interfejs API REST: Zarządzanie grupami trybu failover z wystąpieniami zarządzanymi

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub Zaktualizuj grupę trybu failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuń grupę trybu failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Tryb failover (zaplanowany)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Przełączenie w tryb failover z bieżącego serwera podstawowego na ten serwer. |
| [Wymuś utratę danych w trybie failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |ails z bieżącego serwera podstawowego na ten serwer. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Pobiera grupę trybu failover. |
| [Lista grup trybu failover — lista według lokalizacji](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Wyświetla listę grup trybu failover w lokalizacji. |

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z szczegółowymi samouczkami, zobacz
    - [Dodawanie pojedynczej bazy danych do grupy trybu failover](sql-database-single-database-failover-group-tutorial.md)
    - [Dodawanie puli elastycznej do grupy trybu failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Dodawanie wystąpienia zarządzanego do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md)
- Aby zapoznać się z przykładowymi skryptami, zobacz:
  - [Używanie programu PowerShell do konfigurowania aktywnej replikacji geograficznej dla pojedynczej bazy danych w Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Użyj programu PowerShell, aby skonfigurować aktywną replikację geograficzną dla bazy danych w puli w Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Dodawanie Azure SQL Database pojedynczej bazy danych do grupy trybu failover przy użyciu programu PowerShell](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [SQL Database zautomatyzowane kopie zapasowe](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych na potrzeby odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](sql-database-recovery-using-backups.md).
- Aby dowiedzieć się więcej o wymaganiach dotyczących uwierzytelniania dla nowego serwera podstawowego i bazy danych, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md).
