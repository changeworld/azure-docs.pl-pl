---
title: Apache Spark przesyła strumieniowo z Apache Kafka — Azure HDInsight
description: Dowiedz się, jak używać platformy Apache Spark do przesyłania strumieniowego danych do lub z platformy Apache Kafka przy użyciu strumieni DStreams. Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327396"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) przykład z Apache Kafka na HDInsight

Dowiedz się, jak używać [platformy Apache Spark](https://spark.apache.org/) do strumieniowego przesyłania danych do lub z [programu Apache Kafka](https://kafka.apache.org/) w programie HDInsight za pomocą [strumieni DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). W tym przykładzie użyto [notesu Jupyter,](https://jupyter.org/) który działa w klastrze platformy Spark.

> [!NOTE]  
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

> [!IMPORTANT]  
> W tym przykładzie użyto DStreams, który jest starszą technologią przesyłania strumieniowego Platformy Spark. Na przykład, który używa nowszych funkcji przesyłania strumieniowego platformy Spark, zobacz [spark Structured Streaming z Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentu.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Apache Kafka na HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystko, co mówi do platformy Kafka musi znajdować się w tej samej sieci wirtualnej platformy Azure co węzły w klastrze platformy Kafka. W tym przykładzie klastry platformy Kafka i Spark znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie pokazano, jak przepływy komunikacji między klastrami:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Chociaż sama platforma Kafka jest ograniczona do komunikacji w sieci wirtualnej, inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Chociaż można ręcznie utworzyć sieć wirtualną platformy Azure, kafka i klastry platformy Spark, łatwiej jest użyć szablonu usługi Azure Resource Manager. Poniższe kroki można wykonać, aby wdrożyć klastry wirtualne platformy Azure, platformy Kafka i platformy Spark w ramach subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon usługi Azure Resource **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**Manager znajduje się pod adresem .

    > [!WARNING]  
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia utworzenie klastra Kafka zawierającego trzy węzły procesu roboczego.

    Ten szablon tworzy klaster HDInsight 3.6 dla platformy Kafka i Spark.

1. Aby wypełnić wpisy w sekcji **Wdrażania niestandardowego,** użyj następujących informacji:

    |Właściwość |Wartość |
    |---|---|
    |Grupa zasobów|Utwórz grupę lub wybierz istniejącą.|
    |Lokalizacja|Wybierz lokalizację geograficznie blisko ciebie.|
    |Podstawowa nazwa klastra|Ta wartość jest używana jako nazwa podstawowa klastrów Platformy Spark i Kafka. Na przykład wprowadzenie **hdistreaming** tworzy klaster Spark o nazwie __spark-hdistreaming__ i kafka klastra o nazwie **kafka-hdistreaming**.|
    |Nazwa użytkownika logowania klastra|Nazwa użytkownika administratora klastrów Platformy Spark i Kafka.|
    |Hasło logowania klastra|Hasło użytkownika administratora klastrów Platformy Spark i Kafka.|
    |Nazwa użytkownika SSH|Użytkownik SSH do tworzenia dla klastrów Platformy Spark i Kafka.|
    |Hasło SSH|Hasło użytkownika SSH dla klastrów Platformy Spark i Kafka.|

    ![Parametry wdrożenia niestandardowego usługi HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

1. Na koniec wybierz **pozycję Kup**. Utworzenie klastrów zajmuje około 20 minut.

Po utworzeniu zasobów zostanie wyświetlona strona podsumowania.

![Podsumowanie grupy zasobów dla sieci wirtualnej i klastrów](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Należy zauważyć, że nazwy klastrów HDInsight to **spark-BASENAME** i **kafka-BASENAME**, gdzie BASENAME to nazwa podana w szablonie. Nazwy te są używane w późniejszych krokach podczas łączenia się z klastrami.

## <a name="use-the-notebooks"></a>Korzystanie z notesów

Kod dla przykładu opisanego w tym [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)dokumencie jest dostępny pod adresem .

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki w tym dokumencie utworzyć oba klastry w tej samej grupie zasobów platformy Azure, można usunąć grupę zasobów w witrynie Azure portal. Usunięcie grupy usuwa wszystkie zasoby utworzone przez następujące ten dokument, sieci wirtualnej platformy Azure i konta magazynu używane przez klastry.

## <a name="next-steps"></a>Następne kroki

W tym przykładzie opisano, jak używać platformy Spark do odczytu i zapisu w platformie Kafka. Skorzystaj z następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Wprowadzenie do apache Kafka w programie HDInsight](kafka/apache-kafka-get-started.md)
* [Użyj MirrorMaker, aby utworzyć replikę Apache Kafka na HDInsight](kafka/apache-kafka-mirroring.md)
* [Użyj Apache Storm z Apache Kafka na HDInsight](hdinsight-apache-storm-with-kafka.md)
