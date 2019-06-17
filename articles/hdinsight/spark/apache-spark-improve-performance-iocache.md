---
title: Zwiększa wydajność obciążeń platformy Apache Spark przy użyciu usługi Azure HDInsight we/wy Cache (wersja zapoznawcza)
description: Więcej informacji na temat usługi Azure HDInsight we/wy w pamięci podręcznej i jak z niej korzystać w celu zwiększenia wydajności platformy Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: b77e7e9d5a68439e7f336ecb26e91031d80a7606
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695202"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache-preview"></a>Zwiększa wydajność obciążeń platformy Apache Spark przy użyciu usługi Azure HDInsight we/wy Cache (wersja zapoznawcza)

We/Wy pamięci podręcznej jest usługa buforowania danych dla usługi Azure HDInsight, które poprawia wydajność zadań platformy Apache Spark. We/Wy pamięci podręcznej działa także w przypadku [Apache TEZ](https://tez.apache.org/) i [Apache Hive](https://hive.apache.org/) obciążeń, które mogą być uruchamiane na [platformy Apache Spark](https://spark.apache.org/) klastrów. Składnik pamięci podręcznej typu open source o nazwie RubiX korzysta z operacji We/Wy pamięci podręcznej. RubiX jest lokalny dysk pamięci podręcznej do użytku z programem aparaty analizy danych big data, uzyskujących dostęp do danych z systemów magazynów w chmurze. RubiX jest unikatowa wśród systemów, buforowanie, ponieważ używa ona Solid-State dyski (SSD) zamiast rezerwa pamięci operacyjnej na potrzeby buforowania. Usługa pamięć podręczna we/wy uruchamia i zarządza serwerami metadanych RubiX w każdym węźle procesu roboczego klastra. Konfiguruje również wszystkie usługi klastra do użytku przejrzystych RubiX pamięci podręcznej.

Większość dyski SSD zapewniają więcej niż 1 GB na sekundę przepustowości. Przepustowość, uzupełnione pamięć podręczna plików w pamięci systemu operacyjnego, zawiera wystarczającą przepustowość do ładowania danych big data aparatami przetwarzania obliczeniowych, takich jak Apache Spark. Operacyjne pamięć pozostanie dostępna dla platformy Apache Spark do przetwarzania zadań silnie zależne od pamięci, takich jak przesuwa. Wyłącznego użytku systemu operacyjnego pamięci posiadające umożliwia platformy Apache Spark w celu osiągnięcia optymalnego rozmieszczenia zasobów użycia.  

>[!Note]  
>We/Wy pamięci podręcznej aktualnie używa RubiX jako składnik pamięci podręcznej, ale może to ulec zmianie w przyszłych wersjach usługi. Użyj interfejsów We/Wy pamięci podręcznej i nie wykona jakieś zależności, bezpośrednio od implementacji RubiX.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Zalety usługi Azure HDInsight we/wy w pamięci podręcznej

Za pomocą operacji We/Wy pamięci podręcznej zapewnia zwiększenie wydajności dla zadań, które odczytują dane z usługi Azure Blob Storage.

Nie trzeba wprowadzać zmian w swojej zadań platformy Spark, aby zobaczyć zwiększa wydajność, korzystając z operacji We/Wy pamięci podręcznej. Podczas operacji We/Wy pamięć podręczna jest wyłączona, ten kod Spark wczytane zdalnie dane z usługi Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Po aktywowaniu We/Wy pamięci podręcznej, ten sam wiersz kodu powoduje, że w pamięci podręcznej odczytu za pośrednictwem operacji We/Wy pamięci podręcznej. W przypadku następujących operacji odczytu dane są odczytywane lokalnie z dysków SSD. Węzły procesu roboczego w klastrze HDInsight są wyposażone w dyski SSD podłączonych lokalnie, dedykowanej. HDInsight we/wy w pamięci podręcznej wykorzystuje do buforowania, te lokalne dyski SSD, który zapewnia najniższy poziom opóźnienia i zmaksymalizować przepustowość.

## <a name="getting-started"></a>Wprowadzenie

Usługa Azure HDInsight we/wy w pamięci podręcznej jest dezaktywowany domyślnie w wersji zapoznawczej. We/Wy pamięci podręcznej jest dostępna w klastrach Spark usługi Azure HDInsight 3.6 + uruchamianych Apache Spark 2.3.  Aby aktywować We/Wy pamięci podręcznej, wykonaj następujące czynności:

1. Wybierz klaster usługi HDInsight w [witryny Azure portal](https://portal.azure.com).

1. W **Przegląd** strony (domyślnie otwierany po wybraniu klastra) wybierz **głównej Ambari** w obszarze **pulpity nawigacyjne klastra**.

1. Wybierz **We/Wy pamięci podręcznej** usługi po lewej stronie.

1. Wybierz **akcje** i **aktywować**.

    ![Włączanie usługi We/Wy pamięci podręcznej w Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "włączyć usługę We/Wy pamięci podręcznej w Ambari")

1. Potwierdź ponowne uruchomienie wszystkich odpowiednich usług w klastrze.

>[!NOTE]  
> Mimo, że pasek postępu pokazuje aktywowane, we/wy pamięci podręcznej faktycznie nie jest włączone, dopiero po ponownym uruchomieniu dotyczy innych usług.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
  
Mogą wystąpić błędy miejsca na dysku, uruchamia wszystkie zadania Spark po włączeniu operacji We/Wy pamięci podręcznej. Te błędy, ponieważ Spark używa również magazynu lokalnego dysku do przechowywania danych podczas zmiana kolejności operacji. Platforma Spark może zabraknąć miejsca na dysku SSD, po operacji We/Wy pamięć podręczna jest włączona i miejsca do magazynowania platformy Spark jest mniejsze. Ilość miejsca używanego przez domyślne we/wy pamięci podręcznej do połowy całkowita miejsca na dysku SSD. Użycie miejsca na dysku dla operacji We/Wy pamięci podręcznej jest konfigurowane w Ambari. Jeśli występują błędy miejsca na dysku, należy zmniejszyć ilość miejsca na dysku SSD, używanych przez pamięć podręczną we/wy i uruchom ponownie usługę. Aby zmienić ustawienie dla operacji We/Wy pamięci podręcznej miejsca, wykonaj następujące czynności:

1. Apache Ambari wybierz **systemu plików HDFS** usługi po lewej stronie.

1. Wybierz **Configs** i **zaawansowane** karty.

    ![Edytuj Zaawansowana konfiguracja systemu plików HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "edycji systemu plików HDFS Zaawansowana konfiguracja")

1. Przewiń w dół, a następnie rozwiń węzeł **lokacji podstawowej niestandardowe** obszaru.

1. Odszukaj właściwość **hadoop.cache.data.fullness.percentage**.

1. Zmień wartość w polu.

    ![Edytuj We/Wy pamięci podręcznej stopnia ukończenia procent](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Edytuj We/Wy pamięci podręcznej stopnia ukończenia procent")

1. Wybierz **Zapisz** w prawym górnym rogu.

1. Wybierz **ponowne uruchomienie** > **ponownego uruchomienia, wszystkie objęte**.

    ![Uruchom ponownie wszystkie objęte](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "ponownego uruchomienia, wszystkie objęte")

1. Wybierz **Potwierdź ponowne uruchomienie wszystkich**.

Jeśli to nie zadziała, wyłącz We/Wy pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat operacji We/Wy pamięci podręcznej, w tym testy wydajności, w tym wpisie w blogu: [Platforma Apache Spark zadania uzyskanie do 9 x przyspieszenie z pamięcią podręczną we/wy HDInsight](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
