---
title: Użyj usługi Hadoop Hive i pulpitu zdalnego w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywania połączenia z klastrem usługi Hadoop w usłudze HDInsight przy użyciu pulpitu zdalnego, a następnie uruchom zapytań programu Hive za pomocą interfejsu wiersza polecenia Hive.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 70eab088ce87d8a62d3f258b70aaec5e2e147d0e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400643"
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight przy użyciu pulpitu zdalnego
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

W tym artykule będzie Dowiedz się, jak nawiązać połączenia z klastrem usługi HDInsight przy użyciu pulpitu zdalnego, a następnie uruchom zapytań Hive przy użyciu Hive interfejsu wiersza polecenia (CLI).

> [!IMPORTANT]
> Pulpit zdalny jest dostępna tylko w klastrach HDInsight, które używają systemu Windows jako system operacyjny. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> Dla usługi HDInsight w wersji 3.4 lub większą, zobacz [używanie Hive z HDInsight i Beeline](apache-hadoop-use-hive-beeline.md) informacji na temat uruchamiania zapytań Hive bezpośrednio w klastrze z wiersza polecenia.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, będą potrzebne następujące czynności:

* Klastra z systemem Windows HDInsight (Hadoop w usłudze HDInsight)
* Komputer kliencki z systemem Windows 10, Windows 8 lub Windows 7

## <a id="connect"></a>Uzyskuj dostęp do usług pulpitu zdalnego
Włączenie pulpitu zdalnego dla klastra usługi HDInsight, a następnie nawiązać zgodnie z instrukcjami w [Connect do klastrów usługi HDInsight za pomocą protokołu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Użyj polecenia gałęzi
Po podłączeniu do pulpitu dla klastra usługi HDInsight, wykonaj następujące kroki pracy z gałęzi:

1. Z poziomu pulpitu HDInsight start **wiersza polecenia usługi Hadoop**.
2. Wprowadź następujące polecenie, aby uruchomić interfejs wiersza polecenia Hive:

        %hive_home%\bin\hive

    Rozpoczęto interfejsu wiersza polecenia, można zobaczyć w wierszu polecenia programu Hive interfejsu wiersza polecenia: `hive>`.
3. Przy użyciu interfejsu wiersza polecenia, wpisz poniższe instrukcje, aby utworzyć nową tabelę o nazwie **log4jLogs** przy użyciu przykładowych danych:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Te instrukcje, wykonaj następujące czynności:

   * **DROP TABLE**: usuwa tabeli i plik danych, jeśli tabela już istnieje.
   * **Tworzenie tabeli zewnętrznej**: tworzy nową tabelę "zewnętrzne" w gałęzi. Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi rejestru (danych pozostaje w oryginalnej lokalizacji).

     > [!NOTE]
     > Jeśli oczekujesz danych do zaktualizowania przez źródło zewnętrzne (na przykład procesu przekazywania danych) lub przez inną operację MapReduce, należy użyć tabel zewnętrznych, ale ma zawsze zapytań programu Hive za pomocą najnowszych danych.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** Usuń dane, definicję tabeli.
     >
     >
   * **FORMAT wiersza**: Określa, że Hive, jak dane są sformatowane. W takim przypadku pól w każdym dzienniku są oddzielone spacją.
   * **PRZECHOWYWANE jako lokalizacji TEXTFILE**: informuje gałąź rejestru, których dane są przechowywane (katalogu przykładzie/danych) i są przechowywane jako tekst.
   * **Wybierz**: wybiera liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Powinny zostać zwrócone wartości **3** ponieważ istnieją trzy wiersze, które zawierają tę wartość.
   * **INPUT__FILE__NAME takich jak "%.log"** -informuje, który mamy powinno zwrócić tylko danych z plików w gałęzi. dziennika. To ogranicza wyszukiwanie do pliku sample.log, który zawiera dane, a następnie utrzymuje je z zwracać dane z innych przykładowe pliki danych, która nie pasuje do schematu, zdefiniowanego.
4. Poniższe instrukcje umożliwiają utworzenie nowej tabeli "internal" o nazwie **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Te instrukcje, wykonaj następujące czynności:

   * **Utwórz Tabela Jeśli nie ISTNIEJE**: tworzy tabelę, jeśli jeszcze nie istnieje. Ponieważ **zewnętrznych** — słowo kluczowe nie jest używany, to wewnętrznej tabeli, która jest przechowywana w magazynie danych programu Hive i zarządza całkowicie Hive.

     > [!NOTE]
     > W odróżnieniu od **zewnętrznych** tabel, również porzucenie wewnętrznej tabeli powoduje usunięcie danych.
     >
     >
   * **ORC AS PRZECHOWYWANE**: przechowuje dane w formacie (ORC) kolumnowym zoptymalizowane wiersza. Jest to wysoce zoptymalizowane i wydajne format do przechowywania danych Hive.
   * **ZASTĄP INSERT... Wybierz**: wybiera wiersze z **log4jLogs** tabeli, która zawiera **[Błąd]**, następnie wstawia dane do **errorLogs** tabeli.

     Aby zweryfikować, że tylko wiersze zawierające **[Błąd]** w kolumnie były przechowywane t4 **errorLogs** tabeli, użyj poniższych instrukcji, aby zwracanie wszystkich wierszy z **errorLogs**:

       Wybierz * z errorLogs;

     Trzy wiersze danych ma zostać zwrócony, zawierający wszystkie **[Błąd]** w t4 kolumny.

## <a id="summary"></a>Podsumowanie
Jak widać, polecenie gałęzi udostępnia prostą metodą interakcyjnego uruchamianie zapytań Hive w klastrze usługi HDInsight, monitorować stan zadania i pobrać dane wyjściowe.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat programu Hive w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Jeśli używasz aplikacji Tez przy użyciu Hive, zobacz informacji o debugowaniu w następujących dokumentach:

* [Użyj interfejsu użytkownika aplikacji Tez w usłudze HDInsight z systemu Windows](../hdinsight-debug-tez-ui.md)
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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
