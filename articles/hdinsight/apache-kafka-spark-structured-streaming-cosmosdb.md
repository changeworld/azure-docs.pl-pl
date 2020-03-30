---
title: Apache Spark & Apache Kafka z usługą Cosmos DB — Usługa Azure HDInsight
description: Dowiedz się, jak używać usługi Apache Spark Structured Streaming do odczytywania danych z platformy Apache Kafka, a następnie przechowywania ich w usłudze Azure Cosmos DB. Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406171"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Korzystanie z usługi Apache Spark Structured Streaming z apache kafka i usługą Azure Cosmos DB

Dowiedz się, jak używać [usługi Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) do odczytywania danych z platformy [Apache Kafka](https://kafka.apache.org/) w usłudze Azure HDInsight, a następnie przechowywania danych w usłudze Azure Cosmos DB.

[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie rozproszona baza danych z wieloma modelami. W tym przykładzie użyto modelu bazy danych interfejsu API SQL. Aby uzyskać więcej informacji, zobacz dokument [Powitalny w usłudze Azure Cosmos DB.](../cosmos-db/introduction.md)

Przesyłanie strumieniowe ze strukturą platformy Spark korzysta z aparatu przetwarzania strumienia opartego na module Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych. Aby uzyskać więcej informacji na temat strumieniowania strukturalnego, zobacz [Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) w Apache.org.

> [!IMPORTANT]  
> W tym przykładzie użyto platformy Spark 2.2 w programie HDInsight 3.6.
>
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Apache Kafka na HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystko, co mówi do platformy Kafka musi znajdować się w tej samej sieci wirtualnej platformy Azure co węzły w klastrze platformy Kafka. W tym przykładzie klastry platformy Kafka i Spark znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie pokazano, jak przepływy komunikacji między klastrami:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Komunikacja usługi Kafka jest ograniczona do sieci wirtualnej. Inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Chociaż można ręcznie utworzyć sieć wirtualną platformy Azure, kafka i klastry platformy Spark, łatwiej jest użyć szablonu usługi Azure Resource Manager. Poniższe kroki można wykonać, aby wdrożyć klastry wirtualne platformy Azure, platformy Kafka i platformy Spark w ramach subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Szablon usługi Azure Resource Manager znajduje się w repozytorium[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)GitHub dla tego projektu ( ).

    Ten szablon umożliwia utworzenie następujących zasobów:

   * Klaster Kafka w usłudze HDInsight 3.6.

   * Iskra w klastrze HDInsight 3.6.

   * Sieć wirtualna platformy Azure zawierająca klastry usługi HDInsight. Sieć wirtualna utworzona przez szablon używa przestrzeni adresowej 10.0.0.0/16.

   * Baza danych interfejsu SQL usługi Azure Cosmos DB.

    > [!IMPORTANT]  
    > Używany w tym przykładzie notes do przesyłania strumieniowego ze strukturą wymaga platformy Spark w usłudze HDInsight 3.6. W przypadku użycia starszej wersji platformy Spark w usłudze HDInsight podczas korzystania z notesu wystąpią błędy.

1. Aby wypełnić wpisy w sekcji **Wdrażania niestandardowego,** użyj następujących informacji:

    |Właściwość |Wartość |
    |---|---|
    |Subskrypcja|Wybierz swoją subskrypcję platformy Azure.|
    |Grupa zasobów|Utwórz grupę lub wybierz istniejącą. Ta grupa zawiera klaster HDInsight.|
    |Nazwa konta usługi Cosmos DB|Ta wartość jest używana jako nazwa konta usługi Cosmos DB. Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.|
    |Podstawowa nazwa klastra|Ta wartość jest używana jako nazwa podstawowa klastrów Platformy Spark i Kafka. Na przykład wprowadzenie **myhdi** tworzy klaster Spark o nazwie __spark-myhdi__ i klaster Kafka o nazwie **kafka-myhdi**.|
    |Wersja klastra|Wersja klastra HDInsight. W tym przykładzie jest testowany z HDInsight 3.6 i może nie działać z innymi typami klastra.|
    |Nazwa użytkownika logowania klastra|Nazwa użytkownika administratora klastrów Platformy Spark i Kafka.|
    |Hasło logowania klastra|Hasło użytkownika administratora klastrów Platformy Spark i Kafka.|
    |Nazwa użytkownika Ssh|Użytkownik SSH do tworzenia dla klastrów Platformy Spark i Kafka.|
    |Hasło Ssh|Hasło użytkownika SSH dla klastrów Platformy Spark i Kafka.|

    ![Wartości wdrożenia niestandardowego usługi HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

1. Na koniec wybierz **pozycję Kup**. Utworzenie klastrów, sieci wirtualnej i konta usługi Cosmos DB może potrwać do 45 minut.

## <a name="create-the-cosmos-db-database-and-collection"></a>Tworzenie bazy danych i kolekcji usługi Cosmos DB

Projekt używany w tym dokumencie przechowuje dane w usłudze Cosmos DB. Przed uruchomieniem kodu należy najpierw utworzyć _bazę danych_ i _kolekcję_ w wystąpieniu usługi Cosmos DB. Należy również pobrać punkt końcowy dokumentu i _klucz_ używany do uwierzytelniania żądań do usługi Cosmos DB.

Jednym ze sposobów, aby to zrobić, jest użycie [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Poniższy skrypt utworzy `kafkadata` bazę danych `kafkacollection`o nazwie i kolekcję o nazwie . Następnie zwraca klucz podstawowy.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

Informacje o punkcie końcowym dokumentu i kluczu podstawowym są podobne do następujących tekstów:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Zapisz wartości punktu końcowego i klucza, ponieważ są one potrzebne w notesach jupytera.

## <a name="get-the-notebooks"></a>Pobierz notesy

Kod dla przykładu opisanego w tym [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)dokumencie jest dostępny pod adresem .

## <a name="upload-the-notebooks"></a>Przekazywanie notesów

Aby przekazać notesy z projektu do klastra Platformy Spark w programie HDInsight, należy wykonać następujące czynności:

1. W przeglądarce internetowej połącz się z notesem Jupyter w klastrze Spark. Zastąp element `CLUSTERNAME` w poniższym adresie URL nazwą klastra __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

2. W prawym górnym rogu strony użyj przycisku __Przekaż,__ aby przesłać plik __Stream-taxi-data-to-kafka.ipynb__ do klastra. Wybierz pozycję __Otwórz__, aby rozpocząć przekazywanie.

3. Znajdź wpis __Stream-taxi-data-to-kafka.ipynb__ na liście notesów i wybierz przycisk __Przekaż__ obok niego.

4. Powtórz kroki 1-3, aby załadować notes __Strumienia z kafka-do-Cosmos-DB.ipynb.__

## <a name="load-taxi-data-into-kafka"></a>Załaduj dane taksówek do Kafki

Po przesłaniu plików wybierz wpis __Stream-taxi-data-to-kafka.ipynb,__ aby otworzyć notes. Wykonaj kroki opisane w notesie, aby załadować dane do platformy Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Przetwarzanie danych taksówek przy użyciu usługi Spark Structured Streaming

Na stronie głównej [notesu Jupyter](https://jupyter.org/) wybierz wpis __Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__ Wykonaj kroki w notesie, aby przesyłać strumieniowo dane z platformy Kafka i do usługi Azure Cosmos DB przy użyciu usługi Spark Structured Streaming.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z apache Spark Structured Streaming, zobacz następujące dokumenty, aby dowiedzieć się więcej o pracy z Apache Spark, Apache Kafka i Azure Cosmos DB:

* [Jak korzystać z apache Spark streaming (DStream) z Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Zacznij od notebooka Jupyter i platformy Spark Apache na hdinsight](spark/apache-spark-jupyter-spark-sql.md)
* [Azure Cosmos DB — Zapraszamy!](../cosmos-db/introduction.md)
