---
title: Co to jest Apache Hive i HiveQL - Azure HDInsight | Dokumentacja firmy Microsoft
description: Apache Hive jest platforma Hadoop w systemie magazynu danych. Można badać danych przechowywanych w gałęzi przy użyciu HiveQL, który podobnie do języka Transact-SQL. W tym dokumencie opisano do korzystania z usługi Azure HDInsight Hive i HiveQL.
keywords: hiveql, co to jest hive i hadoop hiveql, jak korzystanie z programu hive, Dowiedz się, hive, co to jest gałąź
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 9f67bc93758b98060efbef2cb7f1c200a62d7e65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?

[Apache Hive](http://hive.apache.org/) jest systemem magazynu danych dla platformy Hadoop. Gałąź umożliwia podsumowania danych, wyszukiwanie i analizy danych. Zapytań programu hive są zapisywane w HiveQL, który jest językiem zapytania podobne do bazy danych SQL.

Gałąź umożliwia struktury projektu na danych niestrukturalnych w dużej mierze. Po zdefiniowaniu struktury służy HiveQL do wykonywania zapytań o dane bez znajomości języka Java lub MapReduce.

Usługa HDInsight zapewnia kilka typów klastra, które są dopasowane do konkretnych obciążeń. Następujące typy klastrów są najczęściej używane dla zapytań programu Hive:

* __Interakcyjne zapytania__: klastra usługi Hadoop A, która zapewnia [niskim opóźnieniu analitycznego przetwarzania (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkcji, aby skrócić czas odpowiedzi dla interakcyjnych zapytań. Aby uzyskać więcej informacji, zobacz [rozpoczynać interakcyjne zapytania w usłudze HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

* __Hadoop__: klaster A Hadoop dostosowana pod kątem obciążeń przetwarzania wsadowego. Aby uzyskać więcej informacji, zobacz [Start z usługą Hadoop w usłudze HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentu.

* __Platforma Spark__: platforma Apache Spark ma wbudowaną funkcję do pracy z gałęzi. Aby uzyskać więcej informacji, zobacz [rozpoczynać Spark w usłudze HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentu.

* __HBase__: HiveQL można wykonać zapytania o dane przechowywane w bazie danych HBase. Aby uzyskać więcej informacji, zobacz [rozpoczynać się od bazy danych HBase w usłudze HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentu.

## <a name="how-to-use-hive"></a>Jak używać gałęzi

Skorzystaj z poniższej tabeli, aby odnaleźć różne sposoby korzystanie z programu Hive z usługą HDInsight:

| **Ta metoda** Jeśli chcesz... | ... **interakcyjne** zapytań | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [Widok gałęzi](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Wszelkie (opartych na przeglądarce) |
| [Beeline klienta](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X lub systemu Windows |
| [Interfejs API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux lub Windows * |Linux, Unix, Mac OS X lub systemu Windows |
| [Narzędzia HDInsight tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X lub systemu Windows |
| [Narzędzia HDInsight tools for Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux lub Windows * |Windows |
| [Środowisko Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux lub Windows * |Windows |

> [!IMPORTANT]
> \* Linux jest tylko system operacyjny używany w usłudze HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="hiveql-language-reference"></a>Dokumentacja języka HiveQL

Dokumentacja języka HiveQL jest dostępna w [ręcznego języka (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Struktura danych i hive

Gałąź rozumie, jak pracować z strukturalnych i częściowo ustrukturyzowanych danych. Na przykład pliki tekstowe gdy pola są rozdzielone określonych znaków. Następująca instrukcja HiveQL tworzy tabelę danych rozdzielonych spacjami:

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

Hive obsługuje również niestandardowe **serializator/deserializers (SerDe)** złożonego lub nieregularnych strukturalnych danych. Aby uzyskać więcej informacji, zobacz [sposobu używania niestandardowych SerDe JSON z usługą HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) dokumentu.

Aby uzyskać więcej informacji na formaty plików obsługiwane przez Hive, zobacz [(ręczna) językahttps://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Hive tabel zewnętrznych vs wewnętrzny tabel

Istnieją dwa typy tabel, które można utworzyć przy użyciu Hive:

* __Wewnętrzny__: dane są przechowywane w magazynie danych programu Hive. Magazyn danych znajduje się pod adresem `/hive/warehouse/` na domyślny magazyn dla klastra.

    Użyj wewnętrznego tabel, gdy zastosować jedną z następujących warunków:

    * Dane są tymczasowe.
    * Ma gałęzi do zarządzania cyklem życia tabeli i danych.

* __Zewnętrzne__: dane są przechowywane poza hurtowni danych. Dane mogą być przechowywane na jeden z magazynów dostępnych dla klastra.

    Używać tabel zewnętrznych, zastosuj jedną z następujących warunków:

    * Dane służy także poza Hive. Na przykład pliki danych są aktualizowane przez inny proces (co oznacza, że nie blokuje pliki.)
    * Dane muszą pozostać w lokalizacji źródłowej, nawet po usunięcie tabeli.
    * Należy lokalizacjach niestandardowych, takich jak konta magazynu z systemem innym niż domyślny.
    * Z programem innym niż hive zarządza format danych, lokalizacji itp.

Aby uzyskać więcej informacji, zobacz [Hive wewnętrznych i zewnętrznych wprowadzenie tabel] [ cindygross-hive-tables] wpis w blogu.

## <a name="user-defined-functions-udf"></a>Funkcje zdefiniowane przez użytkownika (UDF)

Można również rozszerzać hive za pośrednictwem **funkcje zdefiniowane przez użytkownika (UDF)**. UDF pozwala na wdrożenie funkcji lub logikę, która nie jest łatwo zgodnie z modelem w HiveQL. Na przykład za pomocą funkcji UDF przy użyciu Hive można znaleźć w następujących dokumentach:

* [Użyj funkcji zdefiniowanej przez użytkownika języka Java przy użyciu Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Użyj funkcji zdefiniowanej przez użytkownika Python z gałęzi](../hadoop/python-udf-hdinsight.md)

* [C# — funkcja zdefiniowana przez użytkownika za pomocą gałęzi](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Jak dodać niestandardowe funkcji zdefiniowanej przez użytkownika gałęzi do usługi HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Przykład Hive funkcji zdefiniowanej przez użytkownika konwersji formaty daty/godziny ze znacznikiem czasu gałęzi](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Przykładowe dane

Hive w usłudze HDInsight jest dostarczany wstępnie załadowane z wewnętrznej tabeli o nazwie `hivesampletable`. Usługa HDInsight zapewnia również przykład zestawów danych, które mogą być używane z gałęzi. Te zestawy danych są przechowywane w `/example/data` i `/HdiSamples` katalogów. Te katalogi istnieje w magazynie domyślny dla klastra.

## <a id="job"></a>Przykładowe zapytanie Hive

Poniższe instrukcje HiveQL projektu kolumn na `/example/data/sample.log` pliku:

```hiveql
set hive.execution.engine=tez;
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

* `set hive.execution.engine=tez;`: Ustawia aparat wykonywania do użycia w aplikacji Tez. Przy użyciu aplikacji Tez może zapewnić wzrost wydajności zapytań. Aby uzyskać więcej informacji o aplikacji Tez, zobacz [Użyj Apache Tez zwiększonej wydajności](#usetez) sekcji.

    > [!NOTE]
    > Ta instrukcja jest tylko wymagane w przypadku korzystania z klastra usługi HDInsight opartej na systemie Windows. Tez jest domyślny aparat wykonywania dla usługi HDInsight opartej na systemie Linux.

* `DROP TABLE`: Jeśli tabela już istnieje, usuń go.

* `CREATE EXTERNAL TABLE`: Tworzy nowy **zewnętrznych** tabeli w gałęzi rejestru. Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi. Danych pozostaje w oryginalnej lokalizacji i w oryginalnym formacie.

* `ROW FORMAT`: Nakazuje Hive sposób formatowania danych. W takim przypadku pól w każdym dzienniku są oddzielone spacją.

* `STORED AS TEXTFILE LOCATION`: Nakazuje gałęzi, w której są przechowywane dane ( `example/data` katalogu) i które są przechowywane jako tekst. Dane można w jednym pliku lub rozłożyć na wiele plików w katalogu.

* `SELECT`: Wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Ta instrukcja zwraca wartość **3** ponieważ istnieją trzy wiersze, które zawierają tę wartość.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive próbuje stosowane do wszystkich plików w katalogu schematu. W takim przypadku katalog zawiera pliki, które nie pasuje do schematu. Aby zapobiec odzyskiwanie danych w wynikach, tej instrukcji informuje Hive, czy firma Microsoft powinno zwrócić dane tylko z plików w. dziennika.

> [!NOTE]
> Jeśli oczekujesz zaktualizowania za pomocą zewnętrznego źródła danych, należy użyć tabel zewnętrznych. Na przykład procesu przekazywania danych lub operacja MapReduce.
>
> Usunięcie tabeli zewnętrznej jest **nie** usuwania danych, tylko usuwa definicję tabeli.

Aby utworzyć **wewnętrzny** tabeli zamiast zewnętrznego, użyj następujących HiveQL:

```hiveql
set hive.execution.engine=tez;
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

* `CREATE TABLE IF NOT EXISTS`: Jeśli tabela nie istnieje, należy go utworzyć. Ponieważ **zewnętrznych** — słowo kluczowe nie jest używany, ta instrukcja tworzy wewnętrznej tabeli. Tabela jest przechowywany w magazynie danych programu Hive i zarządza całkowicie Hive.

* `STORED AS ORC`: Przechowuje dane w formacie zoptymalizowanych pod kątem wiersza kolumnowy (ORC). ORC jest wysoce zoptymalizowane i wydajne formatu do przechowywania danych gałęzi.

* `INSERT OVERWRITE ... SELECT`: Wybiera wiersze z **log4jLogs** tabeli, która zawiera **[Błąd]**, a następnie wstawia dane do **errorLogs** tabeli.

> [!NOTE]
> W przeciwieństwie do tabel zewnętrznych porzucenie wewnętrznej tabeli spowoduje również usunięcie danych.

## <a name="improve-hive-query-performance"></a>Poprawiać wydajność zapytań Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) to platforma, która umożliwia aplikacjom znacznym danych, takich jak Hive, aby znacznie bardziej efektywną pracę na dużą skalę. Tez jest domyślnie włączona dla klastrów usługi HDInsight opartych na systemie Linux.

> [!NOTE]
> Tez jest obecnie domyślnie wyłączona w przypadku klastrów usługi HDInsight opartej na systemie Windows, a musi być włączona. Aby móc korzystać z aplikacji Tez, dla zapytania programu Hive musi być ustawiona następującą wartość:
>
> `set hive.execution.engine=tez;`
>
> Tez jest domyślny aparat dla klastrów usługi HDInsight opartych na systemie Linux.

[Hive w aplikacji Tez dokumentów projektowych](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) zawiera szczegółowe informacje na temat opcji wdrażania i dostrajania konfiguracji.

Aby pomóc w debugowaniu zadań została uruchomiona przy użyciu aplikacji Tez, usługa HDInsight zapewnia następujące web UI, dzięki którym można wyświetlić szczegóły zadań Tez:

* [Użyj widoku Ambari Tez w HDInsight opartych na systemie Linux](../hdinsight-debug-ambari-tez-view.md)

* [Użyj interfejsu użytkownika aplikacji Tez w usłudze HDInsight z systemu Windows](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Małe opóźnienia przetwarzania analitycznego (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (czasami znana jako funkcja Live długich i procesów) to nowa funkcja w gałęzi 2.0, umożliwiającą buforowanie w pamięci zapytań. LLAP sprawia, że zapytań Hive jest znacznie szybsze do [26 x szybciej niż Hive 1.x w niektórych przypadkach](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

Usługa HDInsight zapewnia LLAP w zapytaniu interakcyjne typu klastra. Aby uzyskać więcej informacji, zobacz [rozpoczynać się od zapytania interakcyjne](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

## <a name="scheduling-hive-queries"></a>Planowanie zapytań programu Hive

Istnieje kilka usług, które mogą służyć do uruchamiania zapytań Hive w ramach przepływu pracy według harmonogramu lub na żądanie.

### <a name="azure-data-factory"></a>Azure Data Factory

Fabryka danych Azure umożliwia używanie usługi HDInsight w ramach potoku fabryki danych. Aby uzyskać więcej informacji o korzystaniu z programu Hive z potoku, zobacz [Przekształcanie danych za pomocą działania gałęzi w fabryce danych Azure](/data-factory/transform-data-using-hadoop-hive.md) dokumentu.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Zadania hive i SQL Server Integration Services

SQL Server Integration Services (SSIS) służy do uruchamiania zadania Hive. Azure Feature Pack dla SSIS zawiera następujące składniki, które współpracują z zadań Hive w usłudze HDInsight.

* [Zadania Azure HDInsight Hive][hivetask]

* [Menedżer połączeń subskrypcji platformy Azure][connectionmanager]

Aby uzyskać więcej informacji, zobacz [Azure Feature Pack] [ ssispack] dokumentacji.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie jest przepływu pracy i koordynacji system, który zarządza zadaniami na platformie Hadoop. Aby uzyskać więcej informacji o korzystaniu z Hive Oozie, zobacz [Oozie używany do definiowania i uruchomić przepływ pracy](../hdinsight-use-oozie-linux-mac.md) dokumentu.

## <a id="nextsteps"></a>Następne kroki

Teraz, kiedy znasz już gałąź jest i jak z niego korzystać z platformą Hadoop w usłudze HDInsight, użyj następujących linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]
* [Korzystanie z zadań MapReduce z usługą HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
