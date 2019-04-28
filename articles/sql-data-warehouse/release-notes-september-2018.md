---
title: Usługa Azure SQL Data Warehouse — informacje o wersji września 2018 r. | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474979"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Wrzesień 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w września 2018 r.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Nowy punkt wejścia niższe dla Gen2 magazynu danych SQL
W kwietniu 2018 r. [firma Microsoft ogłosiła](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Gen2 magazynu danych SQL Azure, oferująca 5 x wydajności, 5 x skalowanie obliczeń, 4 x współbieżności i nieograniczony magazyn. Jak wspomniano w [hurtowni danych w chmurze testów porównawczych](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) przez Joego, SQL Data Warehouse Gen2 **przewyższa stosowane przekształcania Amazon Redshift przez 42%**.

Gen2 — jest teraz ogólnie dostępna w dolnym wpis punktu z DWU500c możliwość uruchamiania środowisk magazynu lub projektowania/testowania mniejszy rozmiar danych za pomocą wszystkich najnowszych ulepszeń usługi. Nowy punkt wejścia zachowuje wszystkie funkcje Gen2, w tym [buforowanie adaptacyjne](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [oświetlenia szybko danych zmiana kolejności](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)oraz obsługę [magazynu danych w czasie rzeczywistym](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>Ocena luk w zabezpieczeniach SQL
[Ocena luk w zabezpieczeniach SQL (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) to łatwy w użyciu usługa, która stale monitoruje magazynu danych. Pomaga zapewnić wysokiego poziomu zabezpieczeń przez cały czas i spełniono zasad organizacji. Oferuje ona raportu kompleksowych funkcji zabezpieczeń wraz z możliwością wykonywania akcji zaradczych każdego znalezione problemy. Ten raport ułatwia aktywnie zarządzać stature zabezpieczenia swojej bazy danych i koncentrować na najwyższym akcji wpływ, nawet jeśli nie masz zabezpieczeń ekspertów. W przypadku dynamicznych środowiskach, w której zmiany są często i trudne do śledzenia oceny luk w zabezpieczeniach jest bezcenne w wykrywaniu ustawienia, które może narazić, Magazyn danych na ataki.

## <a name="improved-availability-with-query-restartability"></a>Wyższą dostępność, za pomocą restartability zapytania
Podczas wykonywania zapytania, może wystąpić dowolną liczbę problemów, może spowodować, że zapytanie nie powiedzie się. Zaniku połączenia sieciowego, awarii sprzętu lub innych rozłączenia. może to spowodować zakłócenie. Usługa SQL Data Warehouse obsługuje teraz restartability zapytania dla kroku lub poziomie instrukcji zapytań SELECT. 

Za pomocą Restartability zapytania zapytanie, które jest w locie, który jest przerwane z powodu błędu zostanie automatycznie uruchomiony ponownie. W zależności od liczby kroków, kształt zapytania, albo w przypadku, gdy zapytanie zostało zatrzymane w czasie wykonywania, aparat SQL Data Warehouse albo ponownie uruchom pełne zapytanie zostanie wznowione od ostatniego kroku zapytania zakończone. Z punktu widzenia użytkownika po prostu wykonuje zapytanie. 

## <a name="maintenance-scheduling-preview"></a>Konserwacja planowania (wersja zapoznawcza)
Azure SQL Data magazynu konserwacji jest teraz w wersji zapoznawczej. Ta nowa funkcja bezproblemowo integrują się planowanej konserwacji powiadomień dotyczących kondycji usługi, Monitor sprawdzanie kondycji zasobu i usługą planowania konserwacji usługi Azure SQL Data Warehouse. Planowanie konserwacji umożliwia zaplanowanie przedział czasu, gdy jest to wygodne otrzymać nowe funkcje i uaktualnień i poprawki.

Planowanie konserwacji korzysta z usługi Azure Monitor i umożliwia klientom określić, jak chcesz otrzymywać powiadomienia o zbliżającym się zdarzenia konserwacji i który zautomatyzowane przepływy powinna być wyzwolona Zarządzanie przestojów i zminimalizowania wpływu na swojej działalności. Powiadomienia mogą obejmować wiadomości e-mail lub tekstu. 

## <a name="wide-row-support-in-polybase"></a>Obsługa szerokiego wiersza w technologii Polybase
Podczas ładowania danych do usługi SQL Data Warehouse, ogólną wytyczną jest użycie [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) jest obsługa równoległe ładowania danych. To wydanie umożliwia obsługę szersze kolumny z 32 KB do 1MB — umożliwia ładowanie tabel o rozmiarze szerokości wiersza. Obsługa szerokiego wierszy upraszcza proces w przypadku tabel z wierszami szerokiego ładowania danych.

> [!Note]
> Rozmiar wiersz sumy nie może przekraczać 1 MB rozmiaru.

## <a name="additional-language-support"></a>Dodatkowe języki
W tej wersji wprowadzono obsługę następujące elementy języka T-SQL:

### <a name="stringsplit"></a>STRING_SPLIT
[STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) funkcja dzieli ciąg znaków, przy użyciu określonego separatora. STRING_SPLIT jest przydatne w scenariuszach, w której kolumna może mieć wiele wartości można przeanalizować i wstawić do innej tabeli ładowania danych.

#### <a name="example"></a>Przykład
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>KOMPRESUJ/błąd funkcji
[SKOMPRESOWAĆ](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [błąd](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) funkcje umożliwiają kompresja lub dekompresja ciąg wejściowy za pomocą algorytmu GZIP.

#### <a name="example"></a>Przykład

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>Jeśli klauzulą EXISTS dla porzucenie widoków
Dodanie w klauzuli IF ISTNIEJE [PORZUĆ widok](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) instrukcji upraszcza kod T-SQL, wymagane do usunięcia widoku z magazynu danych. Jeśli ISTNIEJE składni, gdy jest stosowany do instrukcji DROP WIDOKU spowoduje porzucenie widoku, jeśli istnieje, lub zignoruje instrukcję, jeśli widok nie istnieje.

#### <a name="example"></a>Przykład
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Czas kompilacji ulepszone wstawia pojedyncze i instrukcji DDL 
Od tradycyjnego modelu wyodrębniania, przekształcania i ładowania (ETL) do wstawiania danych często prowadzi do uruchamiania Wstawianie pojedyncze ([WSTAWIENIA wartości](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) do tabeli w bazie danych. W tej wersji poprawia wydajność operacji wstawiania singleton, skracając czas kompilacji, wymagane do wykonania tego typu instrukcji. W niektórych przypadkach poprawy obserwowanych kompilacji jest maksymalnie 3 x szybsze. To ulepszenie zmniejsza się czas wymagany do wykonania instrukcji insert pojedynczego wystąpienia. 

Ulepszanie również korzyści danych magazynów z dużą liczbą obiektów w podobny sposób zmniejszając czas kompilacji kwerendy dla instrukcji języka definicji danych (DDL), w tym tworzenia, ZMIENIANIA i usuwania działań. 

Na koniec ulepszanie zmniejsza ogólną wykonywania instrukcji, które są wykonywane za pośrednictwem szerokich tabel — tabele, które mają dużą liczbę kolumn. Poprawa jest skrócenie czasu w kroku kompilacji kwerendę, która zmniejsza całkowity czas wykonywania zapytań.

## <a name="bug-fixes"></a>Poprawki błędów

| Tytuł | Opis |
|:---|:---|
| **Poprawka, tworząc statystyki dystrybucji dla unikatowych ograniczeń** | Ta poprawka rozwiązuje błąd, który użytkownik napotka po określeniu uruchomionej UPDATE STATISTICS z tabeli tylko po tabeli ma zdefiniowane ograniczenia unique. |
| **Poprawka podczas kompilowania zapytania za pośrednictwem tabel zewnętrznych** | Ta poprawka rozwiązuje wada, która dotyczy czas kompilacji dla zapytań dotyczących tabel zewnętrznych.|
| **Poprawka podczas wykonywania instrukcji z typami duże** | Spełnić wada kompilacji przygotowanej instrukcji za pomocą parametrów zadeklarowanych jako jeden z *dużych* typów (nvarchar(max), varchar(max) i varbinary(max)). |
| **Napraw, gdy wystąpi błąd głęboko zagnieżdżonych zapytań** | Zapewnia wyczyść komunikat o błędzie, gdy zapytanie głęboko zagnieżdżone przekracza limit systemu.|
| **Napraw błędy kompilacji, jeśli instrukcja zawiera skorelowane podzapytanie i stały czas wykonywania** |Opisuje błąd kompilacji dla kwerend z określoną kombinacją skorelowany podzapytań i stałe czas wykonywania (na przykład GETDATE()).|
| **Limit czasu uzyskiwania blokady obiektu PDW i gniazd współbieżności dla statystyk automatycznych adresu** |Poprawka dodaje limit czasu blokady, aby uniemożliwić żądania statystyk automatycznych blokuje żądania zainicjowanego przez długi czas.|

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
