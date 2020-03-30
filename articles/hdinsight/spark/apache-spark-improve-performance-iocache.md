---
title: Wydajność platformy Apache Spark — pamięć podręczna we/wy usługi Azure HDInsight (wersja zapoznawcza)
description: Dowiedz się więcej o pamięci podręcznej we/wy usługi Azure HDInsight i o tym, jak jej używać do zwiększania wydajności programu Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494987"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Zwiększ wydajność obciążeń Apache Spark przy użyciu pamięci podręcznej we/wy usługi Azure HDInsight

Pamięć podręczna we/wy to usługa buforowania danych dla usługi Azure HDInsight, która zwiększa wydajność zadań Platformy Apache Spark. Pamięć podręczna we/wy współpracuje również z obciążeniami [Apache TEZ](https://tez.apache.org/) i [Apache Hive,](https://hive.apache.org/) które można uruchamiać w klastrach [Platformy Apache Spark.](https://spark.apache.org/) Pamięć podręczna we/wy używa składnika buforowania typu open source o nazwie RubiX. RubiX to lokalna pamięć podręczna dysków do użytku z silnikami analizy dużych zbiorów danych, które uzyskują dostęp do danych z systemów przechowywania danych w chmurze. RubiX jest unikalny wśród systemów buforowania, ponieważ używa dysków SSD( SSD), a nie rezerwuje pamięć operacyjną do celów buforowania. Usługa Pamięci podręcznej we/wy uruchamia serwery metadanych RubiX i zarządza nimi w każdym węźle procesu roboczego klastra. Konfiguruje również wszystkie usługi klastra do przejrzystego wykorzystania pamięci podręcznej RubiX.

Większość ssd zapewnia przepustowość większą niż 1 GByte na sekundę. Ta przepustowość, uzupełniona przez pamięć podręczną plików systemu operacyjnego w pamięci podręcznej, zapewnia wystarczającą przepustowość, aby załadować aparaty przetwarzania danych big data, takie jak Apache Spark. Pamięć operacyjna jest dostępna dla platformy Apache Spark do przetwarzania zadań silnie zależnych od pamięci, takich jak przetasowania. Wyłączne wykorzystanie pamięci operacyjnej umożliwia apache spark osiągnięcie optymalnego wykorzystania zasobów.  

> [!Note]  
> Pamięć podręczna we/wy obecnie używa RubiX jako składnika buforowania, ale może się to zmienić w przyszłych wersjach usługi. Użyj interfejsów pamięci podręcznej We/Wy i nie należy przyjmować żadnych zależności bezpośrednio na implementacji RubiX.
>Pamięć podręczna we/wy jest obsługiwana tylko w usłudze Azure BLOB Storage w tej chwili.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Zalety pamięci podręcznej we/wy usługi Azure HDInsight

Za pomocą pamięci podręcznej we/wy zapewnia wzrost wydajności dla zadań, które odczytywane dane z usługi Azure Blob Storage.

Nie trzeba wprowadzać żadnych zmian w zadaniach platformy Spark, aby zobaczyć wzrost wydajności podczas korzystania z pamięci podręcznej we/wy. Gdy pamięć podręczna we/wy jest wyłączona, ten kod `spark.read.load('wasbs:///myfolder/data.parquet').count()`platformy Spark będzie odczytywany dane zdalnie z usługi Azure Blob Storage: . Po aktywowaniu pamięci podręcznej we/wy ten sam wiersz kodu powoduje, że buforowany odczyt za pośrednictwem pamięci podręcznej we/wy. Po następujących odczytach dane są odczytywane lokalnie z ssd. Węzły robocze w klastrze HDInsight są wyposażone w lokalnie podłączone, dedykowane dyski SSD. Pamięć podręczna we/wy HDInsight używa tych lokalnych dysków SSD do buforowania, co zapewnia najniższy poziom opóźnień i maksymalizuje przepustowość.

## <a name="getting-started"></a>Wprowadzenie

Pamięć podręczna we/wy usługi Azure HDInsight jest domyślnie dezaktywowana w wersji zapoznawczej. Pamięć podręczna we/wy jest dostępna w klastrach platformy Azure HDInsight 3.6+ Spark, które uruchamiają apache Spark 2.3.  Aby aktywować pamięć podręczną we/wy w programie HDInsight 4.0, wykonaj następujące czynności:

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Wybierz usługę **Pamięć podręczna we/wy** po lewej stronie.

1. Wybierz **akcje** **(Akcje usługi** w HDI 3.6) i **Aktywuj**.

    ![Włączanie usługi pamięci podręcznej we/wy w ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Włączanie usługi pamięci podręcznej we/wy w ambari")

1. Potwierdź ponowne uruchomienie wszystkich usług, których dotyczy problem w klastrze.

> [!NOTE]  
> Mimo że pasek postępu jest aktywowany, pamięć podręczna we/wy nie jest włączona, dopóki nie uruchomisz ponownie innych usług, których dotyczy problem.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
  
Po włączeniu pamięci podręcznej we/wy mogą wystąpić błędy miejsca na dysku z uruchomionymi zadaniami platformy Spark. Te błędy występują, ponieważ spark używa również magazynu dysku lokalnego do przechowywania danych podczas operacji tasowania. Po włączeniu pamięci podręcznej IO może zabraknąć miejsca na iskrę, a miejsce na magazyn Iskry zostanie zmniejszone. Ilość miejsca używanego przez pamięć podręczną we/wy domyślnie stanowi połowę całkowitego miejsca na dysku SSD. Użycie miejsca na dysku dla pamięci podręcznej we/wy można konfigurować w ambari. Jeśli masz błędy miejsca na dysku, zmniejsz ilość miejsca na dysku SSD używanego do pamięci podręcznej we/wy i uruchom ponownie usługę. Aby zmienić miejsce ustawione dla pamięci podręcznej we/wy, wykonaj następujące czynności:

1. W Apache Ambari wybierz usługę **HDFS** po lewej stronie.

1. Wybierz karty **Konfiguracje** i **Zaawansowane.**

    ![Edytowanie zaawansowanej konfiguracji plików HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Edytowanie zaawansowanej konfiguracji plików HDFS")

1. Przewiń w dół i rozwiń obszar **Niestandardowa lokacja podstawowa.**

1. Znajdź właściwość **hadoop.cache.data.fullness.percentage**.

1. Zmień wartość w polu.

    ![Edytuj procent pełności pamięci podręcznej we/wy](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Edytuj procent pełności pamięci podręcznej we/wy")

1. Wybierz **pozycję Zapisz** w prawym górnym rogu.

1. Wybierz pozycję **Uruchom ponownie** > **ponownie wszystkie, których dotyczy problem**.

    ![Apache Ambari ponownie uruchamia wszystkie](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Uruchom ponownie wszystkie, których dotyczy problem")

1. Wybierz **pozycję Potwierdź ponownie wszystkie**.

Jeśli to nie zadziała, wyłącz pamięć podręczną we/wy.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej o pamięci podręcznej IO, w tym testów porównawczych wydajności w tym blogu: [Apache Spark praca zyskać do 9x przyspieszyć z HDInsight IO Cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
