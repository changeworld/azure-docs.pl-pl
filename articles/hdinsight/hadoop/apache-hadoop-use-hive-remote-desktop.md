---
title: Use Apache Hive i pulpitu zdalnego w HDInsight — Azure
description: Dowiedz się, jak połączyć się z klastrem usługi Hadoop w HDInsight przy użyciu pulpitu zdalnego, a następnie uruchomić zapytania programu Hive przy użyciu interfejsu wiersza polecenia programu Hive.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 86c1d9adc3b4ff66e2f02617df551e496a5482c8
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014620"
---
# <a name="use-apache-hive-with-apache-hadoop-on-hdinsight-with-remote-desktop"></a>Apache Hive za pomocą technologii Apache Hadoop w HDInsight przy użyciu pulpitu zdalnego
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

W tym artykule zostanie Dowiedz się, jak połączyć się z klastra usługi HDInsight przy użyciu pulpitu zdalnego, a następnie uruchom zapytania programu Hive przy użyciu interfejsu wiersza polecenia programu Hive (CLI).

> [!IMPORTANT]
> Pulpit zdalny jest dostępna tylko w klastrach HDInsight, które używają Windows jako systemu operacyjnego. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> HDInsight 3.4 lub nowszej, zobacz [korzystanie z programu Hive z HDInsight i z usługi Beeline](apache-hadoop-use-hive-beeline.md) informacji na temat uruchamiania zapytań programu Hive bezpośrednio w klastrze z wiersza polecenia.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy:

* Klaster HDInsight dla komputerów z systemem Windows (platformy Hadoop w HDInsight)
* Komputer kliencki z systemem Windows 10, Windows 8 lub Windows 7

## <a id="connect"></a>Połącz przy użyciu pulpitu zdalnego
Włączanie pulpitu zdalnego dla klastra HDInsight, a następnie nawiązać z nim, postępując zgodnie z instrukcjami na [nawiązywanie połączenia z klastrami HDInsight przy użyciu protokołu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Należy użyć polecenia gałąź
Po połączeniu pulpitu dla klastra HDInsight wykonaj następujące kroki do pracy z technologią Hive:

1. Na pulpicie HDInsight start **wiersza polecenia usługi Hadoop**.
2. Wprowadź następujące polecenie, aby uruchomić interfejs wiersza polecenia programu Hive:

        %hive_home%\bin\hive

    Po rozpoczęciu interfejsu wiersza polecenia zostanie wyświetlony Hive wiersza: `hive>`.
3. Przy użyciu interfejsu wiersza polecenia, wprowadź poniższe instrukcje, aby utworzyć nową tabelę o nazwie **log4jLogs** przy użyciu przykładowych danych:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Te instrukcje, wykonaj następujące czynności:

   * **DROP TABLE**: usuwa tabelę i plik danych, jeśli tabela już istnieje.
   * **CREATE EXTERNAL TABLE**: tworzy nową tabelę "external" w gałęzi. Tabele zewnętrzne przechowywać w definicji tabeli w gałęzi (danych jest pozostawiany w oryginalnej lokalizacji).

     > [!NOTE]
     > Jeśli oczekujesz, że danych bazowych do zaktualizowania przez zewnętrznego źródła (takich jak proces przekazywania danych) lub inna operacja MapReduce, należy użyć tabel zewnętrznych, ale zawsze mają zapytań programu Hive za pomocą najnowszych danych.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** usunąć dane w definicji tabeli.
     >
     >
   * **FORMAT wiersza**: informuje, Hive, jak dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.
   * **PRZECHOWYWANE lokalizacji TEXTFILE AS**: informuje, Hive, gdzie dane są przechowywane (katalog przykładowe i dane), i są przechowywane jako tekst.
   * **Wybierz**: wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Powinny zostać zwrócone wartości **3** ponieważ istnieją trzy wiersze, które zawierają tę wartość.
   * **INPUT__FILE__NAME takich jak "%.log"** — informuje Hive, firma Microsoft powinno zwrócić tylko dane z plików kończy się rozszerzeniem. log. To ogranicza wyszukiwanie do pliku sample.log, który zawiera dane i utrzymuje je zwracanie danych z innych przykładu plików danych, które nie są zgodne ze schematem zdefiniowaliśmy.
4. Należy zastosować następujące instrukcje, aby utworzyć nową tabelę "internal" o nazwie **przesłano**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Te instrukcje, wykonaj następujące czynności:

   * **Tworzenie tabeli IF NOT EXISTS**: tworzy tabelę, jeśli jeszcze nie istnieje. Ponieważ **zewnętrznych** — słowo kluczowe nie jest używany, to tabeli wewnętrznej, są przechowywane w magazynie danych programu Hive, która jest całkowicie zarządzana przez Hive.

     > [!NOTE]
     > W odróżnieniu od **zewnętrznych** tabel, również porzucenie wewnętrznej tabeli powoduje usunięcie danych bazowych.
     >
     >
   * **PRZECHOWYWANE ORC AS**: przechowuje dane w wiersz zoptymalizowane tabelarycznego (ORC). Jest wysoce zoptymalizowane i wydajne format do przechowywania danych programu Hive.
   * **ZASTĄP INSERT... Wybierz**: wybiera wiersze z **log4jLogs** tabeli, która zawiera **[Błąd]**, następnie wstawia dane do **przesłano** tabeli.

     Aby zweryfikować, że tylko wiersze zawierające **[Błąd]** w kolumnie były przechowywane t4 **przesłano** tabeli, użyj następującej instrukcji, aby zwrócić wszystkie wiersze z **przesłano**:

       Wybierz * z przesłano;

     Trzy wiersze danych ma zostać zwrócone, zawierający wszystkie **[Błąd]** w kolumnie t4.

## <a id="summary"></a>Podsumowanie
Jak widać, polecenie gałęzi umożliwia łatwe interaktywnie uruchamianie zapytań programu Hive w klastrze usługi HDInsight, monitorować stan zadania i pobieranie danych wyjściowych.

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

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
