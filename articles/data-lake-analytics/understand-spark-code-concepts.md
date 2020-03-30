---
title: Poznaj pojęcia dotyczące kodu Platformy Spark apache dla deweloperów U-SQL usługi Azure Data Lake Analytics.
description: W tym artykule opisano pojęcia platformy Apache Spark, aby pomóc deweloperom U-SQL zrozumieć pojęcia kodu platformy Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424007"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Zrozumienie kodu Platformy Spark apache dla deweloperów U-SQL

Ta sekcja zawiera wskazówki wysokiego poziomu dotyczące przekształcania skryptów U-SQL do platformy Apache Spark.

- Zaczyna się od [porównania paradygmatów przetwarzania obu języków](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Zawiera wskazówki dotyczące:
   - [Przekształcanie skryptów,](#transform-u-sql-scripts) w tym [wyrażeń zestawu wierszy](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) języka U-SQL
   - [Kod .NET](#transform-net-code)
   - [Typy danych](#transform-typed-values)
   - [Obiekty katalogu](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Zrozumienie języka U-SQL i Spark oraz paradygmatów przetwarzania

Przed rozpoczęciem migracji skryptów U-SQL usługi Azure Data Lake Analytics do platformy Spark warto zapoznać się z ogólnym językiem i filozofiami przetwarzania obu systemów.

U-SQL jest sql-jak deklaratywny język kwerendy, który używa paradygmatu przepływu danych i pozwala łatwo osadzać i skalować w poziomie kodu użytkownika napisanego w .NET (na przykład C#), Python i R. Rozszerzenia użytkownika można zaimplementować proste wyrażenia lub funkcje zdefiniowane przez użytkownika, ale może również zapewnić użytkownikowi możliwość zaimplementowania tak zwanych operatorów zdefiniowanych przez użytkownika, które implementują operatorów niestandardowych do wykonywania przekształceń na poziomie zestawu wierszy, wyodrębnienia i zapisu danych wyjściowych.

Spark to struktura skalowania w poziomie oferująca kilka powiązań językowych w scali, Java, Pythonie, .NET itp., w której przede wszystkim piszesz kod w jednym z tych języków, tworzysz abstrakcje danych o nazwie odporne rozproszone zestawy danych (RDD), ramki danych i zestawy danych oraz następnie użyj języka specyficznego dla domeny podobnej do LINQ (DSL), aby je przekształcić. Zapewnia również SparkSQL jako deklaratywny podjęzyk na abstrakcjach dataframe i zestaw danych. DSL zawiera dwie kategorie operacji, przekształcenia i akcje. Zastosowanie przekształceń do abstrakcji danych nie spowoduje wykonania transformacji, ale zamiast tego nagromadzenia planu wykonania, który zostanie przesłany do oceny za pomocą akcji (na przykład zapisanie wyniku w tymczasowej tabeli lub pliku lub wydrukowanie wynik).

Tak więc podczas tłumaczenia skryptu U-SQL do programu Spark, trzeba będzie zdecydować, który język ma być używany do co najmniej generowania abstrakcji ramki danych (który jest obecnie najczęściej używane abstrakcji danych) i czy chcesz napisać deklaratywne przekształcenia przepływu danych przy użyciu DSL lub SparkSQL. W niektórych bardziej złożonych przypadkach może być konieczne podzielenie skryptu U-SQL na sekwencję platformy Spark i inne kroki zaimplementowane za pomocą usługi Azure Batch lub azure functions.

Ponadto usługa Azure Data Lake Analytics oferuje u-SQL w środowisku usługi zadań bez serwera, podczas gdy zarówno usługi Azure Databricks, jak i usługi Azure HDInsight oferują platformę Spark w formie usługi klastrowania. Podczas przekształcania aplikacji, należy wziąć pod uwagę implikacje teraz tworzenia, zmiany rozmiaru, skalowania i likwidacji klastrów.

## <a name="transform-u-sql-scripts"></a>Przekształcanie skryptów U-SQL

Skrypty U-SQL wykonaj następujący wzorzec przetwarzania:

1. Dane są odczytywane z plików nieustrukturyzowanych, przy użyciu `EXTRACT` instrukcji, specyfikacji lokalizacji lub zestawu plików oraz wbudowanego lub zdefiniowanego przez użytkownika ekstraktora i żądanego schematu lub z tabel U-SQL (tabel zarządzanych lub zewnętrznych). Jest reprezentowana jako zestaw wierszy.
2. Zestawy wierszy są przekształcane w wielu instrukcjach U-SQL, które stosują wyrażenia U-SQL do zestawów wierszy i tworzą nowe zestawy wierszy.
3. Na koniec wynikowe zestawy wierszy są dane wyjściowe do plików przy użyciu `OUTPUT` instrukcji, która określa lokalizacje i wbudowany lub zdefiniowany przez użytkownika wynikowy lub do tabeli U-SQL.

Skrypt jest oceniany leniwie, co oznacza, że każdy krok wyodrębniania i transformacji składa się z drzewa wyrażeń i globalnie oceniane (przepływ danych).

Programy spark są podobne, ponieważ można użyć łączników Spark do odczytu danych i tworzenia ramek danych, a następnie zastosować przekształcenia na ramkach danych przy użyciu DSL podobnej do LINQ lub SparkSQL, a następnie zapisać wynik w plikach, tymczasowych tabelach Spark, niektórych typów języków programowania lub konsoli.

## <a name="transform-net-code"></a>Przekształcanie kodu platformy .NET

Język wyrażenia U-SQL jest C# i oferuje wiele sposobów skalowania w poziomie niestandardowego kodu .NET.

Ponieważ spark obecnie nie obsługuje natywnie wykonywania kodu .NET, trzeba będzie albo przepisać wyrażenia do równoważnego wyrażenia Spark, Scala, Java lub Python lub znaleźć sposób wywołania kodu .NET. Jeśli skrypt korzysta z bibliotek platformy .NET, dostępne są następujące opcje:

- Przetłumacz swój kod .NET na Scala lub Python.
- Podziel skrypt U-SQL na kilka kroków, w których używasz procesów usługi Azure Batch do zastosowania przekształceń platformy .NET (jeśli można uzyskać dopuszczalną skalę)
- Użyj powiązania języka .NET dostępnego w open source o nazwie Moebius. Ten projekt nie jest w stanie obsługiwanym.

W każdym przypadku, jeśli masz dużą ilość logiki platformy .NET w skryptach U-SQL, skontaktuj się z nami za pośrednictwem przedstawiciela konta Microsoft, aby uzyskać dalsze wskazówki.

Poniższe szczegóły są dla różnych przypadków .NET i C# użycia w skryptach U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Przekształcanie scalar wbudowanych wyrażeń języka C# U-SQL

Język wyrażenia U-SQL to C#. Wiele scalar wbudowanych wyrażeń U-SQL są implementowane natywnie dla poprawy wydajności, podczas gdy bardziej złożone wyrażenia mogą być wykonywane za pomocą wywoływania do platformy .NET.

Platforma Spark ma swój własny język wyrażeń skalarnych (jako część DSL lub SparkSQL) i umożliwia wywoływanie funkcji zdefiniowanych przez użytkownika napisanych w jego języku hostingu.

Jeśli masz wyrażenia skalarne w U-SQL, należy najpierw znaleźć najbardziej odpowiednie natywnie zrozumiałe wyrażenie skalarne Platformy Spark, aby uzyskać jak największą wydajność, a następnie mapować inne wyrażenia do zdefiniowanej przez użytkownika funkcji wybranego języka hostingu platformy Spark.

Należy pamiętać, że .NET i C# mają semantykę innego typu niż języki hostingu platformy Spark i dsl platformy Spark. Zobacz [poniżej,](#transform-typed-values) aby uzyskać więcej informacji na temat różnic systemowych typu.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Przekształcanie zdefiniowanych przez użytkownika funkcji skalarnych .NET i agregatorów zdefiniowanych przez użytkownika

U-SQL zapewnia sposoby wywoływania dowolnych funkcji skalarnych .NET i wywoływania agregatorów zdefiniowanych przez użytkownika zapisanych w programie .NET.

Spark oferuje również obsługę funkcji zdefiniowanych przez użytkownika i agregatorów zdefiniowanych przez użytkownika napisanych w większości języków hostingu, które mogą być wywoływane z dsl i sparksql spark.

### <a name="transform-user-defined-operators-udos"></a>Przekształcanie operatorów zdefiniowanych przez użytkownika (UDO)

U-SQL udostępnia kilka kategorii operatorów zdefiniowanych przez użytkownika (UDO), takich jak ekstraktory, wyjścia, reduktory, procesory, aplikacje i kombajny, które mogą być zapisywane w .NET (i - do pewnego stopnia - w Pythonie i R).

Platforma Spark nie oferuje tego samego modelu rozszerzalności dla operatorów, ale ma równoważne możliwości dla niektórych.

Spark odpowiednik ekstraktorów i outputters jest Łączniki Platformy Spark. Dla wielu ekstraktorów U-SQL może się okazać łącznika równoważne w społeczności Platformy Spark. Dla innych, trzeba będzie napisać łącznik niestandardowy. Jeśli ekstraktor U-SQL jest złożony i korzysta z kilku bibliotek .NET, może być lepiej zbudować łącznik w Scali, który używa interop do wywołania biblioteki .NET, która wykonuje rzeczywiste przetwarzanie danych. W takim przypadku należy wdrożyć środowisko uruchomieniowe .NET Core w klastrze platformy Spark i upewnić się, że biblioteki .NET, do których istnieją odwołania, są zgodne ze standardem .NET Standard 2.0.

Inne typy UDO U-SQL będą musiały zostać przepisane przy użyciu funkcji zdefiniowanych przez użytkownika i agregatorów oraz semantycznie odpowiednich spark DLS lub SparkSQL wyrażenie. Na przykład procesor może być mapowany do SELECT różnych wywołań UDF, pakowane jako funkcja, która przyjmuje dataframe jako argument i zwraca dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Przekształcanie bibliotek opcjonalnych języka U-SQL

U-SQL zapewnia zestaw bibliotek opcjonalnych i demo, które oferują [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, obsługa AVRO](https://github.com/Azure/usql/tree/master/Examples/DataFormats)i niektóre [funkcje usług kognitywnych.](data-lake-analytics-u-sql-cognitive.md)

Spark oferuje własną integrację Języka Python i R, pySpark i SparkR odpowiednio i zapewnia łączniki do odczytu i zapisu JSON, XML i AVRO.

Jeśli chcesz przekształcić skrypt odwołujący się do bibliotek usług kognitywnych, zalecamy skontaktowanie się z nami za pośrednictwem przedstawiciela konta Microsoft.

## <a name="transform-typed-values"></a>Przekształcanie wpisanych wartości

Ponieważ system typu U-SQL jest oparty na systemie typu .NET i Spark ma swój własny system typu, który ma wpływ na powiązanie języka hosta, należy upewnić się, że typy, na których działasz, są bliskie i dla niektórych typów zakresy typów, precyzja i/lub skala mogą się nieznacznie różnić. Ponadto U-SQL i Spark `null` traktować wartości inaczej.

### <a name="data-types"></a>Typy danych

W poniższej tabeli przedstawiono równoważne typy w programach Spark, Scala i PySpark dla danego typu języka U-SQL.

| U-SQL | platforma Spark |  Scala | PySpark (pyspark) |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Aby uzyskać więcej informacji, zobacz:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL i DataFrames Typy](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Typy wartości Scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Leczenie NULL

W programie Spark typy na wartość domyślna zezwalają na wartości NULL, podczas gdy w języku U-SQL jawnie oznacza się skalarne, niebędące obiektami jako nullable. Podczas spark pozwala zdefiniować kolumnę jako nie można anulować, nie będzie wymuszać ograniczenia i [może prowadzić do złego wyniku](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

W spark null wskazuje, że wartość jest nieznany. Wartość spark NULL różni się od dowolnej wartości, w tym siebie. Porównania między dwiema wartościami spark NULL lub między wartością NULL a dowolną inną wartością zwracają wartość nieznaną, ponieważ wartość każdej wartości NULL jest nieznana.  

To zachowanie różni się od U-SQL, który następuje `null` Semantyka C#, gdzie różni się od dowolnej wartości, ale równa sobie.  

W ten sposób `SELECT` instrukcja SparkSQL, która `WHERE column_name = NULL` używa zwraca `column_name`zero wierszy, nawet jeśli istnieją wartości `column_name` NULL w `null`, podczas gdy w U-SQL, zwróci wiersze, gdzie jest ustawiona na . Podobnie Spark `SELECT` instrukcji, która `WHERE column_name != NULL` używa zwraca zero wierszy, nawet jeśli `column_name`istnieją wartości inne niż null w , podczas gdy w U-SQL, zwróci wiersze, które mają inne niż null. W związku z tym jeśli chcesz Semantyki sprawdzania null U-SQL, należy użyć [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) i [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) odpowiednio (lub ich odpowiednik DSL).

## <a name="transform-u-sql-catalog-objects"></a>Przekształcanie obiektów katalogu U-SQL

Jedną z głównych różnic jest to, że skrypty U-SQL mogą korzystać z jego obiektów katalogu, z których wiele nie ma bezpośredniego odpowiednika Platformy Spark.

Platforma Spark zapewnia obsługę koncepcji magazynu Hive Meta, głównie baz danych i tabel, dzięki czemu można mapować bazy danych i schematy U-SQL do baz danych hive i tabel U-SQL do tabel Platformy Spark (zobacz [Przenoszenie danych przechowywanych w tabelach U-SQL),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)ale nie obsługuje widoków, funkcji o wartości tabeli (TVF), procedur przechowywanych, zestawów U-SQL, zewnętrznych źródeł danych itp.

Obiekty kodu U-SQL, takie jak widoki, pliki TVF, procedury przechowywane i zestawy, mogą być modelowane za pomocą funkcji kodu i bibliotek w programie Spark i odwoływane przy użyciu funkcji języka hosta i mechanizmów abstrakcji proceduralnej (na przykład poprzez importowanie python lub odwoływanie się do funkcji Scala).

Jeśli katalog U-SQL został użyty do udostępniania danych i obiektów kodu w projektach i zespołach, należy użyć równoważnych mechanizmów udostępniania (na przykład Maven do udostępniania obiektów kodu).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Przekształcanie wyrażeń zestawu wierszy U-SQL i wyrażeń skalarnych opartych na języku SQL

Podstawowym językiem U-SQL jest przekształcanie zestawów wierszy i jest oparty na sql. Poniżej znajduje się niewyczerpywna lista najczęściej wyrażeń zestawu wierszy oferowanych w U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Agregaty+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` wyrażenia
- `CROSS`/`OUTER``APPLY` wyrażenia
- `PIVOT`/`UNPIVOT`Wyrażenia
- `VALUES`konstruktor zestawu wierszy

- Ustawianie wyrażeń`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Ponadto U-SQL zapewnia wiele wyrażeń skalarnych opartych na języku SQL, takich jak

- `OVER`wyrażeń okiennych
- szereg wbudowanych agregatorów i funkcji`SUM` `FIRST` rankingowych ( itp.)
- Niektóre z najbardziej znanych wyrażeń `CASE` `LIKE`skalarnych `IN` `AND`SQL: , , (`NOT`) , `OR` itp.

Spark oferuje równoważne wyrażenia w formularzu DSL i SparkSQL dla większości z tych wyrażeń. Niektóre wyrażenia nie są obsługiwane natywnie w spark będą musiały być przepisane przy użyciu kombinacji natywnych wyrażeń Platformy Spark i wzorce semantycznie równoważne. Na przykład, `OUTER UNION` będzie musiał zostać przełożony na równoważną kombinację prognoz i związków.

Ze względu na różne obsługi wartości NULL sprzężenia U-SQL zawsze będzie pasować do wiersza, jeśli obie kolumny są porównywane zawierają wartość NULL, podczas gdy sprzężenie w programie Spark nie będzie zgodne z takimi kolumnami, chyba że jawne kontrole null są dodawane.

## <a name="transform-other-u-sql-concepts"></a>Przekształcanie innych pojęć języka U-SQL

U-SQL oferuje również wiele innych funkcji i pojęć, takich jak zapytania federacyjne względem baz danych programu SQL Server, `OPTION` parametrów, zmiennych skalarnych i lambda, zmiennych systemowych, wskazówek.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Zapytania federacyjne dotyczące baz danych programu SQL Server/tabel zewnętrznych

U-SQL udostępnia źródło danych i tabele zewnętrzne, a także bezpośrednie zapytania względem usługi Azure SQL Database. Chociaż spark nie oferuje te same abstrakcje obiektów, zapewnia [łącznik platformy Spark dla usługi Azure SQL Database,](../sql-database/sql-database-spark-connector.md) który może służyć do kwerendy baz danych SQL.

### <a name="u-sql-parameters-and-variables"></a>Parametry i zmienne U-SQL

Parametry i zmienne użytkownika mają równoważne pojęcia w spark i ich języków hostingu.

Na przykład w Scali można zdefiniować zmienną za pomocą słowa kluczowego: `var`

```
var x = 2 * 3;
println(x)
```

Zmienne systemowe U-SQL (zmienne `@@`zaczynające się od) można podzielić na dwie kategorie:

- Ustawialne zmienne systemowe, które można ustawić na określone wartości, aby wpłynąć na zachowanie skryptów
- Zmienne systemu informacyjnego, które pytają o informacje o systemie i poziomie zadania

Większość zmiennych systemowych settable nie ma bezpośredniego odpowiednika w spark. Niektóre zmienne systemu informacyjnego mogą być modelowane przez przekazywanie informacji jako argumentów podczas wykonywania zadania, inne mogą mieć równoważną funkcję w języku hostingu platformy Spark.

### <a name="u-sql-hints"></a>Wskazówki U-SQL

U-SQL oferuje kilka sposobów składni, aby zapewnić wskazówki do optymalizatora kwerend i aparatu wykonywania:  

- Ustawianie zmiennej systemowej U-SQL
- klauzula `OPTION` skojarzona z wyrażeniem zestawu wierszy w celu dostarczenia wskazówki dotyczącej danych lub planu
- wskazówka sprzężenia w składni wyrażenia sprzężenia (na przykład `BROADCASTLEFT`)

Optymalizator zapytań oparty na kosztach firmy Spark ma własne możliwości dostarczania wskazówek i dostosowywania wydajności kwerendy. Zapoznaj się z odpowiednią dokumentacją.

## <a name="next-steps"></a>Następne kroki

- [Zrozumienie formatów danych platformy Spark dla deweloperów języka U-SQL](understand-spark-data-formats.md)
- [Platforma .NET dla platformy Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Uaktualnianie rozwiązań do analizy dużych zbiorów danych z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Przekształcanie danych przy użyciu aktywności platformy Spark w usłudze Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Przekształcanie danych przy użyciu działania gałęzi usługi Hadoop w usłudze Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co to jest platforma Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
