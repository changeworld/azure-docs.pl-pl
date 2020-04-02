---
title: Aktywna replikacja geograficzna
description: Użyj aktywnej replikacji geograficznej, aby utworzyć czytelne pomocnicze bazy danych poszczególnych baz danych w tym samym lub innym centrum danych (regionie).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: b80b58d64ea27df95c2704243d8a89fa6ca12e2a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548507"
---
# <a name="creating-and-using-active-geo-replication"></a>Tworzenie i używanie aktywnej replikacji geograficznej

Aktywna replikacja geograficzna to funkcja bazy danych SQL Azure, która umożliwia tworzenie czytelnych pomocniczych baz danych poszczególnych baz danych na serwerze bazy danych SQL w tym samym lub innym centrum danych (regionie).

> [!NOTE]
> Aktywna replikacja geograficzna nie jest obsługiwana przez wystąpienie zarządzane. W przypadku geograficznego trybu failover wystąpień zarządzanych należy użyć [grup auto-trybu failover](sql-database-auto-failover-group.md).

Aktywna replikacja geograficzna została zaprojektowana jako rozwiązanie ciągłości biznesowej, które umożliwia aplikacji szybkie odzyskiwanie poszczególnych baz danych po awarii w przypadku regionalnej awarii lub awarii na dużą skalę. Jeśli replikacja geograficzna jest włączona, aplikacja może zainicjować przebłag w pracy awaryjnej do pomocniczej bazy danych w innym regionie platformy Azure. Maksymalnie cztery pomocnicze są obsługiwane w tym samym lub różnych regionach, a pomocnicze mogą być również używane do zapytań o dostęp tylko do odczytu. Praca awaryjna musi być inicjowana ręcznie przez aplikację lub użytkownika. Po przełączeniu w błąd nowy podstawowy ma inny punkt końcowy połączenia. Na poniższym diagramie przedstawiono typową konfigurację aplikacji chmury geograficznej nadmiarowej przy użyciu aktywnej replikacji geograficznej.

![aktywna replikacja geograficzna](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> Baza danych SQL obsługuje również grupy auto-failover. Aby uzyskać więcej informacji, zobacz korzystanie z [grup automatycznego trybu failover](sql-database-auto-failover-group.md). Ponadto aktywna replikacja geograficzna nie jest obsługiwana dla baz danych utworzonych w wystąpieniu zarządzanym. Należy rozważyć użycie [grup trybu failover](sql-database-auto-failover-group.md) z wystąpieniami zarządzanymi.

Jeśli z jakiegoś powodu podstawowa baza danych ulegnie awarii lub po prostu musi zostać przesuń do trybu offline, można zainicjować tryb failover do dowolnej pomocniczej bazy danych. Gdy przełączenie w błąd jest aktywowane w jednej z pomocniczych baz danych, wszystkie inne pomocnicze są automatycznie połączone z nową podstawową.

Można zarządzać replikacją i trybem failover pojedynczej bazy danych lub zestawu baz danych na serwerze lub w puli elastycznej przy użyciu aktywnej replikacji geograficznej. Można to zrobić za pomocą:

- [Portal platformy Azure](sql-database-geo-replication-portal.md)
- [PowerShell: Pojedyncza baza danych](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastyczna pula](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Pojedyncza baza danych lub pula elastyczna](/sql/t-sql/statements/alter-database-azure-sql-database)
- [INTERFEJS API REST: Pojedyncza baza danych](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Aktywna replikacja geograficzna wykorzystuje technologię [Zawsze włączone](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) programu SQL Server do asynchronicznie replikowania zatwierdzonych transakcji w podstawowej bazie danych do pomocniczej bazy danych przy użyciu izolacji migawki. Grupy automatycznego trybu failover zapewniają semantycę grupy na podstawie aktywnej replikacji geograficznej, ale używany jest ten sam mechanizm replikacji asynchroniczna. Podczas gdy w danym momencie pomocniczej bazy danych może być nieco za podstawową bazą danych, dane pomocnicze jest gwarantowane nigdy nie mają częściowych transakcji. Nadmiarowość między regionami umożliwia aplikacjom szybkie odzyskiwanie po trwałej utracie całego centrum danych lub części centrum danych spowodowanej klęskami żywiołowymi, katastrofalnymi błędami ludzkimi lub złośliwymi działaniami. Szczegółowe dane RPO można znaleźć w [przeglądzie ciągłości biznesowej](sql-database-business-continuity.md).

> [!NOTE]
> Jeśli występuje awaria sieci między dwoma regionami, ponawiamy próbę co 10 sekund, aby ponownie ustanowić połączenia.
> [!IMPORTANT]
> Aby zagwarantować, że krytyczna zmiana w podstawowej bazie danych zostanie zreplikowana do pomocniczego przed przejściem awaryjnym, można wymusić synchronizację w celu zapewnienia replikacji zmian krytycznych (na przykład aktualizacji haseł). Wymuszona synchronizacja wpływa na wydajność, ponieważ blokuje wątek wywołujący, dopóki wszystkie zatwierdzone transakcje nie zostaną zreplikowane. Aby uzyskać szczegółowe informacje, zobacz [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Aby monitorować opóźnienie replikacji między podstawową bazą danych a funkcją geo pomocniczą, zobacz [plik sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Na poniższym rysunku przedstawiono przykład aktywnej replikacji geograficznej skonfigurowanej z podstawową w regionie północno-środkowe stany USA i pomocniczą w regionie południowo-środkowe stany USA.

![relacja replikacji geograficznej](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Ponieważ pomocnicze bazy danych są czytelne, mogą służyć do odciążania obciążeń tylko do odczytu, takich jak zadania raportowania. Jeśli używasz aktywnej replikacji geograficznej, istnieje możliwość utworzenia pomocniczej bazy danych w tym samym regionie z podstawowym, ale nie zwiększa odporności aplikacji na katastrofalne awarie. Jeśli używasz grup automatycznego trybu failover, pomocnicza baza danych jest zawsze tworzona w innym regionie.

Oprócz odzyskiwania po awarii aktywna replikacja geograficzna może być używana w następujących scenariuszach:

- **Migracja bazy danych:** Można użyć aktywnej replikacji geograficznej do migracji bazy danych z jednego serwera na drugi w trybie online przy minimalnym przestoju.
- **Uaktualnienia aplikacji:** Podczas uaktualniania aplikacji można utworzyć dodatkową pomocniczą jako kopię powrotną po awarii.

Aby osiągnąć rzeczywistą ciągłość działania, dodawanie nadmiarowości bazy danych między centrami danych jest tylko częścią rozwiązania. Odzyskiwanie aplikacji (usługi) end-to-end po katastrofalnej awarii wymaga odzyskania wszystkich składników, które stanowią usługi i wszelkie usługi zależne. Przykładami tych składników są oprogramowanie klienckie (na przykład przeglądarka z niestandardowym javascriptem), frontonów internetowych, magazynu i DNS. Ważne jest, aby wszystkie składniki są odporne na te same błędy i stają się dostępne w ramach celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkie usługi zależne i zrozumieć gwarancje i możliwości, które zapewniają. Następnie należy podjąć odpowiednie kroki, aby upewnić się, że usługa działa podczas pracy awaryjnej usług, od których zależy. Aby uzyskać więcej informacji na temat projektowania rozwiązań do odzyskiwania po awarii, zobacz [Projektowanie rozwiązań w chmurze dla odzyskiwania po awarii przy użyciu aktywnej replikacji geograficznej](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktywna terminologia i możliwości replikacji geograficznej

- **Automatyczna replikacja asynchroniza**

  Pomocniczą bazę danych można utworzyć tylko przez dodanie do istniejącej bazy danych. Pomocniczy można utworzyć w dowolnym serwerze bazy danych SQL azure. Po utworzeniu pomocnicza baza danych jest wypełniana danymi skopiowanymi z podstawowej bazy danych. Proces ten jest znany jako wysiewu. Po utworzeniu i rozstawieniu pomocniczej bazy danych aktualizacje podstawowej bazy danych są automatycznie replikowane do pomocniczej bazy danych. Replikacja asynchroniowa oznacza, że transakcje są zatwierdzane w podstawowej bazie danych, zanim zostaną zreplikowane do pomocniczej bazy danych.

- **Czytelne pomocnicze bazy danych**

  Aplikacja może uzyskać dostęp do pomocniczej bazy danych dla operacji tylko do odczytu przy użyciu tych samych lub różnych podmiotów zabezpieczeń używanych do uzyskiwania dostępu do podstawowej bazy danych. Pomocnicze bazy danych działają w trybie izolacji migawki, aby upewnić się, że replikacja aktualizacji podstawowych (powtarzanie dziennika) nie jest opóźniona przez kwerendy wykonywane w pomocniczym.

> [!NOTE]
> Powtarzanie dziennika jest opóźnione w pomocniczej bazie danych, jeśli istnieją aktualizacje schematu na podstawowym. Ten ostatni wymaga blokady schematu w pomocniczej bazie danych.
> [!IMPORTANT]
> Replikacja geograficzna służy do tworzenia pomocniczej bazy danych w tym samym regionie co podstawowa. Można użyć tego pomocniczego do równoważenia obciążenia obciążeń tylko do odczytu w tym samym regionie. Jednak pomocnicza baza danych w tym samym regionie nie zapewnia dodatkowe odporności na błędy i dlatego nie jest odpowiedni obiekt docelowy pracy awaryjnej dla odzyskiwania po awarii. Nie gwarantuje również izolacji strefy dostępności. Użyj warstwy usług krytyczna dla firm lub Premium z [konfiguracją nadmiarową strefy,](sql-database-high-availability.md#zone-redundant-configuration) aby uzyskać izolację strefy dostępności.   
>

- **Planowane przemija awaryjne**

  Planowana praca awaryjna przełącza role podstawowych i pomocniczych baz danych po zakończeniu pełnej synchronizacji. Jest to operacja online, która nie powoduje utraty danych. Czas operacji zależy od rozmiaru dziennika transakcji na podstawowej, która musi zostać zsynchronizowana. Planowana praca awaryjna jest przeznaczona dla następujących scenariuszy: (a) do wykonywania ćwiczeń odzyskiwania po awarii w produkcji, gdy utrata danych jest nie do przyjęcia; b) przeniesienie bazy danych do innego regionu; oraz (c) zwracać bazę danych do regionu podstawowego po ograniczeniu awarii (powrót po awarii).

- **Nieplanowany tryb failover**

  Nieplanowane lub wymuszone tryb failover natychmiast przełącza pomocniczą rolę podstawową bez synchronizacji z podstawową. Wszystkie transakcje zatwierdzone do podstawowego, ale nie replikowane do pomocniczego zostaną utracone. Ta operacja jest zaprojektowana jako metoda odzyskiwania podczas awarii, gdy podstawowy nie jest dostępny, ale dostępność bazy danych musi zostać szybko przywrócona. Gdy oryginalny podstawowy jest z powrotem w trybie online automatycznie ponownie połączyć i stać się nowym wtórnym. Wszystkie niezsynchronizowane transakcje przed oddaniem trybu failover zostaną zachowane w pliku kopii zapasowej, ale nie zostaną zsynchronizowane z nowym podstawowym, aby uniknąć konfliktów. Transakcje te będą musiały być ręcznie scalane z najnowszą wersją podstawowej bazy danych.
 
- **Wiele czytelnych pomocniczych**

  Dla każdej podstawowej można utworzyć maksymalnie 4 pomocnicze bazy danych. Jeśli istnieje tylko jedna pomocnicza baza danych, a zakończy się niepowodzeniem, aplikacja jest narażona na większe ryzyko, dopóki nie zostanie utworzona nowa pomocnicza baza danych. Jeśli istnieje wiele pomocniczych baz danych, aplikacja pozostaje chroniona, nawet jeśli jedna z pomocniczych baz danych nie powiedzie się. Dodatkowe pomocnicze mogą być również używane do skalowania w poziomie obciążeń tylko do odczytu

  > [!NOTE]
  > Jeśli używasz aktywnej replikacji geograficznej do tworzenia aplikacji rozproszonej globalnie i musisz zapewnić dostęp tylko do odczytu do danych w więcej niż czterech regionach, możesz utworzyć pomocniczy pomocniczy (proces znany jako tworzenie łańcucha). W ten sposób można osiągnąć praktycznie nieograniczoną skalę replikacji bazy danych. Ponadto tworzenie łańcucha zmniejsza obciążenie replikacji z podstawowej bazy danych. Kompromisem jest zwiększone opóźnienie replikacji w pomocniczych bazach danych najbardziej liściowych.

- **Replikacja geograficzna baz danych w puli elastycznej**

  Każda pomocnicza baza danych może oddzielnie uczestniczyć w puli elastycznej lub nie być w dowolnej puli elastycznej w ogóle. Wybór puli dla każdej pomocniczej bazy danych jest oddzielny i nie zależy od konfiguracji innej pomocniczej bazy danych (podstawowej lub pomocniczej). Każda pula elastyczna znajduje się w jednym regionie, dlatego wiele pomocniczych baz danych w tej samej topologii nigdy nie może współużytkować puli elastycznej.


- **Kontrolowane przez użytkownika trybu failover i powiększania po awarii**

  Pomocnicza baza danych można jawnie przełączyć do roli podstawowej w dowolnym momencie przez aplikację lub użytkownika. Podczas prawdziwej awarii należy użyć opcji "nieplanowane", która natychmiast promuje drugorzędne jako podstawowe. Gdy nie powiodło się podstawowy odzyskuje i jest dostępny ponownie, system automatycznie oznacza odzyskane podstawowe jako pomocnicze i przynieść go na bieżąco z nowym podstawowym. Ze względu na asynchroniczne charakter replikacji, niewielka ilość danych może zostać utracona podczas nieplanowanych pracy awaryjnej, jeśli podstawowy nie powiedzie się, zanim replikuje najnowsze zmiany pomocnicze. Gdy podstawowy z wieloma pomocniczymi plikami danych kończy się niepowodzeniem, system automatycznie ponownie konfiguruje relacje replikacji i łączy pozostałe pomocnicze bazy danych z nowo wypromowane podstawowe bez konieczności interwencji użytkownika. Po awarii, która spowodowała pracy awaryjnej jest złagodzone, może być pożądane, aby zwrócić aplikację do regionu podstawowego. Aby to zrobić, polecenie trybu failover należy wywołać za pomocą opcji "planowane".

## <a name="preparing-secondary-database-for-failover"></a>Przygotowywanie pomocniczej bazy danych do pracy awaryjnej

Aby upewnić się, że aplikacja może natychmiast uzyskać dostęp do nowej podstawowej po pracy awaryjnej, upewnij się, że wymagania uwierzytelniania dla serwera pomocniczego i bazy danych są poprawnie skonfigurowane. Aby uzyskać szczegółowe informacje, zobacz [Zabezpieczenia bazy danych SQL po porodzie](sql-database-geo-replication-security-config.md). Aby zagwarantować zgodność po pracy awaryjnej, upewnij się, że zasady przechowywania kopii zapasowych w pomocniczej bazie danych są zgodne z zasadami podstawowymi. Te ustawienia nie są częścią bazy danych i nie są replikowane. Domyślnie pomocniczy zostanie skonfigurowany z domyślnym okresem przechowywania PITR wynoszącym siedem dni. Aby uzyskać szczegółowe informacje, zobacz [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).

> [!IMPORTANT]
> Jeśli baza danych jest członkiem grupy trybu failover, nie można zainicjować jej pracy awaryjnej przy użyciu polecenia faiover replikacji geograficznej. Należy rozważyć użycie polecenia trybu failover dla grupy. Jeśli konieczne jest przełączenie w stan failover pojedynczej bazy danych, należy najpierw usunąć ją z grupy trybu failover. Zobacz [grupy trybu failover,](sql-database-auto-failover-group.md) aby uzyskać szczegółowe informacje. 


## <a name="configuring-secondary-database"></a>Konfigurowanie pomocniczej bazy danych

Bazy danych podstawowych i pomocniczych muszą mieć tę samą warstwę usług. Zdecydowanie zaleca się również utworzenie pomocniczej bazy danych o tym samym rozmiarze obliczeniowym (DTU lub vCores) co podstawowa. Jeśli podstawowa baza danych występuje duże obciążenie zapisu, pomocniczy o mniejszym rozmiarze obliczeń może nie być w stanie nadążyć za nim. Spowoduje to opóźnienie ponownego na wtórne i potencjalne niedostępności. Pomocnicza baza danych, która pozostaje w tyle za podstawową również ryzyko utraty dużych danych, jeśli wymuszone pracy awaryjnej być wymagane. Aby ograniczyć te zagrożenia, efektywna aktywna replikacja geograficzna ograniczy szybkość dziennika podstawowego, aby umożliwić jego pomocniczym nadrobić zaległości. Inną konsekwencją nierównoważnej konfiguracji pomocniczej jest to, że po przeróniu awaryjnym wydajność aplikacji będzie cierpieć z powodu niewystarczającej pojemności obliczeniowej nowej podstawowej. Będzie wymagane uaktualnienie do wyższego obliczeń do niezbędnego poziomu, co nie będzie możliwe, dopóki awaria nie zostanie złagodzona. 


> [!IMPORTANT]
> Opublikowany RPO = 5 s nie może być zagwarantowany, chyba że pomocnicza baza danych jest skonfigurowana z tym samym rozmiarem obliczeń co podstawowy. 


Jeśli zdecydujesz się utworzyć pomocniczy o mniejszym rozmiarze obliczeń, wykres procentowy we/wy dziennika w witrynie Azure portal zapewnia dobry sposób oszacowania minimalnego rozmiaru obliczeń pomocniczego, który jest wymagany do utrzymania obciążenia replikacji. Na przykład jeśli podstawowa baza danych jest P6 (1000 DTU) i jego procent we/wy dziennika jest 50% pomocniczy musi być co najmniej P4 (500 DTU). Można również pobrać dane we/wy dziennika przy użyciu [pliku sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) lub [sys.dm_db_resource_stats widoków](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) bazy danych.  Ograniczanie jest zgłaszane jako stan oczekiwania HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO w widokach bazy danych [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_os_wait_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 

Aby uzyskać więcej informacji na temat rozmiarów obliczeń bazy danych SQL, zobacz [Co to są warstwy usług bazy danych SQL](sql-database-purchase-models.md).

## <a name="cross-subscription-geo-replication"></a>Replikacja geograficzna między subskrypcjami

Aby skonfigurować aktywną replikację geograficzną między dwiema bazami danych należącymi do różnych subskrypcji (w ramach tej samej dzierżawy lub nie), należy wykonać specjalną procedurę opisaną w tej sekcji.  Procedura jest oparta na poleceniach SQL i wymaga: 

- Tworzenie uprzywilejowanego logowania na obu serwerach
- Dodawanie adresu IP do listy dozwolonych klienta wykonującego zmianę na obu serwerach (na przykład adres IP hosta z programem SQL Server Management Studio). 

Klient wykonujący zmiany potrzebuje dostępu sieciowego do serwera podstawowego. Chociaż ten sam adres IP klienta musi zostać dodany do listy dozwolonych na serwerze pomocniczym, łączność sieciowa z serwerem pomocniczym nie jest ściśle wymagana. 

### <a name="on-the-master-of-the-primary-server"></a>Na wzorcu serwera podstawowego

1. Dodawanie adresu IP do listy dozwolonych klienta wykonującego zmiany (aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory](sql-database-firewall-configure.md)). 
1. Utwórz login dedykowany do konfigurowania aktywnej replikacji geograficznej (i w razie potrzeby dostosuj poświadczenia):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Utwórz odpowiedniego użytkownika i przypisz go do roli dbmanagera: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Zanotuj identyfikator SID nowego logowania za pomocą tej kwerendy: 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>W źródłowej bazie danych na serwerze podstawowym

1. Utwórz użytkownika dla tego samego logowania:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Dodaj użytkownika do roli db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Na serwerze głównym serwera pomocniczego 

1. Dodaj adres IP do listy dozwolonych klienta wykonującego zmiany. Musi dokładnie mieć ten sam adres IP serwera podstawowego. 
1. Utwórz ten sam login, co na serwerze podstawowym, używając tego samego hasła nazwy użytkownika i identyfikatora SID: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Utwórz odpowiedniego użytkownika i przypisz go do roli dbmanagera:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Na wzorcu serwera podstawowego

1. Zaloguj się do wzorca serwera podstawowego przy użyciu nowego loginu. 
1. Utwórz pomocniczą replikę źródłowej bazy danych na serwerze pomocniczym (w razie potrzeby dostosuj nazwę bazy danych i nazwę serwera):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Po początkowej konfiguracji można usunąć utworzone reguły użytkowników, logowania i zapory. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Synchronizacja poświadczeń i reguł zapory

Zaleca się używanie [reguł zapory IP](sql-database-firewall-configure.md) na poziomie bazy danych dla replikowanych geograficznie baz danych, aby te reguły mogły być replikowane z bazą danych, aby upewnić się, że wszystkie pomocnicze bazy danych mają te same reguły zapory IP, co podstawowe. Takie podejście eliminuje potrzebę ręcznego konfigurowania i obsługi reguł zapory na serwerach obsługujących zarówno podstawowe, jak i pomocnicze bazy danych. Podobnie przy użyciu [użytkowników zawartej bazy danych](sql-database-manage-logins.md) dostępu do danych zapewnia, że zarówno podstawowe i pomocnicze bazy danych zawsze mają te same poświadczenia użytkownika, więc podczas pracy awaryjnej nie ma żadnych zakłóceń z powodu niezgodności z logowania i hasła. Dzięki dodaniu [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)klienci mogą zarządzać dostępem użytkowników do podstawowych i pomocniczych baz danych i eliminując konieczność zarządzania poświadczeniami w bazach danych.

## <a name="upgrading-or-downgrading-primary-database"></a>Uaktualnianie lub obniżanie wersji podstawowych bazy danych

Można uaktualnić lub obniżyć podstawową bazę danych do innego rozmiaru obliczeń (w tej samej warstwie usług, a nie między ogólnego przeznaczenia i krytyczne dla firmy) bez odłączania żadnych dodatkowych baz danych. Podczas uaktualniania zaleca się uaktualnienie pomocniczej bazy danych, a następnie uaktualnienie podstawowego. Podczas obniżania poziomu, odwrócić kolejność: downgrade podstawowy pierwszy, a następnie obniżyć pomocniczy. Po uaktualnieniu lub obniżeniu bazy danych do innej warstwy usług to zalecenie jest wymuszane.

> [!NOTE]
> Jeśli utworzono pomocniczą bazę danych jako część konfiguracji grupy trybu failover, nie zaleca się obniżania poziomu pomocniczej bazy danych. Ma to na celu zapewnienie, że warstwa danych ma wystarczającą pojemność do przetwarzania zwykłego obciążenia po uaktywnieniu pracy awaryjnej.

> [!IMPORTANT]
> Podstawowa baza danych w grupie trybu failover nie można skalować do wyższej warstwy, chyba że pomocnicza baza danych jest najpierw skalowana do wyższej warstwy. Jeśli spróbujesz skalować podstawową bazę danych przed skalowaniem pomocniczej bazy danych, może pojawić się następujący błąd:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie krytycznych danych

Ze względu na duże opóźnienia sieci rozległych, ciągła kopia używa mechanizmu replikacji asynchronicznego. Replikacja asynchronizalna sprawia, że niektóre utraty danych nieuniknione, jeśli wystąpi błąd. Jednak niektóre aplikacje mogą wymagać utraty danych. Aby chronić te aktualizacje krytyczne, deweloper aplikacji może wywołać procedurę systemu [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) natychmiast po zaśedmianiu transakcji. Wywołanie **sp_wait_for_database_copy_sync** blokuje wątek wywołujący, dopóki ostatnia zatwierdzona transakcja nie została przekazana do pomocniczej bazy danych. Jednak nie czeka na transmitowane transakcje, które mają być odtwarzane i zatwierdzone w drugorzędnym. **sp_wait_for_database_copy_sync** jest objęty zakresem określonego łącza do kopiowania ciągłego. Każdy użytkownik z prawami do połączenia z podstawową bazą danych może wywołać tę procedurę.

> [!NOTE]
> **sp_wait_for_database_copy_sync** zapobiega utracie danych po pracy awaryjnej, ale nie gwarantuje pełnej synchronizacji dostępu do odczytu. Opóźnienie spowodowane wywołaniem procedury **sp_wait_for_database_copy_sync** może być istotne i zależy od rozmiaru dziennika transakcji w momencie wywołania.

## <a name="monitoring-geo-replication-lag"></a>Monitorowanie opóźnienia replikacji geograficznej

Aby monitorować opóźnienie w odniesieniu do celu RPO, użyj *replication_lag_sec* kolumny [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) w podstawowej bazie danych. Pokazuje opóźnienie w sekundach między transakcjami zatwierdzonymi na podstawowej i utrwalone na pomocniczej. Na przykład Jeśli wartość opóźnienia wynosi 1 sekundę, oznacza to, że jeśli podstawowa ma wpływ awarii w tej chwili i jest inicjowana w pracy awaryjnej, 1 sekunda najnowszych przejść nie zostanie zapisana. 

Aby zmierzyć opóźnienie w odniesieniu do zmian w podstawowej bazie danych, które zostały zastosowane w pomocniczej, czyli dostępne do odczytu z pomocniczego, porównaj *last_commit* czas w pomocniczej bazie danych z tą samą wartością w podstawowej bazie danych.

> [!NOTE]
> Czasami *replication_lag_sec* w podstawowej bazie danych ma wartość NULL, co oznacza, że podstawowy nie wie obecnie, jak daleko znajduje się pomocniczy.   Zazwyczaj dzieje się tak po ponownym uruchomieniu procesu i powinien być stan przejściowy. Należy rozważyć alerty aplikacji, jeśli *replication_lag_sec* zwraca NULL przez dłuższy okres czasu. Oznacza to, że pomocnicza baza danych nie może komunikować się z podstawową z powodu trwałego błędu łączności. Istnieją również warunki, które mogą spowodować, że różnica między *last_commit* czasu w pomocniczej i podstawowej bazy danych, aby stać się duże. Na przykład Jeśli zatwierdzenie jest dokonywane na podstawowym po długim okresie bez zmian, różnica przeskoczy do dużej wartości przed szybko powrót do 0. Należy wziąć pod uwagę warunek błędu, gdy różnica między tymi dwiema wartościami pozostaje duża przez długi czas.


## <a name="programmatically-managing-active-geo-replication"></a>Programowe zarządzanie aktywną replikacją geograficzną

Jak wspomniano wcześniej, aktywna replikacja geograficzna może być również zarządzana programowo przy użyciu programu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń. Aktywna replikacja geograficzna zawiera zestaw interfejsów API usługi Azure Resource Manager do zarządzania, w tym [interfejs API REST bazy danych SQL sql azure](https://docs.microsoft.com/rest/api/sql/) i polecenia [cmdlet azure powershell.](https://docs.microsoft.com/powershell/azure/overview) Te interfejsy API wymagają użycia grup zasobów i obsługi zabezpieczeń opartych na rolach (RBAC). Aby uzyskać więcej informacji na temat implementowania ról dostępu, zobacz [Kontrola dostępu oparta na rolach platformy Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Zarządzanie trybem failover pojedynczych i puli baz danych

> [!IMPORTANT]
> Te polecenia Transact-SQL mają zastosowanie tylko do aktywnej replikacji geograficznej i nie mają zastosowania do grup trybu failover. W związku z tym nie mają one również zastosowania do wystąpień zarządzanych, ponieważ obsługują tylko grupy trybu failover.

| Polecenie | Opis |
| --- | --- |
| [ZMIEŃ BAZĘ DANYCH](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Użyj argumentu DODAJ POMOCNICZE NA SERWERZE, aby utworzyć pomocniczą bazę danych dla istniejącej bazy danych i rozpocząć replikację danych |
| [ZMIEŃ BAZĘ DANYCH](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Użyj funkcji FAILOVER lub FORCE_FAILOVER_ALLOW_DATA_LOSS, aby przełączyć pomocniczą bazę danych jako podstawową w celu zainicjowania trybu failover |
| [ZMIEŃ BAZĘ DANYCH](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Użyj funkcji USUŃ POMOCNICZĄ NA SERWERZE, aby zakończyć replikację danych między bazą danych SQL a określoną pomocniczą bazą danych. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Zwraca informacje o wszystkich istniejących łączach replikacji dla każdej bazy danych na serwerze usługi Azure SQL Database. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Pobiera czas ostatniej replikacji, ostatnie opóźnienie replikacji i inne informacje o łączu replikacji dla danej bazy danych SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Pokazuje stan wszystkich operacji bazy danych, w tym stan łączy replikacji. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |powoduje, że aplikacja czekać, aż wszystkie zatwierdzone transakcje są replikowane i potwierdzone przez aktywną pomocniczą bazę danych. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>Program PowerShell: zarządzanie trybem failover pojedynczych i buforowanych baz danych

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

| Polecenie cmdlet | Opis |
| --- | --- |
| [Baza danych Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
| [Nowy-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover. |
| [Usuń-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Przerywa replikację danych między bazą danych SQL Database i wybraną pomocniczą bazą danych. |
| [Link do rozłączenia AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Pobiera linki replikacji geograficznej między bazą danych Azure SQL Database i grupą zasobów lub programem SQL Server. |
|  | |

> [!IMPORTANT]
> Aby zapoznać się z przykładowymi skryptami, zobacz [Konfigurowanie i przełączenie w tryb failover pojedynczej bazy danych przy użyciu aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) oraz [konfigurowanie i przełączenie w tryb failover puli bazy danych przy użyciu aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>INTERFEJS API REST: Zarządzanie trybem failover pojedynczych i puli baz danych

| interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizowanie bazy danych (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Tworzy, aktualizuje lub przywraca podstawową lub pomocniczą bazę danych. |
| [Pobierz stan tworzenia lub aktualizowania bazy danych](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Zwraca stan podczas operacji tworzenia. |
| [Ustawianie pomocniczej bazy danych jako podstawowej (planowane trybu failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Ustawia pomocniczą bazę danych, która jest podstawowa, przez niepowodzenie z bieżącej podstawowej bazy danych. **Ta opcja nie jest obsługiwana dla wystąpienia zarządzanego.**|
| [Ustawianie pomocniczej bazy danych jako podstawowej (nieplanowane trybu failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Ustawia pomocniczą bazę danych, która jest podstawowa, przez niepowodzenie z bieżącej podstawowej bazy danych. Ta operacja może spowodować utratę danych. **Ta opcja nie jest obsługiwana dla wystąpienia zarządzanego.**|
| [Pobierz łącze replikacji](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Pobiera łącze określonej replikacji dla danej bazy danych SQL w partnerstwie replikacji geograficznej. Pobiera informacje widoczne w widoku katalogu sys.geo_replication_links katalogu. **Ta opcja nie jest obsługiwana dla wystąpienia zarządzanego.**|
| [Łącza replikacji — lista według bazy danych](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Pobiera wszystkie łącza replikacji dla danej bazy danych SQL w partnerstwie replikacji geograficznej. Pobiera informacje widoczne w widoku katalogu sys.geo_replication_links katalogu. |
| [Usuń łącze replikacji](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Usuwa łącze replikacji bazy danych. Nie można wykonać podczas pracy awaryjnej. |
|  | |

## <a name="next-steps"></a>Następne kroki

- Przykładowe skrypty można znaleźć w:
  - [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- Baza danych SQL obsługuje również grupy auto-failover. Aby uzyskać więcej informacji, zobacz korzystanie z [grup automatycznego trybu failover](sql-database-auto-failover-group.md).
- Aby zapoznać się z omówieniem ciągłości biznesowej i scenariuszami, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych usługi Azure SQL Database, zobacz [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej na temat używania automatycznych kopii zapasowych do odzyskiwania, zobacz [Przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](sql-database-recovery-using-backups.md).
- Aby dowiedzieć się więcej o wymaganiach dotyczących uwierzytelniania nowego serwera podstawowego i bazy danych, zobacz [Zabezpieczenia bazy danych SQL po porodzie](sql-database-geo-replication-security-config.md).
