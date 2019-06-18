---
title: Dzienniki monitora platformy Azure dla platformy Apache Kafka — Azure HDInsight
description: Dowiedz się, jak analizować dzienniki z klastra platformy Apache Kafka w usłudze Azure HDInsight przy użyciu dzienników usługi Azure Monitor.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 1e141aea3b22bfdcb981513f03e595b6c2f15466
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147983"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analizowanie dzienników dla platformy Apache Kafka w HDInsight

Dowiedz się, jak analizować dzienniki generowane przez platformy Apache Kafka w HDInsight przy użyciu dzienników usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Włączanie dzienników usługi Azure Monitor dla platformy Apache Kafka

Kroki, aby włączyć dzienniki usługi Azure Monitor for HDInsight są takie same dla wszystkich klastrów HDInsight. Aby zrozumieć, jak utworzyć i skonfigurować wymagane usługi, należy użyć następujących łączy:

1. Utwórz obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [dzienników w usłudze Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) dokumentu.

2. Utwórz platformy Kafka w klastrze HDInsight. Aby uzyskać więcej informacji, zobacz [rozpoczynać platformy Apache Kafka w HDInsight](apache-kafka-get-started.md) dokumentu.

3. Skonfiguruj klaster platformy Kafka przy użyciu dzienników usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Monitor dzienników do monitorowania HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

> [!IMPORTANT]  
> Może upłynąć około 20 minut, zanim dane są dostępne w przypadku dzienników usługi Azure Monitor.

## <a name="query-logs"></a>Dzienniki zapytań

1. Z [witryny Azure portal](https://portal.azure.com), wybierz swój obszar roboczy usługi Log Analytics.

2. W menu po lewej stronie w obszarze **ogólne**, wybierz opcję **dzienniki**. W tym miejscu można wyszukiwać dane zebrane z platformy Kafka. W oknie zapytania wprowadź kwerendę, a następnie wybierz pozycję **Uruchom**. Poniżej przedstawiono niektóre przykładowe wyszukiwania:

* Użycie dysku:

    ```kusto
    Perf 
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Użycie procesora CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Komunikaty przychodzące na sekundę:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Przychodzące bajty na sekundę:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Wychodzące bajty na sekundę:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > Zastąp wartości zapytania konkretnych informacji klastra. Na przykład `ClusterName_s` należy ustawić na nazwę klastra. `HostName_s` należy określić nazwę domeny z węzłem procesu roboczego w klastrze.
    
    Możesz też wprowadzić `*` aby wyszukać wszystkie typy rejestrowanych. Obecnie dostępne dla zapytań są następujące dzienniki:
    
    | Typ dziennika | Opis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server.log brokera platformy Kafka |
    | log\_kafkacontroller\_CL | Controller.log brokera platformy Kafka |
    | metrics\_kafka\_CL | Metryki JMX z platformy Kafka |
    
    ![Obraz wyszukiwania użycia procesora CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Monitor, zobacz [Omówienie usługi Azure Monitor](../../log-analytics/log-analytics-get-started.md), i [zapytań usługi Azure Monitor dzienników do monitorowania klastrów HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Aby uzyskać więcej informacji na temat pracy z platformą Apache Kafka zobacz następujące dokumenty:

* [Platforma Apache Kafka dublowanie między klastrami HDInsight](apache-kafka-mirroring.md)
* [Zwiększ skalowalność platformy Apache Kafka w HDInsight](apache-kafka-scalability.md)
* [Use Apache Spark streaming (DStreams) dzięki platformie Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Korzystanie z platformy Apache Spark structured streaming dzięki platformie Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
