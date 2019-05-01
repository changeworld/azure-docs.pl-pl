---
title: Konfigurowanie ustawień platformy Spark — Azure HDInsight
description: Sposób konfigurowania platformy Spark dla klastra usługi Azure HDInsight.
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 77f4ec9cce5d02ea4cbcc4968d02773a13edfe5b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681300"
---
# <a name="configure-apache-spark-settings"></a>Konfigurowanie ustawień platformy Apache Spark

Klaster usługi HDInsight Spark obejmuje instalację [platformy Apache Spark](https://spark.apache.org/) biblioteki.  Każdy klaster HDInsight zawiera domyślne parametry konfiguracji dla wszystkich jego zainstalowanych usług, w tym platformy Spark.  Kluczowe aspekty zarządzania klastrem usługi HDInsight Apache Hadoop to monitorowania obciążenia, w tym zadań platformy Spark, aby upewnić się, że zadania działają w sposób przewidywalny. Uruchamianie platformy Spark najlepiej, zadań, należy wziąć pod uwagę konfiguracji klastra fizycznego podczas ustalania, jak zoptymalizować konfigurację logicznej klastra.

Klaster HDInsight Apache Spark domyślna zawiera następujące węzły: trzy [Apache ZooKeeper](https://zookeeper.apache.org/) węzłów, dwa węzły główne i jeden lub więcej węzłów procesu roboczego:

![Architektura HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

Liczba maszyn wirtualnych i rozmiarach maszyn wirtualnych, dla węzłów w klastrze usługi HDInsight może również wpływać na konfigurację platformy Spark. Wartości konfiguracji HDInsight inne niż domyślne często wymagają wartości konfiguracji aparatu Spark innych niż domyślne. Podczas tworzenia klastra usługi HDInsight Spark, są wyświetlane sugerowane rozmiarów maszyn wirtualnych dla poszczególnych składników. Obecnie [rozmiarów zoptymalizowanych pod kątem pamięci maszyny Wirtualnej systemu Linux](../../virtual-machines/linux/sizes-memory.md) dla platformy Azure są D12 v2 lub nowszej.

## <a name="apache-spark-versions"></a>Wersje platformy Apache Spark

Użyj najlepsze wersji platformy Spark dla klastra.  Usługa HDInsight obejmuje kilka wersji platformy Spark i HDInsight sam.  Każda wersja platformy Spark zawiera zestaw domyślnych ustawień klastra.  

Podczas tworzenia nowego klastra, istnieje wiele wersji platformy Spark do wyboru. Aby zapoznać się z pełną listą [HDInsight składniki i wersje](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Domyślna wersja platformy Apache Spark w usłudze HDInsight mogą ulec zmianie bez powiadomienia. Jeśli masz zależność wersji, firma Microsoft zaleca określić tej konkretnej wersji, podczas tworzenia klastrów za pomocą zestawu SDK platformy .NET, programu Azure PowerShell i klasycznego wiersza polecenia platformy Azure.

Platforma Apache Spark ma trzy lokalizacji konfiguracji systemu:

* Właściwości Spark kontrolować większość parametry aplikacji i można ustawić przy użyciu `SparkConf` obiektu, lub za pomocą języka Java właściwości systemu.
* Zmienne środowiskowe może służyć do ustawienia dla poszczególnych komputerów, takie jak adres IP za pośrednictwem `conf/spark-env.sh` skrypt na każdym węźle.
* Rejestrowanie można skonfigurować za pomocą `log4j.properties`.

Po wybraniu konkretnej wersji platformy Spark, klaster zawiera ustawienia konfiguracji domyślnej.  Możesz zmienić domyślnych wartości konfiguracji aparatu Spark za pomocą niestandardowego pliku konfiguracji platformy Spark.  Poniżej przedstawiono przykład.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

Powyższym przykładzie zastępuje kilka wartości domyślne dla pięciu parametry konfiguracji aparatu Spark.  Są to kodera-dekodera kompresji, Apache Hadoop MapReduce podziału minimalny rozmiar i rozmiary bloków parquet, a także partycji SQL dźwigar i wartości domyślne rozmiary otwartego pliku.  Te zmiany w konfiguracji są wybierane, ponieważ powiązane dane i zadań (w tym przykładzie danych dotyczących genomu) mają szczególne cechy, które wykona lepiej przy użyciu tych ustawień konfiguracji niestandardowej.

---

## <a name="view-cluster-configuration-settings"></a>Ustawienia konfiguracji klastra widoku

Przed wykonaniem optymalizacji wydajności w klastrze, należy sprawdzić bieżące ustawienia konfiguracji klastra HDInsight. Uruchomić Pulpit nawigacyjny HDInsight w witrynie Azure portal, klikając **pulpit nawigacyjny** łącze w okienku klastra Spark. Zaloguj się przy użyciu nazwy użytkownika i hasło administratora klastra.

Interfejs sieci Web Apache Ambari pojawia się przy użyciu widoku pulpitu nawigacyjnego metryki wykorzystania zasobów klastra klucza.  Pulpit nawigacyjny Ambari pokazuje konfiguracji platformy Apache Spark i innych usług, które zostały zainstalowane. Pulpit nawigacyjny zawiera **historii konfiguracji** karty, w którym można wyświetlać informacje o konfiguracji dla wszystkich zainstalowanych usług, w tym platformy Spark.

Aby wyświetlić wartości konfiguracji dla platformy Apache Spark, wybierz **historii konfiguracji**, a następnie wybierz **Spark2**.  Wybierz **Configs** kartę, a następnie wybierz `Spark` (lub `Spark2`, w zależności od używanej wersji) link na liście usług.  Możesz wyświetlić listę wartości konfiguracji dla klastra:

![Konfiguracje platformy Spark](./media/apache-spark-settings/spark-config.png)

Aby wyświetlić i zmienić poszczególne wartości konfiguracji aparatu Spark, wybierz dowolne łącze z wyrazem "spark" w tytule łącza.  Konfiguracje dla platformy Spark obejmują obie wartości niestandardowe i Zaawansowana konfiguracja, w następujące kategorie:

* Niestandardowe Spark2 wartości domyślne
* Niestandardowe właściwości Spark2 — metryki
* Advanced Spark2-defaults
* Zaawansowane środowisko Spark2
* Zaawansowane spark2-hive — lokacji — zastąpienie

Jeśli utworzysz zestaw innych niż domyślne wartości konfiguracji, a następnie można także wyświetlić historię aktualizacji konfiguracji.  Ta historia konfiguracji mogą być pomocne konfigurację innych niż domyślne, która ma optymalną wydajność.

> [!NOTE]  
> Aby wyświetlić, ale nie jest to zmienić, typowe ustawienia konfiguracji klastra platformy Spark, wybierz **środowiska** kartę na najwyższym poziomie **interfejsu użytkownika zadania Spark** interfejsu.

## <a name="configuring-spark-executors"></a>Konfigurowanie executors platformy Spark

Na poniższym diagramie przedstawiono obiektów kluczy Spark: program sterowników i jego skojarzonego kontekstu aparatu Spark i Menedżer klastra i jego *n* węzłów procesu roboczego.  Każdy węzeł procesu roboczego obejmuje wykonawca pamięci podręcznej, a *n* wystąpień zadań.

![Obiekty klastra](./media/apache-spark-settings/spark-arch.png)

Zadań platformy Spark korzystać z zasobów procesu roboczego, szczególnie pamięci, więc jest często Dostosuj wartości węzła procesu roboczego Executors w konfiguracji platformy Spark.

Są trzy najważniejsze parametry, które często są dostosowywane do dostrajania konfiguracji platformy Spark w celu wymagania aplikacji `spark.executor.instances`, `spark.executor.cores`, i `spark.executor.memory`. Program wykonujący to proces uruchamiany dla aplikacji platformy Spark. Wykonawca działa na węzeł procesu roboczego i jest odpowiedzialny za zadania dla aplikacji. Dla każdego klastra domyślna liczba executors i rozmiary wykonywania jest obliczany na podstawie liczby węzłów procesu roboczego i rozmiar węzła procesu roboczego. Są one przechowywane w `spark-defaults.conf` na głównymi węzłami klastra.  Te wartości w działającego klastra można edytować, wybierając **niestandardowe platformy spark — domyślne** łącze w interfejs webowy Ambari.  Po wprowadzeniu zmian, pojawi się monit przez interfejs użytkownika do **ponowne uruchomienie** wszystkich odpowiednich usług.

> [!NOTE]  
> Parametry tych trzech konfiguracji można konfigurować na poziomie klastra (dla wszystkich aplikacji, które działają w klastrze) i również określone dla poszczególnych aplikacji.

Innym źródłem informacji na temat zasoby używane przez Spark Executors jest interfejs użytkownika aplikacji aparatu Spark.  W Interfejsie użytkownika platformy Spark, wybierz **Executors** kartę, aby wyświetlić widoki Podsumowanie i szczegóły konfiguracji i zasobów używanych przez executors.  Widoki te mogą ułatwić określenie, czy chcesz zmienić wartości domyślne dla executors platformy Spark dla całego klastra lub konkretny zestaw Liczba wykonań zadań.

![Executors platformy Spark](./media/apache-spark-settings/spark-executors.png)

Alternatywnie umożliwia interfejs API REST Ambari programowo Sprawdź ustawienia konfiguracji klastra HDInsight i platformy Spark.  Więcej informacji znajduje się w temacie [dokumentacja interfejsu API Apache Ambari w serwisie GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

W zależności od obciążenia Spark można stwierdzić, niedomyślna Konfiguracja platforma Spark zapewnia bardziej zoptymalizowanego Spark Liczba wykonań zadań.  Należy przeprowadzić test porównawczy testowanie za pomocą przykładowych obciążeń do sprawdzania poprawności konfiguracji klastra innych niż domyślne.  Wspólne parametry, które można rozważyć zmianę, należą:

* `--num-executors` Ustawia liczbie funkcji wykonawczych.
* `--executor-cores` Ustawia liczbę rdzeni dla każdej funkcji wykonawczej. Zalecamy używanie middle-sized executors, ponieważ inne procesy zużywają również niektórych części ilość dostępnej pamięci.
* `--executor-memory` Określa rozmiar pamięci (Rozmiar sterty) każdej funkcji wykonawczej na [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), i należy pozostawić trochę pamięci dla wykonywania czynności.

Oto przykład z dwoma węzłami procesu roboczego z wartościami różnych konfiguracji:

![Dwie konfiguracje węzłów](./media/apache-spark-settings/executor-config.png)

Na poniższej liście przedstawiono kluczowe wykonawca Spark parametrów pamięci.

* `spark.executor.memory` Definiuje łączna ilość pamięci dostępnej dla wykonawcy.
* `spark.storage.memoryFraction` (wartość domyślna około 60%) Określa ilość pamięci dostępna do przechowywania danych utrwalonych.
* `spark.shuffle.memoryFraction` (wartość domyślna ~ 20%) Określa ilość pamięci zarezerwowanej dla losowa.
* `spark.storage.unrollFraction` i `spark.storage.safetyFraction` (łącznie 30 procent całkowitej ilości pamięci) — te wartości są używane wewnętrznie przez rozwiązanie Spark i nie powinny być zmieniane.

YARN steruje maksymalną suma pamięci używanych przez kontenery w każdym węźle platformy Spark. Na poniższym diagramie przedstawiono na węzeł relacje między obiektami konfiguracji usługi YARN i platformy Spark.

![Zarządzanie pamięcią platformy Spark usługi YARN](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmiana parametrów aplikacji uruchomionej w aplikacji Jupyter notebook

Klastry Spark w HDInsight obejmują wiele składników, domyślnie. Każda z tych składników obejmuje domyślnych wartości konfiguracji, które można przesłonić, zgodnie z potrzebami.

* Platforma Spark Core — Spark Core, Spark SQL, Spark, interfejsy API przesyłania strumieniowego, GraphX oraz MLlib platformy Spark Apache.
* Anaconda — Menedżer pakietami języka python.
* [Apache, usługi Livy](https://livy.incubator.apache.org/) -Apache Spark interfejsu API REST, używane do przesyłania zadań zdalne z klastrem usługi HDInsight Spark.
* [Jupyter](https://jupyter.org/) i [Apache Zeppelin](https://zeppelin.apache.org/) notebooki — interakcyjne oparte na przeglądarce interfejsie użytkownika dla interakcji z klastrem Spark.
* Sterownik ODBC — nawiązanie klastry Spark w HDInsight narzędzia analizy biznesowej, takich jak Microsoft Power BI i Tableau.

Aplikacje działające na platformie notesu programu Jupyter, można użyć `%%configure` polecenie, aby konfiguracja zmieni się z w obrębie samego notesu. Te zmiany konfiguracji zostaną zastosowane do zadania Spark są uruchamiane z wystąpienia notesu. Należy wprowadzić takie zmiany na początku aplikacji, przed uruchomieniem swojej pierwszej komórki kodu. Zmiany konfiguracji są stosowane do sesji usługi Livy, gdy zostanie utworzona.

> [!NOTE]  
> Aby zmienić konfigurację w terminie późniejszym etapie w aplikacji, użyj `-f` parametru (force). Jednak wszystkie postęp w aplikacji zostaną utracone.

Poniższy kod przedstawia sposób zmiany konfiguracji dla aplikacji działającej w notesie Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Podsumowanie

Istnieje kilka podstawowych ustawień konfiguracyjnych, które należy monitorować i dostosować, aby upewnić się, że Twoje zadania Spark działają w sposób przewidywalny i wydajne. Te ustawienia pomagają określić najlepszą konfiguracji klastra platformy Spark dla określonego obciążenia.  Należy również monitorować wykonywania długotrwałych i/lub korzystające z zasobów wykonywania zadań platformy Spark.  Najbardziej typowe wyzwania Centrum wokół wykorzystanie pamięci z powodu nieprawidłowej konfiguracji (szczególnie niepoprawnie o rozmiarze executors) długotrwałych operacji i zadań, które skutkują Kartezjańskiego operacji.

## <a name="next-steps"></a>Kolejne kroki

* [Apache Hadoop, składniki i wersje, które udostępniono HDInsight?](../hdinsight-component-versioning.md)
* [Zarządzanie zasobami klastra Apache Spark w HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i więcej](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfiguracja platformy Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Uruchamianie platformy Apache Spark w usłudze Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
