---
title: Możliwości modelu wielu bazy danych SQL platformy Azure | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database umożliwia pracę z wieloma modelami danych w tej samej bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 4351017cc1848e29cca038f82fd96548ae3492e0
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892470"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Wielomodelowa możliwości usługi Azure SQL Database

Wielomodelowe bazy danych pozwalają na przechowywanie i pracować z danymi reprezentowane w wielu formatach danych, takich jak dane relacyjne, wykresy, dokumenty JSON/XML, pary klucz wartość itp.

## <a name="when-to-use-multi-model-capabilities"></a>Kiedy należy używać funkcji wielu modeli

Usługa Azure SQL Database jest przeznaczona do pracy przy użyciu modelu relacyjnego, która zapewnia najlepszą wydajność, w większości przypadków dla różnych aplikacji do zastosowań. Jednak usługi Azure SQL Database nie jest ograniczona do relacyjnych — tylko dane. Usługa Azure SQL Database umożliwia użycie różnych formatach nierelacyjnych, które są ściśle zintegrowane model relacyjny.
Należy rozważyć użycie wielu modeli możliwości usługi Azure SQL Database w następujących przypadkach:
- Masz niektóre informacje lub struktur, które lepiej nadają się do modeli NoSQL, a nie chcesz używać oddzielnej bazy danych NoSQL.
- Większość danych nadaje się do modelu relacyjnego, i należy do modelowania niektórych części danych w stylu NoSQL.
- Chcesz korzystać z rozbudowanych języka Transact-SQL do tworzenia zapytań i analizowania zarówno relacyjnych, jak i danych NoSQL i zintegruj ją z różnych narzędzi i aplikacji, które mogą używać języka SQL.
- Chcesz zastosować funkcje bazy danych, takich jak [technologiom pamięci](sql-database-in-memory.md) aby poprawić wydajność usługi analityczne lub przetwarzanie Twojego strucutres danych NoSQL, użyj [replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md) lub [replik z możliwością odczytu](sql-database-read-scale-out.md) do utworzenia kopii danych w innym miejscu i odciążania niektórych obciążeń analitycznych z podstawowej bazy danych.

## <a name="overview"></a>Przegląd

Usługi Azure SQL, oferuje następujące funkcje wielomodelowa:
- [Wykres funkcji](#graph-features) pozwalają do reprezentowania danych jako zestaw węzłów i krawędzi, a następnie użyj standardowego zapytania Transact-SQL, rozszerzony o wykres `MATCH` — operator zapytań o dane wykresu.
- [Funkcje JSON](#json-features) umożliwia umieszczanie dokumenty JSON w tabelach i przekształcania danych relacyjnych dokumentów JSON i na odwrót. Możesz użyć standardowego języka Transact-SQL, ulepszone funkcje podczas analizowania dokumentów JSON i umożliwia zoptymalizowanie zapytań bez klastrowanych indeksów, indeksy magazynu kolumn lub w przypadku tabel zoptymalizowanych pod kątem pamięci.
- [Funkcje przestrzenne](#spatial-features) pozwala na przechowywanie danych geograficznych i geometryczne, indeksować je przy użyciu indeksów przestrzennych i pobierać dane za pomocą zapytań przestrzennych.
- [Funkcje języka XML](#xml-features) pozwalają na przechowywanie i indeksowanie danych XML w bazie danych i używać natywnego operacji XQuery/XPath do pracy z danymi XML. Usługa Azure SQL database specjalizuje wbudowany aparat kwerendy XML, który przetwarza dane XML.
- [Pary klucz wartość](#key-value-pairs) nie są jawnie obsługiwane jako specjalne funkcje ponieważ Paryż pary klucz wartość, które mogą być natywnie modelowane jako tabele dwie kolumny.

  > [!Note]
  > Dostęp do wszelkich danych przechowywanych w bazie danych, można użyć wyrażenie ścieżki JSON, wyrażenia XQuery/XPath, funkcje przestrzenne i wyrażenia zapytania programu graph w jednym zapytaniu języka Transact-SQL. Ponadto narzędzi lub języka programowania, która umożliwia wykonanie zapytania Transact-SQL umożliwia również interfejsu zapytania do dostępu do danych wielu modeli. Jest to klucza różnica w porównaniu z wielomodelowe bazy danych, takich jak [usługi Azure Cosmos DB](/azure/cosmos-db/) wyspecjalizowane interfejs API zapewnia różne modele danych.

W poniższych sekcjach można Poznaj najważniejsze możliwości wielomodelowej bazy danych SQL Azures.

## <a name="graph-features"></a>Funkcjonalności grafu

Usługa Azure SQL Database oferuje możliwości bazy danych programu graph do modelowania relacji wiele do wielu w bazie danych. Wykres jest kolekcją węzłów (lub wierzchołki) i krawędzi (lub relacji). Reprezentuje węzeł jednostki (na przykład osoby lub organizacji) i krawędź reprezentuje relację między dwoma węzłami, które nawiązuje połączenie (na przykład, polubień lub znajomych). Poniżej przedstawiono niektóre funkcje, które unikatowość grafowej bazy danych:
- Krawędzie lub relacje są najwyższej klasy jednostek w bazie danych programu Graph i są atrybutów lub właściwości skojarzone z nimi.
- Jednej krawędzi elastycznie można połączyć wiele węzłów w bazie danych programu Graph.
- Łatwo można wyrazić dopasowywania do wzorca i zapytania nawigacji z wieloma przeskokami.
- Łatwo można wyrazić zamknięcia przechodnie i zapytania polimorficznego.

Relacje wykresu i możliwości zapytań grafu są zintegrowane z instrukcji Transact-SQL i otrzymywać korzyści z używania programu SQL Server jako system zarządzania podstawowe bazy danych.
[Przetwarzanie wykresów](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) jest funkcja aparatu bazy danych programu SQL Server core, więc przetwarzania można znaleźć więcej informacji na temat programu Graph.

### <a name="when-to-use-a-graph-capability"></a>Kiedy należy używać funkcji programu graph

Nie ma nic, który może osiągnąć bazy danych grafów, które nie mogą być osiągnięte relacyjnej bazy danych. Jednak bazy danych grafów może ułatwić określenie niektórych zapytań. Twoją decyzję, aby wybrać jedno zamiast drugiego może bazować na następujące czynniki:

- Model danych hierarchicznych, gdzie jeden węzeł może mieć wielu elementów nadrzędnych, więc nie można używać HierarchyId
- Model ma Twoja aplikacja zawiera złożone relacje wiele do wielu; zgodnie z rozwojem aplikacji są dodawane nowe relacje.
- Potrzebujesz przeanalizować wzajemnie połączonych danych i relacje.

## <a name="json-features"></a>Funkcje JSON

Azure umożliwia bazy danych SQL, analizy i kwerend danych reprezentowanych w JavaScript Object Notation [(JSON)](https://www.json.org/) formatowania i eksportowanie danych relacyjnych jako tekst w formacie JSON.

JSON to format popularnych dane używane do wymiany danych w nowoczesne rozwiązania sieci web i aplikacji mobilnych. JSON jest również używany do przechowywania danych z częściową strukturą w plikach dziennika lub bazy danych NoSQL, takie jak [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Wiele usług sieci web REST zwracane wyniki formatować jako tekstu JSON lub akceptować dane w formacie JSON. Większość Azure services, takich jak [usługi Azure Search](https://azure.microsoft.com/services/search/), [usługi Azure Storage](https://azure.microsoft.com/services/storage/), i [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) punkty końcowe REST, które zwracają lub zużywają JSON.

Usługa Azure SQL Database pozwala łatwo pracować z danymi JSON i integrowanie bazy danych przy użyciu nowoczesnych usług. Usługa Azure SQL Database oferuje następujące funkcje do pracy z danymi w formacie JSON:

![Funkcje JSON](./media/sql-database-json-features/image_1.png)

W przypadku tekstu JSON można wyodrębniania danych z formatu JSON, lub sprawdź JSON jest poprawnie sformatowany za pomocą wbudowanych funkcji [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), i [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funkcja pozwala zaktualizować wartość w tekście JSON. Więcej zaawansowanych zapytań i analiz, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funkcji można przekształcić Tablica obiektów JSON do zestawu wierszy. Każde zapytanie SQL mogą być wykonywane na zestaw wyników zwrócony. Na koniec istnieje [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) klauzula, która umożliwia formatowanie — dane przechowywane w tabelach relacyjnych jako tekst w formacie JSON.

Aby uzyskać więcej informacji, zobacz [sposób pracy z danymi JSON w usłudze azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) jest funkcja aparatu bazy danych programu SQL Server core, dzięki czemu można znaleźć więcej informacji na temat z funkcji JSON.

### <a name="when-to-use-a-json-capability"></a>Kiedy należy używać funkcji JSON

Modele dokumentu mogą być używane zamiast modeli relacyjnych w niektórych określonych scenariuszach:
- Wysoka normalizacji schematu nie zachowa znaczące korzyści związane, ponieważ możesz uzyskać dostęp do wszystkich pól obiektów jednocześnie lub nigdy nie Aktualizuj znormalizowane części obiektów. Jednak znormalizowane modelu zwiększa złożoność zapytań z powodu dużej liczby tabel, które należy przyłączyć do pobierania danych.
- Pracujesz z aplikacjami, czy natywnie dokumentów JSON użycia są komunikacji lub modeli danych i nie należy wprowadzać dodatkowych warstw, które przekształca dane relacyjne, JSON i na odwrót.
- Należy uprościć model danych przez cofnąć Zakończono normalizowanie tabele podrzędne lub wzorców wartości w przypadku obiektu jednostki.
- Należy załadować lub wyeksportować dane przechowywane w formacie JSON bez niektóre dodatkowe narzędzia, która analizuje dane.

## <a name="spatial-features"></a>Funkcje przestrzenne

Dane przestrzenne reprezentuje informacje o lokalizacji fizycznej i kształt geometryczny obiektów. Te obiekty mogą być lokalizacji punktu lub bardziej złożone obiekty, takie jak kraje, dróg lub lakes.

Usługa Azure SQL Database obsługuje dwa typy danych przestrzennych — typ danych Geometria i dane geograficzne typu.
- Typ geometrii reprezentuje dane w euklidesowa współrzędnych (płaski).
- Typ lokalizacji geograficznej reprezentuje dane w układzie współrzędnych round ziemi.

Liczba przestrzenne obiektów, które mogą być używane takie jak w usłudze Azure SQL database jest [punktu](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [wielokąta](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)itp.

Usługa Azure SQL Database udostępnia również wyspecjalizowane [Indeksy przestrzenne](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) można poprawić wydajność zapytań przestrzennych.

[Obsługa przestrzenne](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) jest funkcja aparatu bazy danych programu SQL Server core, dzięki czemu można znaleźć więcej informacji na temat tej funkcji przestrzennych.

## <a name="xml-features"></a>Funkcje języka XML

Program SQL Server udostępnia to zaawansowana platforma do tworzenia rozbudowanych aplikacji do zarządzania danymi z częściową strukturą. Obsługa XML jest zintegrowana z wszystkich składników w programie SQL Server i zawiera następujące części:

- Typ danych xml. Wartości XML mogą być przechowywane w sposób natywny w kolumnie Typ danych xml, które mogą być wpisana zgodnie z kolekcji schematów XML lub lewym bez typu. Umożliwia Indeksowanie kolumn XML.
- Możliwość określenia XQuery zapytanie XML — dane przechowywane w kolumnach i zmiennych typu xml. Funkcje języka XQuery może służyć w każdym zapytaniu języka Transact-SQL, którego dostęp z dowolnego modelu danych, którego używasz w bazie danych.
- Automatyczne indeksowanie wszystkich elementów w dokumentach XML przy użyciu [podstawowy indeks XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) lub określić dokładną ścieżek, które powinny być indeksowane, za pomocą [pomocniczy indeks XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, który umożliwia ładowanie zbiorcze danych XML.
- Przekształcanie danych relacyjnych w formacie XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) jest funkcja aparatu bazy danych programu SQL Server core, dzięki czemu można znaleźć więcej informacji na temat tę funkcję XML.

### <a name="when-to-use-an-xml-capability"></a>Kiedy należy używać funkcji XML

Modele dokumentu mogą być używane zamiast modeli relacyjnych w niektórych określonych scenariuszach:
- Wysoka normalizacji schematu nie zachowa znaczące korzyści związane, ponieważ możesz uzyskać dostęp do wszystkich pól obiektów jednocześnie lub nigdy nie Aktualizuj znormalizowane części obiektów. Jednak znormalizowane modelu zwiększa złożoność zapytań z powodu dużej liczby tabel, które należy przyłączyć do pobierania danych.
- Pracujesz z aplikacjami, czy natywnie Użyj dokumentów XML są komunikacji lub modeli danych i nie należy wprowadzać dodatkowych warstw, które przekształca dane relacyjne na format XML i odwrotnie.
- Należy uprościć model danych przez cofnąć Zakończono normalizowanie tabele podrzędne lub wzorców wartości w przypadku obiektu jednostki.
- Należy załadować lub wyeksportować dane przechowywane w formacie XML bez niektóre dodatkowe narzędzia, która analizuje dane.

## <a name="key-value-pairs"></a>Pary klucz-wartość

Usługa Azure SQL Database nie ma specjalne typy lub struktur, które obsługują pary klucz wartość, ponieważ struktury pary klucz wartość, które mogą być natywnie reprezentowane jako standardowa tabelach relacyjnych:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Można dostosować tę strukturę pary klucz wartość do własnych potrzeb bez żadnych ograniczeń. Na przykład wartość może być dokumentu XML zamiast `nvarchar(max)` typu, jeśli wartość to dokument JSON, możesz umieścić `CHECK` ograniczenia, które sprawdza poprawność zawartości w formacie JSON. Można umieścić dowolną liczbę wartości związane z jednego klucza w dodatkowych kolumn, dodawanie kolumn obliczanych i indeksy, aby uprościć i optymalizowanie dostępu do danych, zdefiniuj tabelę jako/zoptymalizowane pod kątem pamięci tylko do schematu tabeli, aby uzyskać lepszą wydajność, itp.

Zobacz [jak BWin korzysta OLTP w pamięci, aby osiągnąć niezrównaną wydajność i skalę](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) ich buforowania platformy ASP.NET rozwiązanie, które uzyskuje 1.200.000 partii na sekundę, na przykład jak relacyjne modelu mogą być skutecznie używane jako rozwiązanie pary klucz wartość w praktyce.

## <a name="next-steps"></a>Kolejne kroki
Wielomodelowa możliwości w bazach danych SQL Azure są również podstawowe funkcje aparatu bazy danych programu SQL Server, które są wspólne dla usługi Azure SQL Database i programu SQL Server. Aby uzyskać więcej informacji o tych funkcjach, odwiedź strony dokumentacji usługi SQL relacyjnej bazy danych:

* [Przetwarzanie wykresów](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dane JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Obsługa przestrzenne](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Dane XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
