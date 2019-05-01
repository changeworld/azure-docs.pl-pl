---
title: Apache Spark, przesyłanie strumieniowe ze strukturą z platformy Apache Kafka do usługi Azure Cosmos DB — usługa Azure HDInsight
description: Dowiedz się, jak za pomocą Apache Spark Structured Streaming odczytywać dane z platformy Apache Kafka, a następnie przechowywać ją do usługi Azure Cosmos DB. Ten przykład obejmuje strumieniowe przesyłanie danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: c2f3d882ac01427ea017d4f9b81edc3c64cc932d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728718"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use Apache Spark Structured Streaming przy użyciu platformy Apache Kafka i Azure Cosmos DB

Dowiedz się, jak używać [platformy Apache Spark](https://spark.apache.org/) [przesyłanie strumieniowe ze strukturą](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) można odczytać danych z [platformy Apache Kafka](https://kafka.apache.org/) w usłudze Azure HDInsight, a następnie magazynu danych do usługi Azure Cosmos DB.

[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie dystrybuowana, wielomodelowa baza danych. W tym przykładzie używa interfejsu API SQL model bazy danych. Aby uzyskać więcej informacji, zobacz [usługi Azure Cosmos DB — Zapraszamy](../cosmos-db/introduction.md) dokumentu.

Przesyłanie strumieniowe ze strukturą platformy Spark korzysta z aparatu przetwarzania strumienia opartego na module Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych. Aby uzyskać więcej informacji na temat przesyłanie strumieniowe ze strukturą, zobacz [Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) w serwisie Apache.org.

> [!IMPORTANT]  
> W tym przykładzie użyto platformy Spark 2.2 w HDInsight 3.6.
>
> Kroki przedstawione w tym dokumencie obejmują tworzenie grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Po zakończeniu pracy z tym dokumentem pamiętaj o usunięciu tych klastrów, aby uniknąć naliczania opłat.

## <a name="create-the-clusters"></a>Tworzenie klastrów

Platforma Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystko, co rozmawia z platformą Kafka musi być w tej samej sieci wirtualnej platformy Azure jako węzły w klastrze Kafka. W tym przykładzie klastrów Kafka i Spark znajdują się w sieci wirtualnej platformy Azure. Na poniższym diagramie przedstawiono przepływ komunikacji między klastrami:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Komunikacja usługi Kafka jest ograniczona do sieci wirtualnej. Inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Chociaż można utworzyć sieci wirtualnej platformy Azure, platformy Kafka i Spark clusters ręcznie, jest łatwiejszy w użyciu szablonu usługi Azure Resource Manager. Wykonaj następujące kroki wdrażania siecią wirtualną platformy Azure, platformy Kafka i Spark klastrów do subskrypcji platformy Azure.

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

    Szablon usługi Azure Resource Manager znajduje się w repozytorium GitHub dla tego projektu ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Ten szablon umożliwia utworzenie następujących zasobów:

   * Klaster Kafka w usłudze HDInsight 3.6.

   * Platforma Spark w klastrze HDInsight 3.6.

   * Sieć wirtualna platformy Azure zawierająca klastry usługi HDInsight.

       > [!NOTE]  
       > Przestrzeń adresów 10.0.0.0/16 korzysta z sieci wirtualnej utworzonej przez szablon.

   * Baza danych interfejsu API SQL usługi Azure Cosmos DB.

     > [!IMPORTANT]  
     > Używany w tym przykładzie notes do przesyłania strumieniowego ze strukturą wymaga platformy Spark w usłudze HDInsight 3.6. W przypadku użycia starszej wersji platformy Spark w usłudze HDInsight podczas korzystania z notesu wystąpią błędy.

2. Użyj poniższych informacji, aby wypełnić wpisy na **wdrożenie niestandardowe** sekcji:
   
    ![HDInsight wdrożenia niestandardowego](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Subskrypcja**: Wybierz swoją subskrypcję platformy Azure.
   
    * **Grupa zasobów**: Utwórz grupę lub wybierz istniejącą. Ta grupa zawiera klastra HDInsight.

    * **Lokalizacja**: Wybierz lokalizację lokalizacji geograficznej blisko.

    * **Nazwa konta usługi cosmos DB**: Ta wartość jest używana jako nazwa konta usługi Cosmos DB.

    * **Podstawowa nazwa klastra**: Ta wartość jest używana jako nazwa podstawowa w przypadku klastrów Spark i Kafka. Na przykład wprowadzenie **myhdi** tworzony klaster Spark, o nazwie __spark myhdi__ i klastra platformy Kafka o nazwie **kafka myhdi**.

    * **Wersja klastra**: Wersja klastra HDInsight.

        > [!IMPORTANT]  
        > W tym przykładzie jest testowana HDInsight 3.6 i może nie działać z innych typów klastra.

    * **Nazwa użytkownika logowania klastra**: Nazwa użytkownika administratora dla klastrów Spark i Kafka.

    * **Hasło logowania klastra**: Hasło użytkownika będącego administratorem klastrów Spark i Kafka.

    * **Nazwa użytkownika SSH**: Użytkownika SSH do tworzenia klastrów Spark i Kafka.

    * **SSH hasła**: Hasło użytkownika SSH w przypadku klastrów Spark i Kafka.

3. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

4. Na koniec wybierz pozycję **zakupu**. Tworzenie klastrów trwa około 20 minut.

> [!IMPORTANT]  
> Może potrwać do 45 minut do tworzenia klastrów, sieci wirtualnej i konta usługi Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Tworzenie bazy danych Cosmos DB i kolekcję

Projekt użyty w tym dokumencie przechowuje dane w usłudze Cosmos DB. Przed uruchomieniem kodu, należy najpierw utworzyć _bazy danych_ i _kolekcji_ w wystąpieniu usługi Cosmos DB. Należy również pobrać punktu końcowego dokumentu i _klucz_ używany do uwierzytelniania żądań do usługi Cosmos DB. 

Jednym ze sposobów, aby zrobić to jest użycie [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Poniższy skrypt utworzy bazę danych o nazwie `kafkadata` i kolekcję o nazwie `kafkacollection`. Zwraca klucz podstawowy.

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
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

Dokument punktu końcowego oraz informacje o kluczu podstawowym jest podobny do następującego tekstu:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Zapisywanie punktu końcowego i wartości klucza, odpowiednio w notesach Jupyter.

## <a name="get-the-apache-kafka-brokers"></a>Pobierz brokerów platformy Apache Kafka

Kod w tym przykładzie łączy do hostów brokera platformy Kafka w klastrze Kafka. Aby znaleźć adresy dwa hosty brokera platformy Kafka, należy użyć w poniższym przykładzie programu PowerShell lub Bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> Przykład Bash oczekuje `$CLUSTERNAME` aby zawierały nazwę klastra Kafka.
>
> W tym przykładzie użyto [jq](https://stedolan.github.io/jq/) narzędzia do analizy danych z dokumentu JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Po wyświetleniu monitu wprowadź hasło dla konta logowania (administratora) klastra

Dane wyjściowe będą podobne do następującego tekstu:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Zapisz te informacje, ponieważ jest on używany w następujących częściach niniejszego dokumentu.

## <a name="get-the-notebooks"></a>Pobierz notesów

Kod przykładu opisanego w tym dokumencie jest dostępny na stronie [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Przekazywanie notesów

Wykonaj następujące kroki, aby przekazać notesów z projektu na platformę Spark w klastrze HDInsight:

1. W przeglądarce internetowej połącz się z notesem Jupyter w klastrze Spark. Zastąp element `CLUSTERNAME` w poniższym adresie URL nazwą klastra __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

2. W prawym górnym rogu strony, użyj __przekazywanie__ przycisk, aby przekazać __Stream taksówek data do kafka.ipynb__ plików do klastra. Wybierz pozycję __Otwórz__, aby rozpocząć przekazywanie.

3. Znajdź __Stream taksówek data do kafka.ipynb__ wpisu na liście notesów, a następnie wybierz pozycję __przekazywanie__ przycisk obok niej.

4. Powtórz kroki 1 – 3, aby załadować __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ notesu.

## <a name="load-taxi-data-into-kafka"></a>Ładowanie danych taksówek do platformy Kafka

Po przekazaniu plików wybierz __Stream taksówek data do kafka.ipynb__ wpis, aby otworzyć notes. Wykonaj kroki w notesie, aby załadować dane do platformy Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Przetwarzanie danych taksówek za pomocą przesyłanie strumieniowe ze strukturą platformy Spark

Z [notesu programu Jupyter](https://jupyter.org/) strony głównej wybierz __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ wpisu. Postępuj zgodnie z instrukcjami w notesie przesyłanie strumieniowe danych z usługi Kafka i do usługi Azure Cosmos DB przy użyciu przesyłanie strumieniowe ze strukturą platformy Spark.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak używać Apache Spark Structured Streaming, zobacz następujące dokumenty, aby dowiedzieć się więcej na temat pracy z usługą Apache Spark, Apache Kafka i Azure Cosmos DB:

* [Jak używać platformy Apache Spark streaming (DStream) dzięki platformie Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Zacznij od notesu Jupyter i platformy Apache Spark w HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Witamy w usłudze Azure Cosmos DB](../cosmos-db/introduction.md)
