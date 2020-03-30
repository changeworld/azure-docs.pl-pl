---
title: Użyj widoku gałęzi Apache Ambari z Apache Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak używać widoku gałęzi w przeglądarce sieci Web do przesyłania zapytań hive. Widok hive jest częścią interfejsu użytkownika sieci Web Ambari dostarczanego z klastrem HDInsight opartym na systemie Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 6c199a0dd75b89d9c9368e799c97a28b73758d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73097099"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Korzystanie z widoku programu Hive narzędzia Apache Ambari z usługą Apache Hadoop w usłudze HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak uruchamiać zapytania hive przy użyciu apache Ambari Hive View. Widok gałęzi umożliwia tworzenie, optymalizowanie i uruchamianie zapytań hive z przeglądarki sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop na HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Przeglądarka internetowa

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. Z [witryny Azure portal](https://portal.azure.com/)wybierz swój klaster.  Zobacz [Lista i pokaż klastry,](../hdinsight-administer-use-portal-linux.md#showClusters) aby uzyskać instrukcje. Klaster jest otwierany w nowym bloku portalu.

1. Z **pulpitów nawigacyjnych klastra**wybierz **widoki Ambari**. Po wyświetleniu monitu o uwierzytelnienie `admin`użyj nazwy konta i hasła logowania klastra (domyślnie) podanej podczas tworzenia klastra. Alternatywnie przejdź `https://CLUSTERNAME.azurehdinsight.net/#/main/views` do przeglądarki, `CLUSTERNAME` gdzie jest nazwa klastra.

1. Z listy widoków wybierz pozycję __Widok gałęzi__.

    ![Apache Ambari wybierz widok ula Apache](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Strona widoku gałęzi jest podobna do następującej ilustracji:

    ![Obraz arkusza kwerendy dla widoku Gałąź](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Na karcie __Kwerenda__ wklej następujące instrukcje HiveQL do arkusza:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Te instrukcje wykonują następujące akcje:

   * `DROP TABLE`: Usuwa tabelę i plik danych, jeśli tabela już istnieje.

   * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "zewnętrzną" w obszarze Hive.
     Tabele zewnętrzne przechowują tylko definicję tabeli w obszarze Gałęzi. Dane są pozostawione w oryginalnej lokalizacji.

   * `ROW FORMAT`: Pokazuje sposób formatowania danych. W takim przypadku pola w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Pokazuje, gdzie dane są przechowywane i czy są przechowywane jako tekst.

   * `SELECT`: Wybiera liczbę wszystkich wierszy, w których kolumna t4 zawiera wartość [BŁĄD].

   > [!IMPORTANT]  
   > Pozostaw __domyślny__wybór __bazy danych__ . Przykłady w tym dokumencie użyć domyślnej bazy danych dołączonej do usługi HDInsight.

1. Aby uruchomić kwerendę, wybierz pozycję **Wykonaj** poniżej arkusza. Przycisk zmieni kolor na pomarańczowy, a tekst zmieni się na **Zatrzymaj**.

1. Po zakończeniu kwerendy na karcie **Wyniki** zostaną wyświetlone wyniki operacji. Następujący tekst jest wynikiem kwerendy:

        loglevel       count
        [ERROR]        3

    Za pomocą karty **LOG** można wyświetlić informacje dotyczące rejestrowania utworzonego zadania.

   > [!TIP]  
   > Pobieranie lub zapisywanie wyników z okna dialogowego **Działania** rozwijane na karcie **Wyniki.**

### <a name="visual-explain"></a>Wizualne wyjaśnienie

Aby wyświetlić wizualizację planu kwerend, wybierz kartę **Objaśnienie wizualne** pod arkuszem.

**Visual Explain** widok kwerendy może być pomocne w zrozumieniu przepływu złożonych zapytań.

### <a name="tez-ui"></a>Interfejs użytkownika Tez

Aby wyświetlić interfejs użytkownika tez dla kwerendy, wybierz kartę **Tez UI** pod arkuszem.

> [!IMPORTANT]  
> Tez nie jest używany do rozwiązywania wszystkich zapytań. Można rozwiązać wiele zapytań bez użycia Tez.

## <a name="view-job-history"></a>Wyświetlanie historii zadań

Na karcie __Zadania__ jest wyświetlana historia zapytań hive.

![Historia kart zadań widoku ula Apache](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabele bazy danych

Za pomocą karty __Tabele__ można pracować z tabelami w bazie danych hive.

![Obraz karty Tabele ula Apache](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Zapisane kwerendy

Na karcie **Kwerenda** można opcjonalnie zapisywać kwerendy. Po zapisaniu kwerendy można ją ponownie użyć na karcie __Zapisane kwerendy.__

![Karta Zapisane kwerendy w widoku gałęzi Apache](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Zapisane kwerendy są przechowywane w domyślnym magazynie klastra. Zapisane zapytania można znaleźć pod `/user/<username>/hive/scripts`ścieżką . Są one przechowywane jako `.hql` pliki zwykłego tekstu.
>
> Jeśli usuniesz klaster, ale zachowasz magazyn, możesz użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) lub Data Lake Storage Explorer (z [witryny Azure Portal),](https://portal.azure.com)aby pobrać zapytania.

## <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Gałąź można rozszerzyć za pomocą funkcji zdefiniowanych przez użytkownika (UDF). Użyj UDF do zaimplementowania funkcji lub logiki, która nie jest łatwo modelowane w HiveQL.

Deklarowanie i zapisywanie zestawu plików UDF przy użyciu karty **UDF** w górnej części widoku gałęzi. Te pliki UDF mogą być używane z **Edytorem zapytań**.

![Wyświetlanie karty UDF widoku gałęzi Apache](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po dodaniu UDF do widoku gałęzi u dołu **Edytora zapytań**pojawi się przycisk **Wstaw udfs.** Wybranie tego wpisu powoduje wyświetlenie listy rozwijanej plików UDF zdefiniowanych w widoku gałęzi. Wybranie UDF dodaje instrukcje HiveQL do kwerendy, aby włączyć UDF.

Na przykład, jeśli zdefiniowano UDF z następującymi właściwościami:

* Nazwa zasobu: myudfs

* Ścieżka zasobu: /myudfs.jar

* Nazwa UDF: myawesomeudf

* Nazwa klasy UDF: com.myudfs.Awesome

Za pomocą przycisku **Wstaw udfs** jest wyświetlany wpis o nazwie **myudfs**, z inną listą rozwijaną dla każdego UDF zdefiniowanego dla tego zasobu. W tym przypadku, to **myawesomeudf**. Wybranie tego wpisu powoduje dodanie następującego na początku kwerendy:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Następnie można użyć UDF w zapytaniu. Na przykład `SELECT myawesomeudf(name) FROM people;`.

Aby uzyskać więcej informacji na temat korzystania z plików UDF z gałęzią w programie HDInsight, zobacz następujące artykuły:

* [Korzystanie z Pythona z ula Apache i Świnia Apache w HDInsight](python-udf-hdinsight.md)
* [Jak dodać niestandardowy Apache Hive UDF do HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Ustawienia gałęzi

Można zmienić różne ustawienia gałęzi, takie jak zmiana aparatu wykonywania dla hive z Tez (domyślnie) na MapReduce.

## <a name="next-steps"></a><a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat hive w hdinsight:

* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje na temat innych sposobów pracy z Hadoop na HDInsight:

* [Użyj Apache Pig z Apache Hadoop na HDInsight](hdinsight-use-pig.md)
* [Użyj MapReduce z Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
