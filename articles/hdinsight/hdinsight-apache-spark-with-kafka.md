---
title: Apache Spark przesyłania strumieniowego z Kafka - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak służące do lub wychodzący przy użyciu DStreams Kafka Apache Spark Apache Spark do transmisji danych. W tym przykładzie można przesyłać strumieniowo dane za pomocą notesu Jupyter z platformy Spark w usłudze HDInsight.
keywords: przykład kafka, dozorcy kafka, spark, przesyłanie strumieniowe kafka, przesyłania strumieniowego przykład kafka spark
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: a9463b5983b5f41683a5cfe416ca125bf2810062
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Apache Spark przesyłania strumieniowego (DStream) przykład: Kafka w usłudze HDInsight

Dowiedz się, jak używać Spark Apache Spark do transmisji danych do lub wychodzący Kafka Apache na HDInsight przy użyciu DStreams. W tym przykładzie użyto notesu Jupyter w klastrze Spark.

> [!NOTE]
> Kroki opisane w tym dokumencie Tworzenie grupy zasobów platformy Azure, który zawiera zarówno Spark w usłudze HDInsight i Kafka w klastrze usługi HDInsight. Te klastry są oba znajdujących się w sieci wirtualnej platformy Azure, dzięki czemu klastra Spark do bezpośredniego komunikowania się z Kafka klastra.
>
> Po zakończeniu kroków w tym dokumencie, pamiętaj, aby usuwać te klastry, aby zapobiec zmianom nadmiarowe.

> [!IMPORTANT]
> W tym przykładzie użyto DStreams, która jest starsza technologii przesyłania strumieniowego Spark. Aby uzyskać przykład używa nowszej Spark przesyłania strumieniowego funkcji, zobacz [Spark strukturalnych przesyłania strumieniowego z Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentu.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Kafka Apache na HDInsight nie zapewnia dostępu do brokerzy Kafka za pośrednictwem publicznej sieci internet. Wszystkie elementy, które komunikuje się Kafka musi być w tej samej sieci wirtualnej platformy Azure jako węzły w klastrze Kafka. W tym przykładzie zarówno Kafka, jak i Spark klastrach znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono sposób komunikacji przepływa między klastrami:

![Diagram klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Chociaż Kafka sam jest ograniczone do komunikacji w sieci wirtualnej, są dostępne inne usługi w klastrze, takich jak SSH i Ambari w Internecie. Aby uzyskać więcej informacji na portach publicznego dostępne z usługą HDInsight, zobacz [portów i identyfikatory URI używany przez HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Podczas tworzenia sieci wirtualnej platformy Azure, Kafka, i Spark klastrów ręcznie, łatwiej jest użyć szablonu usługi Azure Resource Manager. Wykonaj następujące kroki, aby wdrożyć sieci wirtualnej platformy Azure, Kafka i Spark klastrów do subskrypcji platformy Azure.

1. Poniższy przycisk umożliwia logowanie do platformy Azure i otwórz szablon w portalu Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Szablon usługi Azure Resource Manager znajduje się pod adresem **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia tworzenie klastra Kafka, który zawiera trzy węzłów procesu roboczego.

    Ten szablon umożliwia tworzenie klastra usługi HDInsight 3,6 Kafka i Spark.

2. Użyj poniższych informacji, aby wypełnić wpisy na **wdrożenie niestandardowe** sekcji:
   
    ![Niestandardowe wdrożenie usługi HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupa zasobów**: Utwórz grupę lub wybierz istniejący. Ta grupa zawiera klastra usługi HDInsight.

    * **Lokalizacja**: Wybierz lokalizację lokalizacji geograficznej blisko.

    * **Podstawowa nazwa klastra**: Ta wartość jest używana jako nazwa podstawowa platformy Spark i Kafka klastrów. Na przykład wprowadzenie **hdi** tworzy klaster Spark o nazwie __spark hdi__ i Kafka klastra o nazwie **kafka hdi**.

    * **Nazwa użytkownika logowania klastra**: nazwa użytkownika administratora klastrów platformy Spark i Kafka.

    * **Hasło logowania klastra**: hasła użytkownika administratora klastrów platformy Spark i Kafka.

    * **Nazwa użytkownika SSH**: użytkownika SSH do tworzenia klastrów platformy Spark i Kafka.

    * **Hasło SSH**: hasło dla użytkownika SSH dla klastrów Spark i Kafka.

3. Odczyt **warunków i postanowień**, a następnie wybierz **akceptuję warunki i postanowienia, o których wspomniano**.

4. Ponadto sprawdź **Przypnij do pulpitu nawigacyjnego** , a następnie wybierz **zakupu**. Tworzenie klastrów trwa około 20 minut.

Po utworzeniu zasobów zostanie wyświetlona strona podsumowania.

![Grupa zasobów podsumowania dla sieci wirtualnej i klastrów](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Należy zauważyć, że nazwy klastrów usługi HDInsight są **nazwę BAZOWĄ spark** i **nazwę BAZOWĄ kafka**, gdzie nazwę BAZOWĄ jest podana nazwa do szablonu. Te nazwy można użyć w kolejnych krokach, podczas nawiązywania połączenia z klastrów.

## <a name="use-the-notebooks"></a>Użyj notebooki

Kod, na przykład opisanych w niniejszym dokumencie jest dostępny na [ https://github.com/Azure-Samples/hdinsight-spark-scala-kafka ](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Aby ukończyć w tym przykładzie, postępuj zgodnie z instrukcjami `README.md`.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ponieważ kroki opisane w tym dokumencie utworzyć obu klastrów w tej samej grupy zasobów platformy Azure, można usunąć grupy zasobów w portalu Azure. Usuwanie grupy usuwa wszystkie zasoby utworzone przez tego dokumentu, Azure Virtual Network i konto magazynu używane przez klaster.

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie przedstawiono sposób użycia platformy Spark do odczytu i zapisu do Kafka. Aby dowiedzieć się, inne metody pracy z Kafka, użyj następujących łączy:

* [Wprowadzenie do Apache Kafka w usłudze HDInsight](kafka/apache-kafka-get-started.md)
* [Tworzenie repliki platformy Kafka w usłudze HDInsight przy użyciu narzędzia MirrorMaker](kafka/apache-kafka-mirroring.md)
* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](hdinsight-apache-storm-with-kafka.md)

