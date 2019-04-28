---
title: Praca z technologią Hive w HDInsight (Apache Hadoop) — Azure przy użyciu widoków Ambari Apache
description: Dowiedz się, jak przesłać zapytania Hive przy użyciu programu Hive widoku z przeglądarki sieci web. Widok programu Hive jest częścią Interfejsu sieci Web Ambari, które są dostarczane z klastra usługi HDInsight opartych na systemie Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095323"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Użyj widoku Hive narzędzia Ambari Apache przy użyciu technologii Apache Hadoop w HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak uruchamianie zapytań Hive przy użyciu widoku Hive narzędzia Ambari Apache. Hive View pozwala na tworzenie, optymalizowanie i uruchamianie zapytań Hive z poziomu przeglądarki sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Przeglądarki sieci web

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. Z [witryny Azure portal](https://portal.azure.com/), wybierz klaster.  Zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#showClusters) instrukcje. Klaster jest otwarty w nowym bloku portalu.

2. Z **pulpity nawigacyjne klastra**, wybierz opcję **widoków Ambari**. Gdy monit o uwierzytelnienie, należy użyć danych logowania klastra (domyślny `admin`) konta i hasło podane podczas tworzenia klastra.

3. Wybierz z listy widoków __Hive View__.

    ![Z widoku Hive wybrane](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Strona widoku Hive jest podobna do poniższej ilustracji:

    ![Obraz przedstawiający arkusz zapytania z widoku Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Z __zapytania__ kartę, wklej poniższe instrukcje HiveQL do arkusza:

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

   * `DROP TABLE`: Usuwa tabelę i plik danych, w przypadku, gdy tabela już istnieje.

   * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "external" w gałęzi.
     Tabele zewnętrzne przechowywać w definicji tabeli w gałęzi. Dane pozostaną w oryginalnej lokalizacji.

   * `ROW FORMAT`: Pokazuje, jak dane są sformatowane. W tym przypadku pola w każdym dzienniku są oddzielone spacją.

   * `STORED AS TEXTFILE LOCATION`: Pokazuje, w którym są przechowywane dane, a który są przechowywane jako tekst.

   * `SELECT`: Wybiera liczbę wszystkich wierszy, gdzie t4 kolumna zawiera wartość [Błąd].

   > [!IMPORTANT]  
   > Pozostaw __bazy danych__ zaznaczenia w __domyślne__. W przykładach w tym dokumencie używany domyślna baza danych dołączone do HDInsight.

5. Aby uruchomić kwerendę, zaznacz **Execute** pod arkuszem. Przycisk zmieni kolor na pomarańczowy i tekst zostanie zmieniony na **zatrzymać**.

6. Po zakończeniu zapytanie **wyniki** karcie są wyświetlane wyniki operacji. Następujący tekst jest wynik kwerendy:

        loglevel       count
        [ERROR]        3

    Możesz użyć **dziennika** kartę, aby wyświetlić dane rejestrowania, utworzonego przez zadanie.

   > [!TIP]  
   > Pobierz lub zapisać wyniki z **akcje** okno dialogowe listy rozwijanej w obszarze **wyniki** kartę.

### <a name="visual-explain"></a>Wyjaśniono Visual

Aby wyświetlić wizualizację planu zapytania, zaznacz **Visual wyjaśnić** kartę pod arkuszem.

**Visual wyjaśnić** widoku zapytania mogą być pomocne w zrozumienie przepływu złożonych zapytań.

### <a name="tez-ui"></a>Interfejsu użytkownika tez

Aby wyświetlić interfejsu użytkownika Tez dla zapytania, zaznacz **interfejsu użytkownika Tez** kartę pod arkuszem.

> [!IMPORTANT]  
> Tez nie jest używany do rozpoznawania wszystkich zapytań. Wiele zapytań można rozwiązać, bez korzystania z aplikacji Tez. 

## <a name="view-job-history"></a>Wyświetlanie historii zadań

__Zadań__ karta zawiera historię zapytań programu Hive.

![Obraz w historii zadań](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>tabele bazy danych

Możesz użyć __tabel__ kartę do pracy z tabelami w bazie danych programu Hive.

![Obraz karty tabel](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Zapisane zapytania

Z **zapytania** karcie, można opcjonalnie zapisać zapytania. Po zapisaniu kwerendy, można użyć ponownie go z __zapisane kwerendy__ kartę.

![Obraz karty zapisane zapytania](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Zapisane zapytania są przechowywane w domyślnego magazynu klastra. Zapisane zapytania w ścieżce można znaleźć `/user/<username>/hive/scripts`. Są one przechowywane jako zwykły tekst `.hql` plików.
>
> Jeśli usunąć klaster, ale nadal magazynu, możesz użyć narzędzia, takiego jak [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) lub Data Lake Storage Explorer (z [witryny Azure Portal](https://portal.azure.com)) można pobrać zapytań.

## <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Gałąź można rozszerzyć za pomocą funkcji zdefiniowanych przez użytkownika (UDF). Użyj funkcji zdefiniowanej przez użytkownika, aby zaimplementować funkcje lub logikę, która nie jest łatwo modelowane w HiveQL.

Deklarowanie i Zapisz zestaw funkcji zdefiniowanych przez użytkownika za pomocą **UDF** kartę w górnej części programu Hive widoku. Te funkcje zdefiniowane przez użytkownika mogą być używane z **edytora zapytań**.

![Obraz karty funkcji zdefiniowanej przez użytkownika](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po dodaniu funkcji zdefiniowanej przez użytkownika do widoku Hive **Wstaw UDF** pojawi się przycisk w dolnej części **edytora zapytań**. Wybranie tego wpisu powoduje wyświetlenie listy rozwijanej funkcje zdefiniowane przez użytkownika zdefiniowane w widoku Hive. Wybieranie funkcji zdefiniowanej przez użytkownika dodaje instrukcje HiveQL do zapytania, aby umożliwić funkcji zdefiniowanej przez użytkownika.

Na przykład, jeśli zdefiniowano funkcji zdefiniowanej przez użytkownika z następującymi właściwościami:

* Nazwa zasobu: myudfs

* Resource path: /myudfs.jar

* Nazwa funkcji zdefiniowanej przez użytkownika: myawesomeudf

* UDF class name: com.myudfs.Awesome

Za pomocą **Wstaw UDF** przycisk powoduje wyświetlenie wpis o nazwie **myudfs**, z innej listy rozwijanej dla każdej funkcji zdefiniowanej przez użytkownika zdefiniowane dla tego zasobu. W tym przypadku jest to **myawesomeudf**. Wybranie tego wpisu spowoduje dodanie następujących początek zapytania:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Można następnie użyć funkcji zdefiniowanej przez użytkownika w zapytaniu. Na przykład `SELECT myawesomeudf(name) FROM people;`.

Aby uzyskać więcej informacji na temat korzystania z funkcji zdefiniowanych przez użytkownika za pomocą technologii Hive w HDInsight zobacz następujące artykuły:

* [Apache Hive i Apache Pig w HDInsight przy użyciu języka Python](python-udf-hdinsight.md)
* [Jak dodać niestandardowe Apache Hive funkcji zdefiniowanej przez użytkownika do HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Ustawienia programu hive

Możesz zmienić różne ustawienia programu Hive, takie jak zmiana aparatu wykonywania programu Hive w aplikacji Tez (ustawienie domyślne), aby MapReduce.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat programu Hive na HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)

Więcej informacji dotyczących innych sposobów korzystania z usługi Hadoop w HDInsight:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)
