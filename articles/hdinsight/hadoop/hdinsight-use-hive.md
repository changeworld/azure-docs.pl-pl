---
title: Co to jest Gałąź Apache i HiveQL — Usługa Azure HDInsight
description: Apache Hive to system magazynowania danych dla Apache Hadoop. Można wysyłać zapytania do danych przechowywanych w gałęzi przy użyciu hiveQl, który podobny do Transact-SQL. W tym dokumencie dowiedz się, jak używać hive i hiveQl z usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: f7dc7b520cba2bbf2351d93795a1a26b3b5124be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471357"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co to jest Gałąź apache i hiveQl w usłudze Azure HDInsight?

[Apache Hive](https://hive.apache.org/) to system magazynowania danych dla Apache Hadoop. Gałąź umożliwia podsumowanie danych, wykonywanie zapytań i analizę danych. Zapytania gałęzi są zapisywane w HiveQL, który jest językiem kwerendpodopodejmówki do SQL.

Gałąź umożliwia projekt struktury na danych w dużej mierze nieustrukturyzowane. Po zdefiniowaniu struktury można użyć HiveQL do wykonywania zapytań o dane bez znajomości języka Java lub MapReduce.

Usługa HDInsight udostępnia kilka typów klastrów, które są dostrojone do określonych obciążeń. Następujące typy klastrów są najczęściej używane w kwerendach hive:

|Typ klastra |Opis|
|---|---|
|Zapytanie interakcyjne|Klaster Hadoop, który zapewnia funkcję [llap (Low Latency Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP) w celu skrócenia czasu odpowiedzi dla zapytań interaktywnych. Aby uzyskać więcej informacji, zobacz [Rozpocznij od zapytania interaktywnego w dokumencie HDInsight.](../interactive-query/apache-interactive-query-get-started.md)|
|Hadoop|Klaster Hadoop, który jest dostrojony do obciążeń przetwarzania wsadowego. Aby uzyskać więcej informacji, zobacz [Start z Apache Hadoop w dokumencie HDInsight.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)|
|platforma Spark|Apache Spark ma wbudowaną funkcję do pracy z Hive. Aby uzyskać więcej informacji, zobacz [Start z Apache Spark w hdinsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentu.|
|HBase|HiveQL może służyć do wykonywania zapytań o dane przechowywane w Apache HBase. Aby uzyskać więcej informacji, zobacz [Start z Apache HBase w dokumencie HDInsight.](../hbase/apache-hbase-tutorial-get-started-linux.md)|

## <a name="how-to-use-hive"></a>Jak korzystać z hive

Poniższa tabela służy do odnajdowania różnych sposobów używania funkcji Hive z programem HDInsight:

| **Użyj tej metody,** jeśli chcesz... | ... zapytania **interaktywne** | ... przetwarzanie **wsadowe** | ... z tego **systemu operacyjnego klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [Narzędzia HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X lub Windows |
| [Narzędzia HDInsight dla programu Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Dowolna (oparta na przeglądarce) |
| [Klient beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X lub Windows |
| [INTERFEJS API ODPOCZYNKU](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X lub Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Odwołanie do języka HiveQL

Numer referencyjny języka HiveQL jest dostępny w [podręczniku języka](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Ula i struktura danych

Hive rozumie, jak pracować z danymi strukturalnymi i częściowo ustrukturyzowanymi. Na przykład pliki tekstowe, w których pola są rozdzielane określonymi znakami. Następująca instrukcja HiveQL tworzy tabelę nad danymi rozdzielanym przestrzenią:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive obsługuje również niestandardowe **serializator/deserializers (SerDe)** dla złożonych lub nieregularnie strukturalnych danych. Aby uzyskać więcej informacji, zobacz jak używać niestandardowego pliku [JSON SerDe z dokumentem HDInsight.](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)

Aby uzyskać więcej informacji na temat formatów plików obsługiwanych przez hive, zobacz [podręcznik języka (https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Tabele wewnętrzne gałęzi a tabele zewnętrzne

Istnieją dwa typy tabel, które można utworzyć za pomocą gałęzi:

* __Wewnętrzny__: Dane są przechowywane w magazynie danych hive. Magazyn danych znajduje `/hive/warehouse/` się w domyślnym magazynie dla klastra.

    Użyj tabel wewnętrznych, gdy ma zastosowanie jeden z następujących warunków:

    * Dane są tymczasowe.
    * Chcesz, aby hive zarządzać cyklem życia tabeli i danych.

* __Zewnętrzne__: Dane są przechowywane poza magazynem danych. Dane mogą być przechowywane w dowolnym magazynie dostępnym przez klaster.

    Użyj tabel zewnętrznych, gdy ma zastosowanie jeden z następujących warunków:

    * Dane są również używane poza Hive. Na przykład pliki danych są aktualizowane przez inny proces (który nie blokuje plików).
    * Dane muszą pozostać w podstawowej lokalizacji, nawet po upuszczeniu tabeli.
    * Potrzebujesz niestandardowej lokalizacji, takiej jak konto magazynu nieobjętego domyślnie.
    * Program inny niż gałąź zarządza formatem danych, lokalizacją itd.

Aby uzyskać więcej informacji, zobacz wpis w blogu [Intro tabel wewnętrznych i zewnętrznych hive.](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/)

## <a name="user-defined-functions-udf"></a>Funkcje zdefiniowane przez użytkownika (UDF)

Gałąź można również rozszerzyć za pomocą **funkcji zdefiniowanych przez użytkownika (UDF).** UDF umożliwia implementowanie funkcji lub logiki, która nie jest łatwo modelowane w HiveQL. Na przykład przy użyciu plików UDF z gałęzią zobacz następujące dokumenty:

* [Używanie funkcji zdefiniowanej przez użytkownika w języku Java z gałęzią Apache](../hadoop/apache-hadoop-hive-java-udf.md)

* [Używanie funkcji zdefiniowanej przez użytkownika języka Python z gałęzią Apache](../hadoop/python-udf-hdinsight.md)

* [Używanie funkcji zdefiniowanej przez użytkownika języka C# z gałęzią Apache](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Jak dodać niestandardową funkcję zdefiniowaną przez użytkownika Apache Hive do usługi HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Przykładowa funkcja zdefiniowana przez użytkownika Apache Hive do konwersji formatów daty/godziny na sygnaturę czasową hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Przykładowe dane

Ul na HDInsight jest fabrycznie załadowany `hivesampletable`z wewnętrzną tabelą o nazwie . HDInsight zawiera również przykładowe zestawy danych, które mogą być używane z gałęzi. Te zestawy danych są `/example/data` `/HdiSamples` przechowywane w katalogach i katalogach. Te katalogi istnieją w domyślnym magazynie dla klastra.

## <a name="example-hive-query"></a>Przykładowa kwerenda hive

Następujące kolumny projektu instrukcji HiveQL `/example/data/sample.log` do pliku:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

W poprzednim przykładzie instrukcje HiveQL wykonują następujące akcje:

|Instrukcja |Opis |
|---|---|
|TABELA UPUSZCZANIA|Jeśli tabela już istnieje, usuń ją.|
|TWORZENIE TABELI ZEWNĘTRZNEJ|Tworzy nową tabelę **zewnętrzną** w obszarze Gałęzi. Tabele zewnętrzne przechowują tylko definicję tabeli w obszarze Gałęzi. Dane są pozostawiane w oryginalnej lokalizacji i w oryginalnym formacie.|
|FORMAT WIERSZA|Informuje Hive, jak dane są sformatowane. W takim przypadku pola w każdym dzienniku są oddzielone spacją.|
|PRZECHOWYWANE JAKO LOKALIZACJA PLIKU TEKSTOWEGO|Informuje hive, gdzie dane `example/data` są przechowywane (katalog) i że są przechowywane jako tekst. Dane mogą znajdować się w jednym pliku lub rozłożone na wiele plików w katalogu.|
|SELECT|Wybiera liczbę wszystkich wierszy, w których kolumna **t4** zawiera wartość **[BŁĄD]**. Ta instrukcja zwraca wartość **3,** ponieważ istnieją trzy wiersze, które zawierają tę wartość.|
|INPUT__FILE__NAME JAK '%.log'|Hive próbuje zastosować schemat do wszystkich plików w katalogu. W takim przypadku katalog zawiera pliki, które nie pasują do schematu. Aby zapobiec danych śmieci w wynikach, ta instrukcja informuje Hive, że należy zwracać tylko dane z plików kończących się w .log.|

> [!NOTE]  
> Tabele zewnętrzne powinny być używane, gdy oczekujesz, że dane źródłowe mają być aktualizowane przez źródło zewnętrzne. Na przykład zautomatyzowany proces przekazywania danych lub Operacja MapReduce.
>
> Upuszczenie tabeli zewnętrznej **nie** powoduje usunięcia danych, a jedynie usunięcie definicji tabeli.

Aby utworzyć tabelę **wewnętrzną** zamiast tabeli zewnętrznej, należy użyć następującego numerów HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Te instrukcje wykonują następujące akcje:

|Instrukcja |Opis |
|---|---|
|UTWÓRZ TABELĘ, JEŚLI NIE ISTNIEJE|Jeśli tabela nie istnieje, utwórz ją. Ponieważ **słowo** kluczowe EXTERNAL nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Tabela jest przechowywana w magazynie danych hive i jest całkowicie zarządzana przez hive.|
|Przechowywane jako ORC|Przechowuje dane w formacie Zoptymalizowany wiersz kolumnowy (ORC). ORC to wysoce zoptymalizowany i wydajny format do przechowywania danych hive.|
|WSTAW ZASTĄPIENIE ... Wybierz|Wybiera wiersze z **log4jLogs** tabeli, która zawiera **[BŁĄD],** a następnie wstawia dane do tabeli **errorLogs.**|

> [!NOTE]  
> W przeciwieństwie do tabel zewnętrznych upuszczenie tabeli wewnętrznej powoduje również usunięcie danych źródłowych.

## <a name="improve-hive-query-performance"></a>Zwiększanie wydajności zapytań programu Hive

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) to struktura, która umożliwia aplikacjom intensywnie korzystającym z danych, takim jak Hive, znacznie bardziej wydajne działanie na dużą skalę. Tez jest domyślnie włączona.  [Apache Hive na Tez dokumentów projektowych](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) zawiera szczegółowe informacje na temat wyboru implementacji i konfiguracji dostrajania.

### <a name="low-latency-analytical-processing-llap"></a>Przetwarzanie analityczne o małym opóźnieniu (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (czasami znany jako Live Long i Process) to nowa funkcja w Hive 2.0, która umożliwia buforowanie w pamięci zapytań. LLAP sprawia, że zapytania Hive znacznie szybciej, do [26x szybciej niż Hive 1.x w niektórych przypadkach.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)

Usługa HDInsight udostępnia polecenie LLAP w typie klastra zapytania interaktywnego. Aby uzyskać więcej informacji, zobacz dokument [Rozpocznij z zapytaniem interaktywnym.](../interactive-query/apache-interactive-query-get-started.md)

## <a name="scheduling-hive-queries"></a>Planowanie zapytań programu Hive

Istnieje kilka usług, które mogą służyć do uruchamiania zapytań hive w ramach zaplanowanego lub na żądanie przepływu pracy.

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory umożliwia korzystanie z usługi HDInsight jako części potoku usługi data factory. Aby uzyskać więcej informacji na temat korzystania z gałęzi z potoku, zobacz [Transformowanie danych przy użyciu działania hive w](../../data-factory/transform-data-using-hadoop-hive.md) dokumencie usługi Azure Data Factory.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Zadania gałęzi i usługi integracji programu SQL Server

Do uruchomienia zadania gałęzi można użyć programu SQL Server Integration Services (SSIS). Pakiet funkcji platformy Azure dla pakietu SSIS zawiera następujące składniki, które współpracują z zadaniami gałęzi w programie HDInsight.

* [Zadanie gałęzi usługi Azure HDInsight](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Menedżer połączeń subskrypcji platformy Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Aby uzyskać więcej informacji, zobacz dokumentację [pakietu funkcji platformy Azure.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to system przepływu pracy i koordynacji, który zarządza zadaniami Hadoop. Aby uzyskać więcej informacji na temat korzystania z Oozie z gałęzi, zobacz [Użyj Apache Oozie do definiowania i uruchamiania](../hdinsight-use-oozie-linux-mac.md) dokumentu przepływu pracy.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, czym jest gałąź i jak go używać z Hadoop w usłudze HDInsight, skorzystaj z poniższych łączy, aby zapoznać się z innymi sposobami pracy z usługą Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md)
* [Korzystanie z funkcji zdefiniowanych przez użytkownika języka Python (UDF) z gałęzią Apache i świnią Apache w formacie HDInsight](./python-udf-hdinsight.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
