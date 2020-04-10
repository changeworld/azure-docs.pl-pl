---
title: Informacje o wersji
description: Dowiedz się więcej o nowych funkcjach i ulepszeniach w usłudze Azure SQL Database oraz w dokumentacji bazy danych SQL Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: sstein
ms.openlocfilehash: b677fd7fe2b14e1c42443478a887ddfa2481dfbf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011450"
---
# <a name="sql-database-release-notes"></a>Informacje o wersji bazy danych SQL

W tym artykule wymieniono funkcje bazy danych SQL, które są obecnie w publicznej wersji zapoznawczej. Aby uzyskać informacje o aktualizacjach i ulepszeniach bazy danych SQL, zobacz [aktualizacje usługi bazy danych SQL](https://azure.microsoft.com/updates/?product=sql-database). Aby uzyskać aktualizacje i ulepszenia innych usług platformy Azure, zobacz [Aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkcje w publicznej wersji zapoznawczej

### <a name="single-database"></a>[Pojedyncza baza danych](#tab/single-database)

| Funkcja | Szczegóły |
| ---| --- |
| Nowe generacje sprzętu serii Fsv2 i M| Aby uzyskać więcej informacji, zobacz [Generacje sprzętu](sql-database-service-tiers-vcore.md#hardware-generations).|
| Przyspieszone odzyskiwanie bazy danych z pojedynczymi bazami danych i elastycznymi pulami | Aby uzyskać więcej informacji, zobacz [Przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md).|
|Przybliżona liczba odrębna|Aby uzyskać więcej informacji, zobacz [Przybliżona liczba odrębna](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Tryb wsadowy w rowstore (na poziomie zgodności 150)|Aby uzyskać więcej informacji, zobacz [Tryb wsadowy w Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Odnajdowanie i klasyfikacja danych  |Aby uzyskać więcej informacji, zobacz [Klasyfikacja & odnajdowania danych usługi Azure SQL Database i SQL Data Warehouse](sql-database-data-discovery-and-classification.md).|
| Zadania elastycznych baz danych | Aby uzyskać więcej informacji, zobacz [Tworzenie, konfigurowanie i zarządzanie zadaniami elastycznymi](elastic-jobs-overview.md). |
| Zapytania elastyczne | Aby uzyskać więcej informacji, zobacz [omówienie kwerendy elastycznej](sql-database-elastic-query-overview.md). |
| Transakcje elastyczne | [Transakcje rozproszone w bazach danych w chmurze](sql-database-elastic-transactions-overview.md). |
|Informacje zwrotne o przyznaniu pamięci (tryb wiersza) (na poziomie zgodności 150)|Aby uzyskać więcej informacji, zobacz [Informacje zwrotne dotyczące przyznania pamięci (tryb wiersza)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Edytor zapytań w witrynie Azure portal |Aby uzyskać więcej informacji, zobacz Łączenie i wykonywanie zapytań o dane za [pomocą edytora zapytań SQL w witrynie Azure portal.](sql-database-connect-query-portal.md)|
| Usługi R / uczenie maszynowe z pojedynczymi bazami danych i elastycznymi pulami |Aby uzyskać więcej informacji, zobacz [Usługi uczenia maszynowego w bazie danych SQL usługi Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analiza SQL|Aby uzyskać więcej informacji, zobacz [Usługa Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Kompilacja odroczona zmiennej tabeli (na poziomie zgodności 150)|Aby uzyskać więcej informacji, zobacz [Tabela zmiennej odroczonej kompilacji](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

| Funkcja | Szczegóły |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pule wystąpień</a> | Wygodny i ekonomiczny sposób migracji mniejszych wystąpień SQL do chmury. |
| <a href="https://aka.ms/managed-instance-aadlogins">Podmioty serwera usługi Azure AD na poziomie wystąpienia (loginy)</a> | Tworzenie logowania na poziomie serwera przy użyciu <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">instrukcji CREATE LOGIN from EXTERNAL PROVIDER.</a> |
| [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md) | Replikuj zmiany z tabel do innych baz danych umieszczonych w wystąpieniach zarządzanych, pojedynczych bazach danych lub wystąpieniach programu SQL Server lub aktualizuj tabele, gdy niektóre wiersze zostaną zmienione w innych wystąpieniach zarządzanych lub wystąpieniu programu SQL Server. Aby uzyskać informacje, zobacz [Konfigurowanie replikacji w bazie danych zarządzanych wystąpień usługi Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Wykrywanie zagrożeń |Aby uzyskać informacje, zobacz [Konfigurowanie wykrywania zagrożeń w wystąpieniu zarządzanym usługi Azure SQL Database](sql-database-managed-instance-threat-detection.md).|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Wystąpienie zarządzane — nowe funkcje i znane problemy

### <a name="managed-instance-h2-2019-updates"></a>Aktualizacje wystąpienia zarządzanego H2 2019

- [Konfiguracja podsieci wspomaganej usługą](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Bezpieczny i wygodny sposób zarządzania konfiguracją podsieci, w której można kontrolować ruch danych podczas wystąpienia zarządzanego, zapewnia nieprzerwany przepływ ruchu zarządzania
- [Przejrzyste szyfrowanie danych (TDE) z bring your own key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) umożliwia scenariusz bring-your-own-key (BYOK) dla ochrony danych w spoczynku i pozwala organizacjom na oddzielenie obowiązków zarządzania kluczami i danymi.
- [Grupy automatycznego trybu failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umożliwiają replikowanie wszystkich baz danych z wystąpienia podstawowego do wystąpienia pomocniczego w innym regionie.
- Skonfiguruj zachowanie wystąpienia zarządzanego za pomocą [globalnych flag śledzenia](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aktualizacje wystąpienia zarządzanego H1 2019

Następujące funkcje są włączone w modelu wdrażania wystąpienia zarządzanego w H1 2019:
  - Obsługa subskrypcji z <a href="https://aka.ms/sql-mi-visual-studio-subscribers">miesięcznym kredytem platformy Azure dla subskrybentów programu Visual Studio</a> i [zwiększonymi limitami regionalnymi.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
  - Obsługa <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">programów SharePoint 2016 i SharePoint 2019</a> oraz <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">usługi Dynamics 365 Business Central</a>
  - Tworzenie wystąpień z <a href="https://aka.ms/managed-instance-collation">sortowanie na poziomie serwera</a> i <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">strefy czasowej</a> do wyboru.
  - Wystąpienia zarządzane są teraz chronione za pomocą <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">wbudowanej zapory</a>.
  - Skonfiguruj wystąpienia do używania [publicznych punktów końcowych,](sql-database-managed-instance-public-endpoint-configure.md) [zastępowanie](sql-database-connectivity-architecture.md#connection-policy) połączenia przez serwer proxy w celu uzyskania lepszej wydajności sieci, <a href="https://aka.ms/four-cores-sql-mi-update">4 pola wirtualne w generacji sprzętu Gen5</a> lub <a href="https://aka.ms/managed-instance-configurable-backup-retention">konfigurowanie przechowywania kopii zapasowych do 35 dni</a> przywracania w czasie. Długoterminowe przechowywanie kopii zapasowych (do 10 lat) nadal nie jest włączone, więc można użyć <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">kopii zapasowych tylko do kopiowania</a> jako alternatywy.
  - Nowe funkcje umożliwiają <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geo-przywracanie bazy danych do innego centrum danych za pomocą programu PowerShell</a>, [zmiana nazwy bazy danych](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), usuwanie [klastra wirtualnego](sql-database-managed-instance-delete-virtual-cluster.md).
  - Nowa [wbudowana rola współautora wystąpienia](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) umożliwia rozdzielenie obowiązków (SoD) zgodnie z zasadami zabezpieczeń i zgodności ze standardami przedsiębiorstwa.
  - Wystąpienie zarządzane jest dostępne w następujących regionach usługi Azure Government do ga (US Gov Texas, US Gov Arizona), a także w Chinach North 2 i China East 2. Jest również dostępny w następujących regionach publicznych: Australia Central, Australia Central 2, Brazylia Południowa, Francja Południowa, ZJEDNOCZONE EMIRATY CENTRALNE, ZJEDNOCZONE EMIRATY PÓŁNOCNE, REPUBLIKA POŁUDNIOWEJ AFRYKI Północ, Republika Południowej Afryki Zachód.

### <a name="known-issues"></a>Znane problemy

|Problem  |Data odnaleziona  |Stan  |Data rozwiązana  |
|---------|---------|---------|---------|
|[Uprawnienia do grupy zasobów niestosowano do wystąpienia zarządzanego](#permissions-on-resource-group-not-applied-to-managed-instance)|Luty 2020 r.|Ma obejście||
|[Ograniczenie ręcznego pracy awaryjnej za pośrednictwem portalu dla grup trybu failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Styczeń 2020 r.|Ma obejście||
|[Role agenta SQL wymagają jawnych uprawnień EXECUTE dla logowania innych niż sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Grudzień 2019 r.|Ma obejście||
|[Zadania programu SQL Agent mogą zostać przerwane przez ponowne uruchomienie procesu agenta](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Grudzień 2019 r.|Brak obejścia|Mar 2020|
|[Loga AAD i użytkownicy nie są obsługiwani w SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Listopad 2019 r.|Brak obejścia||
|[Limity pamięci OLTP w pamięci nie są stosowane](#in-memory-oltp-memory-limits-are-not-applied)|Październik 2019 r.|Ma obejście||
|[Nieprawidłowy błąd zwrócony podczas próby usunięcia pliku, który nie jest pusty](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Październik 2019 r.|Ma obejście||
|[Zmienianie warstwy usługi i tworzenie operacji wystąpienia są blokowane przez trwające przywracanie bazy danych](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Wrz 2019|Ma obejście||
|[Gubernator zasobów w warstwie usług o krytycznym znaczeniu dla firmy może wymagać ponownej konfiguracji po przemijaniu awaryjnym](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Wrz 2019|Ma obejście||
|[Okna dialogowe brokera usług między bazami danych muszą zostać ponownie zainicjowane po uaktualnieniu warstwy usług](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Sierpień 2019 r.|Ma obejście||
|[Bezosobiłwienie typów logowania usługi Azure AD nie jest obsługiwane](#impersonification-of-azure-ad-login-types-is-not-supported)|Lipiec 2019|Brak obejścia||
|[@queryparametr nie jest obsługiwany w sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Kwiecień 2019|Brak obejścia||
|[Replikacja transakcyjna musi zostać ponownie skonfigurowana po przemijeniu geograficznym w błąd](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Brak obejścia||
|[Tymczasowa baza danych jest używana podczas operacji PRZYWRACANIE](#temporary-database-is-used-during-restore-operation)||Ma obejście||
|[Struktura i zawartość TEMPDB są ponownie tworzone](#tempdb-structure-and-content-is-re-created)||Brak obejścia||
|[Przekroczenie przestrzeni dyskowej za pomocą małych plików bazy danych](#exceeding-storage-space-with-small-database-files)||Ma obejście||
|[Wartości identyfikatorów GUID wyświetlane zamiast nazw baz danych](#guid-values-shown-instead-of-database-names)||Ma obejście||
|[Dzienniki błędów nie są zachowywane](#error-logs-arent-persisted)||Brak obejścia||
|[Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Ma obejście||
|[Moduły CLR i serwery połączone czasami nie mogą odwoływać się do lokalnego adresu IP](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Ma obejście||
|Spójność bazy danych nie została zweryfikowana przy użyciu bazy danych DBCC CHECKDB po przywróceniu bazy danych z usługi Azure Blob Storage.||Resolved|Listopad 2019 r.|
|Przywracanie bazy danych w czasie z warstwy Krytyczne dla firmy do warstwy ogólnego przeznaczenia nie powiedzie się, jeśli źródłowa baza danych zawiera obiekty OLTP w pamięci.||Resolved|Październik 2019 r.|
|Funkcja Poczty bazy danych z zewnętrznymi serwerami poczty (nienawiązanego z platformą Azure) przy użyciu bezpiecznego połączenia||Resolved|Październik 2019 r.|
|Zawarte bazy danych nie są obsługiwane w wystąpieniu zarządzanym||Resolved|Sierpień 2019 r.|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Uprawnienia do grupy zasobów niestosowano do wystąpienia zarządzanego

Rola RBAC współautora wystąpienia zarządzanego po zastosowaniu do grupy zasobów (RG) nie jest stosowana do wystąpienia zarządzanego i nie ma wpływu.

**Obejście:** Konfigurowanie roli współautora wystąpienia zarządzanego dla użytkowników na poziomie subskrypcji.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Ograniczenie ręcznego pracy awaryjnej za pośrednictwem portalu dla grup trybu failover

Jeśli grupa trybu failover obejmuje wystąpienia w różnych subskrypcjach platformy Azure lub grupach zasobów, ręczne tryb failover nie może być zainicjowany z wystąpienia podstawowego w grupie trybu failover.

**Obejście:** Inicjowanie pracy awaryjnej za pośrednictwem portalu z wystąpienia pomocniczego geo pomocniczego.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL wymagają jawnych uprawnień EXECUTE dla logowania innych niż sysadmin

Jeśli nie-sysadmin logowania są dodawane do dowolnego [programu SQL Agent stałych ról bazy danych,](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)istnieje problem, w którym jawne uprawnienia EXECUTE muszą być przyznane do wzorca procedur przechowywanych dla tych logowania do pracy. Jeśli wystąpi ten problem, zostanie wyświetlony komunikat o błędzie "Uprawnienie EXECUTE zostało odrzucone w obiekcie <object_name> (Microsoft SQL Server, Error: 229)".

**Obejście:** Po dodaniu logowania do jednej z ról stałej bazy danych programu SQL Agent: SQLAgentUserRole, SQLAgentReaderRole lub SQLAgentOperatorRole, dla każdego logowania dodanego do tych ról wykonać poniższy skrypt T-SQL jawnie udzielić uprawnień EXECUTE do wymienionych procedur przechowywanych.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Zadania programu SQL Agent mogą zostać przerwane przez ponowne uruchomienie procesu agenta

SQL Agent tworzy nową sesję przy każdym uruchomieniu zadania, stopniowo zwiększając zużycie pamięci. Aby uniknąć przekroczenie limitu pamięci wewnętrznej, która zablokuje wykonywanie zaplanowanych zadań, proces agenta zostanie uruchomiony ponownie, gdy jego zużycie pamięci osiągnie próg. Może to spowodować przerwanie wykonywania zadań uruchomionych w momencie ponownego uruchomienia.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity pamięci OLTP w pamięci nie są stosowane

Warstwa usługi o znaczeniu krytycznym dla firmy nie spowoduje prawidłowego zastosowania [maksymalnych limitów pamięci dla obiektów zoptymalizowanych pod kątem pamięci](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) w niektórych przypadkach. Wystąpienie zarządzane może włączyć obciążenia do korzystania z większej ilości pamięci dla operacji OLTP w pamięci, co może mieć wpływ na dostępność i stabilność wystąpienia. W pamięci zapytania OLTP, które osiągają limity nie może nie zakończyć się natychmiast. Ten problem zostanie wkrótce rozwiązany. Kwerendy, które używają więcej pamięci OLTP w pamięci, nie powiedzie się wcześniej, jeśli osiągną [limity.](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)

**Obejście:** [Monitorowanie użycia magazynu OLTP w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) przy użyciu programu SQL Server Management [Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) w celu upewnienia się, że obciążenie nie używa więcej niż dostępnej pamięci. Zwiększ limity pamięci, które zależą od liczby korpów wirtualnych, lub zoptymalizuj obciążenie, aby zużywać mniej pamięci.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Nieprawidłowy błąd zwrócony podczas próby usunięcia pliku, który nie jest pusty

SQL Server/Wystąpienie zarządzane [nie zezwala użytkownikowi na upuszczenie pliku, który nie jest pusty](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Jeśli spróbujesz usunąć niepusty `ALTER DATABASE REMOVE FILE` plik danych `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` przy użyciu instrukcji, błąd nie zostanie natychmiast zwrócony. Wystąpienie zarządzane będzie próbować upuścić plik, a operacja zakończy `Internal server error`się niepowodzeniem po 30 min z .

**Obejście**: Usuń zawartość pliku `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` za pomocą polecenia. Jeśli jest to jedyny plik w grupie plików, należy usunąć dane z tabeli lub partycji skojarzonej z tą grupą plików przed zmniejszeniem pliku i opcjonalnie załadować te dane do innej tabeli/partycji.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Zmienianie warstwy usługi i tworzenie operacji wystąpienia są blokowane przez trwające przywracanie bazy danych

Bieżące instrukcje, `RESTORE` proces migracji usługi migracji danych i wbudowane przywracanie czasu w punkcie zablokuje aktualizowanie warstwy usług lub zmieni rozmiar istniejącego wystąpienia i tworzenie nowych wystąpień do czasu zakończenia procesu przywracania. Proces przywracania zablokuje te operacje w pulach zarządzanych wystąpień i wystąpień w tej samej podsieci, w której jest uruchomiony proces przywracania. Nie ma to wpływu na wystąpienia w pulach wystąpień. Tworzenie lub zmiana operacji warstwy usług nie zakończy się niepowodzeniem lub limit czasu — będą one kontynuowane po zakończeniu lub anulowaniu procesu przywracania.

**Obejście:** Poczekaj, aż proces przywracania zakończy się lub anuluj proces przywracania, jeśli operacja tworzenia lub aktualizacji warstwy usługi ma wyższy priorytet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Gubernator zasobów w warstwie usług o krytycznym znaczeniu dla firmy może wymagać ponownej konfiguracji po przemijaniu awaryjnym

[Funkcja Resource Governor,](/sql/relational-databases/resource-governor/resource-governor) która umożliwia ograniczenie zasobów przypisanych do obciążenia użytkownika może niepoprawnie klasyfikować niektóre obciążenia użytkownika po przejściu w pracę awaryjną lub zainicjowanej przez użytkownika zmianie warstwy usług (na przykład zmiana maksymalnego rozmiaru magazynu vCore lub maksymalnego rozmiaru magazynu wystąpienia).

**Obejście:** Uruchamiaj `ALTER RESOURCE GOVERNOR RECONFIGURE` okresowo lub jako część zadania agenta SQL, które wykonuje zadanie SQL po uruchomieniu wystąpienia, jeśli używasz [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Okna dialogowe brokera usług między bazami danych muszą zostać ponownie zainicjowane po uaktualnieniu warstwy usług

Między bazami danych Service Broker dialogi zatrzyma dostarczanie wiadomości do usług w innych bazach danych po zmianie operacji warstwy usług. Wiadomości nie zostaną **utracone** i można je znaleźć w kolejce nadawcy. Każda zmiana pola wirtualnego lub rozmiar magazynu wystąpień w wystąpieniu zarządzanym spowoduje `service_broke_guid` zmianę wartości w widoku [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) dla wszystkich baz danych. Każdy `DIALOG` utworzony przy użyciu [instrukcji BEGIN DIALOG,](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) która odwołuje się do service brokerów w innej bazie danych zatrzyma dostarczanie wiadomości do usługi docelowej.

**Obejście:** Zatrzymaj wszelkie działania, które używa między bazami danych Service Broker konwersacji dialogowych przed aktualizacją warstwy usługi i ponownie zainicjować je po. Jeśli istnieją pozostałe wiadomości, które są niedostarczone po zmianie warstwy usługi, przeczytaj wiadomości z kolejki źródłowej i ponownie wyśmiać je do kolejki docelowej.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Bezosobiłwienie typów logowania usługi Azure AD nie jest obsługiwane

Personifikacja przy użyciu `EXECUTE AS USER` lub `EXECUTE AS LOGIN` następujących podmiotów AAD nie jest obsługiwana:
-   Aliased użytkowników AAD. W takim przypadku `15517`zwracany jest następujący błąd.
- Logowania AAD i użytkowników na podstawie aplikacji AAD lub podmiotów świadczących usługi. Następujące błędy są zwracane `15517` w `15406`tym przypadku i .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametr nie jest obsługiwany w sp_send_db_mail

Parametr `@query` w procedurze [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) nie działa.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Replikacja transakcyjna musi zostać ponownie skonfigurowana po przemijeniu geograficznym w błąd

Jeśli replikacja transakcyjna jest włączona w bazie danych w grupie automatycznego trybu failover, administrator wystąpienia zarządzanego musi oczyścić wszystkie publikacje na starym podstawowym i ponownie skonfigurować je w nowej podstawowej po przejściu w tryb failover do innego regionu. Zobacz [Replikacja,](sql-database-managed-instance-transact-sql-information.md#replication) aby uzyskać więcej informacji.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Loga AAD i użytkownicy nie są obsługiwani w SSDT

Narzędzia SQL Server Data Tools nie obsługują w pełni logowania i użytkowników usługi Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tymczasowa baza danych jest używana podczas operacji PRZYWRACANIE

Gdy baza danych jest przywracana na wystąpienie zarządzane, usługa przywracania najpierw utworzy pustą bazę danych o żądanej nazwie, aby przydzielić nazwę w wystąpieniu. Po pewnym czasie ta baza danych zostanie porzucona i zostanie uruchomione przywracanie rzeczywistej bazy danych. Baza danych, która jest w stan *przywracania* będzie tymczasowy mają losową wartość GUID zamiast nazwy. Tymczasowa nazwa zostanie zmieniona na `RESTORE` żądaną nazwę określoną w instrukcji po zakończeniu procesu przywracania. W początkowej fazie użytkownik może uzyskać dostęp do pustej bazy danych, a nawet utworzyć tabele lub załadować dane w tej bazie danych. Ta tymczasowa baza danych zostanie porzucona po uruchomieniu usługi przywracania w drugiej fazie.

**Obejście:** Nie należy uzyskiwać dostępu do przywracania bazy danych, dopóki nie zostanie wyświetlony, że przywracanie zostanie zakończone.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura i zawartość TEMPDB są ponownie tworzone

Baza `tempdb` danych jest zawsze podzielona na 12 plików danych i nie można zmienić struktury plików. Nie można zmienić maksymalnego rozmiaru na plik i `tempdb`nie można dodać nowych plików do programu . `Tempdb`jest zawsze ponownie utworzony jako pusta baza danych, gdy wystąpienie `tempdb` rozpoczyna się lub kończy się niepowodzeniem, a wszelkie zmiany wprowadzone w nie zostaną zachowane.

### <a name="exceeding-storage-space-with-small-database-files"></a>Przekroczenie przestrzeni dyskowej za pomocą małych plików bazy danych

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`i `RESTORE DATABASE` instrukcje mogą zakończyć się niepowodzeniem, ponieważ wystąpienie może osiągnąć limit usługi Azure Storage.

Każde wystąpienie zarządzane ogólnego przeznaczenia ma maksymalnie 35 TB miejsca na dysku premium platformy Azure. Każdy plik bazy danych jest umieszczany na osobnym dysku fizycznym. Rozmiary dysków mogą mieć rozmiary 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Nieużywane miejsce na dysku nie jest naliczane, ale całkowita suma rozmiarów dysków w usłudze Azure Premium nie może przekraczać 35 TB. W niektórych przypadkach wystąpienie zarządzane, które nie wymaga 8 TB w sumie może przekroczyć limit azure 35 TB na rozmiar magazynu z powodu fragmentacji wewnętrznej.

Na przykład wystąpienie zarządzane ogólnego przeznaczenia może mieć jeden duży plik o rozmiarze 1,2 TB umieszczony na dysku o pojemności 4 TB. Może również mieć 248 plików o rozmiarze 1 GB, które są umieszczane na oddzielnych dyskach 128 GB. W tym przykładzie:

- Całkowity przydzielony rozmiar magazynu dysku wynosi 1 x 4 TB + 248 x 128 GB = 35 TB.
- Całkowita ilość zarezerwowanego miejsca dla baz danych w wystąpieniu wynosi 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

W tym przykładzie pokazano, że w pewnych okolicznościach, ze względu na określoną dystrybucję plików, wystąpienie zarządzane może osiągnąć limit 35 TB, który jest zarezerwowany dla dołączonego dysku premium azure, gdy nie można się tego spodziewać.

W tym przykładzie istniejące bazy danych nadal działają i mogą rosnąć bez problemu, o ile nowe pliki nie są dodawane. Nie można utworzyć ani przywrócić nowych baz danych, ponieważ nie ma wystarczającej ilości miejsca na nowe dyski, nawet jeśli całkowity rozmiar wszystkich baz danych nie osiągnie limitu rozmiaru wystąpienia. Błąd, który jest zwracany w tym przypadku nie jest jasne.

Liczbę [pozostałych plików](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) można zidentyfikować za pomocą widoków systemowych. Jeśli osiągniesz ten limit, spróbuj [opróżnić i usunąć niektóre mniejsze pliki za pomocą instrukcji DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) lub przełączyć się do [warstwy Krytyczna dla firmy, która nie ma tego limitu.](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)

### <a name="guid-values-shown-instead-of-database-names"></a>Wartości identyfikatorów GUID wyświetlane zamiast nazw baz danych

Kilka widoków systemowych, liczniki wydajności, komunikaty o błędach, XEvents i wpisy dziennika błędów wyświetlają identyfikatory baz danych GUID zamiast rzeczywistych nazw baz danych. Nie należy polegać na tych identyfikatorów GUID, ponieważ są one zastępowane rzeczywistymi nazwami baz danych w przyszłości.

**Obejście**: Użyj widoku sys.databases, aby rozpoznać rzeczywistą nazwę bazy danych z nazwy fizycznej bazy danych, określonej w postaci identyfikatorów bazy danych GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Dzienniki błędów nie są zachowywane

Dzienniki błędów, które są dostępne w wystąpieniu zarządzanym nie są zachowywane, a ich rozmiar nie jest uwzględniony w maksymalnym limicie magazynu. Dzienniki błędów mogą zostać automatycznie usunięte w przypadku pracy awaryjnej. Mogą istnieć luki w historii dziennika błędów, ponieważ wystąpienie zarządzane zostało przeniesione kilka razy na kilku maszynach wirtualnych.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany

Klasa `TransactionScope` w .NET nie działa, jeśli dwa zapytania są wysyłane do dwóch baz danych w tym samym wystąpieniu w tym samym zakresie transakcji:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Mimo że ten kod działa z danymi w tym samym wystąpieniu, wymaga msdtc.

**Obejście:** Użyj [SqlConnection.ChangeDatabase(String),](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) aby użyć innej bazy danych w kontekście połączenia zamiast używać dwóch połączeń.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduły CLR i serwery połączone czasami nie mogą odwoływać się do lokalnego adresu IP

Moduły CLR umieszczone w wystąpieniu zarządzanym i połączonych serwerach lub kwerendach rozproszonych, które odwołują się do bieżącego wystąpienia czasami nie mogą rozpoznać adresu IP wystąpienia lokalnego. Ten błąd jest przejściowy problem.

**Obejście:** W miarę możliwości należy używać połączeń kontekstowych w module CLR.

## <a name="updates"></a>Aktualizacje

Aby uzyskać listę aktualizacji i ulepszeń bazy danych SQL, zobacz [aktualizacje usługi bazy danych SQL](https://azure.microsoft.com/updates/?product=sql-database).

Aby uzyskać aktualizacje i ulepszenia wszystkich usług platformy Azure, zobacz [Aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Współtworzenie zawartości

Aby udostępnić dokumentację usługi Azure SQL Database, zobacz [Przewodnik po współautorze dokumentów](https://docs.microsoft.com/contribute/).
