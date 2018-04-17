---
title: Przesyłanie strumieniowe ze strukturą w systemie Apache Spark za pomocą platformy Kafka — Azure HDInsight | Microsoft Docs
description: Dowiedz się, jak przekazywać dane do platformy Apache Kafka lub pobierać je z platformy Apache Kafka za pomocą przesyłania strumieniowego systemu Apache Spark (DStream). Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight

Dowiedz się, jak używać przesyłania strumieniowego ze strukturą w systemie Spark w celu odczytywania danych z platformy Apache Kafka w usłudze Azure HDInsight.

Przesyłanie strumieniowe ze strukturą platformy Spark korzysta z aparatu przetwarzania strumienia opartego na module Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych. Aby uzyskać więcej informacji o przesyłaniu strumieniowym ze strukturą, zobacz dokument [Structured Streaming Programming Guide [Alpha] (Przewodnik po programowaniu przesyłania strumieniowego ze strukturą [Alpha])](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) w witrynie Apache.org.

> [!IMPORTANT]
> W tym przykładzie użyto platformy Spark 2.2 w usłudze HDInsight 3.6.
>
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Platforma Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystkie elementy, które używają platformy Kafka, muszą znajdować się w tej samej sieci wirtualnej platformy Azure. W tym samouczku zarówno klaster Kafka, jak i klaster Spark znajdują się w tej samej sieci wirtualnej platformy Azure. 

Na poniższym diagramie przedstawiono przepływ komunikacji między platformami Spark i Kafka:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Komunikacja usługi Kafka jest ograniczona do sieci wirtualnej. Inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Dla wygody do utworzenia klastrów Kafka i Spark wewnątrz sieci wirtualnej użyto szablonu usługi Azure Resource Manager.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Szablon usługi Azure Resource Manager znajduje się tutaj: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Ten szablon umożliwia utworzenie następujących zasobów:

    * Klaster Kafka w usłudze HDInsight 3.6.
    * Klaster Spark 2.2.0 w usłudze HDInsight 3.6.
    * Sieć wirtualna platformy Azure zawierająca klastry usługi HDInsight.

    > [!IMPORTANT]
    > Używany w tym przykładzie notes do przesyłania strumieniowego ze strukturą wymaga platformy Spark w usłudze HDInsight 3.6. W przypadku użycia starszej wersji platformy Spark w usłudze HDInsight podczas korzystania z notesu wystąpią błędy.

2. Wypełnij pola w sekcji **Dostosowany szablon**, korzystając z poniższych informacji:

    | Ustawienie | Wartość |
    | --- | --- |
    | Subskrypcja | Twoja subskrypcja platformy Azure |
    | Grupa zasobów | Grupa zasobów zawierająca zasoby. |
    | Lokalizacja | Region świadczenia usługi Azure, w którym są tworzone zasoby. |
    | Nazwa klastra Spark | Nazwa klastra Spark. |
    | Nazwa klastra Kafka | Nazwa klastra Kafka. |
    | Nazwa użytkownika logowania klastra | Nazwa użytkownika będącego administratorem klastrów. |
    | Hasło logowania klastra | Hasło użytkownika będącego administratorem klastrów. |
    | Nazwa użytkownika SSH | Użytkownik SSH tworzony na potrzeby obsługi klastrów. |
    | Hasło SSH | Hasło użytkownika SSH. |
   
    ![Zrzut ekranu przedstawiający dostosowany szablon](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Na koniec zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Kup**. 

> [!NOTE]
> Tworzenie klastrów może potrwać do 20 minut.

## <a name="get-the-notebook"></a>Pobieranie notesu

Kod przykładu opisanego w tym dokumencie jest dostępny na stronie [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Przekazywanie notesów

Aby przekazać notes z projektu na platformę Spark w klastrze usługi HDInsight, wykonaj następujące kroki:

1. W przeglądarce internetowej połącz się z notesem Jupyter w klastrze Spark. Zastąp element `CLUSTERNAME` w poniższym adresie URL nazwą klastra __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

2. Za pomocą przycisku __Przekaż__ wyświetlanego w prawej górnej części strony przekaż plik __spark-structured-streaming-kafka.ipynb__ do klastra. Wybierz pozycję __Otwórz__, aby rozpocząć przekazywanie.

    ![Wybieranie i przekazywanie notesu za pomocą przycisku przekazywania](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Wybieranie pliku KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Znajdź pozycję __spark-structured-streaming-kafka.ipynb__ na liście notesów, a następnie wybierz przycisk __Przekaż__ widoczny obok tej pozycji.

    ![Aby przekazać notes, użyj przycisku przekazywania dla pozycji KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Używanie notesu

Po przekazaniu plików wybierz pozycję __spark-structured-streaming-kafka.ipynb__, aby otworzyć notes. Aby dowiedzieć się, jak używać przesyłania strumieniowego ze strukturą w systemie Spark z platformą Kafka w usłudze HDInsight, postępuj zgodnie z instrukcjami zawartymi w notesie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

> [!WARNING]
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć.
> 
> Usunięcie platformy Kafka w klastrze usługi HDInsight powoduje usunięcie wszystkich danych przechowywanych na platformie Kafka.

## <a name="next-steps"></a>Następne kroki

Gdy już wiesz, jak używać przesyłania strumieniowego ze strukturą na platformie Spark, zapoznaj się z następującymi dokumentami, aby dowiedzieć się więcej na temat pracy z platformami Spark i Kafka:

* [How to use Spark streaming (DStream) with Kafka (Jak korzystać z przesyłania strumieniowego w usłudze Spark [DStream] za pomocą usługi Kafka)](hdinsight-apache-spark-with-kafka.md).
* [Start with Jupyter Notebook and Spark on HDInsight (Rozpoczynanie korzystania z notesu Jupyter i platformy Spark w usłudze HDInsight)](spark/apache-spark-jupyter-spark-sql.md)