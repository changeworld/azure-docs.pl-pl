---
title: Omówienie zapytania elastycznego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Elastyczne zapytanie umożliwia uruchamianie zapytanie Transact-SQL, która obejmuje wiele baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 01/19/2019
ms.openlocfilehash: fad9437a631254d6c60d6d97267ae111d195040f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585714"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Omówienie zapytanie elastyczne w usłudze Azure SQL Database (wersja zapoznawcza)

Funkcja elastycznego zapytania (w wersji zapoznawczej) umożliwia uruchamianie zapytanie Transact-SQL, która obejmuje wiele baz danych w usłudze Azure SQL Database. Umożliwia ona wykonywania zapytań między bazami danych, aby dostęp do tabel zdalnych, a do łączenia z firmy Microsoft oraz narzędzi innych firm (programu Excel, Power BI, Tableau, itp.) do wykonywania zapytań w warstwach danych z wieloma bazami danych. Przy użyciu tej funkcji, można skalować w poziomie zapytania do warstw dużych ilości danych w bazie danych SQL i wizualizacja wyników w raportach analizy biznesowej.

## <a name="why-use-elastic-queries"></a>Dlaczego warto korzystać z zapytania elastyczne

### <a name="azure-sql-database"></a>Azure SQL Database

Zapytania w bazach danych Azure SQL całkowicie w języka T-SQL. Zezwala na tylko do odczytu podczas wykonywania zapytań zdalnego baz danych i udostępnia opcję obecni klienci programu SQL Server w środowisku lokalnym, migrowanie aplikacji przy użyciu trzech - i czteroczęściowych nazw lub połączonego serwera do bazy danych SQL.

### <a name="available-on-standard-tier"></a>Dostępne w warstwie standardowa

Elastyczne zapytanie jest obsługiwane w następujących warstwach usługi Standard i Premium. Zobacz sekcję dotyczącą ograniczenia wersji zapoznawczej, poniżej na ograniczenia wydajności, które w niższych warstwach usług.

### <a name="push-parameters-to-remote-databases"></a>Parametry wypychania do zdalnej bazy danych

Zapytania elastyczne może teraz wypychać parametrów SQL do zdalnej bazy danych do wykonania.

### <a name="stored-procedure-execution"></a>Wykonywanie procedury składowanej

Wykonywanie wywołania zdalnej procedury składowanej lub funkcji zdalnego przy użyciu [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Elastyczność

Tabele zewnętrzne przy użyciu zapytania elastycznego mogą odwoływać się do tabel zdalnych przy użyciu innego schematu lub nazwę tabeli.

## <a name="elastic-query-scenarios"></a>Scenariusze zapytania elastycznego

Celem jest ułatwienie zapytań scenariuszy, w której wiele baz danych współtworzyć wierszy w jeden wynik ogólny. Zapytania mogą się składać, albo przez użytkownika lub aplikacji bezpośrednio lub pośrednio za pomocą narzędzi, które są połączone z bazą danych. Jest to szczególnie przydatne podczas tworzenia raportów, za pomocą BI handlowej lub narzędzia do integracji danych lub dowolnej aplikacji, której nie można zmienić. Przy użyciu elastycznego zapytania można tworzyć zapytania w wielu bazach danych w narzędziach takich jak Excel, Power BI, Tableau lub Cognos przy użyciu dobrze znanych środowisko połączenia programu SQL Server.
Zapytanie elastyczne umożliwia łatwy dostęp do całej kolekcji baz danych za pośrednictwem zapytań wystawione przez program SQL Server Management Studio lub Visual Studio i ułatwia tworzenie wielu baz danych, wykonywania zapytań z programu Entity Framework lub innych środowiskach ORM. Rysunek 1 przedstawia scenariusz, w którym istniejące aplikacja w chmurze (wykonującemu [Biblioteka kliencka elastic database](sql-database-elastic-database-client-library.md)) oparte na danych skalowanych w poziomie warstwy i elastyczne zapytanie jest używane na potrzeby raportowania między bazami danych.

**Rysunek 1** elastyczne Zapytanie używane w warstwie danych skalowanych w poziomie

![Elastyczne Zapytanie używane w warstwie danych skalowanych w poziomie][1]

Scenariusze klienta dla zapytania elastycznego charakteryzują się następujących topologii:

* **Partycjonowanie pionowe - zapytań między bazami danych** (topologia 1): Dane są partycjonowane w pionie między liczbą baz danych w warstwie danych. Zazwyczaj różne zestawy tabele znajdują się w różnych bazach danych. Oznacza to, że schemat różni się w różnych bazach danych. Na przykład wszystkie tabele spisu znajdują się na jedną bazę danych podczas, gdy wszystkie tabele powiązane księgowości znajdują się w drugiej bazy danych. Typowe przypadki użycia z tym topologią wymagane do tworzenia zapytań i skompilować raporty między tabelami w wielu bazach danych.
* **Partycjonowanie poziome — fragmentowanie** (topologia 2): Dane są partycjonowane w poziomie, aby rozdystrybuować wiersze danych skalowanych w poziomie warstwy. W przypadku tej metody schemat jest taka sama dla wszystkich uczestniczących baz danych. To podejście jest również nazywany "fragmentowania". Dzielenie na fragmenty, które mogą być wykonywane i zarządzanych (1 elastycznej bazy danych przy użyciu narzędzia, biblioteki lub (2) self dzielenie na fragmenty. Elastyczne zapytanie służy do przeszukiwania i skompilować raporty obejmujące wiele fragmentów.

> [!NOTE]
> Elastyczne zapytanie sprawdza się najlepiej w sytuacji, gdzie większość przetwarzania (filtrowanie, agregacja) mogą być wykonywane po stronie źródła zewnętrznego. Nie jest odpowiednia dla operacji ETL gdzie duże ilości danych są przesyłane z zdalnej bazy danych. Dla dużych obciążeń raportowania lub przy użyciu bardziej złożone zapytania magazynowania danych, również należy rozważyć użycie [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Partycjonowanie pionowe - zapytań między bazami danych

Aby rozpocząć, kodowania, zobacz [rozpoczęcie pracy z bazami danych zapytań (partycje pionowe)](sql-database-elastic-query-getting-started-vertical.md).

Elastyczne zapytanie może służyć do danych znajdujących się w usłudze SQL database dostępne dla innych baz danych SQL. Dzięki temu zapytania z jednej bazy danych do odwoływania się do tabel w żadnych innych zdalną bazą danych SQL. Pierwszym krokiem jest określenie zewnętrznego źródła danych dla każdej zdalnej bazy danych. Zewnętrzne źródło danych jest zdefiniowany w lokalnej bazie danych, z którego chcesz uzyskać dostęp do tabel znajdujących się na zdalnej bazy danych. Żadne zmiany nie są niezbędne dla zdalnej bazy danych. W przypadku typowego pionowy partycjonowania scenariuszy gdzie mają różne schematy w różnych bazach danych zapytań elastycznych może służyć do wdrażania typowych przypadków użycia takich jak dostęp do danych referencyjnych i wykonywania zapytań między bazami danych.

> [!IMPORTANT]
> Musi mieć uprawnienie ALTER ANY zewnętrznego źródła danych. To uprawnienie jest dołączony do uprawnienie ALTER DATABASE. Aby odwołać się do bazowego źródła danych są potrzebne uprawnienia ALTER ANY zewnętrznego źródła danych.
>

**Dane referencyjne**: Topologia jest używana do zarządzania danymi odwołania. Na poniższej ilustracji dwie tabele (T1 i T2) z danymi referencyjnymi są przechowywane na dedykowany bazy danych. Przy użyciu zapytania elastycznego, są teraz dostępne tabele T1 i T2 zdalnie innych baz danych, jak pokazano na rysunku. Użyj topologia 1, jeśli tabele odwołań są małe lub zdalne zapytania do tabeli referencyjnej mieć predykatów selektywnego.

**Rysunek 2** partycjonowanie pionowe — elastycznych referencyjnymi zapytania do zapytania

![Partycjonowanie pionowe — elastycznych referencyjnymi zapytania do zapytania][3]

**Wykonywanie zapytań między bazami danych**: Zapytania elastyczne umożliwiają przypadki użycia, które wymagają wykonywania zapytań w wielu bazach danych SQL. Rysunek 3 przedstawia cztery różne bazy danych: CRM, spisu, godzinę i produktów. Zapytania wykonywane w jednej z baz danych potrzebują również dostępu do jednego lub wszystkich innych baz danych. Przy użyciu zapytania elastycznego, można skonfigurować bazy danych dla tego przypadku, uruchamiając kilka prostych instrukcji DDL na każdym z czterech baz danych. Po tej jednorazowej konfiguracji dostępu do tabeli zdalnej jest tak proste, jak odwołujące się do lokalnej tabeli z zapytań T-SQL lub narzędzi do analizy Biznesowej. Takie podejście jest zalecane w przypadku zapytań zdalnych nie zwracają duże wyniki.

**Rysunek 3** partycjonowanie pionowe — za pomocą elastycznych zapytań lub zapytań w różnych bazach danych

![Partycjonowanie pionowe — za pomocą elastycznych zapytań lub zapytań w różnych bazach danych][4]

Poniższe kroki należy skonfigurować zapytania elastycznej bazy danych dla pionowy partycjonowania scenariuszy, które wymagają dostępu do tabeli znajduje się na zdalnej bazy danych SQL za pomocą tego samego schematu:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Polecenia CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **RDBMS**
* [Tabela zewnętrzna polecenia CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po uruchomieniu instrukcji DDL, można uzyskać dostęp tabeli zdalnej "mytable", tak jakby był to lokalnej tabeli. Usługa Azure SQL Database automatycznie otwiera połączenie do zdalnej bazy danych przetwarza żądanie w zdalnej bazy danych i zwraca wyniki.

## <a name="horizontal-partitioning---sharding"></a>Partycjonowanie poziome - dzielenia na fragmenty

Przy użyciu zapytania elastycznego do wykonywania zadań raportowania za pośrednictwem podzielonej na fragmenty, oznacza to, poziome podzielone na partycje, Warstwa danych wymaga [mapowanie fragmentów w postaci elastycznych baz danych](sql-database-elastic-scale-shard-map-management.md) do reprezentowania baz danych w warstwie danych. Zazwyczaj jest używana tylko jednego mapowania fragmentów w postaci, w tym scenariuszu, a dedykowany bazy danych (węzła głównego) oraz możliwości elastycznego zapytania służy jako punkt wejścia dla raportowania zapytań. Tylko to dedykowany bazy danych musi mieć dostęp do mapy fragmentów. Rysunek 4 przedstawia tej topologii i jego konfiguracji z mapą zapytanie elastyczne bazy danych i fragmentów. Bazy danych w warstwie danych mogą być wersji lub wersji usługi Azure SQL Database. Aby uzyskać więcej informacji na temat biblioteki klienta elastycznej bazy danych i tworzenie mapowań fragmentów w postaci zobacz [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md).

**Rysunek 4** partycjonowanie poziome — przy użyciu zapytania elastycznego raportowania za pośrednictwem warstw danych podzielonych na fragmenty

![Partycjonowanie poziome — przy użyciu zapytania elastycznego raportowania za pośrednictwem warstw danych podzielonych na fragmenty][5]

> [!NOTE]
> Elastyczne zapytanie bazy danych (węzła głównego) może być oddzielnej bazy danych lub może być tej samej bazy danych, który jest hostem mapowania fragmentów.
> Niezależnie od konfiguracji, możesz wybrać, upewnij się, że warstwy usług i mocy obliczeniowej rozmiar tej bazy danych jest wystarczająco wysoka, aby obsłużyć oczekiwane żądania logowania/zapytań.

Poniższe kroki należy skonfigurować zapytania elastycznej bazy danych na poziomie partycjonowania scenariusze, które wymagają dostępu do zestawu tabel znajdujących się na (zazwyczaj) kilka zdalnej bazy danych SQL:

* [CREATE MASTER KEY](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Tworzenie [mapowanie fragmentów w postaci](sql-database-elastic-scale-shard-map-management.md) reprezentujący warstwie danych za pomocą biblioteki klienckiej elastycznej bazy danych.
* [Polecenia CREATE/DROP EXTERNAL DATA SOURCE](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource typu **SHARD_MAP_MANAGER**
* [Tabela zewnętrzna polecenia CREATE/DROP](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Po wykonaniu tych czynności, możesz uzyskać dostęp tabeli partycjonowanej w poziomie "mytable" tak, jakby była lokalnej tabeli. Usługa Azure SQL Database automatycznie otwiera wiele równoległych połączeń zdalnych baz danych, gdzie fizycznie tabel są przechowywane, przetwarza żądania zdalnego baz danych i zwraca wyniki.
Więcej informacji na temat kroków wymaganych dla poziomej partycjonowania scenariusza można znaleźć w [zapytania elastycznego do partycjonowania poziomego](sql-database-elastic-query-horizontal-partitioning.md).

Aby rozpocząć, kodowania, zobacz [wprowadzenie do zapytań elastycznych dla partycjonowania poziomego (fragmentowania)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Wykonywanie zapytań T-SQL

Po zdefiniowaniu z zewnętrznymi źródłami danych i tabel zewnętrznych służy zwykłe ciągi połączeń programu SQL Server do połączenia bazy danych, gdzie zdefiniowane tabel zewnętrznych. Następnie można uruchomić instrukcje języka T-SQL za pośrednictwem tabel zewnętrznych dla tego połączenia z ograniczeniami opisane poniżej. Dodatkowe informacje i przykłady zapytań T-SQL można znaleźć w tematach dokumentacji dotyczących [partycjonowanie poziome](sql-database-elastic-query-horizontal-partitioning.md) i [partycjonowanie pionowe](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Zwykłe ciągi połączeń programu SQL Server służy do łączenia z aplikacji i narzędzi do integracji analizy Biznesowej lub danych do baz danych zawierających tabele zewnętrzne. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla swojego narzędzia. Po nawiązaniu połączenia można znaleźć zapytanie elastyczne bazy danych i tabele zewnętrzne w tej bazie danych tak samo, jak samo, jak wszystkie inne bazy danych SQL Server, możesz nawiązać połączenie z narzędziem.

> [!IMPORTANT]
> Uwierzytelnianie przy użyciu usługi Azure Active Directory za pomocą elastycznych zapytań nie jest obecnie obsługiwane.

## <a name="cost"></a>Koszty

Elastyczne zapytanie znajduje się na koszt baz danych Azure SQL Database. Należy zauważyć, że topologiach gdzie zdalnej bazy danych znajdują się w centrum danych innego niż punkt końcowy elastycznego zapytania są obsługiwane, ale wyjście danych z baz danych, zdalnego jest rozliczany według regularnie [stawkami platformy Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Uruchamianie pierwszego zapytania elastyczne może potrwać kilka minut na warstwie usługi standardowa. Ten czas jest niezbędne, aby załadować zapytania elastycznego funkcjonalności. poprawia wydajność ładowania z wyższej warstwy usług i rozmiarów wystąpień obliczeniowych.
* Obsługa skryptów zewnętrznych źródeł danych lub tabele zewnętrzne z programu SSMS lub program SSDT nie jest jeszcze obsługiwana.
* Import/Export dla bazy danych SQL nie obsługuje jeszcze zewnętrznych źródeł danych i tabel zewnętrznych. Jeśli musisz użyć usługi Import/Export, Porzuć te obiekty przed wyeksportowaniem, a następnie ponownie utwórz je po zaimportowaniu.
* Zapytanie elastyczne aktualnie obsługuje tylko dostęp tylko do odczytu do tabel zewnętrznych. Można jednak, używając pełnej funkcjonalności języka T-SQL w bazie danych którym jest zdefiniowany w tabeli zewnętrznej. Może to być przydatne, np. zachować tymczasowych wyników, na przykład przy użyciu, lokalizację, wybierz OPCJĘ < column_list > w < local_table > lub Definiowanie procedur składowanych w bazie danych zapytania elastycznego, odwołując się do tabel zewnętrznych.
* Z wyjątkiem nvarchar(max) typy obiektów LOB nie są obsługiwane w definicji tabeli zewnętrznej. Obejść ten problem możesz utworzyć widok w zdalnej bazy danych, który rzutuje typu LOB do nvarchar(max), definiowanie tabeli zewnętrznej za pośrednictwem widoku zamiast tabeli podstawowej, a następnie przerzuć go do oryginalnego typu LOB w zapytaniach.
* Kolumnami typu danych nvarchar(max) w wyniku zestaw Wyłącz zaawansowane techniki używane w celu wykonania zapytania elastycznego dzielenia na partie i może mieć wpływ na wydajność zapytanie o rząd wielkości lub nawet w dwóch rzędów w canonical przypadki użycia, w przypadku, gdy duża ilość zagregowane dane są przesyłane w wyniku zapytania.
* Statystyki kolumny za pośrednictwem tabel zewnętrznych nie są obecnie obsługiwane. Statystyki tabeli są obsługiwane, ale muszą zostać utworzone ręcznie.

## <a name="feedback"></a>Opinia

Udostępniać opinie na temat środowiska zapytania elastyczne z nami poniżej, na forach MSDN lub w witrynie Stack Overflow. Jesteśmy zainteresowani wszelkiego rodzaju opinię na temat usługi (usterek, nierówne krawędzie, luki funkcji).

## <a name="next-steps"></a>Kolejne kroki

* Pionowe samouczek partycjonowania, zobacz [rozpoczęcie pracy z bazami danych zapytań (partycje pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Składnia i przykładowe zapytania dotyczące danych partycjonowanych w pionie, można zobaczyć [zapytań w pionie partycjonowania danych)](sql-database-elastic-query-vertical-partitioning.md)
* Samouczek partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do zapytań elastycznych dla partycjonowania poziomego (fragmentowania)](sql-database-elastic-query-getting-started.md).
* Aby uzyskać składnię i przykładowe zapytania dotyczące danych partycjonowanej w poziomie, zobacz [zapytań w poziomie partycjonowania danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury przechowywanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej usługi Azure SQL Database lub zestawu baz danych, służąc jako fragmentów w poziomie schematu partycjonowania.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
