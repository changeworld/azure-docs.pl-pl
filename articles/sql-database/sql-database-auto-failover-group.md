---
title: Grupy trybu failover
description: Grupy automatycznego trybu failover to funkcja bazy danych SQL, która umożliwia zarządzanie replikacją i automatyczną/ skoordynowaną funkcją failover grupy baz danych na serwerze bazy danych SQL lub wszystkimi bazami danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269837"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Użyj grup automatycznego trybu failover, aby włączyć przezroczystą i skoordynowaną funkcję trybu failover wielu baz danych

Grupy automatycznego trybu failover to funkcja bazy danych SQL, która umożliwia zarządzanie replikacją i trybem failover grupy baz danych na serwerze bazy danych SQL lub wszystkich baz danych w wystąpieniu zarządzanym do innego regionu. Jest to abstrakcja deklaratywna na podstawie istniejącej aktywnej funkcji [replikacji geograficznej,](sql-database-active-geo-replication.md) zaprojektowana w celu uproszczenia wdrażania i zarządzania geograficznie replikowanymi bazami danych na dużą skalę. Można zainicjować tryb failover ręcznie lub można delegować go do usługi bazy danych SQL na podstawie zasad zdefiniowanych przez użytkownika. Ta ostatnia opcja umożliwia automatyczne odzyskiwanie wielu powiązanych baz danych w regionie pomocniczym po katastrofalnym niepowodzeniu lub innym nieplanowanym zdarzeniu, które powoduje pełną lub częściową utratę dostępności usługi bazy danych SQL w regionie podstawowym. Grupa trybu failover może zawierać jedną lub wiele baz danych, zwykle używane przez tę samą aplikację. Ponadto można użyć czytelnych pomocniczych baz danych, aby odciążyć obciążenia kwerend tylko do odczytu. Ponieważ grupy automatycznego trybu failover obejmują wiele baz danych, te bazy danych muszą być skonfigurowane na serwerze podstawowym. Grupy auto-failover obsługują replikację wszystkich baz danych w grupie tylko na jeden serwer pomocniczy w innym regionie.

> [!NOTE]
> Podczas pracy z pojedynczymi lub buforowanymi bazami danych na serwerze bazy danych SQL i wielu pomocniczych w tych samych lub różnych regionach należy użyć [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md). 

W przypadku korzystania z grup automatycznego trybu failover z zasadami automatycznego trybu failover każda awaria, która ma wpływ na jedną lub kilka baz danych w grupie, powoduje automatyczne tryb failover. Zazwyczaj są to zdarzenia, które nie mogą być samołagodzone przez wbudowane automatyczne operacje wysokiej dostępności. Przykłady wyzwalaczy pracy awaryjnej obejmują zdarzenie spowodowane przez pierścień dzierżawy SQL lub pierścień sterujący jest w dół z powodu wycieku pamięci jądra systemu operacyjnego na kilku węzłach obliczeniowych lub zdarzenia spowodowanego przez jeden lub więcej pierścieni dzierżawy jest w dół, ponieważ niewłaściwy kabel sieciowy został przecięty podczas rutynowej likwidacji sprzętu.  Aby uzyskać więcej informacji, zobacz [Wysoka dostępność bazy danych SQL](sql-database-high-availability.md).

Ponadto grupy automatycznego trybu failover zapewniają punkty końcowe odbiornika do odczytu i odbiornika tylko do odczytu, które pozostają niezmienione podczas pracy awaryjnej. Niezależnie od tego, czy używasz ręcznej, czy automatycznej aktywacji trybu failover, praca awaryjna przełącza wszystkie pomocnicze bazy danych w grupie na podstawowe. Po zakończeniu pracy awaryjnej bazy danych rekord DNS jest automatycznie aktualizowany w celu przekierowania punktów końcowych do nowego regionu. Aby zapoznać się z danymi określonego celu celowego i rto, zobacz [Omówienie ciągłości działania](sql-database-business-continuity.md).

W przypadku korzystania z grup automatycznego trybu failover z zasadami automatycznego trybu failover każda awaria, która ma wpływ na bazy danych na serwerze bazy danych SQL lub wystąpieniu zarządzanym, powoduje automatyczne tryb failover. Grupę automatycznego trybu failover można zarządzać za pomocą:

- [Portal Azure](sql-database-implement-geo-distributed-database.md)
- [Interfejsu wiersza polecenia platformy Azure: grupa trybu failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: grupa trybu failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [INTERFEJS API REST: grupa trybu failover](/rest/api/sql/failovergroups).

Po przewijaniu awaryjnego upewnij się, że wymagania uwierzytelniania dla serwera i bazy danych są skonfigurowane na nowym podstawowym. Aby uzyskać szczegółowe informacje, zobacz [Zabezpieczenia bazy danych SQL po porodzie](sql-database-geo-replication-security-config.md).

Aby osiągnąć rzeczywistą ciągłość działania, dodawanie nadmiarowości bazy danych między centrami danych jest tylko częścią rozwiązania. Odzyskiwanie aplikacji (usługi) end-to-end po katastrofalnej awarii wymaga odzyskania wszystkich składników, które stanowią usługi i wszelkie usługi zależne. Przykładami tych składników są oprogramowanie klienckie (na przykład przeglądarka z niestandardowym javascriptem), frontonów internetowych, magazynu i DNS. Ważne jest, aby wszystkie składniki są odporne na te same błędy i stają się dostępne w ramach celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkie usługi zależne i zrozumieć gwarancje i możliwości, które zapewniają. Następnie należy podjąć odpowiednie kroki, aby upewnić się, że usługa działa podczas pracy awaryjnej usług, od których zależy. Aby uzyskać więcej informacji na temat projektowania rozwiązań do odzyskiwania po awarii, zobacz [Projektowanie rozwiązań w chmurze dla odzyskiwania po awarii przy użyciu aktywnej replikacji geograficznej](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Terminologia i możliwości grupy automatycznego trybu failover

- **Grupa trybu failover (MGŁA)**

  Grupa trybu failover to nazwana grupa baz danych zarządzanych przez pojedynczy serwer bazy danych SQL lub w ramach jednego wystąpienia zarządzanego, które może zostać przejęte awaryjnie jako jednostka do innego regionu w przypadku, gdy wszystkie lub niektóre podstawowe bazy danych staną się niedostępne z powodu awarii w regionie podstawowym. Po utworzeniu dla wystąpień zarządzanych grupa trybu failover zawiera wszystkie bazy danych użytkowników w wystąpieniu i dlatego tylko jedna grupa trybu failover może być skonfigurowana w wystąpieniu.
  
  > [!IMPORTANT]
  > Nazwa grupy trybu failover musi być `.database.windows.net` unikatowa globalnie w domenie.

- **Serwery usługi SQL Database**

     Za pomocą serwerów bazy danych SQL niektóre lub wszystkie bazy danych użytkowników na jednym serwerze bazy danych SQL mogą być umieszczane w grupie trybu failover. Ponadto serwer bazy danych SQL obsługuje wiele grup trybu failover na jednym serwerze bazy danych SQL.

- **Podstawowy**

  Serwer bazy danych SQL lub wystąpienie zarządzane, w którym znajdują się podstawowe bazy danych w grupie trybu failover.

- **Pomocniczy**

  Serwer bazy danych SQL lub wystąpienie zarządzane, w którym znajdują się pomocnicze bazy danych w grupie trybu failover. Pomocniczy nie może znajdować się w tym samym regionie co podstawowy.

- **Dodawanie pojedynczych baz danych do grupy trybu failover**

  Można umieścić kilka pojedynczych baz danych na tym samym serwerze bazy danych SQL do tej samej grupy trybu failover. Jeśli dodasz pojedynczą bazę danych do grupy trybu failover, automatycznie utworzy pomocniczą bazę danych przy użyciu tej samej wersji i rozmiaru obliczeń na serwerze pomocniczym.  Określono ten serwer podczas tworzenia grupy trybu failover. Jeśli dodasz bazę danych, która ma już pomocniczą bazę danych na serwerze pomocniczym, to łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, nowy pomocniczy jest tworzony na serwerze pomocniczym.

  > [!IMPORTANT]
  > Upewnij się, że serwer pomocniczy nie ma bazy danych o tej samej nazwie, chyba że jest istniejącą pomocniczą bazą danych. W grupach trybu failover dla wystąpienia zarządzanego wszystkie bazy danych użytkowników są replikowane. Nie można wybrać podzbioru baz danych użytkowników do replikacji w grupie trybu failover.

- **Dodawanie baz danych w puli elastycznej do grupy trybu failover**

  Można umieścić wszystkie lub kilka baz danych w puli elastycznej do tej samej grupy trybu failover. Jeśli podstawowa baza danych znajduje się w puli elastycznej, pomocniczy jest automatycznie tworzony w puli elastycznej o tej samej nazwie (puli pomocniczej). Należy upewnić się, że serwer pomocniczy zawiera pulę elastyczną o tej samej dokładnej nazwie i wystarczającej pojemności wolnej do obsługi pomocniczych baz danych, które zostaną utworzone przez grupę trybu failover. Jeśli dodasz bazę danych w puli, która ma już pomocniczą bazę danych w puli pomocniczej, to łącze replikacji geograficznej jest dziedziczone przez grupę. Po dodaniu bazy danych, która ma już pomocniczą bazę danych na serwerze, który nie jest częścią grupy trybu failover, nowy pomocniczy jest tworzony w puli pomocniczej.
  
- **Wstępne rozmieszczenie** 

  Podczas dodawania baz danych, pul elastycznych lub wystąpień zarządzanych do grupy trybu failover, istnieje początkowa faza rozmieszczenia przed rozpoczęciem replikacji danych. Początkowa faza wysiewu jest najdłuższą i najdroższą operacją. Po zakończeniu początkowego rozmieszczania dane są synchronizowane, a następnie replikowane są tylko kolejne zmiany danych. Czas potrzebny na ukończenie początkowego materiału siewnego zależy od rozmiaru danych, liczby replikowanych baz danych i szybkości łącza między jednostkami w grupie trybu failover. W normalnych warunkach typowa szybkość wysiewu wynosi 50-500 GB na godzinę dla pojedynczej bazy danych lub puli elastycznej i 18-35 GB na godzinę dla wystąpienia zarządzanego. Rozmieszczanie jest wykonywane dla wszystkich baz danych równolegle. Można użyć podanej szybkości wysiewu, wraz z liczbą baz danych i całkowitym rozmiarem danych, aby oszacować, jak długo potrwa początkowa faza wysiewu przed rozpoczęciem replikacji danych.

  W przypadku wystąpień zarządzanych szybkość łącza Trasy ekspresowej między dwoma wystąpieniami również musi być brana pod uwagę przy szacowaniu czasu początkowej fazy wysiewu. Jeśli szybkość połączenia między dwoma wystąpieniami jest mniejsza niż to, co jest konieczne, czas do materiału siewnego jest prawdopodobnie szczególnie wpływ. Można użyć podanej szybkości wysiewu, liczby baz danych, całkowitego rozmiaru danych i szybkości łącza, aby oszacować, jak długo potrwa początkowa faza wysiewu przed rozpoczęciem replikacji danych. Na przykład dla pojedynczej bazy danych 100 GB początkowej fazy inicjatora zajmie wszędzie od 2,8 do 5,5 godziny, jeśli łącze jest w stanie wypychania 35 GB na godzinę. Jeśli łącze można przenieść tylko 10 GB na godzinę, a następnie wysiewu bazy danych 100 GB zajmie około 10 godzin. Jeśli istnieje wiele baz danych do replikacji, wysiew będzie wykonywany równolegle, a w połączeniu z powolną szybkością łącza początkowa faza wysiewu może trwać znacznie dłużej, zwłaszcza jeśli równoległe wysiewanie danych ze wszystkich baz danych przekracza dostępne przepustowości łącza. Jeśli przepustowość sieci między dwoma wystąpieniami jest ograniczona i dodajesz wiele wystąpień zarządzanych do grupy trybu failover, należy rozważyć dodanie wielu wystąpień zarządzanych do grupy trybu failover kolejno, jeden po drugim.

  
- **Strefa DNS**

  Unikatowy identyfikator, który jest generowany automatycznie podczas tworzenia nowego wystąpienia. Certyfikat san (multi-domena) dla tego wystąpienia jest aprowizowane w celu uwierzytelnienia połączeń klienta do dowolnego wystąpienia w tej samej strefie DNS. Dwa wystąpienia zarządzane w tej samej grupie trybu failover muszą współużytkuje strefę DNS.
  
  > [!NOTE]
  > Identyfikator strefy DNS nie jest wymagany dla grup trybu failover utworzonych dla serwerów bazy danych SQL.

- **Detektor odczytu i zapisu grupy trybu failover**

  Rekord CNAME DNS, który wskazuje adres URL bieżącej podstawowej. Jest tworzony automatycznie po utworzeniu grupy trybu failover i umożliwia do odczytu i zapisu obciążenia SQL w sposób przejrzysty ponownie połączyć się z podstawowej bazy danych, gdy podstawowy zmienia się po pracy awaryjnej. Po utworzeniu grupy trybu failover na serwerze bazy danych SQL rekord CNAME DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.database.windows.net`. Po utworzeniu grupy trybu failover w wystąpieniu zarządzanym rekord CNAME `<fog-name>.zone_id.database.windows.net`DNS dla adresu URL odbiornika jest tworzony jako .

- **Detektor tylko do odczytu grupy trybu failover**

  Utworzony rekord CNAME DNS, który wskazuje odbiornik tylko do odczytu, który wskazuje adres URL pomocniczego. Jest tworzony automatycznie podczas tworzenia grupy trybu failover i umożliwia tylko do odczytu obciążenia SQL w sposób przejrzysty połączyć się z pomocniczym przy użyciu określonych reguł równoważenia obciążenia. Po utworzeniu grupy trybu failover na serwerze bazy danych SQL rekord CNAME DNS dla adresu URL odbiornika jest tworzony jako `<fog-name>.secondary.database.windows.net`. Po utworzeniu grupy trybu failover w wystąpieniu zarządzanym rekord CNAME `<fog-name>.zone_id.secondary.database.windows.net`DNS dla adresu URL odbiornika jest tworzony jako .

- **Zasady automatycznego trybu failover**

  Domyślnie grupa trybu failover jest skonfigurowana z zasadą automatycznego trybu failover. Usługa bazy danych SQL wyzwala pracy awaryjnej po wykryciu błędu i okres prolongaty wygasł. System musi sprawdzić, czy awaria nie może być złagodzone przez wbudowaną [infrastrukturę wysokiej dostępności usługi bazy danych SQL](sql-database-high-availability.md) ze względu na skalę wpływu. Jeśli chcesz kontrolować przepływ pracy pracy pracy trybu failover z aplikacji, można wyłączyć automatyczne tryb failover.
  
  > [!NOTE]
  > Ponieważ weryfikacja skali awarii i jak szybko można go złagodzić, wiąże się z działaniami człowieka przez zespół operacyjny, nie można ustawić okresu prolongaty poniżej jednej godziny.  To ograniczenie dotyczy wszystkich baz danych w grupie trybu failover, niezależnie od ich stanu synchronizacji danych. 

- **Zasady trybu failover tylko do odczytu**

  Domyślnie tryb failover odbiornika tylko do odczytu jest wyłączona. Zapewnia, że wydajność podstawowej nie ma wpływu, gdy pomocniczy jest w trybie offline. Jednak oznacza to również, że sesje tylko do odczytu nie będą mogły się połączyć, dopóki nie zostanie odzyskana pomocnicza. Jeśli nie można tolerować przestojów dla sesji tylko do odczytu i są OK tymczasowo używać podstawowego dla ruchu tylko do odczytu i zapisu kosztem potencjalnego pogorszenia `AllowReadOnlyFailoverToPrimary` wydajności podstawowego, można włączyć pracę awaryjną dla odbiornika tylko do odczytu, konfigurując właściwość. W takim przypadku ruch tylko do odczytu zostanie automatycznie przekierowany do podstawowego, jeśli pomocniczy nie jest dostępny.

- **Planowane przemija awaryjne**

   Planowana praca awaryjna wykonuje pełną synchronizację między bazami danych pierwotnych i pomocniczych przed wtórnymi przełącznikami do roli podstawowej. Gwarantuje to brak utraty danych. Planowana praca awaryjna jest używana w następujących scenariuszach:

  - Wykonywanie ćwiczeń odzyskiwania po awarii (DR) w produkcji, gdy utrata danych jest nie dopuszczalna
  - Przenoszenie baz danych do innego regionu
  - Zwraca bazy danych do regionu podstawowego po awarii został złagodzony (powrót po awarii).

- **Nieplanowany tryb failover**

   Nieplanowane lub wymuszone tryb failover natychmiast przełącza pomocniczą rolę podstawową bez synchronizacji z podstawową. Ta operacja spowoduje utratę danych. Nieplanowana opcja trybu failover jest używana jako metoda odzyskiwania podczas awarii, gdy podstawowy nie jest dostępny. Gdy oryginalny podstawowy jest z powrotem w trybie online, automatycznie połączy się ponownie bez synchronizacji i stanie się nowym drugorzędnym.

- **Ręczne tryb failover**

  Tryb failover można zainicjować ręcznie w dowolnym momencie, niezależnie od konfiguracji automatycznego trybu failover. Jeśli zasady automatycznego trybu failover nie są skonfigurowane, ręczna praca awaryjna jest wymagana do odzyskania baz danych w grupie trybu failover do pomocniczego. Można zainicjować wymuszoną lub przyjazną przełęcz w trybie failover (z pełną synchronizacją danych). Ten ostatni może być używany do przeniesienia pierwotnego do regionu pomocniczego. Po zakończeniu pracy awaryjnej rekordy DNS są automatycznie aktualizowane w celu zapewnienia łączności z nowym

- **Okres prolongaty z utratą danych**

  Ponieważ podstawowe i pomocnicze bazy danych są synchronizowane przy użyciu replikacji asynchroniiowej, przewijanie awaryjne może spowodować utratę danych. Zasady automatycznego trybu failover można dostosować, aby odzwierciedlić tolerancję aplikacji na utratę danych. Konfigurując `GracePeriodWithDataLossHours`program , można kontrolować, jak długo system czeka przed rozpoczęciem pracy awaryjnej, która może spowodować utratę danych.

- **Wiele grup trybu failover**

  Można skonfigurować wiele grup trybu failover dla tej samej pary serwerów, aby kontrolować skalę pracy awaryjnej. Każda grupa działa niezależnie. Jeśli aplikacja z wieloma dzierżawami używa pul elastycznych, można użyć tej możliwości do mieszania podstawowych i pomocniczych baz danych w każdej puli. W ten sposób można zmniejszyć wpływ awarii tylko do połowy dzierżawców.

  > [!NOTE]
  > Wystąpienie zarządzane nie obsługuje wielu grup trybu failover.
  
## <a name="permissions"></a>Uprawnienia

Uprawnieniami dla grupy trybu failover zarządza się za pomocą [kontroli dostępu opartej na rolach (RBAC).](../role-based-access-control/overview.md) Rola [współautora programu SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) ma wszystkie uprawnienia niezbędne do zarządzania grupami trybu failover.

### <a name="create-failover-group"></a>Tworzenie grupy trybu failover

Aby utworzyć grupę trybu failover, potrzebujesz dostępu do zapisu RBAC zarówno do serwerów podstawowych, jak i pomocniczych oraz do wszystkich baz danych w grupie trybu failover. W przypadku wystąpienia zarządzanego potrzebny jest dostęp do zapisu RBAC zarówno do podstawowego, jak i pomocniczego wystąpienia zarządzanego, ale uprawnienia do poszczególnych baz danych nie są istotne, ponieważ nie można dodać do grupy trybu failover ani usunąć z niej poszczególnych baz danych zarządzanych. 

### <a name="update-a-failover-group"></a>Aktualizowanie grupy trybu failover

Aby zaktualizować grupę trybu failover, potrzebujesz dostępu do zapisu RBAC do grupy trybu failover i wszystkich baz danych na bieżącym serwerze podstawowym lub wystąpieniu zarządzanym.  

### <a name="failover-a-failover-group"></a>Praca awaryjna grupy trybu failover

Aby wykonać pracę awaryjną grupy trybu failover, potrzebujesz dostępu do zapisu RBAC do grupy trybu failover na nowym serwerze podstawowym lub wystąpieniu zarządzanym.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Najważniejsze wskazówki dotyczące używania grup trybu failover z pojedynczymi bazami danych i pulami elastycznymi

Grupa automatycznego trybu failover musi być skonfigurowana na podstawowym serwerze bazy danych SQL i połączy ją z pomocniczym serwerem bazy danych SQL w innym regionie platformy Azure. Grupy mogą zawierać wszystkie lub niektóre bazy danych na tych serwerach. Na poniższym diagramie przedstawiono typową konfigurację aplikacji w chmurze geograficznie nadmiarowej przy użyciu wielu baz danych i grupy automatycznego trybu failover.

![automatyczna funkcja pracy awaryjnej](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Zobacz [Dodawanie pojedynczej bazy danych do grupy trybu failover, aby](sql-database-single-database-failover-group-tutorial.md) uzyskać szczegółowy samouczek krok po kroku, dodając jedną bazę danych do grupy trybu failover.

Projektując usługę z myślą o ciągłości działania, postępuj zgodnie z ogólnymi wytycznymi:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Używanie jednej lub kilku grup trybu failover do zarządzania funkcją failover wielu baz danych

Jedną lub wiele grup trybu failover można utworzyć między dwoma serwerami w różnych regionach (serwery podstawowe i pomocnicze). Każda grupa może zawierać jedną lub kilka baz danych, które są odzyskiwane jako jednostka w przypadku, gdy wszystkie lub niektóre podstawowe bazy danych stają się niedostępne z powodu awarii w regionie podstawowym. Grupa trybu failover tworzy pomocniczą bazę danych geo pomocniczą z tym samym celem usługi co podstawowy. Jeśli dodasz istniejącą relację replikacji geograficznej do grupy trybu failover, upewnij się, że geo-pomocniczy jest skonfigurowany z tą samą warstwą usług i rozmiarem obliczeń co podstawowy.
  
> [!IMPORTANT]
> Tworzenie grup trybu failover między dwoma serwerami w różnych subskrypcjach nie jest obecnie obsługiwane dla pojedynczych baz danych i pul elastycznych. Jeśli przeniesiesz serwer podstawowy lub pomocniczy do innej subskrypcji po utworzeniu grupy trybu failover, może to spowodować błędy żądań trybu failover i innych operacji.

### <a name="using-read-write-listener-for-oltp-workload"></a>Korzystanie z odbiornika odczytu i zapisu dla obciążenia OLTP

Podczas wykonywania operacji OLTP należy używać `<fog-name>.database.windows.net` jako adres URL serwera, a połączenia są automatycznie kierowane do podstawowego. Ten adres URL nie zmienia się po przemijaniu awaryjnym. Należy zauważyć, że tryb failover obejmuje aktualizowanie rekordu DNS, dzięki czemu połączenia klienta są przekierowywane do nowej podstawowej tylko po odświeżeniu pamięci podręcznej DNS klienta.

### <a name="using-read-only-listener-for-read-only-workload"></a>Używanie odbiornika tylko do odczytu dla obciążenia tylko do odczytu

Jeśli masz logicznie izolowane obciążenie tylko do odczytu, które jest tolerancyjne dla pewnego nieaktualnego nieaktualności danych, można użyć pomocniczej bazy danych w aplikacji. W przypadku sesji tylko `<fog-name>.secondary.database.windows.net` do odczytu użyj jako adresu URL serwera, a połączenie jest automatycznie kierowane do pomocniczego. Zaleca się również wskazanie w ciągu połączenia `ApplicationIntent=ReadOnly`opcji odczytu za pomocą programu . Jeśli chcesz upewnić się, że obciążenie tylko do odczytu można połączyć ponownie po pracy `AllowReadOnlyFailoverToPrimary` awaryjnej lub w przypadku, gdy serwer pomocniczy przejdzie w tryb offline, upewnij się, aby skonfigurować właściwość zasad trybu failover.

### <a name="preparing-for-performance-degradation"></a>Przygotowanie do obniżenia wydajności

Typowa aplikacja platformy Azure używa wielu usług platformy Azure i składa się z wielu składników. Automatyczna praca awaryjna grupy trybu failover jest wyzwalana na podstawie stanu samych składników SQL platformy Azure. Awaria może nie dotyczyć innych usług platformy Azure w regionie podstawowym, a ich składniki mogą być nadal dostępne w tym regionie. Po przełączeniu podstawowych baz danych do regionu odzyskiwania po awarii może wzrosnąć opóźnienie między składnikami zależnymi. Aby uniknąć wpływu większych opóźnień na wydajność aplikacji, należy zapewnić nadmiarowość wszystkich składników aplikacji w regionie odzyskiwania po awarii i postępować zgodnie z tymi [wytycznymi dotyczącymi zabezpieczeń sieci.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Przygotowanie do utraty danych

Jeśli zostanie wykryta awaria, sql czeka na `GracePeriodWithDataLossHours`okres określony przez program . Wartość domyślna to 1 godzina. Jeśli nie możesz sobie pozwolić `GracePeriodWithDataLossHours` na utratę danych, upewnij się, że ustawiono wystarczająco dużą liczbę, na przykład 24 godziny. Użyj ręcznej grupy pracy awaryjnej, aby przywrócić po awarii z pomocniczego do podstawowego.

> [!IMPORTANT]
> Pule elastyczne z 800 lub mniej DTU i ponad 250 baz danych przy użyciu replikacji geograficznej mogą wystąpić problemy, w tym już planowane praca awaryjna i wydajność obniżona.  Te problemy są bardziej prawdopodobne w przypadku obciążeń intensywnie korzystających z zapisu, gdy punkty końcowe replikacji geograficznej są szeroko oddzielone przez geografię lub gdy dla każdej bazy danych jest używanych wiele pomocniczych punktów końcowych.  Objawy tych problemów są wskazane, gdy opóźnienie replikacji geograficznej zwiększa się wraz z czasem.  To opóźnienie można monitorować za pomocą [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Jeśli wystąpią te problemy, a następnie środki zaradcze obejmują zwiększenie liczby dtuli puli lub zmniejszenie liczby replikowanych geograficznie baz danych w tej samej puli.

### <a name="changing-secondary-region-of-the-failover-group"></a>Zmienianie pomocniczego regionu grupy trybu failover

Aby zilustrować sekwencję zmian, zakładamy, że serwer A jest serwerem podstawowym, serwer B jest istniejącym serwerem pomocniczym, a serwer C jest nowym pomocniczym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1.  Tworzenie dodatkowych pomocniczych danych z każdej bazy danych na serwerze A do serwera C przy użyciu [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md). Każda baza danych na serwerze A będzie miała dwa pomocnicze pliki, jeden na serwerze B i jeden na serwerze C. Zagwarantuje to, że podstawowe bazy danych pozostają chronione podczas przejścia.
2.  Usuń grupę trybu failover. W tym momencie logowania będą nie. Dzieje się tak, ponieważ aliasy SQL dla detektorów grupy trybu failover zostały usunięte, a brama nie rozpozna nazwy grupy trybu failover.
3.  Ponownie utwórz grupę trybu failover o tej samej nazwie między serwerami A i C. W tym momencie logowania przestaną się niepowodzeniem.
4.  Dodaj wszystkie podstawowe bazy danych na serwerze A do nowej grupy trybu failover.
5.  Serwer upuszczania B. Wszystkie bazy danych na B zostaną usunięte automatycznie. 


### <a name="changing-primary-region-of-the-failover-group"></a>Zmienianie regionu podstawowego grupy trybu failover

Aby zilustrować sekwencję zmian, zakładamy, że serwer A jest serwerem podstawowym, serwer B jest istniejącym serwerem pomocniczym, a serwer C jest nowym podstawowym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1.  Wykonaj planowaną funkcję przełączania awaryjnego, aby przełączyć serwer podstawowy na B. Serwer A stanie się nowym serwerem pomocniczym. Przewija się w stan failover może spowodować kilka minut przestoju. Rzeczywisty czas zależy od rozmiaru grupy trybu failover.
2.  Tworzenie dodatkowych pomocniczych danych z każdej bazy danych na serwerze B do serwera C przy użyciu [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md). Każda baza danych na serwerze B będzie miała dwa pomocnicze pliki, jeden na serwerze A i jeden na serwerze C. Zagwarantuje to, że podstawowe bazy danych pozostają chronione podczas przejścia.
3.  Usuń grupę trybu failover. W tym momencie logowania będą nie. Dzieje się tak, ponieważ aliasy SQL dla detektorów grupy trybu failover zostały usunięte, a brama nie rozpozna nazwy grupy trybu failover.
4.  Ponownie utwórz grupę trybu failover o tej samej nazwie między serwerami A i C. W tym momencie logowania przestaną się niepowodzeniem.
5.  Dodaj wszystkie podstawowe bazy danych w b do nowej grupy trybu failover. 
6.  Wykonaj planowaną przełączenie awaryjne grupy trybu failover, aby przełączyć B i C. Teraz serwer C stanie się podstawowym i B - pomocniczym. Wszystkie pomocnicze bazy danych na serwerze A zostaną automatycznie połączone z prawyborami na C. Podobnie jak w kroku 1, przewijanie awaryjne może spowodować kilka minut przestoju.
6.  Upuść serwer A. Wszystkie bazy danych na A zostaną usunięte automatycznie.

> [!IMPORTANT]
> Po usunięciu grupy trybu failover rekordy DNS dla punktów końcowych odbiornika są również usuwane. W tym momencie istnieje niezerowe prawdopodobieństwo, że ktoś inny stworzy grupę trybu failover lub alias serwera o tej samej nazwie, co uniemożliwi ponowne użycie go. Aby zminimalizować ryzyko, nie należy używać ogólnych nazw grup trybu failover.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Najważniejsze wskazówki dotyczące używania grup trybu failover z wystąpieniami zarządzanymi

Grupa automatycznego trybu failover musi być skonfigurowana w wystąpieniu podstawowym i połączy ją z wystąpieniem pomocniczym w innym regionie platformy Azure.  Wszystkie bazy danych w wystąpieniu będą replikowane do wystąpienia pomocniczego.

Na poniższym diagramie przedstawiono typową konfigurację aplikacji chmury geograficznie nadmiarowej przy użyciu wystąpienia zarządzanego i grupy automatycznego trybu failover.

![automatyczna funkcja pracy awaryjnej](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Zobacz [Dodawanie wystąpienia zarządzanego do grupy trybu failover, aby](sql-database-managed-instance-failover-group-tutorial.md) uzyskać szczegółowy samouczek krok po kroku, dodając wystąpienie zarządzane do użycia grupy trybu failover.

Jeśli aplikacja używa wystąpienia zarządzanego jako warstwy danych, postępuj zgodnie z tymi ogólnymi wytycznymi podczas projektowania dla ciągłości biznesowej:

### <a name="creating-the-secondary-instance"></a>Tworzenie wystąpienia pomocniczego 

Aby zapewnić łączność bez przerwy z wystąpieniem podstawowym po przełączeniu w tryb failover zarówno wystąpienia podstawowego, jak i pomocniczego muszą znajdować się w tej samej strefie DNS. Zagwarantuje, że ten sam certyfikat sieci SAN (WIELU domen) może służyć do uwierzytelniania połączeń klienta do jednego z dwóch wystąpień w grupie trybu failover. Gdy aplikacja jest gotowa do wdrożenia produkcyjnego, utwórz wystąpienie pomocnicze w innym regionie i upewnij się, że współumieje strefy DNS z wystąpieniem podstawowym. Można to zrobić, określając `DNS Zone Partner` parametr opcjonalny przy użyciu portalu Azure, programu PowerShell lub interfejsu API REST.

> [!IMPORTANT]
> Pierwsze wystąpienie utworzone w podsieci określa strefę DNS dla wszystkich kolejnych wystąpień w tej samej podsieci. Oznacza to, że dwa wystąpienia z tej samej podsieci nie mogą należeć do różnych stref DNS.

Aby uzyskać więcej informacji na temat tworzenia wystąpienia pomocniczego w tej samej strefie DNS co wystąpienie podstawowe, zobacz [Tworzenie pomocniczego wystąpienia zarządzanego](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Włączanie ruchu replikacji między dwoma wystąpieniami

Ponieważ każde wystąpienie jest izolowane we własnej sieci wirtualnej, ruch dwukierunkowy między tymi sieciami wirtualnymi musi być dozwolony. Zobacz [brama sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Tworzenie grupy trybu failover między wystąpieniami zarządzanymi w różnych subskrypcjach

Grupę trybu failover można utworzyć między wystąpieniami zarządzanymi w dwóch różnych subskrypcjach. Podczas korzystania z interfejsu API programu PowerShell można to zrobić, określając `PartnerSubscriptionId` parametr dla wystąpienia pomocniczego. Podczas korzystania z interfejsu API REST, `properties.managedInstancePairs` każdy identyfikator wystąpienia zawarte w parametrze może mieć swój własny identyfikator subskrypcji.
  
> [!IMPORTANT]
> Usługa Azure portal nie obsługuje tworzenia grup trybu failover w różnych subskrypcjach. Ponadto dla istniejących grup trybu failover w różnych subskrypcji i/lub grup zasobów nie można zainicjować ręcznie za pośrednictwem portalu z wystąpienia podstawowego. Zainicjuj go z instancji pomocniczej geo pomocniczej.

### <a name="managing-failover-to-secondary-instance"></a>Zarządzanie przejściem awaryjnym do wystąpienia pomocniczego

Grupa trybu failover będzie zarządzać przewijanego w błąd wszystkich baz danych w wystąpieniu. Po utworzeniu grupy każda baza danych w wystąpieniu zostanie automatycznie zreplikowana geograficznie do wystąpienia pomocniczego. Nie można użyć grup trybu failover do zainicjowania częściowej pracy awaryjnej podzbioru baz danych.

> [!IMPORTANT]
> Jeśli baza danych zostanie usunięta z wystąpienia podstawowego, zostanie również automatycznie usunięta w wystąpieniu pomocniczym geograficznym.

### <a name="using-read-write-listener-for-oltp-workload"></a>Korzystanie z odbiornika odczytu i zapisu dla obciążenia OLTP

Podczas wykonywania operacji OLTP należy używać `<fog-name>.zone_id.database.windows.net` jako adres URL serwera, a połączenia są automatycznie kierowane do podstawowego. Ten adres URL nie zmienia się po przemijaniu awaryjnym. Tryb failover polega na aktualizowaniu rekordu DNS, więc połączenia klienta są przekierowywane do nowej podstawowej tylko po odświeżeniu pamięci podręcznej DNS klienta. Ponieważ wystąpienie pomocnicze współużytkuje strefę DNS z podstawową, aplikacja kliencka będzie mogła ponownie połączyć się z nią przy użyciu tego samego certyfikatu sieci SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Łączenie się z wystąpieniem pomocniczym za pomocą odbiornika tylko do odczytu

Jeśli masz logicznie izolowane obciążenie tylko do odczytu, które jest tolerancyjne dla pewnego nieaktualnego nieaktualności danych, można użyć pomocniczej bazy danych w aplikacji. Aby połączyć się bezpośrednio z replikowaną `server.secondary.zone_id.database.windows.net` geograficznie pomocniczą, użyj jako adresu URL serwera, a połączenie jest nawiązywać bezpośrednio do replikowanej geograficznie pomocniczej.

> [!NOTE]
> W niektórych warstwach usług usługa Azure SQL Database obsługuje używanie replik tylko do [odczytu](sql-database-read-scale-out.md) do obciążenia obciążenia obciążeń `ApplicationIntent=ReadOnly` zapytań tylko do odczytu przy użyciu pojemności jednej repliki tylko do odczytu i przy użyciu parametru w ciągu połączenia. Po skonfigurowaniu replikowanej geograficznie pomocniczej można użyć tej funkcji, aby połączyć się z repliką tylko do odczytu w lokalizacji podstawowej lub w lokalizacji replikowanej geograficznie.
> - Aby połączyć się z repliką tylko `<fog-name>.zone_id.database.windows.net`do odczytu w lokalizacji podstawowej, należy użyć programu .
> - Aby połączyć się z repliką tylko `<fog-name>.secondary.zone_id.database.windows.net`do odczytu w lokalizacji dodatkowej, użyj programu .

### <a name="preparing-for-performance-degradation"></a>Przygotowanie do obniżenia wydajności

Typowa aplikacja platformy Azure używa wielu usług platformy Azure i składa się z wielu składników. Automatyczna praca awaryjna grupy trybu failover jest wyzwalana na podstawie stanu samych składników SQL platformy Azure. Awaria może nie dotyczyć innych usług platformy Azure w regionie podstawowym, a ich składniki mogą być nadal dostępne w tym regionie. Po przełączeniu podstawowych baz danych do regionu odzyskiwania po awarii może wzrosnąć opóźnienie między składnikami zależnymi. Aby uniknąć wpływu większych opóźnień na wydajność aplikacji, należy zapewnić nadmiarowość wszystkich składników aplikacji w regionie odzyskiwania po awarii i postępować zgodnie z tymi [wytycznymi dotyczącymi zabezpieczeń sieci.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Przygotowanie do utraty danych

Jeśli zostanie wykryta awaria, SQL automatycznie wyzwala tryb failover odczytu i zapisu, jeśli nie ma utraty danych zgodnie z naszą najlepszą wiedzą. W przeciwnym razie czeka na okres `GracePeriodWithDataLossHours`określony przez program . Jeśli określono, `GracePeriodWithDataLossHours`przygotuj się na utratę danych. Ogólnie rzecz biorąc podczas awarii platformy Azure sprzyja dostępności. Jeśli nie możesz sobie pozwolić na utratę danych, upewnij się, że ustawisz GracePeriodWithDataLossHours na wystarczająco dużą liczbę, taką jak 24 godziny.

Aktualizacja DNS odbiornika odczytu i zapisu nastąpi natychmiast po zainicjowaniu pracy awaryjnej. Ta operacja nie spowoduje utraty danych. Jednak proces przełączania ról bazy danych może potrwać do 5 minut w normalnych warunkach. Dopóki nie zostanie ukończona, niektóre bazy danych w nowym wystąpieniu podstawowym będą nadal tylko do odczytu. Jeśli praca awaryjna jest inicjowana przy użyciu programu PowerShell, cała operacja jest synchroniczowa. Jeśli jest inicjowany przy użyciu witryny Azure portal, interfejs użytkownika będzie wskazywać stan ukończenia. Jeśli jest inicjowany przy użyciu interfejsu API REST, użyj standardowego mechanizmu sondowania usługi Azure Resource Manager do monitorowania ukończenia.

> [!IMPORTANT]
> Użyj ręcznej grupy pracy awaryjnej, aby przenieść prawybory z powrotem do oryginalnej lokalizacji. Gdy awaria, która spowodowała przełączenie w błąd jest złagodzone, można przenieść podstawowe bazy danych do oryginalnej lokalizacji. Aby to zrobić, należy zainicjować ręczne przejście awaryjne grupy.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Zmienianie pomocniczego regionu grupy trybu failover

Załóżmy, że wystąpienie A jest wystąpieniem podstawowym, wystąpienie B jest istniejącym wystąpieniem pomocniczym, a wystąpienie C jest nowym wystąpieniem pomocniczym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1.  Utwórz wystąpienie C o tym samym rozmiarze co A i w tej samej strefie DNS. 
2.  Usuń grupę trybu failover między wystąpieniami A i B. W tym momencie logowania będą wada, ponieważ aliasy SQL dla detektorów grupy trybu failover zostały usunięte i brama nie rozpozna nazwę grupy trybu failover. Pomocnicze bazy danych zostaną odłączone od prawyborów i staną się bazami danych odczytu i zapisu. 
3.  Utwórz grupę trybu failover o tej samej nazwie między wystąpieniami A i C. Postępuj zgodnie z instrukcjami w [grupie trybu failover za pomocą samouczka wystąpienia zarządzanego](sql-database-managed-instance-failover-group-tutorial.md). Jest to operacja o rozmiarze danych i zostanie ukończona, gdy wszystkie bazy danych z wystąpienia A zostaną rozstawione i zsynchronizowane.
4.  Usuń wystąpienie B, jeśli nie jest to konieczne, aby uniknąć niepotrzebnych opłat.

> [!NOTE]
> Po kroku 2 i do kroku 3 jest zakończona baz danych w wystąpieniu A pozostanie niezabezpieczony przed katastrofalnym niepowodzeniem wystąpienia A.

### <a name="changing-primary-region-of-the-failover-group"></a>Zmienianie regionu podstawowego grupy trybu failover

Załóżmy, że wystąpienie A jest wystąpieniem podstawowym, wystąpienie B jest istniejącym wystąpieniem pomocniczym, a wystąpienie C jest nowym wystąpieniem podstawowym w trzecim regionie.  Aby dokonać przejścia, wykonaj następujące kroki:

1.  Utwórz wystąpienie C o tym samym rozmiarze co B i w tej samej strefie DNS. 
2.  Połącz się z wystąpieniem B i ręcznie przełączaj się w tryb failover, aby przełączyć wystąpienie podstawowe na B. Wystąpienie A automatycznie stanie się nowym wystąpieniem pomocniczym.
3.  Usuń grupę trybu failover między wystąpieniami A i B. W tym momencie logowania będą wada, ponieważ aliasy SQL dla detektorów grupy trybu failover zostały usunięte i brama nie rozpozna nazwę grupy trybu failover. Pomocnicze bazy danych zostaną odłączone od prawyborów i staną się bazami danych odczytu i zapisu. 
4.  Utwórz grupę trybu failover o tej samej nazwie między wystąpieniami A i C. Postępuj zgodnie z instrukcjami w [grupie trybu failover za pomocą samouczka wystąpienia zarządzanego](sql-database-managed-instance-failover-group-tutorial.md). Jest to operacja o rozmiarze danych i zostanie ukończona, gdy wszystkie bazy danych z wystąpienia A zostaną rozstawione i zsynchronizowane.
5.  Usuń wystąpienie A, jeśli nie jest to konieczne, aby uniknąć niepotrzebnych opłat.

> [!NOTE] 
> Po kroku 3 i do kroku 4 jest zakończona baz danych w wystąpieniu A pozostanie niezabezpieczony przed katastrofalnym niepowodzeniem wystąpienia A.

> [!IMPORTANT]
> Po usunięciu grupy trybu failover rekordy DNS dla punktów końcowych odbiornika są również usuwane. W tym momencie istnieje niezerowe prawdopodobieństwo, że ktoś inny stworzy grupę trybu failover lub alias serwera o tej samej nazwie, co uniemożliwi ponowne użycie go. Aby zminimalizować ryzyko, nie należy używać ogólnych nazw grup trybu failover.

## <a name="failover-groups-and-network-security"></a>Grupy trybu failover i zabezpieczenia sieci

W przypadku niektórych aplikacji reguły zabezpieczeń wymagają, aby dostęp sieciowy do warstwy danych był ograniczony do określonego składnika lub składników, takich jak maszyna wirtualna, usługa sieci web itp. Wymóg ten stanowi pewne wyzwania dla projektowania ciągłości biznesowej i korzystania z grup trybu failover. Podczas wdrażania takiego ograniczonego dostępu należy wziąć pod uwagę następujące opcje.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Korzystanie z grup trybu failover i reguł sieci wirtualnej

Jeśli używasz [punktów końcowych usługi sieci wirtualnej i reguł,](sql-database-vnet-service-endpoint-rule-overview.md) aby ograniczyć dostęp do bazy danych SQL, należy pamiętać, że każdy punkt końcowy usługi sieci wirtualnej ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci. W związku z tym tylko aplikacje klienckie wdrożone w tym samym regionie można połączyć się z podstawowej bazy danych. Ponieważ wyniki pracy awaryjnej w sesjach klienta SQL są przekierowywane do serwera w innym regionie (pomocniczy), te sesje zakończy się niepowodzeniem, jeśli pochodzi z klienta spoza tego regionu. Z tego powodu nie można włączyć zasad automatycznego trybu failover, jeśli uczestniczące serwery są uwzględnione w regułach sieci wirtualnej. Aby obsługiwać ręczne tryb failover, wykonaj następujące kroki:

1. Aprowizuj nadmiarowe kopie składników frontonu aplikacji (usługa sieci web, maszyny wirtualne itp.) w regionie pomocniczym
2. Konfigurowanie [reguł sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) indywidualnie dla serwera podstawowego i pomocniczego
3. Włączanie [pracy awaryjnej frontonu przy użyciu konfiguracji usługi Traffic Manager](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Inicjowanie ręcznego trybu failover po wykryciu awarii. Ta opcja jest zoptymalizowana dla aplikacji, które wymagają spójnego opóźnienia między front-end i warstwy danych i obsługuje odzyskiwanie, gdy albo front-end, warstwy danych lub obu mają wpływ awarii.

> [!NOTE]
> Jeśli używasz **odbiornika tylko** do odczytu do równoważenia obciążenia obciążenia tylko do odczytu obciążenia, upewnij się, że to obciążenie jest wykonywane na maszynie Wirtualnej lub innych zasobów w regionie pomocniczym, dzięki czemu można połączyć się z pomocniczej bazy danych.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Korzystanie z grup trybu failover i reguł zapory bazy danych SQL

Jeśli plan ciągłości działania wymaga pracy awaryjnej przy użyciu grup z automatyczną funkcją failover, można ograniczyć dostęp do bazy danych SQL przy użyciu tradycyjnych reguł zapory. Aby obsługiwać automatyczne tryb failover, wykonaj następujące kroki:

1. [Tworzenie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Utwórz publiczny moduł równoważenia obciążenia](../load-balancer/quickstart-load-balancer-standard-public-portal.md) i przypisz do niego publiczny adres IP.
3. [Tworzenie sieci wirtualnej i maszyn wirtualnych](../load-balancer/quickstart-load-balancer-standard-public-portal.md) dla składników frontonu
4. [Utwórz sieciową grupę zabezpieczeń](../virtual-network/security-overview.md) i skonfiguruj połączenia przychodzące.
5. Upewnij się, że połączenia wychodzące są otwarte w bazie danych SQL platformy Azure przy użyciu [tagu usługi "Sql".](../virtual-network/security-overview.md#service-tags)
6. Utwórz [regułę zapory bazy danych SQL,](sql-database-firewall-configure.md) aby zezwolić na ruch przychodzący z publicznego adresu IP utworzonego w kroku 1.

Aby uzyskać więcej informacji na temat konfigurowania dostępu wychodzącego i adresu IP używanego w regułach zapory, zobacz [Połączenia wychodzące modułu równoważenia obciążenia](../load-balancer/load-balancer-outbound-connections.md).

Powyższa konfiguracja zapewni, że automatyczna funkcja failover nie zablokuje połączeń ze składników frontonu i zakłada, że aplikacja może tolerować dłuższe opóźnienie między frontonem a warstwą danych.

> [!IMPORTANT]
> Aby zagwarantować ciągłość działania w przypadku awarii regionalnych, należy zapewnić nadmiarowość geograficzną zarówno dla składników frontonu, jak i baz danych.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Włączanie replikacji geograficznej między wystąpieniami zarządzanymi a ich sieciami wirtualnymi

Podczas konfigurowania grupy trybu failover między podstawowymi i pomocniczymi wystąpieniami zarządzanymi w dwóch różnych regionach każde wystąpienie jest izolowane przy użyciu niezależnej sieci wirtualnej. Aby zezwolić na ruch replikacji między tymi sieciami wirtualnymi, upewnij się, że te wymagania wstępne są spełnione:

1. Dwa wystąpienia zarządzane muszą znajdować się w różnych regionach platformy Azure.
2. Dwa wystąpienia zarządzane muszą być tej samej warstwy usług i mają ten sam rozmiar magazynu.
3. Pomocnicze wystąpienie zarządzane musi być puste (brak baz danych użytkowników).
4. Sieci wirtualne używane przez wystąpienia zarządzane muszą być połączone za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub trasy [ekspresowej.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Gdy dwie sieci wirtualne łączą się za pośrednictwem sieci lokalnej, upewnij się, że nie ma reguły zapory blokującej porty 5022 i 11000-11999. Globalna komunikacja równorzędna sieci wirtualnej nie jest obsługiwana.
5. Dwie sieci wirtualne wystąpienia zarządzanego nie mogą mieć nakładających się adresów IP.
6. Należy skonfigurować sieciowe grupy zabezpieczeń (NSG) w taki sposób, aby porty 5022 i zakres 11000 ~ 12000 były otwarte przychodzące i wychodzące dla połączeń z podsieci innego wystąpienia zarządzanego. Ma to umożliwić ruch replikacji między wystąpieniami.

   > [!IMPORTANT]
   > Nieprawidłowo skonfigurowane reguły zabezpieczeń sieciowej sieciowej sieciowej prowadzą do zablokowanych operacji kopiowania bazy danych.

7. Wystąpienie pomocnicze jest skonfigurowane z prawidłowym identyfikatorem strefy DNS. Strefa DNS jest właściwością wystąpienia zarządzanego i klastra wirtualnego, a jej identyfikator jest zawarty w adresie nazwy hosta. Identyfikator strefy jest generowany jako losowy ciąg, gdy pierwsze wystąpienie zarządzane jest tworzone w każdej sieci wirtualnej, a ten sam identyfikator jest przypisywany do wszystkich innych wystąpień w tej samej podsieci. Po przypisaniu strefy DNS nie można modyfikować. Wystąpienia zarządzane uwzględnione w tej samej grupie trybu failover muszą współużytkowania strefy DNS. Można to osiągnąć, przekazując identyfikator strefy wystąpienia podstawowego jako wartość parametru DnsZonePartner podczas tworzenia wystąpienia pomocniczego. 

   > [!NOTE]
   > Aby uzyskać szczegółowy samouczek dotyczący konfigurowania grup trybu failover z [wystąpieniem zarządzanym, zobacz dodawanie wystąpienia zarządzanego do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uaktualnianie lub obniżanie poziomu podstawowej bazy danych

Można uaktualnić lub obniżyć podstawową bazę danych do innego rozmiaru obliczeń (w tej samej warstwie usług, a nie między ogólnego przeznaczenia i krytyczne dla firmy) bez odłączania żadnych dodatkowych baz danych. Podczas uaktualniania zaleca się najpierw uaktualnić wszystkie pomocnicze bazy danych, a następnie uaktualnić podstawowe. Podczas obniżania poziomu, odwrócić kolejność: downgrade podstawowy pierwszy, a następnie obniżyć wszystkie pomocnicze bazy danych. Po uaktualnieniu lub obniżeniu bazy danych do innej warstwy usług to zalecenie jest wymuszane.

Ta sekwencja jest zalecane w szczególności, aby uniknąć problemu, gdy pomocniczy w niższej jednostce SKU pobiera przeciążenie i musi być ponownie rozstawiony podczas procesu uaktualniania lub zmiany na starszą wersję. Można również uniknąć problemu, tworząc podstawowy tylko do odczytu, kosztem wpływu na wszystkie obciążenia odczytu i zapisu względem podstawowego.

> [!NOTE]
> Jeśli utworzono pomocniczą bazę danych jako część konfiguracji grupy trybu failover, nie zaleca się obniżania poziomu pomocniczej bazy danych. Ma to na celu zapewnienie, że warstwa danych ma wystarczającą pojemność do przetwarzania zwykłego obciążenia po uaktywnieniu pracy awaryjnej.

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie krytycznych danych

Ze względu na duże opóźnienia sieci rozległych, ciągła kopia używa mechanizmu replikacji asynchronicznego. Replikacja asynchronizalna sprawia, że niektóre utraty danych nieuniknione, jeśli wystąpi błąd. Jednak niektóre aplikacje mogą wymagać utraty danych. Aby chronić te aktualizacje krytyczne, deweloper aplikacji może wywołać procedurę systemu [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) natychmiast po zaśedmianiu transakcji. Wywołanie `sp_wait_for_database_copy_sync` blokuje wątek wywołujący, dopóki ostatnia zatwierdzona transakcja nie została przekazana do pomocniczej bazy danych. Jednak nie czeka na transmitowane transakcje, które mają być odtwarzane i zatwierdzone w drugorzędnym. `sp_wait_for_database_copy_sync`jest objęty zakresem określonego łącza do kopiowania ciągłego. Każdy użytkownik z prawami do połączenia z podstawową bazą danych może wywołać tę procedurę.

> [!NOTE]
> `sp_wait_for_database_copy_sync`zapobiega utracie danych po pracy awaryjnej, ale nie gwarantuje pełnej synchronizacji dostępu do odczytu. Opóźnienie spowodowane wywołaniem procedury `sp_wait_for_database_copy_sync` może być istotne i zależy od rozmiaru dziennika transakcji w momencie wywołania.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupy trybu failover i przywracanie punktu w czasie

Aby uzyskać informacje na temat używania przywracania punktu w czasie z grupami pracy awaryjnej, zobacz [Punkt w odzyskiwanie czasu (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Ograniczenia grup trybu failover

Należy pamiętać o następujących ograniczeniach:

- Nie można utworzyć grupy trybu failover między dwoma serwerami lub wystąpieniami w tych samych regionach platformy Azure.
- Nie można zmienić nazwy grupy trybu failover. Należy usunąć grupę i ponownie utworzyć ją pod inną nazwą. 
- Zmiana nazwy bazy danych nie jest obsługiwana dla wystąpień w grupie trybu failover. Aby można było zmienić nazwę bazy danych, należy tymczasowo usunąć grupę trybu failover.

## <a name="programmatically-managing-failover-groups"></a>Programowe zarządzanie grupami trybu failover

Jak wspomniano wcześniej, grupy automatycznego trybu failover i aktywnej replikacji geograficznej można również zarządzać programowo przy użyciu programu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń. Aktywna replikacja geograficzna zawiera zestaw interfejsów API usługi Azure Resource Manager do zarządzania, w tym [interfejs API REST bazy danych SQL sql azure](https://docs.microsoft.com/rest/api/sql/) i polecenia [cmdlet azure powershell.](https://docs.microsoft.com/powershell/azure/overview) Te interfejsy API wymagają użycia grup zasobów i obsługi zabezpieczeń opartych na rolach (RBAC). Aby uzyskać więcej informacji na temat implementowania ról dostępu, zobacz [Kontrola dostępu oparta na rolach platformy Azure](../role-based-access-control/overview.md).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Zarządzanie trybem failover bazy danych SQL za pomocą pojedynczych baz danych i pul elastycznych

| Polecenie cmdlet | Opis |
| --- | --- |
| [Nowa grupa AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno na serwerach podstawowych, jak i pomocniczych|
| [Usuń-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Usuwa grupę trybu failover z serwera |
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera konfigurację grupy trybu failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modyfikuje konfigurację grupy trybu failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Wyzwala przewija powiększe pracy awaryjnej grupy do serwera pomocniczego |
| [Grupa Add-AzSqlDatabaseToFailover](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Dodaje jedną lub więcej baz danych do grupy trybu failover|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Zarządzanie grupami trybu failover bazy danych SQL za pomocą wystąpień zarządzanych

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno w wystąpieniach podstawowych, jak i pomocniczych|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modyfikuje konfigurację grupy trybu failover|
| [Grupa Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Pobiera konfigurację grupy trybu failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Wyzwala przewija się w przypadku pracy awaryjnej grupy trybu failover do wystąpienia pomocniczego|
| [Usuń-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Usuwa grupę trybu failover|

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Zarządzanie trybem failover bazy danych SQL za pomocą pojedynczych baz danych i pul elastycznych

| Polecenie | Opis |
| --- | --- |
| [az sql tworzenie grupy trybu failover](/cli/azure/sql/failover-group#az-sql-failover-group-create) |To polecenie tworzy grupę trybu failover i rejestruje ją zarówno na serwerach podstawowych, jak i pomocniczych|
| [az sql failover-group delete az sql failover-group delete az sql failover-group delete az](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Usuwa grupę trybu failover z serwera |
| [az sql praca awaryjna-grupa pokaż](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Pobiera konfigurację grupy trybu failover |
| [az sql aktualizacja grupy trybu failover](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modyfikuje konfigurację grupy trybu failover i/lub dodaje jedną lub więcej baz danych do grupy trybu failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Wyzwala przewija powiększe pracy awaryjnej grupy do serwera pomocniczego |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Zarządzanie grupami trybu failover bazy danych SQL za pomocą wystąpień zarządzanych

| Polecenie | Opis |
| --- | --- |
| [az sql wystąpienie-praca awaryjna-grupa utworzyć](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | To polecenie tworzy grupę trybu failover i rejestruje ją zarówno w wystąpieniach podstawowych, jak i pomocniczych |
| [az sql instance-failover-group update az sql instance-failover-group update az sql](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modyfikuje konfigurację grupy trybu failover|
| [az sql instance-failover-group show az sql instance-failover-group show az sql instance-failover-group show az](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Pobiera konfigurację grupy trybu failover|
| [az sql wystąpienie-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Wyzwala przewija się w przypadku pracy awaryjnej grupy trybu failover do wystąpienia pomocniczego|
| [az sql wystąpienie-failover-group usunąć](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Usuwa grupę trybu failover |

* * *

> [!IMPORTANT]
> Aby uzyskać przykładowy skrypt, zobacz [Konfigurowanie i praca awaryjna grupy trybu failover dla pojedynczej bazy danych](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>INTERFEJS API REST: Zarządzanie grupami trybu failover bazy danych SQL z pojedynczymi i buforowanymi bazami danych

| interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizowanie grupy trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuń grupę trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Przemija awaryjnie (planowane)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Wyzwala tryb failover z bieżącego serwera podstawowego do serwera pomocniczego z pełną synchronizacją danych.|
| [Wymuszanie pracy awaryjnej zezwalaj na utratę danych](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Wyzwala przemijanie awaryjne z bieżącego serwera podstawowego do serwera pomocniczego bez synchronizacji danych. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Pobiera konfigurację grupy trybu failover. |
| [Lista grup trybu failover według serwera](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Wyświetla listę grup trybu failover na serwerze. |
| [Aktualizowanie grupy trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizuje konfigurację grupy trybu failover. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>INTERFEJS API REST: zarządzanie grupami trybu failover za pomocą wystąpień zarządzanych

| interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizowanie grupy trybu failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Tworzy lub aktualizuje konfigurację grupy trybu failover |
| [Usuń grupę trybu failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Usuwa grupę trybu failover z wystąpienia |
| [Przemija awaryjnie (planowane)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Wyzwala tryb failover z bieżącego wystąpienia podstawowego do tego wystąpienia z pełną synchronizacją danych. |
| [Wymuszanie pracy awaryjnej zezwalaj na utratę danych](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Wyzwala przemijanie awaryjne z bieżącego wystąpienia podstawowego do wystąpienia pomocniczego bez synchronizacji danych. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | pobiera konfigurację grupy trybu failover. |
| [Lista grup trybu failover — lista według lokalizacji](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Wyświetla listę grup trybu failover w lokalizacji. |

## <a name="next-steps"></a>Następne kroki

- Szczegółowe samouczki można znaleźć w części
    - [Dodawanie pojedynczej bazy danych do grupy trybu failover](sql-database-single-database-failover-group-tutorial.md)
    - [Dodawanie puli elastycznej do grupy trybu failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Dodawanie wystąpienia zarządzanego do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md)
- Przykładowe skrypty można znaleźć w:
  - [Konfigurowanie aktywnej replikacji geograficznej dla pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu programu PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie aktywnej replikacji geograficznej dla bazy danych w puli w usłudze Azure SQL Database przy użyciu programu PowerShell](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Dodawanie pojedynczej bazy danych usługi Azure SQL Database do grupy trybu failover za pomocą programu PowerShell](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Aby zapoznać się z omówieniem ciągłości biznesowej i scenariuszami, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych usługi Azure SQL Database, zobacz [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej na temat używania automatycznych kopii zapasowych do odzyskiwania, zobacz [Przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](sql-database-recovery-using-backups.md).
- Aby dowiedzieć się więcej o wymaganiach dotyczących uwierzytelniania nowego serwera podstawowego i bazy danych, zobacz [Zabezpieczenia bazy danych SQL po porodzie](sql-database-geo-replication-security-config.md).
