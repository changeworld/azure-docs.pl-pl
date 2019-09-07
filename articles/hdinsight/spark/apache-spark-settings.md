---
title: Konfigurowanie ustawień platformy Spark — Azure HDInsight
description: Jak wyświetlać i konfigurować ustawienia Apache Spark dla klastra usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 2d369af7c11473d811677f33f9112d41260fcecf
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736023"
---
# <a name="configure-apache-spark-settings"></a>Konfigurowanie ustawień platformy Apache Spark

Klaster Spark HDInsight An obejmuje instalację biblioteki [Apache Spark](https://spark.apache.org/) .  Każdy klaster usługi HDInsight zawiera domyślne parametry konfiguracji dla wszystkich zainstalowanych usług, w tym Spark.  Kluczowym aspektem zarządzania klastrem usługi HDInsight Apache Hadoop jest monitorowanie obciążenia, w tym zadań platformy Spark, aby upewnić się, że zadania działają w przewidywalny sposób. Aby najlepiej uruchamiać zadania platformy Spark, należy rozważyć konfigurację klastra fizycznego podczas określania sposobu optymalizowania konfiguracji logicznej klastra.

Domyślny Klaster Apache Spark usługi HDInsight zawiera następujące węzły: trzy węzły [Apache ZooKeeper](https://zookeeper.apache.org/) , dwa węzły główne i jeden lub więcej węzłów procesu roboczego:

![Architektura usługi HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

Liczba maszyn wirtualnych i rozmiary maszyn wirtualnych dla węzłów w klastrze usługi HDInsight może również wpływać na konfigurację platformy Spark. Inne niż domyślne wartości konfiguracji usługi HDInsight często wymagają niedomyślnych wartości konfiguracji platformy Spark. Podczas tworzenia klastra usługi HDInsight Spark są wyświetlane sugerowane rozmiary maszyn wirtualnych dla każdego składnika. Obecnie [zoptymalizowane pod kątem pamięci rozmiary maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/sizes-memory.md) dla platformy Azure to D12 w wersji 2 lub nowszej.

## <a name="apache-spark-versions"></a>Wersje Apache Spark

Użyj najlepszej wersji platformy Spark dla klastra.  Usługa HDInsight obejmuje kilka wersji zarówno platformy Spark, jak i usługi HDInsight.  Każda wersja platformy Spark zawiera zestaw domyślnych ustawień klastra.  

Podczas tworzenia nowego klastra istnieje wiele wersji platformy Spark do wyboru. Aby zapoznać się z pełną listą, [składniki i wersje usługi HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Domyślna wersja Apache Spark w usłudze HDInsight może ulec zmianie bez powiadomienia. Jeśli masz zależność wersji, firma Microsoft zaleca, aby określić tę konkretną wersję podczas tworzenia klastrów przy użyciu zestawu .NET SDK, Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure.

Apache Spark ma trzy lokalizacje konfiguracji systemu:

* Właściwości platformy Spark kontrolują większość parametrów aplikacji i można je ustawiać przy `SparkConf` użyciu obiektu lub za pośrednictwem właściwości systemu Java.
* Zmienne środowiskowe mogą służyć do ustawiania ustawień dla komputera, takich jak adres IP, za pomocą `conf/spark-env.sh` skryptu w każdym węźle.
* Rejestrowanie można skonfigurować przy użyciu `log4j.properties`.

W przypadku wybrania określonej wersji platformy Spark klaster zawiera domyślne ustawienia konfiguracji.  Domyślne wartości konfiguracji platformy Spark można zmienić przy użyciu niestandardowego pliku konfiguracji platformy Spark.  Poniżej przedstawiono przykład.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

W przykładzie przedstawionym powyżej zastąpił kilka wartości domyślnych dla pięciu parametrów konfiguracji platformy Spark.  Oto koder-dekoder kompresji, Apache Hadoop MapReduce podzielić minimalny rozmiar i rozmiary bloków Parquet, a także wartości domyślne dla partycji SQL i otwartych rozmiarów plików.  Te zmiany konfiguracji są wybierane, ponieważ skojarzone dane i zadania (w tym przykładzie dane genomiki) mają szczególne cechy, które będą działać lepiej przy użyciu tych niestandardowych ustawień konfiguracji.

---

## <a name="view-cluster-configuration-settings"></a>Wyświetl ustawienia konfiguracji klastra

Przed przeprowadzeniem optymalizacji wydajności w klastrze Sprawdź bieżące ustawienia konfiguracji klastra usługi HDInsight. Uruchom pulpit nawigacyjny usługi HDInsight z Azure Portal, klikając link **pulpitu nawigacyjnego** w okienku klaster Spark. Zaloguj się przy użyciu nazwy użytkownika i hasła administratora klastra.

Zostanie wyświetlony interfejs użytkownika sieci Web Apache Ambari z widokiem pulpitu nawigacyjnego metryk użycia zasobów klastra kluczy.  Na pulpicie nawigacyjnym Ambari przedstawiono konfigurację Apache Spark i inne zainstalowane usługi. Pulpit nawigacyjny zawiera kartę **historia konfiguracji** , w której można wyświetlić informacje o konfiguracji wszystkich zainstalowanych usług, w tym Spark.

Aby wyświetlić wartości konfiguracyjne dla Apache Spark, wybierz pozycję **historia konfiguracji**, a następnie wybierz pozycję **Spark2**.  Wybierz kartę **konfiguracje** , a następnie wybierz `Spark` link (lub `Spark2`, w zależności od wersji) na liście usług.  Zostanie wyświetlona lista wartości konfiguracyjnych dla klastra:

![Konfiguracje platformy Spark](./media/apache-spark-settings/spark-config.png)

Aby wyświetlić i zmienić poszczególne wartości konfiguracji platformy Spark, Wybierz dowolne łącze z słowem "Spark" w tytule łącza.  Konfiguracje dla platformy Spark zawierają wartości konfiguracji niestandardowej i zaawansowane w następujących kategoriach:

* Niestandardowe Spark2 — domyślne
* Niestandardowe Spark2 — metryki-właściwości
* Zaawansowane Spark2 — domyślne
* Advanced Spark2 — ENV
* Advanced spark2-Hive-site-override

W przypadku utworzenia niedomyślnego zestawu wartości konfiguracji można także wyświetlić historię aktualizacji konfiguracji.  Ta historia konfiguracji może ułatwić sprawdzenie, która konfiguracja niedomyślna ma optymalną wydajność.

> [!NOTE]  
> Aby zobaczyć, ale nie zmieniać, typowe ustawienia konfiguracji klastra Spark, wybierz kartę **środowisko** w INTERFEJSIE **interfejsu użytkownika zadania Spark** najwyższego poziomu.

## <a name="configuring-spark-executors"></a>Konfigurowanie programów wykonujących testy Spark

Na poniższym diagramie przedstawiono obiekty Key Spark: program sterownika i skojarzony z nim kontekst platformy Spark oraz Menedżer klastra i *węzły procesu roboczego* .  Każdy węzeł procesu roboczego obejmuje moduł wykonujący, pamięć podręczną i *n* wystąpień zadań.

![Obiekty klastra](./media/apache-spark-settings/spark-arch.png)

Zadania platformy Spark korzystają z zasobów procesów roboczych, szczególnie pamięci, dlatego należy dostosować wartości konfiguracji platformy Spark dla modułów wykonujących procesy robocze.

Trzy kluczowe parametry, które są często dostosowywane do dostrajania konfiguracji platformy Spark w celu `spark.executor.instances`poprawy `spark.executor.cores`wymagań aplikacji `spark.executor.memory`, to, i. Program wykonujący to proces uruchomiony dla aplikacji platformy Spark. Program wykonujący działa w węźle procesu roboczego i jest odpowiedzialny za zadania aplikacji. Dla każdego klastra domyślna liczba modułów wykonujących i rozmiary wykonawcze są obliczane na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Są one przechowywane w `spark-defaults.conf` węzłach głównych klastra.  Można edytować te wartości w uruchomionym klastrze, wybierając łącze **niestandardowe Spark-Defaults** w interfejsie użytkownika sieci Web Ambari.  Po wprowadzeniu zmian zostanie wyświetlony monit z interfejsem użytkownika w celu **ponownego uruchomienia** wszystkich objętych usług.

> [!NOTE]  
> Te trzy parametry konfiguracji można skonfigurować na poziomie klastra (dla wszystkich aplikacji uruchamianych w klastrze), a także dla każdej pojedynczej aplikacji.

Innym źródłem informacji o zasobach używanych przez funkcje wykonawcze platformy Spark jest interfejs użytkownika aplikacji platformy Spark.  W interfejsie użytkownika platformy Spark wybierz kartę **wykonawcy** , aby wyświetlić widok podsumowania i szczegółów konfiguracji i zasobów używanych przez program wykonujący.  Te widoki mogą pomóc w ustaleniu, czy należy zmienić wartości domyślne dla funkcji wykonawczych platformy Spark dla całego klastra, czy dla określonego zestawu wykonań.

![Testy platformy Spark](./media/apache-spark-settings/spark-executors.png)

Alternatywnie można użyć interfejsu API REST Ambari, aby programowo sprawdzić ustawienia konfiguracji klastra usługi HDInsight i usługi Spark.  Więcej informacji można znaleźć w [dokumentacji interfejsu API Apache Ambari w witrynie GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

W zależności od obciążenia platformy Spark może się okazać, że bardziej zoptymalizowane wykonania zadań platformy Spark zapewnia niedomyślna konfiguracja platformy Spark.  Należy przeprowadzić testy porównawcze z przykładowymi obciążeniami, aby zweryfikować wszelkie niedomyślne konfiguracje klastrów.  Poniżej wymieniono niektóre typowe parametry, których dostosowanie warto rozważyć:

* `--num-executors`Ustawia liczbę modułów wykonujących.
* `--executor-cores`Ustawia liczbę rdzeni dla każdego wykonawcy. Zalecamy użycie wykonawców o rozmiarze średnim, ponieważ inne procesy zużywają również część dostępnej pamięci.
* `--executor-memory`steruje rozmiarem pamięci (rozmiarem sterty) każdego wykonawcy na [Apache HADOOP przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)i trzeba będzie pozostawić pewną ilość pamięci do wykonania.

Oto przykład dwóch węzłów procesu roboczego z różnymi wartościami konfiguracji:

![Dwie konfiguracje węzłów](./media/apache-spark-settings/executor-config.png)

Na poniższej liście przedstawiono parametry pamięci programu wykonującego testy.

* `spark.executor.memory`Określa łączną ilość pamięci dostępną dla wykonawcy.
* `spark.storage.memoryFraction`(domyślnie ~ 60%) Określa ilość pamięci dostępną do przechowywania utrwalonych odporne.
* `spark.shuffle.memoryFraction`(domyślnie ~ 20%) definiuje ilość pamięci zarezerwowanej do losowego użycia.
* `spark.storage.unrollFraction`i `spark.storage.safetyFraction` (łącznie ~ 30% całkowitej ilości pamięci) — te wartości są używane wewnętrznie przez platformę Spark i nie powinny być zmieniane.

PRZĘDZa kontroluje maksymalną sumę pamięci używaną przez kontenery w każdym węźle Spark. Na poniższym diagramie przedstawiono relacje poszczególnych węzłów między obiektami konfiguracji PRZĘDZenia a obiektami Spark.

![Zarządzanie pamięcią w ramach PRZĘDZy](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmień parametry aplikacji uruchomionej w notesie Jupyter

Klastry Spark w usłudze HDInsight zawierają domyślnie wiele składników. Każdy z tych składników zawiera domyślne wartości konfiguracji, które można przesłonić w razie konieczności.

* Spark Core — Spark Core, Spark SQL, interfejsy API przesyłania strumieniowego Spark, GraphX i Apache Spark MLlib.
* Anaconda — Menedżer pakietów języka Python.
* [Apache usługi Livy](https://livy.incubator.apache.org/) — interfejs API REST Apache Spark używany do przesyłania zadań zdalnych do klastra usługi HDInsight Spark.
* Notesy [Jupyter](https://jupyter.org/) i [Apache Zeppelin](https://zeppelin.apache.org/) — interaktywny interfejs użytkownika oparty na przeglądarce służący do interakcji z klastrem Spark.
* Sterownik ODBC — łączy klastry Spark w usłudze HDInsight z narzędziami analizy biznesowej, takimi jak Microsoft Power BI i Tableau.

W przypadku aplikacji uruchamianych w notesie Jupyter Użyj `%%configure` polecenia, aby wprowadzić zmiany konfiguracji z poziomu notesu. Te zmiany konfiguracji zostaną zastosowane do zadań platformy Spark uruchomionych z wystąpienia notesu. Należy wprowadzić takie zmiany na początku aplikacji, przed uruchomieniem pierwszej komórki kodu. Zmieniona konfiguracja zostanie zastosowana do sesji usługi Livy, gdy zostanie utworzona.

> [!NOTE]  
> Aby zmienić konfigurację na późniejszym etapie w aplikacji, użyj `-f` parametru (Force). Jednak cały postęp w aplikacji zostanie utracony.

Poniższy kod przedstawia sposób zmiany konfiguracji aplikacji uruchomionej w notesie Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Wniosek

Istnieją różne podstawowe ustawienia konfiguracji, które należy monitorować i dostosowywać w celu zapewnienia, że zadania platformy Spark działają w przewidywalny i wydajny sposób. Te ustawienia pomagają określić najlepszą konfigurację klastra platformy Spark dla określonych obciążeń.  Konieczne będzie również monitorowanie wykonywania długotrwałych i/lub czasochłonnych wykonań zadań platformy Spark.  Najczęstsze centrum wyzwań pozwala na wykorzystanie pamięci z powodu nieprawidłowych konfiguracji (szczególnie programów wykonujących nieprawidłowe rozmiary), długotrwałych operacji i zadań, które powodują kartezjańskiego operacji.

## <a name="next-steps"></a>Następne kroki

* [Apache Hadoop składniki i wersje dostępne w usłudze HDInsight?](../hdinsight-component-versioning.md)
* [Zarządzanie zasobami klastra Apache Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfiguracja Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Uruchamianie Apache Spark na Apache Hadoop PRZĘDZy](https://spark.apache.org/docs/latest/running-on-yarn.html)
