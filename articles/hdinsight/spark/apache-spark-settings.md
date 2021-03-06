---
title: Konfigurowanie ustawień platformy Spark — usługa Azure HDInsight
description: Jak wyświetlić i skonfigurować ustawienia platformy Apache Spark dla klastra usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272021"
---
# <a name="configure-apache-spark-settings"></a>Konfigurowanie ustawień platformy Apache Spark

Klaster platformy HDInsight Spark zawiera instalację biblioteki [Platformy Apache Spark.](https://spark.apache.org/)  Każdy klaster HDInsight zawiera domyślne parametry konfiguracji dla wszystkich zainstalowanych usług, w tym spark.  Kluczowym aspektem zarządzania klastrem HDInsight Apache Hadoop jest monitorowanie obciążenia, w tym spark Jobs, aby upewnić się, że zadania są uruchomione w przewidywalny sposób. Aby najlepiej uruchomić zadania platformy Spark, należy wziąć pod uwagę konfigurację klastra fizycznego podczas określania sposobu optymalizacji konfiguracji logicznej klastra.

Domyślny klaster platformy Spark usługi HDInsight Apache zawiera następujące węzły: trzy węzły [Apache ZooKeeper,](https://zookeeper.apache.org/) dwa węzły główne i jeden lub więcej węzłów procesu roboczego:

![Architektura Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Liczba maszyn wirtualnych i rozmiary maszyn wirtualnych dla węzłów w klastrze HDInsight może również mieć wpływ na konfigurację platformy Spark. Nieniezyczne wartości konfiguracji HDInsight często wymagają nieokrążących wartości konfiguracji platformy Spark. Podczas tworzenia klastra platformy SPARK programu HDInsight wyświetlane są sugerowane rozmiary maszyn wirtualnych dla każdego ze składników. Obecnie [zoptymalizowane pod kątem pamięci rozmiary maszyn wirtualnych systemu Linux](../../virtual-machines/linux/sizes-memory.md) dla platformy Azure są D12 v2 lub większe.

## <a name="apache-spark-versions"></a>Wersje Apache Spark

Użyj najlepszej wersji platformy Spark dla klastra.  Usługa HDInsight zawiera kilka wersji zarówno platformy Spark, jak i hdinsight.  Każda wersja programu Spark zawiera zestaw domyślnych ustawień klastra.  

Podczas tworzenia nowego klastra, istnieje wiele wersji platformy Spark do wyboru. Aby wyświetlić pełną listę, [składniki i wersje programu HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Domyślna wersja platformy Apache Spark w usłudze HDInsight może ulec zmianie bez powiadomienia. Jeśli masz zależność wersji, firma Microsoft zaleca określenie tej konkretnej wersji podczas tworzenia klastrów przy użyciu zestawu .NET SDK, Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure.

Apache Spark ma trzy lokalizacje konfiguracji systemu:

* Właściwości platformy Spark kontrolują większość parametrów aplikacji `SparkConf` i można je ustawić za pomocą obiektu lub za pomocą właściwości systemu Java.
* Zmienne środowiskowe mogą służyć do ustawiania ustawień na komputerze, `conf/spark-env.sh` takich jak adres IP, za pośrednictwem skryptu w każdym węźle.
* Rejestrowanie można skonfigurować `log4j.properties`za pomocą programu .

Po wybraniu określonej wersji programu Spark klaster zawiera domyślne ustawienia konfiguracji.  Domyślne wartości konfiguracji platformy Spark można zmienić przy użyciu niestandardowego pliku konfiguracyjnego platformy Spark.  Przykład przedstawiono poniżej.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Przykład pokazany powyżej zastępuje kilka wartości domyślnych dla pięciu parametrów konfiguracji platformy Spark.  Są to kodek kompresji, Apache Hadoop MapReduce split minimalny rozmiar i parkiet rozmiary bloku, a także partycji Spar SQL i otwartych rozmiarów plików wartości domyślnych.  Te zmiany konfiguracji są wybierane, ponieważ skojarzone dane i zadania (w tym przykładzie dane genomowe) mają określone cechy, które będą działać lepiej przy użyciu tych niestandardowych ustawień konfiguracji.

---

## <a name="view-cluster-configuration-settings"></a>Wyświetlanie ustawień konfiguracji klastra

Sprawdź bieżące ustawienia konfiguracji klastra usługi HDInsight przed wykonaniem optymalizacji wydajności w klastrze. Uruchom pulpit nawigacyjny usługi HDInsight z witryny Azure portal, klikając łącze **pulpitu nawigacyjnego** w okienku klastra platformy Spark. Zaloguj się przy użyciu nazwy użytkownika i hasła administratora klastra.

Pojawi się interfejs użytkownika sieci Web Apache Ambari z widokiem pulpitu nawigacyjnego metryk wykorzystania zasobów klastra kluczy.  Pulpit nawigacyjny Ambari pokazuje konfigurację platformy Spark Apache i inne zainstalowane usługi. Pulpit nawigacyjny zawiera kartę **Historia konfiguracji,** na której można wyświetlić informacje o konfiguracji wszystkich zainstalowanych usług, w tym platformy Spark.

Aby wyświetlić wartości konfiguracyjne programu Apache Spark, wybierz pozycję **Historia konfiguracji**, a następnie wybierz **spark2**.  Wybierz kartę **Konfiguracje,** `Spark` a `Spark2`następnie wybierz łącze (lub , w zależności od wersji) na liście usług.  Zobaczysz listę wartości konfiguracji klastra:

![Konfiguracje platformy Spark](./media/apache-spark-settings/spark-configurations.png)

Aby wyświetlić i zmienić poszczególne wartości konfiguracji platformy Spark, wybierz dowolne łącze ze słowem "iskra" w tytule łącza.  Konfiguracje platformy Spark obejmują zarówno niestandardowe, jak i zaawansowane wartości konfiguracji w następujących kategoriach:

* Niestandardowe ustawienia domyślne Spark2
* Niestandardowe właściwości spark2-metrics
* Zaawansowane domyślne ustawienia spark2
* Zaawansowana spark2-env
* Zaawansowane zastępowanie spark2-hive-site

Jeśli utworzysz nieobezawione wartości wartości konfiguracji, można również wyświetlić historię aktualizacji konfiguracji.  Ta historia konfiguracji może być przydatna, aby zobaczyć, która konfiguracja nienawiąca ma optymalną wydajność.

> [!NOTE]  
> Aby wyświetlić, ale nie zmienić, typowe ustawienia konfiguracji klastra Platformy Spark, wybierz kartę **Środowisko** w interfejsie **interfejsu użytkownika zadania programu Spark** najwyższego poziomu.

## <a name="configuring-spark-executors"></a>Konfigurowanie executorów platformy Spark

Na poniższym diagramie przedstawiono kluczowe obiekty platformy Spark: program sterownika i skojarzony z nim kontekst platformy Spark oraz menedżera klastra i *jego* n węzły procesu roboczego.  Każdy węzeł procesu roboczego zawiera executor, pamięć podręczną i *n* wystąpień zadań.

![Obiekty klastra](./media/apache-spark-settings/hdi-spark-architecture.png)

Zadania platformy Spark używają zasobów procesu roboczego, w szczególności pamięci, dlatego często dostosowuje wartości konfiguracji platformy Spark dla executorów węzła procesu roboczego.

Trzy kluczowe parametry, które są często dostosowywane do `spark.executor.instances`konfiguracji `spark.executor.cores`platformy `spark.executor.memory`Spark w celu poprawy wymagań aplikacji to , i . Executor to proces uruchomiony dla aplikacji Spark. Wykonawca działa w węźle procesu roboczego i jest odpowiedzialny za zadania dla aplikacji. Dla każdego klastra domyślna liczba executors i rozmiary wykonawcy jest obliczana na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Są one `spark-defaults.conf` przechowywane w węzłach głównego klastra.  Wartości te można edytować w uruchomionym klastrze, wybierając **łącze Niestandardowe domyślne iskiery** w interfejsie użytkownika sieci Web Ambari.  Po wkonaniu zmian zostanie wyświetlony monit użytkownika o **ponowne uruchomienie** wszystkich usług, których dotyczy problem.

> [!NOTE]  
> Te trzy parametry konfiguracji można skonfigurować na poziomie klastra (dla wszystkich aplikacji uruchamianych w klastrze), a także określić dla każdej aplikacji.

Innym źródłem informacji o zasobach używanych przez funkcje wykonawcze platformy Spark jest interfejs użytkownika aplikacji platformy Spark.  W interfejsie użytkownika platformy Spark wybierz kartę **Executors,** aby wyświetlić widoki Podsumowanie i szczegóły konfiguracji i zasobów zużywanych przez wykonawców.  Te widoki mogą pomóc w ustaleniu, czy należy zmienić wartości domyślne dla funkcji wykonawczych platformy Spark dla całego klastra, czy dla określonego zestawu wykonań.

![Wykonawcy iskry](./media/apache-spark-settings/apache-spark-executors.png)

Alternatywnie można użyć interfejsu API AMbari REST do programowej weryfikacji ustawień konfiguracji klastra HDInsight i Spark.  Więcej informacji można znaleźć w [aplikacji Apache Ambari API reference on GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

W zależności od obciążenia platformy Spark może się okazać, że bardziej zoptymalizowane wykonania zadań platformy Spark zapewnia niedomyślna konfiguracja platformy Spark.  Należy przeprowadzić testy porównawcze z przykładowymi obciążeniami, aby zweryfikować wszelkie niedomyślne konfiguracje klastrów.  Poniżej wymieniono niektóre typowe parametry, których dostosowanie warto rozważyć:

* `--num-executors`ustawia liczbę wykonawców.
* `--executor-cores`ustawia liczbę rdzeni dla każdego wykonawcy. Zaleca się używanie wykonawców średniej wielkości, ponieważ inne procesy również zużywają część dostępnej pamięci.
* `--executor-memory`steruje rozmiarem pamięci (rozmiar sterty) każdego wykonawcy na [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), i musisz pozostawić trochę pamięci do wykonania narzutów.

Oto przykład dwóch węzłów procesu roboczego o różnych wartościach konfiguracyjnych:

![Konfiguracje dwóch węzłów](./media/apache-spark-settings/executor-configuration.png)

Na poniższej liście przedstawiono kluczowe parametry pamięci egzekutora platformy Spark.

* `spark.executor.memory`określa całkowitą ilość pamięci dostępnej dla wykonawcy.
* `spark.storage.memoryFraction`(domyślnie ~60%) określa ilość pamięci dostępnej do przechowywania utrwalonych RDD.
* `spark.shuffle.memoryFraction`(domyślnie ~20%) określa ilość pamięci zarezerwowanej do przetasowania.
* `spark.storage.unrollFraction`i `spark.storage.safetyFraction` (w sumie ~30% całkowitej pamięci) - wartości te są używane wewnętrznie przez Spark i nie powinny być zmieniane.

YARN kontroluje maksymalną sumę pamięci używanej przez kontenery w każdym węźle Spark. Na poniższym diagramie przedstawiono relacje między obiektami konfiguracji YARN a obiektami Spark.

![Zarządzanie pamięcią iskry przędzy](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmienianie parametrów aplikacji uruchomionej w notesie Jupyter

Klastry platformy Spark w umiań HDInsight domyślnie zawierają wiele składników. Każdy z tych składników zawiera domyślne wartości konfiguracji, które można zastąpić w razie potrzeby.

* Spark Core - Spark Core, Spark SQL, Spark streaming API, GraphX i Apache Spark MLlib.
* Anaconda - menedżer pakietów python.
* [Apache Livy](https://livy.incubator.apache.org/) - interfejs API Apache Spark REST, używany do przesyłania zadań zdalnych do klastra platformy SPARK usługi HDInsight.
* [Notebooki Jupyter](https://jupyter.org/) i [Apache Zeppelin](https://zeppelin.apache.org/) — interaktywny interfejs użytkownika oparty na przeglądarce do interakcji z klastrem Spark.
* Sterownik ODBC — łączy klastry platformy Spark w programie HDInsight z narzędziami analizy biznesowej (BI), takimi jak Microsoft Power BI i Tableau.

W przypadku aplikacji działających w notesie `%%configure` Jupyter użyj polecenia, aby wprowadzić zmiany konfiguracji z poziomu samego notesu. Te zmiany konfiguracji zostaną zastosowane do zadań platformy Spark uruchamiane z wystąpienia notesu. Należy wprowadzić takie zmiany na początku aplikacji, przed uruchomieniem pierwszej komórki kodu. Zmieniona konfiguracja jest stosowana do sesji Livy po jej utworzeniu.

> [!NOTE]  
> Aby zmienić konfigurację na późniejszym etapie `-f` aplikacji, należy użyć parametru (siła). Jednak wszystkie postępy w aplikacji zostaną utracone.

Poniższy kod pokazuje, jak zmienić konfigurację aplikacji uruchomionej w notesie Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Podsumowanie

Istnieje wiele podstawowych ustawień konfiguracji, które należy monitorować i dostosowywać, aby zapewnić, że zadania platformy Spark są uruchamiane w przewidywalny i wydajny sposób. Te ustawienia pomagają określić najlepszą konfigurację klastra platformy Spark dla poszczególnych obciążeń.  Należy również monitorować wykonywanie długotrwałych i/lub zużywających zasoby wykonania zadań Platformy Spark.  Najczęstsze wyzwania koncentrują się wokół ciśnienia pamięci z powodu nieprawidłowych konfiguracji (szczególnie niepoprawnie wielkości executors), długotrwałych operacji i zadań, które powodują operacje kartezjańskie.

## <a name="next-steps"></a>Następne kroki

* [Apache Hadoop komponenty i wersje dostępne z HDInsight?](../hdinsight-component-versioning.md)
* [Zarządzanie zasobami klastra Platformy Spark apache w programie HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfiguracja iskry Apache](https://spark.apache.org/docs/latest/configuration.html)
* [Uruchamianie Apache Spark na Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
