---
title: Aktywna replikacja geograficzna — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Użyj aktywnej replikacji geograficznej, aby utworzyć odczytu pomocniczych baz danych w poszczególnych baz danych w centrum danych w tej samej lub innej (region).
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
ms.date: 07/09/2019
ms.openlocfilehash: 4b525c3cbea600859106062ed34dc6df9622dec5
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807317"
---
# <a name="creating-and-using-active-geo-replication"></a>Tworzenie i korzystanie z aktywnej replikacji geograficznej

Aktywna replikacja geograficzna to funkcja usługi Azure SQL Database, która pozwala na tworzenie odczytu pomocniczych baz danych w poszczególnych baz danych na serwerze bazy danych SQL w centrum danych w tej samej lub innej (region).

> [!NOTE]
> Aktywna replikacja geograficzna nie jest obsługiwana przez wystąpienia zarządzanego. Geograficzny tryb failover do wystąpienia zarządzanego, można użyć [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).

Aktywna replikacja geograficzna została zaprojektowana jako rozwiązanie ciągłości biznesowej, które umożliwia aplikacji do odzyskiwania po awarii szybkie pojedynczych baz danych w przypadku regionalnej awarii lub awaria w dużej skali. Jeśli włączono replikację geograficzną, aplikacja może zainicjować trybu failover do pomocniczej bazy danych w innym regionie platformy Azure. Maksymalnie cztery pomocnicze bazy danych są obsługiwane w tej samej lub różnych regionach i pomocnicze bazy danych można także dostęp tylko do odczytu zapytań. Przełączenie w tryb failover musi być inicjowana ręcznie przez użytkownika lub aplikacji. Po zakończeniu przejścia w tryb failover nową podstawową ma punkt końcowy inne połączenie. Na poniższym diagramie przedstawiono typową konfigurację aplikacji chmurowych magazynu geograficznie nadmiarowego przy użyciu aktywnej replikacji geograficznej.

![Aktywna replikacja geograficzna](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database obsługuje także grupy automatyczny tryb failover. Aby uzyskać więcej informacji, zobacz przy użyciu [automatyczny tryb failover grupy](sql-database-auto-failover-group.md). Ponadto aktywnej replikacji geograficznej dla bazy danych utworzone w ramach wystąpienia zarządzanego nie jest obsługiwana. Należy rozważyć użycie [grupy trybu failover](sql-database-auto-failover-group.md) przy użyciu wystąpienia zarządzane przez usługę.

Jeśli z dowolnej przyczyny Twojej podstawowej bazy danych, zakończy się niepowodzeniem lub po prostu musi zostać przełączony do trybu offline, może zainicjować trybu failover do dowolnego z dodatkowej bazy danych. Po aktywowaniu trybu failover do jednej z pomocniczych baz danych, wszystkie inne pomocnicze bazy danych są automatycznie łączeni ze nową podstawową.

Możesz zarządzać replikacji i pracy w trybie failover poszczególnych baz danych lub zestaw baz danych na serwerze lub w puli elastycznej przy użyciu aktywnej replikacji geograficznej. Możesz wykonać, przy użyciu:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: Pojedynczą bazę danych](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Pula elastyczna](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Pojedynczą bazę danych lub puli elastycznej](/sql/t-sql/statements/alter-database-azure-sql-database)
- [INTERFEJS API REST: Pojedynczą bazę danych](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Korzysta z aktywnej replikacji geograficznej [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologii SQL Server, aby informacje o asynchronicznym replikowaniu przekazane transakcje na podstawowej bazy danych do pomocniczej bazy danych przy użyciu izolacji migawki. Automatyczny tryb failover grupy zapewniają semantykę grupy na podstawie aktywnej replikacji geograficznej, ale jest używany ten sam mechanizm replikacji asynchronicznej. Znajduje się na dowolnym etapie, pomocniczej bazy danych może być nieco za podstawowej bazy danych, danych pomocniczych jest gwarantowane, nigdy nie miała transakcji częściowych. Nadmiarowość między regionami umożliwia aplikacjom szybko odzyskać z trwałą utratę całego centrum danych lub ich części centrum danych spowodowane klęski żywiołowe, krytycznego błędami ludzkimi lub złośliwych działań. Określone dane w celu punktu odzyskiwania znajduje się w temacie [omówienie ciągłości](sql-database-business-continuity.md).

> [!NOTE]
> W przypadku awarii sieci między dwoma regionami, firma Microsoft ponów próbę wykonania co 10 sekund, aby ponownie ustanowić połączenia.
> [!IMPORTANT]
> Aby zagwarantować, że krytycznych zmian w podstawowej bazie danych są replikowane do dodatkowej przed przejścia w tryb failover, można wymusić synchronizacji w celu zapewnienia replikacji krytycznej zmian (na przykład, aktualizowania haseł). Wymuszone synchronizacji ma wpływ na wydajność, ponieważ blokuje wątek wywołujący, aż wszystkie zatwierdzone transakcje są replikowane. Aby uzyskać więcej informacji, zobacz [operacja sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Aby monitorować opóźnienie replikacji między podstawowej bazy danych i pomocnicza geograficzna, zobacz [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Poniższej ilustracji przedstawiono przykład aktywnej replikacji geograficznej skonfigurowaną główną w regionie północno-środkowe stany USA i dodatkowych w regionie południowo-środkowe stany USA.

![relacji replikacji geograficznej](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Ponieważ pomocniczych baz danych można odczytać, używać celu odciążenia obciążeń tylko do odczytu, takich jak zadania raportowania. Korzystając z aktywnej replikacji geograficznej, jest możliwe utworzenie pomocniczej bazy danych w tym samym regionie przy użyciu podstawowego, ale nie zwiększa odporność aplikacji na katastrofalnych awarii. Jeśli używasz grup automatyczny tryb failover pomocniczej bazy danych zawsze jest tworzony w innym regionie.

Oprócz awarii aktywnej replikacji geograficznej odzyskiwania może służyć w następujących scenariuszach:

- **Migracja bazy danych**: Aktywna replikacja geograficzna umożliwia migrowanie bazy danych z jednego serwera do innego trybu online z minimalnym przestojem.
- **Uaktualnienia aplikacji**: Podczas uaktualniania aplikacji, można utworzyć dodatkowe pomocniczy jako kopia wstecz kończyć się niepowodzeniem.

Aby osiągnąć rzeczywistych ciągłości działania, dodawanie nadmiarowość bazy danych między centrami danych jest tylko część rozwiązania. Odzyskiwanie aplikacji (usługa) end-to-end, po poważnej awarii wymaga odzyskiwania wszystkich składników wchodzących w skład usługi i usług zależnych. Przykładami tych składników oprogramowania klienta (na przykład przeglądarka za pomocą niestandardowych skryptów JavaScript), frontonów sieci web, magazynu i DNS. Należy koniecznie wszystkie składniki są odporne na awarie tych samych i stają się dostępne w ramach cel czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkich zależnych usług i zrozumieć gwarancje i możliwości, jakie oferują. Następnie należy wykonać odpowiednie kroki w celu zapewnienia, że funkcje usługi podczas pracy awaryjnej usługi, od których zależy. Aby uzyskać więcej informacji na temat projektowania rozwiązań do odzyskiwania po awarii, zobacz [projektowania rozwiązań w chmurze dla za pomocą odzyskiwania po awarii aktywnej replikacji geograficznej](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologia aktywnej replikacji geograficznej i możliwości

- **Automatyczną replikację asynchroniczną**

  Można tylko utworzyć pomocniczą bazę danych, dodając do istniejącej bazy danych. Pomocniczy można utworzyć w dowolnym serwerem Azure SQL Database. Po utworzeniu pomocniczej bazy danych jest wypełniana dane skopiowane z podstawowej bazy danych. Ten proces jest nazywany rozmieszczania. Po utworzeniu pomocniczej bazy danych i zasilany aktualizacje do podstawowej bazy danych są asynchronicznie replikowane do dodatkowej bazy danych automatycznie. Replikacja asynchroniczna oznacza, że transakcje są zatwierdzane w podstawowej bazie danych, zanim będą one replikowane do dodatkowej bazy danych.

- **Odczytu pomocniczych baz danych**

  Aplikacja może uzyskiwać dostęp do dodatkowej bazy danych dla operacji tylko do odczytu przy użyciu podmiotów zabezpieczeń tego samego lub innego, używane do uzyskiwania dostępu do podstawowej bazy danych. Pomocnicze bazy danych działa w trybie izolacji migawki, aby upewnić się, że replikacja aktualizacji podstawową (dziennik powtarzania) nie będzie opóźniony o zapytania wykonywane na serwerze pomocniczym.

> [!NOTE]
> Powtarzanie dziennika jest opóźnione w pomocniczej bazie danych, czy są aktualizacje schematu na serwerze podstawowym. Te ostatnie wymaga blokadę schematu w pomocniczej bazie danych.
> [!IMPORTANT]
> Replikacja geograficzna umożliwia tworzenie pomocniczej bazy danych w tym samym regionie jako podstawowy. W tym samym regionie, można użyć tej pomocniczej do obciążenia równoważenia obciążenia, tylko do odczytu. Jednak pomocniczej bazy danych, w tym samym regionie nie zapewnia dodatkową odporność i dlatego nie jest odpowiedni tryb failover obiektu docelowego dla odzyskiwania po awarii. On również nie gwarantuje avaialability strefy izolacji. Krytyczne dla działania lub warstwie usług Premium [nadmiarowe konfiguracji strefy](sql-database-high-availability.md#zone-redundant-configuration) do osiągnięcia avaialability strefy izolacji.   
>

- **Planowany tryb failover**

  Planowanego trybu failover przełączników role podstawowych i pomocniczych baz danych, po zakończeniu pełnej synchronizacji. Jest operacji w trybie online, który nie powoduje utraty danych. Czas operacji zależy od rozmiaru dziennika transakcji na serwerze podstawowym, które muszą być zsynchronizowane. Planowany tryb failover jest przeznaczony dla następujących scenariuszy: (a), aby wykonać odzyskiwanie po awarii ciągu w środowisku produkcyjnym po utracie danych nie jest dopuszczalne; (b), aby zmienić lokalizację bazy danych do innego regionu; i (c), aby przywrócić bazę danych do regionu podstawowego, po awarii skorygowane (powrót po awarii).

- **Nieplanowany tryb failover**

  Wymuszone lub nieplanowanego trybu failover przełączniki natychmiast pomocniczej do roli podstawowej bez żadnej synchronizacji z podstawowym. Wszystkie transakcje zatwierdzone do podstawowej, ale nie są replikowane do regionu pomocniczego, zostaną utracone. Ta operacja została zaprojektowana jako metody odzyskiwania podczas awarii, gdy podstawowy jest niedostępny, ale dostępności bazy danych można szybko przywrócić. Po powrocie do trybu online oryginalnej podstawowej będzie automatycznie połączyć się ponownie i stanie się nowym serwerem pomocniczym. Wszystkie transakcje niezsynchronizowane przed włączeniem trybu failover zostaną zachowane w pliku kopii zapasowej, ale nie będą synchronizowane z nową podstawową w celu uniknięcia konfliktów. Te transakcje, będą musieli ręcznie scalone z najnowszej wersji głównej bazy danych.
 
- **Wiele repliki możliwe do odczytu**

  Maksymalnie 4 pomocniczych baz danych mogą być tworzone dla każdej lokacji podstawowej. Jeśli istnieje tylko jeden pomocniczej bazy danych, a zakończy się niepowodzeniem, aplikacja jest narażony na większe ryzyko, dopiero po utworzeniu nowej pomocniczej bazy danych. Jeśli istnieje wiele pomocniczych baz danych, aplikacji pozostaje chroniony nawet w przypadku awarii jednej z pomocniczych baz danych. Dodatkowe pomocnicze bazy danych może również służyć do skalowania w poziomie obciążeń tylko do odczytu

  > [!NOTE]
  > Korzystania z aktywnej replikacji geograficznej tworzenie globalnie rozproszonych aplikacji i musi zapewniać dostęp tylko do odczytu do danych z więcej niż czterech regionów można utworzyć pomocniczego pomocniczej (proces znany jako łańcucha). Dzięki temu mogą osiągnąć niemal nieograniczoną skalę replikacji bazy danych. Dodatkowo łańcuch zmniejsza obciążenie replikacji z podstawowej bazy danych. Jest to kompromis lag zwiększoną replikacji w pomocniczych bazach danych większość typu liść.

- **Replikacja geograficzna bazy danych w puli elastycznej**

  Każdą bazę pomocniczą można oddzielnie uczestniczyć w puli elastycznej lub nie być w dowolnej elastycznej puli w ogóle. Wybór puli dla każdej dodatkowej bazy danych jest oddzielony i nie zależą od konfiguracji innych pomocniczej bazy danych (czy podstawowym lub pomocniczym). Każda pula elastyczna jest zawarty w jednym regionie, w związku z tym wiele dodatkowych baz danych w tej samej topologii nigdy nie mogą udostępniać puli elastycznej.


- **Kontrolowane przez użytkownika trybu failover i powrotu po awarii**

  Pomocnicza baza danych jawnie można dostosować do roli głównej w dowolnym momencie przez użytkownika lub aplikacji. Podczas awarii rzeczywistych opcji "nieplanowane" należy który promuje natychmiast pomocniczego jako podstawowy. Gdy nie powiodło się podstawowe odzyskuje i będzie znowu dostępna, system automatycznie oznacza odzyskane podstawowej jako pomocniczego i przełączyć aktualne z nową podstawową. Ze względu na charakter asynchronicznej replikacji niewielką ilość danych może być utracone podczas nieplanowane tryby Failover podstawowego zakończy się niepowodzeniem, przed rozpoczęciem replikacji najnowszych zmian do regionu pomocniczego. Po przejściu podstawowej oraz możliwość definiowania wielu tryb failover, system automatycznie ponownie konfiguruje relacje replikacji i łączy pozostałe pomocnicze bazy danych z nowo wypromowaną podstawową bez konieczności żadnej interwencji użytkownika. Po zminimalizowaniu wpływu awarii, które spowodowało przełączenie w tryb failover może być pożądane, przywrócenie aplikacji do regionu podstawowego. Aby to zrobić, polecenie pracy awaryjnej należy można wywołać z opcją "planowane".

## <a name="preparing-secondary-database-for-failover"></a>Przygotowywanie pomocniczej bazy danych do trybu failover

Aby upewnić się, że aplikacja natychmiast dostęp do nowego podstawowego po włączeniu trybu failover, upewnij się, że wymagania dotyczące uwierzytelniania dla pomocniczego serwera i bazy danych są poprawnie skonfigurowane. Aby uzyskać więcej informacji, zobacz [zabezpieczeń bazy danych SQL Database po awarii](sql-database-geo-replication-security-config.md). Aby zagwarantować zgodność po włączeniu trybu failover, upewnij się, że zasady przechowywania kopii zapasowych w pomocniczej bazie danych jest zgodny z typem podstawowym. Te ustawienia nie są częścią bazy danych i nie są replikowane. Domyślnie pomocniczym zostaną skonfigurowane przy użyciu domyślnego okresu przechowywania Odzyskiwanie siedmiu dni. Aby uzyskać więcej informacji, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md).

## <a name="configuring-secondary-database"></a>Konfigurowanie dodatkowej bazy danych

Podstawowych i pomocniczych baz danych muszą mieć taką samą warstwę usług. Również zdecydowanie zaleca się że tej dodatkowej bazy danych jest tworzony przy użyciu tych samych obliczeń rozmiaru (jednostki Dtu lub rdzeni wirtualnych) jako podstawowy. Jeśli podstawowa baza danych występuje obciążenie wysokie obciążenia podczas zapisu, pomocniczego z niższym rozmiaru obliczeń nie można na bieżąco z nim. Opóźnienie ponawiania spowoduje na niedostępność dodatkowej i możliwości. Opóźnienie pomocniczej bazy danych w stosunku do podstawowej bazy danych stanowi także ryzyko utraty dużej ilości danych w przypadku, gdy byłoby wymagane wymuszone przejście w tryb failover. Aby ograniczyć te zagrożenia, skutecznego aktywnej replikacji geograficznej zostanie wartość ograniczenia przepustowości podstawowy dziennika umożliwia jego serwerów pomocniczych, aby zapoznać się z nimi. Niejednoznacznego określenia imbalanced dodatkowej konfiguracji jest, że po włączeniu trybu failover aplikacji będzie to spowodować obniżenie wydajności ze względu na moc obliczeniową niewystarczające nową podstawową. Będzie konieczne uaktualnienie do wyższej obliczeń do niezbędnego poziomu, nie będzie możliwe do momentu zminimalizowaniu wpływu awarii. 


> [!IMPORTANT]
> Opublikowane cel punktu odzyskiwania = 5 s nie można zagwarantować, chyba że pomocniczej bazy danych jest skonfigurowany przy użyciu tego samego rozmiaru obliczeń jako podstawowy. 


Jeśli zdecydujesz się utworzyć pomocniczej z niższym rozmiaru obliczeń, wykres wartość procentową operacji We/Wy dziennika w witrynie Azure portal oferuje dobry sposób, aby oszacować rozmiar minimalny obliczeń pomocniczy, który jest wymagany do obsługi obciążenia replikacji. Na przykład, jeśli podstawowej bazy danych jest P6 (1000 jednostek DTU) i jego procent we/wy dziennika to 50% pomocnicza musi wynosić co najmniej P4 (500 jednostek DTU). Możesz również pobrać dane we/wy dziennika przy użyciu [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) lub [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) bazy danych widoków.  Ograniczenie jest zgłaszane w stan oczekiwania HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) bazy danych widoków. 

Aby uzyskać więcej informacji na temat rozmiarów wystąpień obliczeniowych bazy danych SQL, zobacz [co to są warstwy usługi bazy danych SQL](sql-database-purchase-models.md).

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Synchronizacja poświadczeń i reguł zapory

Firma Microsoft zaleca używanie [IP reguły zapory na poziomie bazy danych](sql-database-firewall-configure.md) replikowanej geograficznie baz danych, dzięki czemu te reguły mogą być replikowane z bazą danych, aby upewnić się, wszystkie pomocnicze bazy danych ma te same reguły zapory IP jako podstawowy. To podejście eliminuje potrzebę stosowania klientów ręcznego konfigurowania i konserwacji reguły zapory na serwerach hostujących zarówno podstawowych i pomocniczych baz danych. Podobnie za pomocą [zawartych użytkowników bazy danych](sql-database-manage-logins.md) danych zapewnia podstawowych i pomocniczych baz danych zawsze mają taki sam dostęp do poświadczeń użytkownika, więc podczas pracy w trybie failover nie ma żadnych przerw w działaniu z powodu niezgodności z logowania i hasła. Z dodatkiem [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), klienci mogą zarządzać dostępem użytkowników do podstawowych i pomocniczych baz danych i całkowicie eliminując potrzebę zarządzania poświadczeniami w bazach danych.

## <a name="upgrading-or-downgrading-primary-database"></a>Uaktualnianie lub zmiany na starszą wersję podstawowej bazy danych

Można uaktualnić lub obniżyć podstawowej bazy danych do rozmiaru obliczeniowej (w ramach tej samej warstwy usługi, nie między ogólnego przeznaczenia i krytyczne dla działania firmy) bez odłączania wszystkie pomocnicze bazy danych. Podczas uaktualniania, zalecamy najpierw uaktualnić pomocnicze bazy danych, a następnie uaktualnić podstawowy. Przed obniżeniem, odwracanie kolejności: najpierw obniżenia poziomu podstawowego, a następnie obniżyć wersję usługi dodatkowej. Po uaktualnieniu lub starszą wersję bazy danych do warstwy usług różnych tego zalecenia zostanie wymuszona.

> [!NOTE]
> Jeśli utworzono pomocniczej bazy danych jako część konfiguracji grupy trybu failover, który nie jest zalecane w przypadku obniżania pomocniczej bazy danych. To jest, aby upewnić się, że w warstwie danych ma dostatecznie dużą pojemność przetwarzanie regularne obciążenie, po aktywowaniu trybu failover.

> [!IMPORTANT]
> Podstawowej bazy danych w grupie trybu failover nie można skalować do wyższego poziomu, chyba że najpierw skalowania pomocniczej bazy danych wybranej wyższej warstwie. Jeśli zostanie podjęta próba skalowania podstawowej bazy danych przed pomocnicze bazy danych jest skalowana, można otrzymać następujący błąd:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie danych o kluczowym znaczeniu

Ze względu na duże opóźnienia sieci rozległej ciągłych kopii używa mechanizmu replikacji asynchronicznej. Replikacja asynchroniczna sprawia, że utrata danych nieuniknione Jeśli wystąpi awaria. Jednak niektóre aplikacje mogą wymagać bez utraty danych. Aby chronić te aktualizacje krytyczne, Twórca aplikacji może wywołać [operacja sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) procedury system natychmiast po zatwierdzanie transakcji. Wywoływanie **operacja sp_wait_for_database_copy_sync** blokuje wątek wywołujący, aż ostatniej transakcji zatwierdzone została przesłana do pomocniczej bazy danych. Jednak go nie czeka na przesyłane transakcji, które mają być powtórzone i zatwierdzona na serwerze pomocniczym. **Operacja sp_wait_for_database_copy_sync** obejmuje łącze określonych ciągłych kopii. Każdy użytkownik z uprawnieniami połączenia podstawowej bazy danych można wywołać tej procedury.

> [!NOTE]
> **Operacja sp_wait_for_database_copy_sync** zapobiega utracie danych po pracy awaryjnej, ale nie gwarantuje pełną synchronizację, aby uzyskać dostęp do odczytu. Opóźnienie spowodowane **operacja sp_wait_for_database_copy_sync** wywołanie procedury mogą być znaczące i zależy od rozmiaru dziennika transakcji w momencie wywołania.

## <a name="monitoring-geo-replication-lag"></a>Monitorowanie opóźnienia replikacji geograficznej

Aby monitorować opóźnieniem względem celu punktu odzyskiwania, użyj *replication_lag_sec* kolumny [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) w głównej bazie danych. Pokazuje opóźnienie w sekundach między transakcje zatwierdzone na serwerze podstawowym i utrzymywana na pomocniczym. Na przykład Jeśli wartość opóźnienia wynosi 1 s, oznacza to, jeśli podstawowy jest wpływ awaria w tej chwili i trybu failover jest inicjowane, 1 sekundę najnowszych przejścia nie zostaną zapisane. 

Do mierzenia opóźnienie w odniesieniu do zmiany w podstawowej bazie danych, które zostały zastosowane na serwerze pomocniczym, czyli dostępne do odczytu z lokacji dodatkowej, porównaj *last_commit* czasu w pomocniczej bazie danych o tej samej wartości na serwerze podstawowym Baza danych.

> [!NOTE]
> Czasami *replication_lag_sec* na podstawowej bazy danych ma wartość NULL, oznacza to, czy podstawowy nie obecnie wie, jak daleko jest pomocnicza.   Zwykle dzieje się tak po ponownym uruchomieniu procesu i powinien być stan przejściowy. Należy wziąć pod uwagę alerty aplikacji, jeśli *replication_lag_sec* zwraca wartość NULL przez dłuższy czas. Może wskazywać, że pomocniczej bazy danych nie może komunikować się z podstawowym z powodu błędu połączenia trwałe. Dostępne są także warunki, które może spowodować, że różnica między *last_commit* czas na pomocniczym i podstawowej bazy danych, aby stać się duże. Na przykład Jeśli zatwierdzenie jest na serwerze podstawowym po długim czasie bez zmian, różnica korzystać z maksymalnie duża wartość przed zwróceniem szybko w 0. Należy wziąć pod uwagę jej warunek błędu podczas różnicę między tymi dwiema wartościami pozostaje dużych przez długi czas.


## <a name="programmatically-managing-active-geo-replication"></a>Programowe zarządzanie aktywnej replikacji geograficznej

Jak wspomniano wcześniej, aktywną replikację geograficzną można również zarządzać programowo przy użyciu programu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw poleceń dostępnych. Aktywna replikacja geograficzna zawiera zestaw interfejsów API usługi Azure Resource Manager do zarządzania, w tym [interfejs API REST usługi Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) i [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Te interfejsy API korzystają z grup zasobów i obsługuje zabezpieczenia oparte na rolach (RBAC). Aby uzyskać więcej informacji o tym, jak można zaimplementować ról dostępu, zobacz [kontroli dostępu](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Zarządzaj trybem failover jednym i puli baz danych

> [!IMPORTANT]
> Te polecenia języka Transact-SQL tylko dotyczą aktywnej replikacji geograficznej i nie są stosowane do grupy trybu failover. Jako takie również nie mają zastosowania do wystąpienia zarządzanego, ponieważ obsługują one tylko grupy trybu failover.

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Aby utworzyć pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych należy użyć argumentu dodawania serwera POMOCNICZEGO w |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Użyj trybu FAILOVER lub FORCE_FAILOVER_ALLOW_DATA_LOSS, aby przełączyć pomocniczej bazy danych jako główną w celu zainicjowania trybu failover |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Usuń serwer POMOCNICZY ON umożliwia zakończenie replikacji danych między bazą danych SQL i wybraną pomocniczą bazą danych. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Zwraca informacje o wszystkich istniejących linków replikacji dla każdej bazy danych na serwerze usługi Azure SQL Database. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Pobiera czas ostatniej replikacji, ostatnim opóźnienie replikacji i inne informacje na temat łącze replikacji dla danej bazy danych SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Wyświetla stan dla wszystkich operacji bazy danych, w tym stan łącza replikacji. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |powoduje, że aplikacja poczekaj, aż wszystkie zatwierdzone transakcje są replikowane i potwierdzone przez aktywnej pomocniczej bazy danych. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>Program PowerShell: Zarządzaj trybem failover jednym i puli baz danych

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

| Polecenia cmdlet | Opis |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Przerywa replikację danych między bazą danych SQL Database i wybraną pomocniczą bazą danych. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Pobiera linki replikacji geograficznej między bazą danych Azure SQL Database i grupą zasobów lub programem SQL Server. |
|  | |

> [!IMPORTANT]
> Przykładowe skrypty można zobaczyć [Konfiguruj i pracy awaryjnej pojedynczej bazy danych przy użyciu aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) i [Konfiguruj i pracy awaryjnej bazy danych w puli przy użyciu aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>INTERFEJS API REST: Zarządzaj trybem failover jednym i puli baz danych

| interfejs API | Opis |
| --- | --- |
| [Tworzenie lub aktualizacja bazy danych (createMode = przywracanie)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Tworzy, aktualizuje lub przywrócenie podstawowej lub pomocniczej bazy danych. |
| [Pobierz Utwórz lub zaktualizuj stan bazy danych](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Zwraca stan podczas operacji tworzenia. |
| [Ustaw pomocniczej bazy danych jako podstawowy (planowanego trybu Failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Zestawy, które pomocniczej bazy danych jest kluczem podstawowym, przechodzenie w tryb failover z podstawowej bazy danych. **Ta opcja nie jest obsługiwana dla wystąpienia zarządzanego.**|
| [Ustaw pomocniczej bazy danych jako podstawowy (nieplanowany tryb Failover)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Zestawy, które pomocniczej bazy danych jest kluczem podstawowym, przechodzenie w tryb failover z podstawowej bazy danych. Ta operacja może spowodować utratę danych. **Ta opcja nie jest obsługiwana dla wystąpienia zarządzanego.**|
| [Uzyskaj Link replikacji](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Pobiera łącze replikacji dla danej bazy danych SQL w partnerstwie replikacji geograficznej. Pobiera informacje widoczne w widoku wykazu sys.geo_replication_links. **Ta opcja nie jest obsługiwana dla wystąpienia zarządzanego.**|
| [Łącza replikacji — lista przez bazę danych](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Pobiera wszystkie łącza replikacji dla danej bazy danych SQL w partnerstwie replikacji geograficznej. Pobiera informacje widoczne w widoku wykazu sys.geo_replication_links. |
| [Usuń Link replikacji](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Usuwa link replikacji bazy danych. Nie można wykonać podczas pracy awaryjnej. |
|  | |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przykładowe skrypty Zobacz:
  - [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database obsługuje także grupy automatyczny tryb failover. Aby uzyskać więcej informacji, zobacz przy użyciu [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).
- Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do odzyskania, zobacz [przywrócić bazę danych z kopii zapasowych zainicjowanych przez usługę](sql-database-recovery-using-backups.md).
- Aby dowiedzieć się o wymagania dotyczące uwierzytelniania dla nowym serwerem podstawowym i bazy danych, zobacz [zabezpieczeń bazy danych SQL Database po awarii](sql-database-geo-replication-security-config.md).
