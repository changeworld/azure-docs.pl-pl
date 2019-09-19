---
title: Azure Monitor dzienników dla Apache Kafka — Azure HDInsight
description: Dowiedz się, jak za pomocą dzienników Azure Monitor analizować dzienniki z klastra Apache Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 5739883984d4087d2b2a1bda66c01ff3cfa10eb0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122596"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analizowanie dzienników dla Apache Kafka w usłudze HDInsight

Dowiedz się, jak za pomocą dzienników Azure Monitor analizować dzienniki wygenerowane przez Apache Kafka w usłudze HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Włącz Azure Monitor dzienniki dla Apache Kafka

Kroki umożliwiające włączenie dzienników Azure Monitor dla usługi HDInsight są takie same dla wszystkich klastrów usługi HDInsight. Skorzystaj z poniższych linków, aby dowiedzieć się, jak utworzyć i skonfigurować wymagane usługi:

1. Utwórz obszar roboczy Log Analytics. Aby uzyskać więcej informacji, zobacz [dzienniki w Azure monitor](../../azure-monitor/platform/data-platform-logs.md) dokumentu.

2. Utwórz Kafka w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz artykuł [Rozpoczynanie pracy z Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md) .

3. Skonfiguruj klaster Kafka, tak aby korzystał z dzienników Azure Monitor. Aby uzyskać więcej informacji, zobacz [dzienniki używania Azure monitor do monitorowania](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu usługi HDInsight.

> [!IMPORTANT]  
> Dostęp do danych dzienników Azure Monitor może potrwać około 20 minut.

## <a name="query-logs"></a>Dzienniki zapytań

1. Na [Azure Portal](https://portal.azure.com)wybierz obszar roboczy log Analytics.

2. W menu po lewej stronie w obszarze **Ogólne**wybierz pozycję **dzienniki**. W tym miejscu możesz wyszukiwać zebrane dane z Kafka. Wprowadź zapytanie w oknie zapytania, a następnie wybierz polecenie **Uruchom**. Oto kilka przykładowych wyszukiwań:

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
    > Zamień wartości zapytania na informacje specyficzne dla klastra. Na przykład `ClusterName_s` musi być ustawiony na nazwę klastra. `HostName_s`musi być ustawiona na nazwę domeny węzła procesu roboczego w klastrze.

    Możesz również wprowadzić `*` , aby przeszukać wszystkie typy zarejestrowane. Obecnie następujące dzienniki są dostępne dla zapytań:

    | Typ dziennika | Opis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka brokera serwera. log |
    | Dziennik\_kafkacontroller\_CL | Kafka brokera. log |
    | \_metryki\_Kafka CL | Metryki Kafka JMX |

    ![Użycie procesora CPU analizy dzienników Apache Kafka](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Monitor, zobacz [omówienie Azure monitor](../../log-analytics/log-analytics-get-started.md)i [dzienniki Azure monitor zapytań do monitorowania klastrów usługi HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Aby uzyskać więcej informacji na temat pracy z Apache Kafka, zobacz następujące dokumenty:

* [Apache Kafka dublowania między klastrami usługi HDInsight](apache-kafka-mirroring.md)
* [Zwiększenie skalowalności Apache Kafka w usłudze HDInsight](apache-kafka-scalability.md)
* [Użyj Apache Spark streaming (DStreams) z Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Używanie Apache Spark strukturalnych przesyłania strumieniowego z Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
