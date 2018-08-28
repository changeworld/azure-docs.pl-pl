---
title: Platforma Apache Spark streaming przy użyciu platformy Kafka — Azure HDInsight
description: Dowiedz się, jak za pomocą platformy Apache Spark platforma Spark przesyłanie strumieniowe danych do i z platformy Apache Kafka za pomocą DStreams. Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
keywords: kafka przykład dozorcy platformy kafka, spark, przesyłanie strumieniowe kafka i spark streaming przykład platformy kafka
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: jasonh
ms.openlocfilehash: d06e9d26051fbfafc4d717ec180e8760157aefd9
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094170"
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Platforma Apache Spark streaming (DStream) przykład z platformą Kafka w HDInsight

Dowiedz się, jak za pomocą platformy Apache Spark platforma Spark przesyłanie strumieniowe danych do i z platformy Apache Kafka w HDInsight przy użyciu DStreams. W tym przykładzie użyto notesu programu Jupyter, które uruchamia w klastrze Spark.

> [!NOTE]
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

> [!IMPORTANT]
> W tym przykładzie użyto DStreams, czyli starszej technologii przesyłania strumieniowego platformy Spark. Dla przykładu, który używa nowszej Spark streaming funkcji, zobacz [Spark Structured Streaming z platformą Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentu.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Platforma Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystko, co rozmawia z platformą Kafka musi być w tej samej sieci wirtualnej platformy Azure jako węzły w klastrze Kafka. W tym przykładzie klastrów Kafka i Spark znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono przepływ komunikacji między klastrami:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Chociaż platformy Kafka, sama jest ograniczony do komunikacji w obrębie sieci wirtualnej, dostęp do innych usług w klastrze, takich jak SSH i Ambari są dostępne za pośrednictwem Internetu. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Chociaż można utworzyć sieci wirtualnej platformy Azure, platformy Kafka i Spark clusters ręcznie, jest łatwiejszy w użyciu szablonu usługi Azure Resource Manager. Wykonaj następujące kroki wdrażania siecią wirtualną platformy Azure, platformy Kafka i Spark klastrów do subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia utworzenie klastra Kafka zawierającego trzy węzły procesu roboczego.

    Ten szablon umożliwia utworzenie klastra usługi HDInsight 3.6 zarówno dla platformy Kafka i Spark.

2. Użyj poniższych informacji, aby wypełnić wpisy na **wdrożenie niestandardowe** sekcji:
   
    ![HDInsight wdrożenia niestandardowego](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupa zasobów**: Utwórz grupę lub wybierz istniejącą. Ta grupa zawiera klastra HDInsight.

    * **Lokalizacja**: Wybierz lokalizację lokalizacji geograficznej blisko.

    * **Podstawowa nazwa klastra**: Ta wartość jest używana jako nazwa podstawowa dla aparatu Spark i klastry platformy Kafka. Na przykład wprowadzenie **hdi** tworzony klaster Spark, o nazwie __spark hdi__ i klastra platformy Kafka o nazwie **kafka hdi**.

    * **Nazwa użytkownika logowania klastra**: nazwa użytkownika będącego administratorem klastrów Spark i Kafka.

    * **Hasło logowania klastra**: hasło użytkownika będącego administratorem klastrów Spark i Kafka.

    * **Nazwa użytkownika SSH**: użytkownik SSH do tworzenia klastrów Spark i Kafka.

    * **Hasło SSH**: hasło użytkownika SSH w przypadku klastrów Spark i Kafka.

3. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

4. Na koniec zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Kup**. Tworzenie klastrów trwa około 20 minut.

Po utworzeniu zasobów zostanie wyświetlona strona podsumowania.

![Grupa zasobów podsumowania dla sieci wirtualnej i klastrów](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Należy zauważyć, że nazwy klastrów HDInsight są **spark BASENAME** i **kafka BASENAME**, gdzie BASENAME jest nazwa podana w szablonie. W kolejnych krokach będą używać tych nazw, łącząc się z klastrami.

## <a name="use-the-notebooks"></a>Korzystanie z notesów

Kod przykładu opisanego w tym dokumencie jest dostępny na stronie [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Aby ukończyć w tym przykładzie, wykonaj kroki opisane w `README.md`.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki opisane w tym dokumencie utworzyć obu klastrach w tej samej grupie zasobów platformy Azure, możesz usunąć grupę zasobów w witrynie Azure portal. Usunięcie grupy usuwa wszystkie zasoby utworzone w tym dokumencie, Azure Virtual Network, a także konta magazynu używanego przez klaster.

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie przedstawiono sposób użycia platformy Spark do odczytu i zapisu do platformy Kafka. Użyj następujących linków, aby poznać inne sposoby pracy z platformą Kafka:

* [Wprowadzenie do platformy Apache Kafka w HDInsight](kafka/apache-kafka-get-started.md)
* [Tworzenie repliki platformy Kafka w usłudze HDInsight przy użyciu narzędzia MirrorMaker](kafka/apache-kafka-mirroring.md)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](hdinsight-apache-storm-with-kafka.md)

