---
title: Elastyczne zapytanie — omówienie
description: Elastyczna kwerenda umożliwia uruchomienie kwerendy Transact-SQL, która obejmuje wiele baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: 827fab0661a58bfa7d28452960ea6df64d18bf84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873747"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Omówienie elastycznych zapytań usługi Azure SQL Database (wersja zapoznawcza)

Funkcja zapytania elastycznego (w wersji zapoznawczej) umożliwia uruchomienie kwerendy Transact-SQL, która obejmuje wiele baz danych w bazie danych SQL azure. Umożliwia wykonywanie zapytań między bazami danych w celu uzyskania dostępu do tabel zdalnych oraz łączenie narzędzi firmy Microsoft i innych firm (Excel, Power BI, Tableau itp.) w celu wykonywania zapytań między warstwami danych z wieloma bazami danych. Za pomocą tej funkcji można skalować kwerendy w poziomie do dużych warstw danych w bazie danych SQL i wizualizować wyniki w raportach analizy biznesowej (BI).

## <a name="why-use-elastic-queries"></a>Dlaczego warto korzystać z zapytań elastycznych

### <a name="azure-sql-database"></a>Azure SQL Database

Kwerenda w bazach danych SQL platformy Azure całkowicie w języku T-SQL. Umożliwia to wykonywanie zapytań tylko do odczytu zdalnych baz danych i zapewnia opcję dla bieżących lokalnych klientów programu SQL Server do migracji aplikacji przy użyciu nazw trzy- i czteroczęściowych lub połączonego serwera do bazy danych SQL.

### <a name="available-on-standard-tier"></a>Dostępne na poziomie standardowym

Elastyczne zapytanie jest obsługiwane zarówno w warstwach usługi Standardowa, jak i Premium. Zobacz sekcję ograniczenia w wersji zapoznawczej poniżej dotyczące ograniczeń wydajności dla niższych warstw usług.

### <a name="push-parameters-to-remote-databases"></a>Parametry wypychania do zdalnych baz danych

Zapytania elastyczne mogą teraz wypychać parametry SQL do zdalnych baz danych w celu wykonania.

### <a name="stored-procedure-execution"></a>Wykonanie procedury składowanej

Wykonywanie zdalnych wywołań procedur składowanych lub zdalnych funkcji za pomocą [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Elastyczność

Tabele zewnętrzne z kwerendą elastyczną mogą odwoływać się do tabel zdalnych o innej nazwie schematu lub tabeli.

## <a name="elastic-query-scenarios"></a>Elastyczne scenariusze zapytań

Celem jest ułatwienie wykonywania zapytań scenariuszy, w których wiele baz danych współtworzy wiersze w jednym wyniku ogólnym. Kwerenda może składać się bezpośrednio przez użytkownika lub aplikację lub pośrednio za pomocą narzędzi połączonych z bazą danych. Jest to szczególnie przydatne podczas tworzenia raportów, przy użyciu komercyjnych narzędzi bi lub integracji danych lub dowolnej aplikacji, których nie można zmienić. Za pomocą zapytania elastycznego można wysyłać zapytania w kilku bazach danych przy użyciu znanego środowiska łączności programu SQL Server w narzędziach takich jak Excel, Power BI, Tableau lub Cognos.
Zapytanie elastyczne umożliwia łatwy dostęp do całej kolekcji baz danych za pośrednictwem zapytań wydanych przez SQL Server Management Studio lub Visual Studio i ułatwia wykonywanie zapytań między bazami danych z entity framework lub innych środowisk ORM. Rysunek 1 przedstawia scenariusz, w którym istniejąca aplikacja w chmurze (która używa [biblioteki klienta elastycznej bazy danych)](sql-database-elastic-database-client-library.md)tworzy na warstwie danych skalowane w poziomie, a zapytanie elastyczne jest używane do raportowania między bazami danych.

**Rysunek 1** Elastyczna kwerenda używana w warstwie danych skalowane w poziomie

![Elastyczna kwerenda używana w warstwie danych skalowane w poziomie][1]

Scenariusze klienta dla elastycznego zapytania charakteryzują się następującymi topologiami:

* **Partycjonowanie w pionie — kwerendy między bazami danych** (Topologia 1): Dane są podzielone pionowo między szereg baz danych w warstwie danych. Zazwyczaj różne zestawy tabel znajdują się w różnych bazach danych. Oznacza to, że schemat jest inny w różnych bazach danych. Na przykład wszystkie tabele zapasów znajdują się w jednej bazie danych, podczas gdy wszystkie tabele związane z księgowością znajdują się w drugiej bazie danych. Typowe przypadki użycia z tej topologii wymagają jednego do kwerendy w całej lub do kompilacji raportów między tabelami w kilku bazach danych.
* **Partycjonowanie poziome — dzielenie na fragmenty** (topologia 2): Dane są podzielone na partycje w poziomie w celu dystrybucji wierszy w warstwie danych skalowanych w poziomie. Dzięki takiemu podejściu schemat jest identyczny we wszystkich uczestniczących bazach danych. Takie podejście jest również nazywane "dzieleniem na fragmenty". Dzielenie na fragmenty można wykonać i zarządzać za pomocą (1) bibliotek narzędzi elastycznej bazy danych lub (2) samorozwędowy. Zapytanie elastyczne jest używany do kwerendy lub kompilacji raportów w wielu fragmentów. Fragmenty są zazwyczaj baz danych w puli elastycznej. Kwerenda elastyczna może być skuteczna jako skuteczny sposób wykonywania zapytań o wszystkie bazy danych puli elastycznej jednocześnie, o ile bazy danych współużytkują wspólny schemat.

> [!NOTE]
> Elastyczne zapytanie działa najlepiej w scenariuszach raportowania, w których większość przetwarzania (filtrowanie, agregacja) mogą być wykonywane po stronie źródła zewnętrznego. Nie nadaje się do operacji ETL, gdzie duża ilość danych jest przesyłana ze zdalnych baz danych. W przypadku dużych obciążeń raportowania lub scenariuszy magazynowania danych z bardziej złożonymi zapytaniami należy również rozważyć użycie [usługi Azure Synapse Analytics.](https://azure.microsoft.com/services/synapse-analytics)
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Partycjonowanie w pionie — kwerendy między bazami danych

Aby rozpocząć kodowanie, zobacz [Wprowadzenie do kwerendy między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).

Kwerenda elastyczna może służyć do udostępniania danych znajdujących się w bazie danych SQL innym bazom danych SQL. Dzięki temu kwerendy z jednej bazy danych odwoływać się do tabel w innej zdalnej bazy danych SQL. Pierwszym krokiem jest zdefiniowanie zewnętrznego źródła danych dla każdej zdalnej bazy danych. Zewnętrzne źródło danych jest zdefiniowane w lokalnej bazie danych, z której chcesz uzyskać dostęp do tabel znajdujących się w zdalnej bazie danych. Żadne zmiany nie są wymagane w zdalnej bazie danych. W typowych scenariuszach partycjonowania pionowego, w których różne bazy danych mają różne schematy, zapytania elastyczne mogą być używane do implementowania typowych przypadków użycia, takich jak dostęp do danych referencyjnych i wykonywanie zapytań między bazami danych.

> [!IMPORTANT]
> Musisz posiadać alter wszelkie zewnętrzne źródło danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE. ALTER wszelkie uprawnienia zewnętrznego źródła danych są potrzebne do odwoływania się do źródłowego źródła danych.
>

**Dane referencyjne**: Topologia jest używana do zarządzania danymi referencyjnymi. Na poniższym rysunku dwie tabele (T1 i T2) z danymi referencyjnymi są przechowywane w dedykowanej bazie danych. Za pomocą zapytania elastycznego można teraz uzyskać zdalny dostęp do tabel T1 i T2 z innych baz danych, jak pokazano na rysunku. Użyj topologii 1, jeśli tabele odwołań są małymi lub zdalnymi zapytaniami do tabeli referencyjnej, mają predykaty selektywne.

**Rysunek 2** Partycjonowanie w pionie — używanie zapytania elastycznego do wykonywania zapytań o dane referencyjne

![Partycjonowanie w pionie — używanie zapytania elastycznego do wykonywania zapytań o dane referencyjne][3]

**Wykonywanie zapytań między bazami danych:** zapytania elastyczne umożliwiają przypadki użycia, które wymagają wykonywania zapytań w kilku bazach danych SQL. Rysunek 3 przedstawia cztery różne bazy danych: CRM, Inventory, HR i Products. Kwerendy wykonywane w jednej z baz danych również potrzebują dostępu do jednej lub wszystkich innych baz danych. Za pomocą zapytania elastycznego można skonfigurować bazę danych dla tego przypadku, uruchamiając kilka prostych instrukcji DDL w każdej z czterech baz danych. Po tej konfiguracji jednorazowej dostęp do tabeli zdalnej jest tak prosty, jak odwoływanie się do tabeli lokalnej z zapytań T-SQL lub z narzędzi analizy biznesowej. Takie podejście jest zalecane, jeśli kwerendy zdalne nie zwracają dużych wyników.

**Rysunek 3** Partycjonowanie w pionie — używanie zapytania elastycznego do wykonywania zapytań w różnych bazach danych

![Partycjonowanie w pionie — używanie zapytania elastycznego do wykonywania zapytań w różnych bazach danych][4]

Następujące kroki konfigurują zapytania elastycznej bazy danych dla scenariuszy partycjonowania pionowego, które wymagają dostępu do tabeli znajdującej się w zdalnych bazach danych SQL o tym samym schemacie:

* [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [TWORZENIE POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [TWORZENIE/UPUSZCZANIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **RDBMS**
* [TWORZENIE/UPUSZCZANIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po uruchomieniu instrukcji DDL można uzyskać dostęp do tabeli zdalnej "mytable", tak jakby była to tabela lokalna. Usługa Azure SQL Database automatycznie otwiera połączenie ze zdalną bazą danych, przetwarza żądanie w zdalnej bazie danych i zwraca wyniki.

## <a name="horizontal-partitioning---sharding"></a>Partycjonowanie poziome — dzielenie na fragmenty

Za pomocą zapytania elastycznego do wykonywania zadań raportowania za pośrednictwem podzielonej na fragmenty, czyli poziomo podzielony na partycje, warstwa danych wymaga [elastycznej mapy niezależnego fragmentu bazy danych](sql-database-elastic-scale-shard-map-management.md) do reprezentowania baz danych warstwy danych. Zazwyczaj tylko pojedyncza mapa niezależnego fragmentu jest używana w tym scenariuszu, a dedykowana baza danych z możliwościami elastycznych zapytań (węzeł główny) służy jako punkt wejścia dla zapytań raportowania. Tylko ta dedykowana baza danych potrzebuje dostępu do mapy niezależnego fragmentu. Rysunek 4 ilustruje tę topologię i jej konfigurację za pomocą bazy danych zapytań elastycznych i mapy niezależnego fragmentu. Bazy danych w warstwie danych może być dowolnej wersji lub wersji usługi Azure SQL Database. Aby uzyskać więcej informacji na temat biblioteki klienta elastycznej bazy danych i tworzenia map niezależnego fragmentu, zobacz [Zarządzanie mapami niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).

**Rysunek 4** Partycjonowanie poziome — używanie elastycznej kwerendy do raportowania warstw danych podzielonych na fragmenty

![Partycjonowanie poziome — używanie elastycznej kwerendy do raportowania warstw danych podzielonych na fragmenty][5]

> [!NOTE]
> Elastyczna baza danych zapytań (węzeł główny) może być oddzielną bazą danych lub może być tą samą bazą danych, która obsługuje mapę niezależnego fragmentu.
> Niezależnie od konfiguracji wybierzesz, upewnij się, że warstwa usług i rozmiar obliczeń tej bazy danych jest wystarczająco wysoki, aby obsłużyć oczekiwaną ilość żądań logowania/kwerend.

Następujące kroki konfigurują zapytania elastycznej bazy danych dla scenariuszy partycjonowania poziomego, które wymagają dostępu do zestawu tabel znajdujących się na (zazwyczaj) kilku zdalnych bazach danych SQL:

* [TWORZENIE KLUCZA GŁÓWNEGO](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [TWORZENIE POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Utwórz [mapę niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) reprezentującą warstwę danych przy użyciu biblioteki klienta elastycznej bazy danych.
* [TWORZENIE/UPUSZCZANIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource typu **SHARD_MAP_MANAGER**
* [TWORZENIE/UPUSZCZANIE TABELI ZEWNĘTRZNEJ](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Po wykonaniu tych kroków można uzyskać dostęp do poziomo podzielonej na partycje tabeli "mytable", tak jakby była to tabela lokalna. Usługa Azure SQL Database automatycznie otwiera wiele połączeń równoległych do zdalnych baz danych, w których tabele są fizycznie przechowywane, przetwarza żądania w zdalnych bazach danych i zwraca wyniki.
Więcej informacji na temat kroków wymaganych dla scenariusza partycjonowania poziomego można znaleźć w [elastycznym zapytaniu do partycjonowania poziomego](sql-database-elastic-query-horizontal-partitioning.md).

Aby rozpocząć kodowanie, zobacz [Wprowadzenie do elastycznej kwerendy dla partycjonowania poziomego (dzielenia na fragmenty).](sql-database-elastic-query-getting-started.md)

> [!IMPORTANT]
> Pomyślne wykonanie zapytania elastycznego za pomocą dużego zestawu baz danych w dużej mierze opiera się na dostępności każdej z baz danych podczas wykonywania kwerendy. Jeśli jedna z baz danych nie jest dostępna, cała kwerenda zakończy się niepowodzeniem. Jeśli planujesz kwerendy setki lub tysiące baz danych na raz, upewnij się, że aplikacja kliencka ma logikę ponowić próbę osadzone lub należy rozważyć wykorzystanie [zadania elastycznej bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (wersja zapoznawcza) i kwerendy mniejszych podzbiorów baz danych, konsolidując wyniki każdego zapytania w jednym miejscu docelowym.

## <a name="t-sql-querying"></a>Kwerendy T-SQL

Po zdefiniowaniu zewnętrznych źródeł danych i tabel zewnętrznych można używać zwykłych ciągów połączeń programu SQL Server do łączenia się z bazami danych, w których zdefiniowano tabele zewnętrzne. Następnie można uruchomić instrukcje T-SQL za pośrednictwem tabel zewnętrznych w tym połączeniu z ograniczeniami opisanymi poniżej. Więcej informacji i przykładów zapytań T-SQL można znaleźć w tematach dokumentacji dotyczących [partycjonowania poziomego](sql-database-elastic-query-horizontal-partitioning.md) i [partycjonowania pionowego](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Można użyć zwykłych ciągów połączeń programu SQL Server do łączenia aplikacji i narzędzi do integracji danych bi lub danych z bazami danych, które mają tabele zewnętrzne. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla narzędzia. Po połączeniu należy odwołać się do bazy danych zapytań elastycznych i tabel zewnętrznych w tej bazie danych, tak jak w przypadku dowolnej innej bazy danych programu SQL Server, z którą łączysz się za pomocą narzędzia.

> [!IMPORTANT]
> Uwierzytelnianie przy użyciu usługi Azure Active Directory z zapytaniami elastycznymi nie jest obecnie obsługiwane.

## <a name="cost"></a>Koszty

Zapytanie elastyczne jest uwzględniane w kosztach baz danych usługi Azure SQL Database. Należy zauważyć, że topologie, w których zdalne bazy danych znajdują się w innym centrum danych niż punkt końcowy zapytania elastycznego, są obsługiwane, ale wyjście danych ze zdalnych baz danych jest regularnie naliczane [stawki platformy Azure.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Uruchomienie pierwszego zapytania elastycznego może potrwać do kilku minut w warstwie usługi Standardowa. Ten czas jest konieczne, aby załadować funkcję zapytania elastycznego; wydajność ładowania poprawia się dzięki wyższym warstwom usług i rozmiarom obliczeń.
* Skrypty zewnętrznych źródeł danych lub tabel zewnętrznych z usługi SSMS lub SSDT nie są jeszcze obsługiwane.
* Import/eksport dla bazy danych SQL nie obsługuje jeszcze zewnętrznych źródeł danych i tabel zewnętrznych. Jeśli chcesz użyć importu/eksportu, upuść te obiekty przed wyeksportowaniem, a następnie ponownie utwórz je po zaimportowaniu.
* Elastyczne zapytanie obecnie obsługuje tylko dostęp tylko do odczytu do tabel zewnętrznych. Można jednak użyć pełnej funkcji T-SQL w bazie danych, w której zdefiniowano tabelę zewnętrzną. Może to być przydatne na przykład do utrwalania tymczasowych wyników przy użyciu, na przykład, WYBIERZ <column_list> INTO <local_table> lub do definiowania procedur składowanych w bazie danych zapytań elastycznych, które odwołują się do tabel zewnętrznych.
* Z wyjątkiem nvarchar(max), typy LOB (w tym typy przestrzenne) nie są obsługiwane w definicjach tabel zewnętrznych. Aby obejść ten problem, można utworzyć widok w zdalnej bazie danych, który rzutuje typ LOB na nvarchar(max), zdefiniować tabelę zewnętrzną nad widokiem zamiast tabeli bazowej, a następnie przerzucić ją z powrotem do oryginalnego typu LOB w kwerendach.
* Kolumny typu danych nvarchar(max) w zestawie wyników wyłączają zaawansowane techniki wsadowe używane w implementacji zapytania elastycznego i mogą wpływać na wydajność kwerendy dla rzędu wielkości, a nawet dwa rzędy wielkości w przypadkach użycia niekaonowego, w których duża ilość niezagregowane dane są przesyłane w wyniku zapytania.
* Statystyki kolumn nad tabelami zewnętrznymi nie są obecnie obsługiwane. Statystyki tabeli są obsługiwane, ale muszą być tworzone ręcznie.
* Zapytanie elastyczne działa tylko z usługą Azure SQL Database. Nie można go używać do wykonywania zapytań lokalnego programu SQL Server lub programu SQL Server na maszynie wirtualnej.

## <a name="feedback"></a>Opinia

Podziel się opiniami na temat swoich doświadczeń z elastycznych zapytań z nami poniżej, na forach MSDN lub na przepełnienie stosu. Jesteśmy zainteresowani wszelkiego rodzaju opinie na temat usługi (wady, szorstkie krawędzie, luki funkcji).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać samouczek partycjonowania pionowego, zobacz [Wprowadzenie do kwerendy między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych pionowo, zobacz [Wyszukiwanie danych podzielonych pionowo na partycje)](sql-database-elastic-query-vertical-partitioning.md)
* Aby uzyskać poziome partycjonowanie (dzielenie na fragmenty) samouczek, zobacz [Wprowadzenie do elastycznego zapytania do partycjonowania poziomego (dzielenia na fragmenty)](sql-database-elastic-query-getting-started.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych poziomo na partycje, zobacz Wykonywanie zapytań o [dane podzielone na partycje w poziomie)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [\_sp \_wykonać zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej bazy danych SQL azure lub zestaw baz danych służących jako fragmenty w schemat partycjonowania poziomego.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
