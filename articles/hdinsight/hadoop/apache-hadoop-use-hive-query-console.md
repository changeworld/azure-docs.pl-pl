---
title: Use Hadoop Hive w konsoli zapytań w HDInsight — Azure
description: Dowiedz się, jak uruchamianie zapytań programu Hive w klastrze usługi HDInsight Hadoop z poziomu przeglądarki przy użyciu konsoli kwerendy oparte na sieci web.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: e080085faa45e0eb330e49edb4450c5a44eb755b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045613"
---
# <a name="run-hive-queries-using-the-query-console"></a>Uruchamianie zapytań Hive przy użyciu konsoli zapytań
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

W tym artykule dowiesz się, jak korzystanie z konsoli zapytań HDInsight uruchamia zapytania Hive w klastrze usługi HDInsight Hadoop z poziomu przeglądarki.

> [!IMPORTANT]
> Z konsoli zapytań HDInsight jest dostępna tylko w klastrach HDInsight z systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> HDInsight 3.4 lub nowszej, zobacz [uruchamianie zapytań Hive w widoku Hive narzędzia Ambari](apache-hadoop-use-hive-ambari-view.md) informacji na temat uruchamiania zapytań programu Hive za pomocą przeglądarki sieci web.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy.

* Klaster HDInsight Hadoop z systemem Windows
* Nowoczesne przeglądarki

## <a id="run"></a> Uruchamianie zapytań Hive przy użyciu konsoli zapytań
1. Otwórz przeglądarkę internetową i przejdź do **https://CLUSTERNAME.azurehdinsight.net**, gdzie **CLUSTERNAME** jest nazwą klastra usługi HDInsight. Jeśli zostanie wyświetlony monit, wprowadź nazwę użytkownika i hasło, których użyto podczas tworzenia klastra.
2. Z poziomu linków w górnej części strony, wybierz **Edytor Hive**. Spowoduje to wyświetlenie formularza, który może służyć do Podaj instrukcje HiveQL, które chcesz uruchomić w klastrze usługi HDInsight.

    ![Edytor hive](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Zastąp tekst `Select * from hivesampletable` z poniższe instrukcje HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Te instrukcje, wykonaj następujące czynności:

   * **DROP TABLE**: usuwa tabelę i plik danych, jeśli tabela już istnieje.
   * **CREATE EXTERNAL TABLE**: tworzy nową tabelę "external" w gałęzi. Tabele zewnętrzne przechowywane w definicji tabeli w gałęzi; dane pozostaną w oryginalnej lokalizacji.

     > [!NOTE]
     > Jeśli oczekujesz, że danych bazowych do zaktualizowania przez zewnętrznego źródła (takich jak proces przekazywania danych) lub inna operacja MapReduce, należy użyć tabel zewnętrznych, ale zawsze mają zapytań programu Hive za pomocą najnowszych danych.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** usunąć dane w definicji tabeli.
     >
     >
   * **FORMAT wiersza**: informuje, Hive, jak dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.
   * **PRZECHOWYWANE lokalizacji TEXTFILE AS**: informuje, Hive, gdzie dane są przechowywane (katalog przykładowe i dane), i są przechowywane jako tekst
   * **Wybierz**: Wybierz liczbę wszystkich wierszy gdzie kolumna **t4** zawierają wartość **[Błąd]**. Powinny zostać zwrócone wartości **3** ponieważ istnieją trzy wiersze, które zawierają tę wartość.
   * **INPUT__FILE__NAME takich jak "%.log"** — informuje Hive, firma Microsoft powinno zwrócić tylko dane z plików kończy się rozszerzeniem. log. To ogranicza wyszukiwanie do pliku sample.log, który zawiera dane i utrzymuje je zwracanie danych z innych przykładu plików danych, które nie są zgodne ze schematem zdefiniowaliśmy.
3. Kliknij przycisk **przesłać**. **Sesji zadania** w dolnej części strony powinna zostać wyświetlona szczegóły zadania.
4. Gdy **stan** zmiany pola z **Ukończono**, wybierz opcję **Wyświetl szczegóły** dla zadania. Na stronie szczegółów **dane wyjściowe zadania** zawiera `[ERROR]    3`. Możesz użyć **Pobierz** przycisku w tym polu, aby pobrać plik, który zawiera dane wyjściowe zadania.

## <a id="summary"></a>Podsumowanie
Jak widać, konsola zapytań umożliwia łatwe uruchamianie zapytań programu Hive w klastrze usługi HDInsight, monitorować stan zadania i pobieranie danych wyjściowych.

Aby dowiedzieć się więcej o korzystaniu z konsoli zapytań programu Hive do uruchamiania zadań Hive, wybierz **wprowadzenie** u góry konsoli zapytań, a następnie użyć przykładów, które są dostarczane. Każdy przykład przedstawiono proces przy użyciu technologii Hive do analizowania danych, w tym wyjaśnienia dotyczące instrukcje HiveQL użytemu w przykładzie.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat programu Hive w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)

Jeśli używasz aplikacji Tez przy użyciu technologii Hive, zobacz następujące dokumenty, aby uzyskać informacje o debugowaniu:

* [Użyj interfejsu użytkownika Tez w HDInsight z systemem Windows](../hdinsight-debug-tez-ui.md)
* [Użyj widoku Ambari Tez w HDInsight opartych na systemie Linux](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
