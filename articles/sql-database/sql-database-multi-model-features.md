---
title: Możliwości wielu modeli
description: Usługa Azure SQL Database umożliwia pracę z wieloma modelami danych w tej samej bazie danych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802814"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Możliwości wielu modeli bazy danych SQL usługi Azure

Wielomodelowe bazy danych umożliwiają przechowywanie i pracę z danymi reprezentowanymi w wielu formatach danych, takich jak dane relacyjne, wykresy, dokumenty JSON/XML, pary klucz-wartość itp.

## <a name="when-to-use-multi-model-capabilities"></a>Kiedy używać możliwości wieloszeriowych

Usługa Azure SQL Database jest przeznaczona do pracy z modelem relacyjnem, który zapewnia najlepszą wydajność w większości przypadków dla różnych aplikacji ogólnego przeznaczenia. Jednak usługa Azure SQL Database nie jest ograniczona tylko do danych relacyjnych. Usługa Azure SQL Database umożliwia korzystanie z różnych formatów nierelacyjnych, które są ściśle zintegrowane z modelem relacyjnem.
Należy rozważyć użycie możliwości wielu modeli usługi Azure SQL Database w następujących przypadkach:
- Masz pewne informacje lub struktury, które są lepiej dopasowane do modeli NoSQL i nie chcesz używać oddzielnej bazy danych NoSQL.
- Większość danych jest odpowiednia dla modelu relacyjnego i musisz modelować niektóre części danych w stylu NoSQL.
- Chcesz wykorzystać bogaty język Transact-SQL do wykonywania zapytań i analizowania zarówno danych relacyjnych, jak i nosql, a także integrowania go z różnymi narzędziami i aplikacjami, które mogą używać języka SQL.
- Chcesz zastosować funkcje bazy danych, takie jak [technologie w pamięci,](sql-database-in-memory.md) aby poprawić wydajność analitycznych lub przetwarzania struktur danych NoSQL, użyć [replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md) lub [repliki czytelne](sql-database-read-scale-out.md) do tworzenia kopii danych w innym miejscu i odciążania niektórych obciążeń analitycznych z podstawowej bazy danych.

## <a name="overview"></a>Omówienie

Usługa Azure SQL udostępnia następujące funkcje wielu modeli:
- [Funkcje wykresu](#graph-features) umożliwiają reprezentowanie danych jako zestawu węzłów i krawędzi oraz używanie standardowych zapytań `MATCH` Transact-SQL wzbogaconych o operator wykresu do wykonywania zapytań dotyczących danych wykresu.
- [Funkcje JSON](#json-features) umożliwiają umieszczanie dokumentów JSON w tabelach, przekształcanie danych relacyjnych w dokumenty JSON i odwrotnie. Można użyć standardowego języka Transact-SQL wzbogaconego o funkcje JSON do analizowania dokumentów i używać indeksów nieklastrowanych, indeksów magazynu kolumn lub tabel zoptymalizowanych pod kątem pamięci, aby zoptymalizować zapytania.
- [Obiekty przestrzenne](#spatial-features) umożliwiają przechowywanie danych geograficznych i geometrycznych, indeksowania ich przy użyciu indeksów przestrzennych i pobieranie danych przy użyciu zapytań przestrzennych.
- [Funkcje XML](#xml-features) umożliwiają przechowywanie i indeksowanie danych XML w bazie danych oraz używanie natywnych operacji XQuery/XPath do pracy z danymi XML. Baza danych SQL platformy Azure ma wyspecjalizowany wbudowany aparat zapytań XML, który przetwarza dane XML.
- [Pary klucz-wartość](#key-value-pairs) nie są jawnie obsługiwane jako funkcje specjalne, ponieważ pary klucz-wartość mogą być modelowane natywnie jako tabele dwukolumnowe.

  > [!Note]
  > W tej samej kwerendzie Transact-SQL można użyć wyrażenia JSON Path, wyrażeń XQuery/XPath, funkcji przestrzennych i wyrażeń zapytania grafowego w tej samej kwerendzie Transact-SQL, aby uzyskać dostęp do wszystkich danych przechowywanych w bazie danych. Ponadto dowolne narzędzie lub język programowania, który może wykonywać zapytania Transact-SQL, może również użyć tego interfejsu kwerendy, aby uzyskać dostęp do danych wielu modeli. Jest to kluczowa różnica w porównaniu do wielomodelowych baz danych, takich jak [Usługa Azure Cosmos DB,](/azure/cosmos-db/) która zapewnia wyspecjalizowany interfejs API dla różnych modeli danych.

W poniższych sekcjach można dowiedzieć się o najważniejszych możliwościach wielu modeli usługi Azures SQL Database.

## <a name="graph-features"></a>Funkcjonalności grafu

Usługa Azure SQL Database oferuje możliwości bazy danych wykresu do modelowania relacji wiele do wielu w bazie danych. Wykres jest zbiorem węzłów (lub wierzchołków) i krawędzi (lub relacji). Węzeł reprezentuje jednostkę (na przykład osobę lub organizację), a krawędź reprezentuje relację między dwoma węzłami, które łączy (na przykład polubienia lub znajomych). Oto kilka funkcji, które sprawiają, że baza danych wykresu jest unikatowa:
- Krawędzie lub relacje są jednostkami pierwszej klasy w bazie danych wykresu i mogą mieć skojarzone z nimi atrybuty lub właściwości.
- Pojedyncza krawędź można elastycznie połączyć wiele węzłów w bazie danych wykresu.
- Można łatwo wyrazić dopasowania wzorców i wielu zapytań nawigacyjnych.
- Możesz łatwo wyrazić przechodnie zamknięcie i zapytania polimorficzne.

Relacje wykresu i możliwości zapytań wykres są zintegrowane z Transact-SQL i otrzymują korzyści z używania programu SQL Server jako podstawowych systemów zarządzania bazami danych.
[Przetwarzanie wykresu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) jest podstawową funkcją aparatu bazy danych programu SQL Server, dzięki czemu można znaleźć więcej informacji na temat przetwarzania graph tam.

### <a name="when-to-use-a-graph-capability"></a>Kiedy używać funkcji wykresu

Nie ma nic bazy danych wykres można osiągnąć, które nie mogą być osiągnięte przy użyciu relacyjnej bazy danych. Jednak baza danych wykresu może ułatwić wyrażanie niektórych zapytań. Twoja decyzja o wyborze jednego nad drugim może opierać się na następujących czynnikach:

- Modelowanie danych hierarchicznych, w których jeden węzeł może mieć wiele korzystać z wielu łajdanych, więc nie można użyć identyfikatora HierarchyId
- Model ma Twoja aplikacja ma złożone relacje wiele do wielu; w miarę rozwoju aplikacji dodawane są nowe relacje.
- Musisz analizować wzajemnie połączone dane i relacje.

## <a name="json-features"></a>Funkcje JSON

Usługa Azure SQL Database umożliwia analizowanie i wykonywanie zapytań danych reprezentowanych w formacie notacji obiektów JavaScript [(JSON)](https://www.json.org/) oraz eksportowanie danych relacyjnych jako tekstu JSON.

JSON to popularny format danych używany do wymiany danych w nowoczesnych aplikacjach internetowych i mobilnych. JSON służy również do przechowywania danych półstrukturalnych w plikach dziennika lub w bazach danych NoSQL, takich jak [Usługa Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) Wiele usług sieci web REST zwraca wyniki sformatowane jako tekst JSON lub akceptuje dane sformatowane jako JSON. Większość usług platformy Azure, takich jak [Azure Cognitive Search](https://azure.microsoft.com/services/search/), Azure [Storage](https://azure.microsoft.com/services/storage/)i [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) mają punkty końcowe REST, które zwracają lub korzystają z JSON.

Usługa Azure SQL Database umożliwia łatwą pracę z danymi JSON i integrację bazy danych z nowoczesnymi usługami. Usługa Azure SQL Database udostępnia następujące funkcje do pracy z danymi JSON:

![Funkcje JSON](./media/sql-database-json-features/image_1.png)

Jeśli masz tekst JSON, możesz wyodrębnić dane z JSON lub sprawdzić, czy JSON jest poprawnie sformatowany za pomocą wbudowanych funkcji [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)i [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Funkcja [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) umożliwia aktualizowanie wartości wewnątrz tekstu JSON. W przypadku bardziej zaawansowanych zapytań i analiz funkcja [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) może przekształcić tablicę obiektów JSON w zestaw wierszy. Dowolna kwerenda SQL może być wykonana na zwróconym zestawie wyników. Na koniec istnieje [klauzula FOR JSON,](https://msdn.microsoft.com/library/dn921882.aspx) która umożliwia formatowanie danych przechowywanych w tabelach relacyjnych jako tekst JSON.

Aby uzyskać więcej informacji, zobacz [Jak pracować z danymi JSON w bazie danych SQL azure](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) jest podstawową funkcją aparatu bazy danych programu SQL Server, dzięki czemu można tam znaleźć więcej informacji na temat funkcji JSON.

### <a name="when-to-use-a-json-capability"></a>Kiedy używać funkcji JSON

Modele dokumentów mogą być używane zamiast modeli relacyjnych w niektórych konkretnych scenariuszach:
- Wysoka normalizacja schematu nie przynosi znaczących korzyści, ponieważ uzyskujesz dostęp do wszystkich pól obiektów jednocześnie lub nigdy nie aktualizujesz znormalizowanych części obiektów. Jednak znormalizowany model zwiększa złożoność zapytań ze względu na dużą liczbę tabel, które należy dołączyć, aby uzyskać dane.
- Pracujesz z aplikacjami, które natywnie używają dokumentów JSON są modele komunikacji lub danych i nie chcesz wprowadzać dodatkowe warstwy, które przekształca dane relacyjne do JSON i odwrotnie.
- Należy uprościć model danych przez de-normalizacji tabel podrzędnych lub wzorce entity-object-value.
- Należy załadować lub wyeksportować dane przechowywane w formacie JSON bez dodatkowego narzędzia, które analizuje dane.

## <a name="spatial-features"></a>Cechy przestrzenne

Dane przestrzenne reprezentują informacje o fizycznej lokalizacji i kształcie obiektów geometrycznych. Obiekty te mogą być punktami lokalizacji lub bardziej złożonych obiektów, takich jak kraje/regiony, drogi lub jeziora.

Usługa Azure SQL Database obsługuje dwa typy danych przestrzennych — typ danych geometrii i typ danych geografii.
- Typ geometrii reprezentuje dane w układzie współrzędnych euklidesowych (płaskich).
- Typ geografii reprezentuje dane w układzie współrzędnych okrągłego.

Istnieje wiele obiektów przestrzennych, które mogą być używane w bazie danych SQL platformy Azure, takich jak [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon), itp.

Usługa Azure SQL Database udostępnia również wyspecjalizowane [indeksy przestrzenne,](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) które mogą służyć do zwiększenia wydajności zapytań przestrzennych.

[Obsługa przestrzenna](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) jest podstawową funkcją aparatu bazy danych programu SQL Server, dzięki czemu można tam znaleźć więcej informacji na temat funkcji przestrzennej.

## <a name="xml-features"></a>Funkcje XML

SQL Server zapewnia zaawansowana platformę do tworzenia zaawansowanych aplikacji do zarządzania danymi częściowo ustrukturyzowanych. Obsługa XML jest zintegrowana ze wszystkimi składnikami programu SQL Server i obejmuje następujące elementy:

- Typ danych xml. Wartości XML mogą być przechowywane natywnie w kolumnie typu danych xml, które można wpisać zgodnie z kolekcją schematów XML lub pozostawić bez typu. Kolumnę XML można indeksować.
- Możliwość określenia kwerendy XQuery dla danych XML przechowywanych w kolumnach i zmiennych typu xml. Funkcje XQuery mogą być używane w dowolnej kwerendzie Transact-SQL, która uzyskuje dostęp do dowolnego modelu danych używanego w bazie danych.
- Automatyczne indeksowanie wszystkich elementów w dokumentach XML przy użyciu [podstawowego indeksu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) lub określanie dokładnych ścieżek, które powinny być indeksowane przy użyciu [pomocniczego indeksu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, który umożliwia masowe ładowanie danych XML.
- Przekształć dane relacyjne w format XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) jest podstawową funkcją aparatu baz danych programu SQL Server, dzięki czemu można tam znaleźć więcej informacji na temat funkcji XML.

### <a name="when-to-use-an-xml-capability"></a>Kiedy używać funkcji XML

Modele dokumentów mogą być używane zamiast modeli relacyjnych w niektórych konkretnych scenariuszach:
- Wysoka normalizacja schematu nie przynosi znaczących korzyści, ponieważ uzyskujesz dostęp do wszystkich pól obiektów jednocześnie lub nigdy nie aktualizujesz znormalizowanych części obiektów. Jednak znormalizowany model zwiększa złożoność zapytań ze względu na dużą liczbę tabel, które należy dołączyć, aby uzyskać dane.
- Pracujesz z aplikacjami, które natywnie używają dokumentów XML są modele komunikacji lub danych i nie chcesz wprowadzać dodatkowe warstwy, które przekształca dane relacyjne do XML i odwrotnie.
- Należy uprościć model danych przez de-normalizacji tabel podrzędnych lub wzorce entity-object-value.
- Należy załadować lub wyeksportować dane przechowywane w formacie XML bez dodatkowego narzędzia, które analizuje dane.

## <a name="key-value-pairs"></a>Pary klucz-wartość

Usługa Azure SQL Database nie ma wyspecjalizowanych typów ani struktur obsługujących pary klucz-wartość, ponieważ struktury klucz-wartość mogą być reprezentowane natywnie jako standardowe tabele relacyjne:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Tę strukturę klucza i wartości można dostosować do swoich potrzeb bez żadnych ograniczeń. Na przykład wartością może być dokument XML zamiast `nvarchar(max)` typu, jeśli wartością jest `CHECK` dokument JSON, można umieścić ograniczenie, które weryfikuje ważność zawartości JSON. Można umieścić dowolną liczbę wartości związanych z jednym kluczem w dodatkowych kolumnach, dodać obliczone kolumny i indeksy, aby uprościć i zoptymalizować dostęp do danych, zdefiniować tabelę jako tabelę tylko do pamięci/zoptymalizowanej, aby uzyskać lepszą wydajność itp.

Zobacz, [jak BWin używa oltp w pamięci do osiągnięcia bezprecedensowej wydajności i skali](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) dla ich ASP.NET rozwiązania buforowania, które osiągnęły 1.200.000 partii na sekundę, jako przykład, jak model relacyjny może być skutecznie używany jako rozwiązanie pary klucz-wartość w praktyce.

## <a name="next-steps"></a>Następne kroki
Funkcje wielu modeli w bazach danych SQL Azure są również podstawowe funkcje aparatu baz danych programu SQL Server, które są współużytkowane przez usługę Azure SQL Database i SQL Server. Aby dowiedzieć się więcej szczegółów na temat tych funkcji, odwiedź strony dokumentacji relacyjnej bazy danych SQL:

* [Przetwarzanie wykresu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dane JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Wsparcie przestrzenne](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [dane XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
