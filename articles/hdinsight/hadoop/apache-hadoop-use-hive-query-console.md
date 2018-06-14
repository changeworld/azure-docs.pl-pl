---
title: Korzystanie z programu Hadoop Hive w konsoli zapytania w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przy użyciu konsoli zapytania oparte na sieci web do uruchamiania zapytań Hive w klastrze usługi HDInsight Hadoop w przeglądarce.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 04a6ad67fec4145d8f9164743b08f9e105778091
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405949"
---
# <a name="run-hive-queries-using-the-query-console"></a>Uruchamianie zapytań Hive przy użyciu konsoli zapytania
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

W tym artykule dowiesz się, za pomocą konsoli zapytania HDInsight do uruchamiania zapytań Hive w klastrze usługi HDInsight Hadoop w przeglądarce.

> [!IMPORTANT]
> HDInsight konsoli zapytania jest dostępna tylko w klastrach HDInsight opartych na systemie Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).
>
> Dla usługi HDInsight w wersji 3.4 lub większą, zobacz [uruchamianie zapytań Hive w widoku Hive narzędzia Ambari](apache-hadoop-use-hive-ambari-view.md) informacji na temat uruchamiania zapytań Hive z przeglądarki sieci web.

## <a id="prereq"></a>Wymagania wstępne
Aby wykonać kroki opisane w tym artykule, potrzebne następujące elementy.

* Klastra z systemem Windows usługi HDInsight Hadoop
* Nowoczesna przeglądarka sieci web

## <a id="run"></a> Uruchamianie zapytań Hive przy użyciu konsoli zapytania
1. Otwórz przeglądarkę sieci web i przejdź do **https://CLUSTERNAME.azurehdinsight.net**, gdzie **CLUSTERNAME** jest nazwą klastra usługi HDInsight. Jeśli zostanie wyświetlony monit, wprowadź nazwę użytkownika i hasło, które zostały użyte podczas tworzenia klastra.
2. Łącza w górnej części strony, zaznacz **Edytor Hive**. Spowoduje to wyświetlenie formularza, który może służyć do wprowadzania instrukcje HiveQL, które mają być uruchamiane w klastrze usługi HDInsight.

    ![Edytor hive](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Zamień tekst `Select * from hivesampletable` z poniższe instrukcje HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Te instrukcje, wykonaj następujące czynności:

   * **DROP TABLE**: usuwa tabeli i plik danych, jeśli tabela już istnieje.
   * **Tworzenie tabeli zewnętrznej**: tworzy nową tabelę "zewnętrzne" w gałęzi. Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi; dane pozostaną w oryginalnej lokalizacji.

     > [!NOTE]
     > Jeśli oczekujesz danych do zaktualizowania przez źródło zewnętrzne (na przykład procesu przekazywania danych) lub przez inną operację MapReduce, należy użyć tabel zewnętrznych, ale ma zawsze zapytań programu Hive za pomocą najnowszych danych.
     >
     > Usunięcie tabeli zewnętrznej jest **nie** Usuń dane, definicję tabeli.
     >
     >
   * **FORMAT wiersza**: Określa, że Hive, jak dane są sformatowane. W takim przypadku pól w każdym dzienniku są oddzielone spacją.
   * **PRZECHOWYWANE jako lokalizacji TEXTFILE**: informuje gałąź rejestru, których dane są przechowywane (katalogu przykładzie/danych) i są przechowywane jako tekst
   * **Wybierz**: Wybierz liczbę wszystkich wierszy gdzie kolumna **t4** zawiera wartość **[Błąd]**. Powinny zostać zwrócone wartości **3** ponieważ istnieją trzy wiersze, które zawierają tę wartość.
   * **INPUT__FILE__NAME takich jak "%.log"** -informuje, który mamy powinno zwrócić tylko danych z plików w gałęzi. dziennika. To ogranicza wyszukiwanie do pliku sample.log, który zawiera dane, a następnie utrzymuje je z zwracać dane z innych przykładowe pliki danych, która nie pasuje do schematu, zdefiniowanego.
3. Kliknij przycisk **przesłać**. **Sesji zadania** w dolnej części strony powinien być wyświetlany szczegóły zadania.
4. Gdy **stan** pola zmienia się na **Ukończono**, wybierz pozycję **Wyświetl szczegóły** zadania. Na stronie szczegółów **dane wyjściowe zadania** zawiera `[ERROR]    3`. Można użyć **Pobierz** przycisku w tym polu, aby pobrać plik, który zawiera dane wyjściowe zadania.

## <a id="summary"></a>Podsumowanie
Jak widać, konsoli zapytania zapewnia prosty sposób do uruchamiania zapytań Hive w klastra usługi HDInsight, monitorować stan zadania i pobrać dane wyjściowe.

Aby dowiedzieć się więcej na temat uruchamiania zadań Hive za pomocą konsoli zapytania Hive, wybierz **wprowadzenie** u góry konsoli zapytania, a następnie użyć przykłady, które zostały opublikowane. Każda próbka przeprowadzi Cię przez proces za pomocą gałęzi do analizowania danych, w tym objaśnienia dotyczące instrukcje HiveQL użyty w próbce.

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
