---
title: Usługa log Analytics dla platformy Apache Kafka — usługa Azure HDInsight
description: Dowiedz się, jak używać usługi Log Analytics do analizowania dzienników z klastra Apache Kafka w usłudze Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: d07c37803c94d7fa39d398a97796fecbad373af4
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047830"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analizowanie dzienników dla platformy Apache Kafka w HDInsight

Dowiedz się, jak używać usługi Log Analytics do analizowania dzienników generowanych przez platformy Apache Kafka w HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Włączanie analizy dzienników dla platformy Kafka

Procedurę włączania usługi Log Analytics for HDInsight są takie same dla wszystkich klastrów HDInsight. Aby zrozumieć, jak utworzyć i skonfigurować wymagane usługi, należy użyć następujących łączy:

1. Utwórz obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z obszarem roboczym usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics) dokumentu.

2. Utwórz platformy Kafka w klastrze HDInsight. Aby uzyskać więcej informacji, zobacz [rozpoczynać platformy Apache Kafka w HDInsight](apache-kafka-get-started.md) dokumentu.

3. Konfigurowanie klastra platformy Kafka usługa Log Analytics. Aby uzyskać więcej informacji, zobacz [za pomocą usługi Log Analytics do monitorowania HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

    > [!NOTE]
    > Możesz również skonfigurować używanie programu Log Analytics przy użyciu klastra `Enable-AzureRmHDInsightOperationsManagementSuite` polecenia cmdlet. To polecenie cmdlet wymaga następujących informacji:
    >
    > * Nazwa klastra HDInsight.
    > * Identyfikator obszaru roboczego usługi Log Analytics. Identyfikator obszaru roboczego można znaleźć w obszarze roboczym usługi Log Analytics.
    > * Klucz podstawowy połączenia usługi Log Analytics. Aby znaleźć podstawowego klucza, Otwórz obszar roboczy w witrynie Azure portal, wybierz __Zaawansowane ustawienia__ menu po lewej stronie. Wybierz z ustawień zaawansowanych __połączone źródła__>__serwerów z systemem Linux__.


> [!IMPORTANT]
> Może upłynąć około 20 minut, zanim dane są dostępne dla usługi Log Analytics.

## <a name="query-logs"></a>Dzienniki zapytań

1. Z [witryny Azure portal](https://portal.azure.com), wybierz swój obszar roboczy usługi Log Analytics.

2. Wybierz __wyszukiwania w dzienniku__. W tym miejscu można wyszukiwać dane zebrane z platformy Kafka. Poniżej przedstawiono niektóre przykładowe wyszukiwania:

    * Użycie dysku: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Użycie procesora CPU: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Komunikaty przychodzące na sekundę: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Przychodzące bajty na sekundę: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Wychodzące bajty na sekundę: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > Zastąp wartości zapytania konkretnych informacji klastra. Na przykład `ClusterName_s` należy ustawić na nazwę klastra. `HostName_s` należy określić nazwę domeny z węzłem procesu roboczego w klastrze.

    Możesz też wprowadzić `*` aby wyszukać wszystkie typy rejestrowanych. Obecnie dostępne dla zapytań są następujące dzienniki:

    | Typ dziennika | Opis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server.log brokera platformy Kafka |
    | Dziennik\_kafkacontroller\_CL | Controller.log brokera platformy Kafka |
    | metryki\_kafka\_CL | Metryki JMX z platformy Kafka |

    ![Obraz wyszukiwania użycia procesora CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Log Analytics, zobacz [Rozpoczynanie pracy z obszarem roboczym usługi Log Analytics](../../log-analytics/log-analytics-get-started.md) dokumentu.

Aby uzyskać więcej informacji na temat pracy z platformą Kafka zobacz następujące dokumenty:

 * [Kafka dublowanie między klastrami HDInsight](apache-kafka-mirroring.md)
 * [Zwiększ skalowalność platformy Kafka w HDInsight](apache-kafka-scalability.md)
 * [Za pomocą platformy Spark z platformą Kafka przesyłania strumieniowego (DStreams)](../hdinsight-apache-spark-with-kafka.md)
 * [Korzystanie z platformy Spark structured streaming z platformą Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
