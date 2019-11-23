---
title: Apache Spark streaming with Apache Kafka - Azure HDInsight
description: Learn how to use Apache Spark to stream data into or out of Apache Kafka using DStreams. Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327396"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) example with Apache Kafka on HDInsight

Learn how to use [Apache Spark](https://spark.apache.org/) to stream data into or out of [Apache Kafka](https://kafka.apache.org/) on HDInsight using [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). This example uses a [Jupyter Notebook](https://jupyter.org/) that runs on the Spark cluster.

> [!NOTE]  
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

> [!IMPORTANT]  
> This example uses DStreams, which is an older Spark streaming technology. For an example that uses newer Spark streaming features, see the [Spark Structured Streaming with Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) document.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Apache Kafka on HDInsight doesn't provide access to the Kafka brokers over the public internet. Anything that talks to Kafka must be in the same Azure virtual network as the nodes in the Kafka cluster. For this example, both the Kafka and Spark clusters are located in an Azure virtual network. The following diagram shows how communication flows between the clusters:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Though Kafka itself is limited to communication within the virtual network, other services on the cluster such as SSH and Ambari can be accessed over the internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

While you can create an Azure virtual network, Kafka, and Spark clusters manually, it's easier to use an Azure Resource Manager template. Use the following steps to deploy an Azure virtual network, Kafka, and Spark clusters to your Azure subscription.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia utworzenie klastra Kafka zawierającego trzy węzły procesu roboczego.

    This template creates an HDInsight 3.6 cluster for both Kafka and Spark.

1. Use the following information to populate the entries on the **Custom deployment** section:

    |Właściwość |Wartość |
    |---|---|
    |Grupa zasobów|Create a group or select an existing one.|
    |Lokalizacja|Select a location geographically close to you.|
    |Base Cluster Name|This value is used as the base name for the Spark and Kafka clusters. For example, entering **hdistreaming** creates a Spark cluster named __spark-hdistreaming__ and a Kafka cluster named **kafka-hdistreaming**.|
    |Nazwa użytkownika logowania klastra|The admin user name for the Spark and Kafka clusters.|
    |Hasło logowania klastra|The admin user password for the Spark and Kafka clusters.|
    |Nazwa użytkownika SSH|The SSH user to create for the Spark and Kafka clusters.|
    |Hasło SSH|The password for the SSH user for the Spark and Kafka clusters.|

    ![HDInsight custom deployment parameters](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

1. Finally, select **Purchase**. It takes about 20 minutes to create the clusters.

Once the resources have been created, a summary page appears.

![Resource group summary for the vnet and clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Notice that the names of the HDInsight clusters are **spark-BASENAME** and **kafka-BASENAME**, where BASENAME is the name you provided to the template. You use these names in later steps when connecting to the clusters.

## <a name="use-the-notebooks"></a>Use the notebooks

Kod przykładu opisanego w tym dokumencie jest dostępny na stronie [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Since the steps in this document create both clusters in the same Azure resource group, you can delete the resource group in the Azure portal. Deleting the group removes all resources created by following this document, the Azure Virtual Network, and storage account used by the clusters.

## <a name="next-steps"></a>Następne kroki

In this example, you learned how to use Spark to read and write to Kafka. Use the following links to discover other ways to work with Kafka:

* [Get started with Apache Kafka on HDInsight](kafka/apache-kafka-get-started.md)
* [Use MirrorMaker to create a replica of Apache Kafka on HDInsight](kafka/apache-kafka-mirroring.md)
* [Use Apache Storm with Apache Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)
