---
title: Co to jest Apache Hive i HiveQL — Azure HDInsight
description: Apache Hive jest system magazynu danych dla usługi Apache Hadoop. Można tworzyć zapytania — dane przechowywane w gałęzi przy użyciu HiveQL, które podobnie do języka Transact-SQL. W tym dokumencie Dowiedz się, jak używać technologii Hive i HiveQL z usługi Azure HDInsight.
keywords: hiveql, co to jest hive i hadoop hiveql, jak wykorzystać technologię hive, Dowiedz się, hive, co to jest gałąź
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2019
ms.openlocfilehash: 1f0746436fa980b6becfa7a88560734aa07a54e2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58801933"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?

[Apache Hive](https://hive.apache.org/) to system magazynu danych dla usługi Apache Hadoop. Gałąź umożliwia dane podsumowań, zapytań i analizy danych. Zapytania programu hive są zapisywane w HiveQL, który jest językiem zapytań, podobnych do bazy danych SQL.

Gałąź pozwala na strukturę projektu w dużej mierze pozbawionych struktury danych. Po zdefiniowaniu struktury, można użyć HiveQL do wykonywania zapytań o dane bez znajomości języka Java lub MapReduce.

HDInsight zawiera kilka typów klastra, są one dostrojone dla konkretnych obciążeń. Następujące typy klastrów są najczęściej używane dla zapytań programu Hive:

* __Zapytanie interakcyjne__: Klaster Hadoop, która zapewnia [niskim opóźnieniu analitycznego przetwarzania (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkcji, aby skrócić czas odpowiedzi dla zapytań interakcyjnych. Aby uzyskać więcej informacji, zobacz [Start z zapytaniem interakcyjnym w HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

* __Hadoop__: Klaster Hadoop jest ona dostrojona dla obciążeń przetwarzania wsadowego. Aby uzyskać więcej informacji, zobacz [Start z usługą Apache Hadoop w HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentu.

* __Spark__: Platforma Apache Spark ma wbudowaną funkcję do pracy z programu Hive. Aby uzyskać więcej informacji, zobacz [rozpoczynać platformy Apache Spark w HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentu.

* __HBase__: HiveQL może służyć do zapytania o dane przechowywane w bazy danych Apache HBase. Aby uzyskać więcej informacji, zobacz [rozpoczynać bazy danych Apache HBase na HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentu.

## <a name="how-to-use-hive"></a>Jak wykorzystać technologię Hive

Skorzystaj z poniższej tabeli, aby odnaleźć różne sposoby korzystanie z programu Hive z HDInsight:

| **Ta metoda** Jeśli chcesz... | ... **interaktywne** zapytań | ...**partii** przetwarzania | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [Narzędzia HDInsight tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X lub Windows |
| [Narzędzia HDInsight tools for Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Widok programu hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Wszelkie (opartych na przeglądarce) |
| [Klient z usługi beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X lub Windows |
| [Interfejs API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X lub Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |


## <a name="hiveql-language-reference"></a>Dokumentacja języka HiveQL

Dokumentacja języka HiveQL jest dostępna w [Podręcznik języka (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Struktura gałęzi i danych

Gałąź rozumie sposób pracy z danymi ze strukturą lub o połowicznej strukturze. Na przykład pliki tekstowe gdy pola są rozdzielone według określonych znaków. Poniższa instrukcja HiveQL tworzy tabelę danych rozdzielonych spacjami:

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

Hive obsługuje też niestandardowy **serializator/deserializers (SerDe)** złożonego lub nieregularnie strukturalnych danych. Aby uzyskać więcej informacji, zobacz [jak używać niestandardowego SerDe JSON z HDInsight](https://web.archive.org/web/20190217104719/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) dokumentu.

Aby uzyskać więcej informacji na temat formatów plików obsługiwanych przez program Hive, zobacz [(ręczne) języka https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Tabele wewnętrzne vs zewnętrznych tabel programu hive

Istnieją dwa rodzaje tabel, które można utworzyć przy użyciu technologii Hive:

* __Wewnętrzny__: Dane są przechowywane w magazynie danych programu Hive. Magazyn danych znajduje się w `/hive/warehouse/` na domyślny magazyn dla klastra.

    Korzystanie z tabel wewnętrznych, gdy występuje jeden z następujących warunków:

    * Dane są tymczasowe.
    * Chcesz, aby gałąź do zarządzania cyklem życia tabeli i danych.

* __Zewnętrzne__: Dane są przechowywane poza w magazynie danych. Dane mogą być przechowywane na jeden z magazynów dostępnych przez klaster.

    Użyj tabel zewnętrznych, gdy występuje jeden z następujących warunków:

    * Dane, jest również używane poza Hive. Na przykład pliki danych są aktualizowane przez inny proces (co oznacza, że nie blokuje pliki.)
    * Dane muszą pozostać w lokalizacji podstawowej, nawet po upuszczenie w tabeli.
    * Należy lokalizacjach niestandardowych, takich jak konta magazynu innego niż domyślny.
    * Program niż gałąź zarządza format danych, lokalizacja itp.

Aby uzyskać więcej informacji, zobacz [Hive wewnętrznych i zewnętrznych wprowadzenie tabel] [ cindygross-hive-tables] wpis w blogu.

## <a name="user-defined-functions-udf"></a>Funkcje zdefiniowane przez użytkownika (UDF)

Można również rozszerzać hive za pośrednictwem **funkcje zdefiniowane przez użytkownika (UDF)**. Funkcji zdefiniowanej przez użytkownika, można zaimplementować funkcje lub logikę, która nie jest łatwo modelowane w HiveQL. Przykład użycia funkcji zdefiniowanych przez użytkownika za pomocą technologii Hive na ten temat można znaleźć w następujących dokumentach:

* [Apache Hive za pomocą funkcji zdefiniowanej przez użytkownika języka Java](../hadoop/apache-hadoop-hive-java-udf.md)

* [Apache Hive za pomocą funkcji zdefiniowanej przez użytkownika języka Python](../hadoop/python-udf-hdinsight.md)

* [Użyj C# funkcji zdefiniowanej przez użytkownika za pomocą technologii Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Jak dodać niestandardowe Apache Hive funkcji zdefiniowanej przez użytkownika do HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Przykład Apache Hive funkcji zdefiniowanej przez użytkownika, aby przekonwertować formaty daty/godziny na gałąź znacznik czasu:](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Przykładowe dane

Gałąź w HDInsight jest dostarczany wstępnie załadowane z wewnętrznej tabeli o nazwie `hivesampletable`. HDInsight udostępnia również przykładowy zbiory danych, które mogą być używane z programu Hive. Te zestawy danych są przechowywane w `/example/data` i `/HdiSamples` katalogów. Te katalogi istnieje w domyślny magazyn dla klastra.

## <a id="job"></a>Przykładowe zapytanie programu Hive

Poniższe instrukcje HiveQL projektu kolumn na `/example/data/sample.log` pliku:

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

W poprzednim przykładzie instrukcje HiveQL, wykonaj następujące czynności:


* `DROP TABLE`: Jeśli tabela już istnieje, należy go usunąć.

* `CREATE EXTERNAL TABLE`: Tworzy nową **zewnętrznych** tabeli programu Hive. Tabele zewnętrzne przechowywać wyłącznie w definicji tabeli programu Hive. Danych pozostanie w oryginalnej lokalizacji i w oryginalnym formacie.

* `ROW FORMAT`: Informuje, Hive, w jaki sposób dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.

* `STORED AS TEXTFILE LOCATION`: Informuje, Hive, w którym przechowywane są dane ( `example/data` katalogu) i które są przechowywane jako tekst. Dane mogą być w jednym pliku lub rozkładają się na wiele plików w katalogu.

* `SELECT`: Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Ta instrukcja zwraca wartość **3** ponieważ istnieją trzy wiersze, które zawierają tę wartość.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive podejmie próbę zastosowania schematu do wszystkich plików w katalogu. W tym przypadku katalog zawiera pliki, które nie pasuje do schematu. Aby zapobiec odzyskiwanie danych w wynikach, ta instrukcja każe Hive, firma Microsoft z plików kończy się rozszerzeniem tylko powinna zwrócić dane. log.

> [!NOTE]  
> Jeśli potrzebujesz danych bazowych do zaktualizowania za pomocą zewnętrznego źródła, należy używać tabel zewnętrznych. Na przykład proces przekazywania danych lub operacji MapReduce.
>
> Usunięcie tabeli zewnętrznej jest **nie** usuwania danych, usuwa tylko w definicji tabeli.

Aby utworzyć **wewnętrzny** tabeli zamiast zewnętrznych, za pomocą języka HiveQL następujące:

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

Te instrukcje, wykonaj następujące czynności:

* `CREATE TABLE IF NOT EXISTS`: Jeśli tabela nie istnieje, należy go utworzyć. Ponieważ **zewnętrznych** — słowo kluczowe nie jest używany, ta instrukcja tworzy wewnętrznej tabeli. Tabela jest przechowywany w magazynie danych programu Hive i jest całkowicie zarządzana przez Hive.

* `STORED AS ORC`: Przechowuje dane w formacie zoptymalizowane pod kątem wiersz kolumnowych (ORC). ORC jest wysoce zoptymalizowane i wydajne formatu do przechowywania danych programu Hive.

* `INSERT OVERWRITE ... SELECT`: Wybiera wiersze z **log4jLogs** tabelę, która zawiera **[Błąd]**, a następnie wstawia dane do **przesłano** tabeli.

> [!NOTE]  
> W przeciwieństwie do tabel zewnętrznych porzucenie wewnętrznej tabeli spowoduje również usunięcie danych bazowych.

## <a name="improve-hive-query-performance"></a>Poprawić wydajność zapytań technologii Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) to struktura, która umożliwia aplikacji intensywnie korzystających z danych, takich jak Hive, umożliwia bardziej wydajne uruchamianie na dużą skalę. Tez jest domyślnie włączona.  [Apache Hive w aplikacji Tez dokumentów projektowych](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) zawiera szczegółowe informacje na temat opcji wdrażania i dostrajania konfiguracji.

### <a name="low-latency-analytical-processing-llap"></a>Małe opóźnienia przetwarzania analitycznego (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (czasem nazywane Live Long and Process) to nowa funkcja programu Hive w wersji 2.0 umożliwiająca wewnątrzpamięciowa pamięć podręczna zapytań. LLAP sprawia, że zapytania programu Hive, które znacznie szybciej, maksymalnie [26 x szybciej niż Hive 1.x w niektórych przypadkach](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight udostępnia LLAP w typ klastra zapytania interakcyjnego. Aby uzyskać więcej informacji, zobacz [Start z zapytaniem interakcyjnym](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

## <a name="scheduling-hive-queries"></a>Planowanie zapytań programu Hive

Istnieje kilka usług, które mogą służyć do uruchamiania zapytań programu Hive w ramach przepływu pracy zaplanowane lub na żądanie.

### <a name="azure-data-factory"></a>Azure Data Factory

Usługa Azure Data Factory umożliwia HDInsight jako część potoku usługi fabryka danych. Aby uzyskać więcej informacji na temat korzystania z programu Hive w potoku, zobacz [przekształcania danych za pomocą działania programu Hive w usłudze Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) dokumentu.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Zadania programu hive i SQL Server Integration Services

SQL Server Integration Services (SSIS) służy do uruchamiania zadań Hive. Pakiety Azure Feature Pack dla usług SSIS zawiera następujące składniki, współpracujących z zadań Hive HDInsight.

* [Zadanie usługi Azure HDInsight Hive][hivetask]

* [Menedżer połączeń w usłudze Azure subskrypcji][connectionmanager]

Aby uzyskać więcej informacji, zobacz [pakiety Azure Feature Pack] [ ssispack] dokumentacji.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie jest systemem przepływu pracy i koordynacji, który zarządza zadaniami na platformie Hadoop. Aby uzyskać więcej informacji na temat korzystania z technologii Oozie z technologią Hive, zobacz [Użyj Apache Oozie, aby zdefiniować i uruchomić przepływ pracy](../hdinsight-use-oozie-linux-mac.md) dokumentu.

## <a id="nextsteps"></a>Następne kroki

Teraz, kiedy znasz już gałąź jest i jak z niej korzystać z usługą Hadoop w HDInsight, użyj następujących linków, aby poznać inne sposoby pracy z usługi Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z programu Apache Pig z usługą HDInsight][hdinsight-use-pig]
* [Korzystanie z zadań MapReduce z HDInsight][hdinsight-use-mapreduce]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: https://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
