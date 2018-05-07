---
title: Dziennika analizy dla Apache Kafka — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwia analizowanie dzienników z klastra Apache Kafka na Azure HDInsight Log Analytics.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9f366631ced4392831ad9ed97898a88b3290cd22
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analizowanie dzienników dla Kafka Apache na HDInsight

Jak analizować dzienniki generowane przez Kafka Apache na HDInsight przy użyciu analizy dzienników.

## <a name="enable-log-analytics-for-kafka"></a>Włącz analizy dzienników dla Kafka

Procedurę włączania analizy dzienników dla usługi HDInsight są takie same dla wszystkich klastrów usługi HDInsight. Aby zrozumieć, jak utworzyć i skonfigurować wymagane usługi, użyj następujących łączy:

1. Tworzenie obszaru roboczego analizy dzienników. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z obszaru roboczego analizy dzienników](https://docs.microsoft.com/azure/log-analytics) dokumentu.

2. Utwórz Kafka w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz [rozpoczynać Kafka Apache na HDInsight](apache-kafka-get-started.md) dokumentu.

3. Konfigurowanie klastra Kafka w analizy dzienników. Aby uzyskać więcej informacji, zobacz [analizy dzienników używany do monitorowania usługi HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

    > [!NOTE]
    > Można również skonfigurować klaster, aby użyć analizy dzienników przy użyciu `Enable-AzureRmHDInsightOperationsManagementSuite` polecenia cmdlet. To polecenie cmdlet wymaga następujących informacji:
    >
    > * Nazwa klastra usługi HDInsight.
    > * Identyfikator obszaru roboczego analizy dzienników. Identyfikator obszaru roboczego można znaleźć w obszarze roboczym analizy dzienników.
    > * Klucz podstawowy połączenia analizy dzienników. Aby znaleźć klucz podstawowy, wybierz wystąpienie analizy dzienników, a następnie __portalu OMS__. Wybierz z portalu OMS __ustawienia__, __połączonych źródeł__, a następnie __serwerów z systemem Linux__.


> [!IMPORTANT]
> Może potrwać około 20 minut, zanim dane są dostępne dla analizy dzienników.

## <a name="query-logs"></a>Dzienniki zapytania

1. Z [portalu Azure](https://portal.azure.com), wybierz obszar roboczy analizy dzienników.

2. Wybierz __dziennika wyszukiwania__. W tym miejscu można wyszukiwać dane zbierane z Kafka. Poniżej przedstawiono niektóre przykładowe wyszukiwania:

    * Użycie dysku: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Użycie procesora CPU: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Wiadomości przychodzących na sekundę: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Przychodzące bajty na sekundę: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Wychodzące bajty na sekundę: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > Zastąp wartości zapytania Twoje informacje dotyczące klastra. Na przykład `ClusterName_s` należy ustawić nazwę klastra. `HostName_s` należy ustawić nazwę domeny z węzłem procesu roboczego w klastrze.

    Możesz też wprowadzić `*` aby wyszukać wszystkie typy rejestrowanych. Obecnie dostępne dla zapytań są następujące dzienniki:

    | Typ dziennika | Opis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server.log brokera Kafka |
    | Dziennik\_kafkacontroller\_CL | Controller.log brokera Kafka |
    | metryki\_kafka\_CL | Metryki Kafka JMX |

    ![Obraz wyszukiwania użycia procesora CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących analizy dzienników, zobacz [Rozpoczynanie pracy z obszaru roboczego analizy dzienników](../../log-analytics/log-analytics-get-started.md) dokumentu.

Aby uzyskać więcej informacji na temat pracy z Kafka można znaleźć w następujących dokumentach:

 * [Dublowany Kafka między klastrami usługi HDInsight](apache-kafka-mirroring.md)
 * [Zwiększenie skalowalności Kafka w usłudze HDInsight](apache-kafka-scalability.md)
 * [Użyj przesyłania strumieniowego (DStreams) z Kafka Spark](../hdinsight-apache-spark-with-kafka.md)
 * [Korzystanie z platformy Spark strukturę przesyłania strumieniowego z Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
