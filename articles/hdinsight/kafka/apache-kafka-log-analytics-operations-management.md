---
title: Dzienniki usługi Azure Monitor dla platformy Apache Kafka — usługa Azure HDInsight
description: Dowiedz się, jak używać dzienników usługi Azure Monitor do analizowania dzienników z klastra Apache Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471184"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analyze logs for Apache Kafka on HDInsight (Analizowanie dzienników na potrzeby platformy Apache Kafka w usłudze HDInsight)

Dowiedz się, jak używać dzienników usługi Azure Monitor do analizowania dzienników generowanych przez apache kafka w programie HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Lokalizacja dzienników

Dzienniki apache Kafka w klastrze znajdują się w . `/var/log/kafka` Dzienniki platformy Kafka nie są zapisywane ani utrwalane w cyklach życia klastra, niezależnie od tego, czy używane są dyski zarządzane. W poniższej tabeli przedstawiono dostępne dzienniki.

|Log |Opis |
|---|---|
|kafka.out|i stderr procesu Kafka. W tym pliku znajdziesz dzienniki uruchamiania i zamykania platformy Kafka.|
|server.log|Główny dziennik serwera Platformy Kafka. Wszystkie dzienniki brokera Kafka trafiają tutaj.|
|controller.log|Kontroler rejestruje, jeśli broker działa jako kontroler.|
|plik statechange.log|Wszystkie zdarzenia zmiany stanu brokerów są rejestrowane w tym pliku.|
|kafka-gc.log|Statystyki wyrzucania elementów bezużytecznych platformy Kafka.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Włączanie dzienników usługi Azure Monitor dla platformy Apache Kafka

Kroki, aby włączyć dzienniki usługi Azure Monitor dla usługi HDInsight są takie same dla wszystkich klastrów HDInsight. Skorzystaj z następujących łączy, aby dowiedzieć się, jak utworzyć i skonfigurować wymagane usługi:

1. Tworzenie obszaru roboczego usługi Log Analytics. Aby uzyskać więcej informacji, zobacz dzienniki w dokumencie [usługi Azure Monitor.](../../azure-monitor/platform/data-platform-logs.md)

2. Utwórz platformę Kafka w klastrze HDInsight. Aby uzyskać więcej informacji, zobacz [Start z Apache Kafka w dokumencie HDInsight.](apache-kafka-get-started.md)

3. Skonfiguruj klaster platformy Kafka do używania dzienników usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz Użyj dzienników usługi Azure Monitor do monitorowania dokumentu [USŁUGI HDInsight.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Może upłynąć około 20 minut, zanim dane będą dostępne dla dzienników usługi Azure Monitor.

## <a name="query-logs"></a>Dzienniki zapytań

1. Z [witryny Azure portal](https://portal.azure.com)wybierz obszar roboczy usługi Log Analytics.

2. Z lewego menu w obszarze **Ogólne**wybierz pozycję **Dzienniki**. W tym miejscu można przeszukiwać dane zebrane z platformy Kafka. Wprowadź kwerendę w oknie kwerendy, a następnie wybierz polecenie **Uruchom**. Oto kilka przykładowych wyszukiwań:

* Użycie dysku:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Użycie procesora:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Wiadomości przychodzące na `your_kafka_cluster_name` sekundę: (Zamień na nazwę klastra).

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bajty przychodzące na `wn0-kafka` sekundę: (Zamień na nazwę hosta węzła procesu roboczego).

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Bajty wychodzące na `your_kafka_cluster_name` sekundę: (Zamień na nazwę klastra).

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Można również `*` wprowadzić, aby przeszukać wszystkie zarejestrowane typy. Obecnie następujące dzienniki są dostępne dla zapytań:

    | Typ dziennika | Opis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Serwer brokera platformy Kafka.log |
    | log\_kafkacontroller\_CL | Kontroler brokera platformy Kafka.log |
    | metryki\_kafka\_CL | Metryki Kafka JMX |

    ![Apache kafka log analytics użycie procesora](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Monitor, zobacz [Omówienie usługi Azure Monitor](../../log-analytics/log-analytics-get-started.md)i [dzienniki usługi Query Azure Monitor w celu monitorowania klastrów usługi HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Aby uzyskać więcej informacji na temat pracy z apache kafka, zobacz następujące dokumenty:

* [Lustrzane odbicie apache kafka między klastrami HDInsight](apache-kafka-mirroring.md)
* [Zwiększ skalę Apache Kafka na HDInsight](apache-kafka-scalability.md)
* [Użyj strumieniowania Apache Spark (DStreams) z Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Użyj apache Spark ustrukturyzowane streaming z Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
