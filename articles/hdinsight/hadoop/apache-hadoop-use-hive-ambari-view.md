---
title: Używanie widoków Ambari do pracy z Hive w usłudze Azure HDInsight (Hadoop) - | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Hive View z przeglądarki sieci web umożliwia wysyłanie zapytań programu Hive. Widok Hive jest częścią interfejsie użytkownika sieci Web Ambari, które zostały dostarczone z klastrem usługi HDInsight opartej na systemie Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 6c07e9a45cbfbc5e6ed6787277dcfa1bf4cf4b2b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201496"
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Użyj widoku Hive narzędzia Ambari z platformą Hadoop w usłudze HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak uruchamianie zapytań Hive przy użyciu widoku Hive narzędzia Ambari. Hive View pozwala na tworzenie, optymalizacja i uruchamianie zapytań Hive z przeglądarki sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

* Opartych na systemie Linux platformą Hadoop w klastrze usługi HDInsight w wersji 3.4 lub większą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Przeglądarki sieci web

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. Otwórz [portal Azure](https://portal.azure.com).

2. Wybierz z klastrem usługi HDInsight, a następnie wybierz **widoków Ambari** z **szybkie linki** sekcji.

    ![Szybkie linki części portalu](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Gdy monit o uwierzytelnienie, użyj logowania do klastra (domyślna `admin`) konta i hasło podane podczas tworzenia klastra.

3. Wybierz z listy widoków, __Hive View__.

    ![Wybrany widok gałęzi](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Strona widoku Hive jest podobna do poniższej ilustracji:

    ![Obraz arkusza kwerend widoku Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Z __zapytania__ karcie, wklej poniższe instrukcje HiveQL do arkusza:

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

    Te instrukcje, wykonaj następujące czynności:

   * `DROP TABLE`: Usuwa tabeli i plik danych w przypadku, gdy tabela już istnieje.

   * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "external" w gałęzi.
   Tabele zewnętrzne przechowywać tylko definicji tabeli w gałęzi. Dane pozostaną w oryginalnej lokalizacji.

   * `ROW FORMAT`: Pokazuje, jak dane są sformatowane. W takim przypadku pól w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Zawiera przechowywania danych i że jest przechowywane jako tekst.

   * `SELECT`: Wybiera liczbę wszystkich wierszy, gdzie t4 kolumna zawiera wartość [Błąd].

    > [!IMPORTANT]
    > Pozostaw __bazy danych__ zaznaczenie na __domyślne__. Przykłady w tym dokumencie Użyj domyślnej bazy danych uwzględnionych w usłudze HDInsight.

5. Aby uruchomić zapytanie, należy użyć **Execute** znajdujący się poniżej arkusza. Przycisk zmieni kolor pomarańczowy, a tekst zostanie zmieniony na **zatrzymać**.

6. Po zakończeniu zapytanie **wyniki** karcie są wyświetlane wyniki operacji. Następujący tekst jest wynik kwerendy:

        loglevel       count
        [ERROR]        3

    Można użyć **dzienniki** kartę, aby wyświetlić informacje o rejestrowaniu utworzenia zadania.

   > [!TIP]
   > Pobierz lub zapisać wyniki z **Zapisz wyniki** okno dialogowe listy rozwijanej w prawym górnym rogu **wyniki przetwarzania zapytania** sekcji.

### <a name="visual-explain"></a>Wyjaśnić Visual

Aby wyświetlić wizualizacji planu zapytania, zaznacz **wyjaśnić Visual** kartę poniżej arkusza.

**Wyjaśnić Visual** widoku zapytania mogą być pomocne w opis przepływu złożonych zapytań. Odpowiednika tekstową tego widoku można wyświetlić za pomocą **Wyjaśnij** przycisk w edytorze zapytań.

### <a name="tez-ui"></a>Interfejs użytkownika aplikacji tez

Aby wyświetlić interfejs użytkownika aplikacji Tez dla zapytania, zaznacz **Tez** kartę poniżej arkusza.

> [!IMPORTANT]
> Tez nie jest używany do rozpoznawania wszystkich zapytań. Wiele zapytań można rozwiązać, bez korzystania z aplikacji Tez. 

Jeśli Tez użyte do rozpoznania zapytania, zostanie wyświetlony skierowany acykliczne Graph (DAG). Jeśli chcesz wyświetlić grupy DAG dla zapytań, które było wykonywane w ciągu ostatnich lub jeśli chcesz debugować proces aplikacji Tez, użyj [widoku Tez](../hdinsight-debug-ambari-tez-view.md) zamiast tego.

## <a name="view-job-history"></a>Wyświetlanie historii zadań

__Zadania__ kartę Wyświetla historię zapytań programu Hive.

![Obraz w historii zadań](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tabele bazy danych

Można użyć __tabel__ kartę, aby pracować z tabelami w bazie danych Hive.

![Obraz na karcie tabel](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Zapisane zapytania

Z **zapytania** kartę, można opcjonalnie zapisać zapytania. Po zapisaniu kwerendy, można użyć ponownie z __zapisane kwerendy__ kartę.

![Obraz karty zapisane kwerendy](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]
> Zapisane kwerendy są przechowywane w magazynie klastra domyślne. Można znaleźć zapisane kwerendy w ścieżce `/user/<username>/hive/scripts`. Są one przechowywane jako zwykły tekst `.hql` plików.
>
> W przypadku usunięcia klastra, ale zachować magazynu, można użyć narzędzia, takie jak [Eksploratora usługi Storage Azure](https://azure.microsoft.com/features/storage-explorer/) lub Eksploratora usługi Data Lake Storage (z [Azure Portal](https://portal.azure.com)) można pobrać zapytania.

## <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Hive można rozszerzyć za pomocą funkcji zdefiniowanej przez użytkownika (UDF). Użyj funkcji zdefiniowanej przez użytkownika do implementacji funkcji lub logikę, która nie jest łatwo zgodnie z modelem w HiveQL.

Deklarowanie i Zapisywanie zestawu funkcji UDF przy użyciu **UDF** u góry widoku Hive. Te funkcje UDF mogą być używane z **edytora zapytań**.

![Obraz karty funkcji zdefiniowanej przez użytkownika](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po dodaniu funkcji zdefiniowanej przez użytkownika do widoku Hive **Wstaw funkcje UDF** pojawi się przycisk w dolnej części **edytora zapytań**. Wybranie tego wpisu powoduje wyświetlenie listy rozwijanej funkcji UDF, zdefiniowane w widoku Hive. Wybieranie funkcji zdefiniowanej przez użytkownika dodaje instrukcje HiveQL do zapytania, aby włączyć funkcję zdefiniowaną przez użytkownika.

Na przykład, jeśli zdefiniowano UDF z następującymi właściwościami:

* Nazwa zasobu: myudfs

* Resource path: /myudfs.jar

* Nazwa funkcji zdefiniowanej przez użytkownika: myawesomeudf

* Nazwa klasy funkcji zdefiniowanej przez użytkownika: com.myudfs.Awesome

Przy użyciu **Wstaw funkcje UDF** przycisk zawiera wpis o nazwie **myudfs**, z innej listy rozwijanej dla każdej funkcji zdefiniowanej przez użytkownika zdefiniowane dla tego zasobu. W takim przypadku jest **myawesomeudf**. Wybranie tego wpisu dodaje następujące na początku zapytania:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Następnie można UDF w zapytaniu. Na przykład `SELECT myawesomeudf(name) FROM people;`.

Aby uzyskać więcej informacji o korzystaniu z funkcji UDF z Hive w usłudze HDInsight zobacz następujące artykuły:

* [Przy użyciu języka Python z Hive i Pig w usłudze HDInsight](python-udf-hdinsight.md)
* [Jak dodać niestandardowe UDF gałęzi do usługi HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Gałąź, ustawienia

Można zmienić różne ustawienia Hive, takie jak zmiany aparat wykonywania programu Hive w aplikacji Tez (ustawienie domyślne) do MapReduce.

## <a id="nextsteps"></a>Następne kroki

Ogólne informacje na temat Hive w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
