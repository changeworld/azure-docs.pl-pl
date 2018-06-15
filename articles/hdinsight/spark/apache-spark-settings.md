---
title: Skonfiguruj ustawienia Spark - Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak skonfigurować Spark dla klastra usługi HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: db61cc81f51772aa98c034f1bfdf51777cfd68e7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165217"
---
# <a name="configure-spark-settings"></a>Konfigurowanie ustawień platformy Spark

Klaster Spark w usłudze HDInsight obejmuje instalację biblioteki Apache Spark.  Każdy klaster usługi HDInsight zawiera parametry konfiguracji domyślnego dla wszystkich jego zainstalowanych usług, w tym Spark.  Kluczowym aspektem zarządzania klastrem HDInsight Hadoop jest monitorowanie obciążenia, w tym zadania Spark, aby upewnić się, że zadania są uruchomione w sposób przewidywalny. Aby najlepiej uruchomić Spark zadania, należy wziąć pod uwagę konfiguracji klastra fizycznego podczas określania sposobu optymalizacji logicznej konfiguracji klastra.

Domyślne klastra Apache Spark w usłudze HDInsight obejmuje następujące węzły: trzy węzły dozorcy dwóch węzłów głównych i jeden lub więcej węzłów procesu roboczego:

![Architektura usługi HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

Liczbę maszyn wirtualnych i rozmiarów maszyn wirtualnych dla węzłów w klastrze usługi HDInsight mogą również wpływać na konfigurację Spark. Wartości konfiguracji HDInsight inne niż domyślne często wymagają wartości konfiguracji Spark innych niż domyślne. Podczas tworzenia klastra Spark w usłudze HDInsight, są wyświetlane sugerowane rozmiarów maszyn wirtualnych dla poszczególnych składników. Obecnie [rozmiary zoptymalizowanych pod kątem pamięci maszyny Wirtualnej systemu Linux](../../virtual-machines/linux/sizes-memory.md) dla platformy Azure są D12 w wersji 2 lub nowszej.

## <a name="spark-versions"></a>Wersje Spark

Użyj najlepszą wersję Spark dla klastra.  Usługa HDInsight obejmuje kilka wersji zarówno w iskrowym, jak i HDInsight się.  Każda wersja programu Spark zawiera zestaw domyślnych ustawień klastra.  

Podczas tworzenia nowego klastra, w tym miejscu są bieżące wersje Spark do wyboru:

![Wersje Spark](./media/apache-spark-settings/spark-version.png)

Platforma Spark 2.x można uruchomić znacznie lepszą niż Spark 1.x. Platforma Spark 2.x ma kilka optymalizacji wydajności, takich jak wolframu, Catalyst optymalizacji zapytania i inne.  

> [!NOTE]
> Wersja domyślna platforma Apache Spark w usłudze HDInsight mogą ulec zmianie bez uprzedzenia. Jeśli masz zależność wersji, firma Microsoft zaleca określić tej konkretnej wersji, podczas tworzenia klastrów przy użyciu programu .NET SDK/Azure PowerShell i interfejsu wiersza polecenia Azure.

Platforma Apache Spark ma trzy lokalizacje w systemie konfiguracji:

* Właściwości Spark kontroli parametry większość aplikacji i można ustawić przy użyciu `SparkConf` obiekt, lub za pomocą właściwości systemu Java.
* Zmienne środowiskowe może służyć do ustawienia dla poszczególnych komputerów, takie jak adres IP za pośrednictwem `conf/spark-env.sh` skrypt w każdym węźle.
* Można skonfigurować rejestrowania za pośrednictwem `log4j.properties`.

Po wybraniu konkretnej wersji Spark klaster zawiera domyślne ustawienia konfiguracji.  Domyślnych wartości konfiguracji w iskrowym można zmienić za pomocą niestandardowego pliku konfiguracji Spark.  Poniżej przedstawiono przykład.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

Powyższym przykładzie zastępuje kilka wartości domyślne parametrów konfiguracji pięć Spark.  Są to koder-dekoder kompresji, Hadoop MapReduce podzielić minimalny rozmiar i rozmiarze bloku parkiet i partycji dźwigar SQL i wartości domyślne rozmiary otwartych plików.  Te zmiany w konfiguracji są wybrane, ponieważ powiązanych danych i zatrudnienia (w tym przykładzie dane genomu) posiadają pewne specyficzne cechy, które wykona lepiej przy użyciu tych ustawień konfiguracji niestandardowej.

---

## <a name="view-cluster-configuration-settings"></a>Wyświetl ustawienia konfiguracji klastra

Przed wykonaniem optymalizacji wydajności w klastrze, należy sprawdzić bieżące ustawienia konfiguracji klastra usługi HDInsight. Uruchamianie pulpitu nawigacyjnego usługi HDInsight w portalu Azure, klikając **pulpitu nawigacyjnego** łącze w okienku klastra Spark. Zaloguj się przy użyciu nazwy użytkownika i hasła administratora klastra.

Interfejs sieci Web Ambari zostanie wyświetlony widok pulpitu nawigacyjnego metryki użycia zasobów klucza klastra.  Ambari pulpitu nawigacyjnego przedstawia konfigurację Apache Spark i innych usług, które zostały zainstalowane. Pulpit nawigacyjny zawiera **historii konfiguracji** kartę, w którym można wyświetlać informacje o konfiguracji wszystkich zainstalowanych usług, w tym Spark.

Aby wyświetlić wartości konfiguracji Apache Spark, wybierz **historii konfiguracji**, a następnie wybierz pozycję **Spark2**.  Wybierz **Configs** , a następnie wybierz `Spark` (lub `Spark2`w zależności od wersji) łącza na liście usług.  Możesz wyświetlić listę wartości konfiguracji dla klastra:

![Konfiguracje Spark](./media/apache-spark-settings/spark-config.png)

Aby wyświetlić i zmienić poszczególne wartości konfiguracji Spark, wybierz dowolne łącze od słowa "spark" w tytule łącza.  Konfiguracje platformy Spark obejmują obie wartości niestandardowych i Konfiguracja zaawansowana tych kategorii:

* Niestandardowe Spark2 wartości domyślnych
* Niestandardowe właściwości do metryki Spark2
* Zaawansowane Spark2 wartości domyślnych
* Zaawansowane env Spark2
* Zaawansowane spark2-hive — lokacji — zastąpienie

Jeśli tworzysz z systemem innym niż domyślny zestaw wartości konfiguracji, można również sprawdzić historię aktualizacji konfiguracji.  Historia tej konfiguracji mogą być pomocne konfigurację innych niż domyślne, która ma optymalną wydajność.

> [!NOTE]
> Aby zobaczyć, ale nie jest to zmienić, typowe ustawienia konfiguracji klastra Spark, wybierz **środowiska** kartę na najwyższym poziomie **interfejsu użytkownika zadania Spark** interfejsu.

## <a name="configuring-spark-executors"></a>Konfigurowanie modułów Spark

Na poniższym diagramie przedstawiono obiektów w iskrowym kluczy: program sterownika i jego skojarzony kontekst Spark i Menedżer klastra i jego *n* węzły pracownika.  Każdy węzeł pracownika zawiera wykonawcy, pamięci podręcznej, a *n* zadań wystąpień.

![Obiekty klastra](./media/apache-spark-settings/spark-arch.png)

Zadania Spark korzystają z zasobów roboczych, szczególnie pamięci tak często, aby dopasować Spark wartości konfiguracji węzła procesu roboczego modułów.

Są trzy parametry klucza, które są często dostosowana do dostrajania konfiguracji Spark zwiększające wymagań aplikacji `spark.executor.instances`, `spark.executor.cores`, i `spark.executor.memory`. Moduł wykonujący jest uruchomiona aplikacji Spark. Program jest uruchamiany w węźle procesu roboczego i jest odpowiedzialne za zadania dla aplikacji. Dla każdego klastra domyślna liczba modułów i rozmiary Moduł wykonujący jest obliczany na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Są one przechowywane w `spark-defaults.conf` na głównymi węzłami klastra.  Te wartości w klastrze uruchomione można edytować, wybierając **niestandardowe spark — domyślne** łącze w interfejsie użytkownika sieci web Ambari.  Po wprowadzeniu zmian, zostanie wyświetlony monit przez interfejs użytkownika do **ponowne uruchomienie** wszystkich odpowiednich usług.

> [!NOTE]
> Parametry te trzy konfiguracji można konfigurować na poziomie klastra (dla wszystkich aplikacji, które są uruchamiane w klastrze) i również określona dla poszczególnych aplikacji.

Inne źródło informacji o zasoby używane przez modułów Spark jest interfejs użytkownika aplikacji Spark.  W Interfejsie użytkownika Spark, wybierz **modułów** kartę, aby wyświetlić widoki Podsumowanie i szczegóły konfiguracji i zasobów używanych przez modułów.  Widoki te mogą ułatwić określenie, czy można zmienić wartości domyślne dla modułów Spark dla całego klastra lub określonego zestawu wykonania zadania.

![Platforma Spark modułów](./media/apache-spark-settings/spark-executors.png)

Alternatywnie można użyć interfejsu API REST Ambari Aby programowo sprawdzić ustawienia konfiguracji klastra usługi HDInsight i Spark.  Więcej informacji znajduje się w temacie [Ambari API reference w witrynie GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

W zależności od obciążenia Spark należy określić, że konfiguracja Spark innych niż domyślne zapewnia bardziej zoptymalizowane Spark wykonania zadania.  Należy przeprowadzić testowanie obciążeń próbki do sprawdzania poprawności konfiguracji klastra z systemem innym niż domyślny za pomocą testu wydajności.  Niektóre typowe parametry, które można rozważyć dostosowania są:

* `--num-executors` Ustawia liczbę wykonawców.
* `--executor-cores` Ustawia liczbę rdzeni dla każdego wykonawcy. Zalecamy używanie modułów middle-sized zgodnie z innymi procesami również korzystać z niektórych części ilość dostępnej pamięci.
* `--executor-memory` Formanty rozmiar pamięci (rozmiar stosu) każdego wykonawcy na PRZĘDZY, a będziesz musiał zostawić trochę pamięci dla wykonania obciążenie.

Poniżej przedstawiono przykład dwóch węzłów procesu roboczego o wartości innej konfiguracji:

![Dwie konfiguracje węzłów](./media/apache-spark-settings/executor-config.png)

Na poniższej liście przedstawiono klucza przetwarzania Spark parametrów pamięci.

* `spark.executor.memory` Określa całkowitą ilość pamięci dostępnej dla wykonawcy.
* `spark.storage.memoryFraction` (ustawienie domyolne ~ 60%) określa ilość pamięci dostępnej do przechowywania trwałych RDDs.
* `spark.shuffle.memoryFraction` (ustawienie domyolne ~ 20%) określa ilość pamięci zarezerwowanej dla losowo.
* `spark.storage.unrollFraction` i `spark.storage.safetyFraction` (sumowanie ~ 30% całkowitej pamięci) - wartości te są używane wewnętrznie w iskrowym i nie powinno być zmieniane.

YARN steruje maksymalną suma pamięci używanej przez kontenery w każdym węźle Spark. Na poniższym diagramie przedstawiono każdego węzła relacje między obiektami konfiguracji YARN i Spark.

![Zarządzanie pamięcią Spark YARN](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmiana parametrów aplikacji uruchomionej w notesu Jupyter

Domyślnie klastry Spark w usłudze HDInsight zawierają liczbę składników. Każdy z tych składników zawiera domyślnych wartości konfiguracyjnych, które może być zastąpiona.

* Platforma Spark Core - Spark Core, Spark SQL, Spark interfejsów API przesyłania strumieniowego, GraphX oraz MLlib
* Anaconda — Menedżera pakietów języka python
* Livy - Apache Spark interfejsu API REST, używany do przesyłania zdalnego zadań do klastra Spark w usłudze HDInsight
* Notesów Jupyter i Zeppelin - interaktywnego interfejsu do interakcji z klastrem Spark oparty na przeglądarce użytkownika
* Sterownik ODBC - nawiązanie business intelligence (BI) narzędzi, takich jak Microsoft Power BI i Tableau klastry Spark w usłudze HDInsight

Dla aplikacji działających w notesu Jupyter, użyj `%%configure` polecenie, aby konfiguracja zostanie zmieniony z wewnątrz samego notesu. Te zmiany konfiguracji zostaną zastosowane do zadań Spark uruchamiała się z wystąpieniem notesu. Takie zmiany należy ustawić na początku aplikacji, przed uruchomieniem pierwszej komórki kodu. Zmiany konfiguracji jest stosowany do sesji programu Livy, gdy zostanie utworzony.

> [!NOTE]
> Aby zmienić konfigurację na późniejszym etapie w aplikacji, użyj `-f` parametru (force). Jednak wszystkie postęp w aplikacji zostaną utracone.

Poniższy kod przedstawia sposób zmiany konfiguracji dla aplikacji działających w notesu Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Podsumowanie

Istnieje wiele podstawowych ustawień konfiguracyjnych, które należy monitorować i Dostosuj, aby upewnić się, że Twoje zadania Spark uruchamiane w sposób, przewidywalnych i wydajności. Te ustawienia pomagają określić najlepsze konfiguracji klastra Spark dla konkretnego obciążeń.  Należy również monitorować wykonywania długotrwałych i/lub korzystanie z zasobów wykonania zadania Spark.  Najczęstsze wyzwania Centrum wokół wykorzystania pamięci z powodu nieprawidłowej konfiguracji (szczególnie niepoprawnie o rozmiarze z modułów wykonujących) długotrwałych operacji i zadań, które powodują kartezjańskimi operacji.

## <a name="next-steps"></a>Kolejne kroki

* [Składniki platformy Hadoop i wersje dostępne w usłudze HDInsight?](../hdinsight-component-versioning.md)
* [Zarządzanie zasobami klastra Spark w usłudze HDInsight](apache-spark-resource-manager.md)
* [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark konfiguracji](https://spark.apache.org/docs/latest/configuration.html)
* [Systemem Spark YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
