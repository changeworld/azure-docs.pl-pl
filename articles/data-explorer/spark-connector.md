---
title: Użyj łącznika usługi Azure Eksplorator danych, aby uzyskać Apache Spark przenoszenia danych między Eksplorator danych platformy Azure i klastrami platformy Spark.
description: W tym temacie pokazano, jak przenosić dane między usługą Azure Eksplorator danych i klastrami Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208613"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Łącznik usługi Azure Eksplorator danych dla Apache Spark

[Apache Spark](https://spark.apache.org/) to ujednolicony aparat analityczny do przetwarzania danych na dużą skalę. Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym w przypadku dużych ilości danych. 

Łącznik usługi Azure Eksplorator danych dla platformy Spark to [projekt Open Source](https://github.com/Azure/azure-kusto-spark) , który można uruchomić w dowolnym klastrze Spark. Implementuje źródło danych i ujścia danych na potrzeby przesyłania danych w ramach klastrów Azure Eksplorator danych i Spark. Korzystając z usługi Azure Eksplorator danych i Apache Spark, możesz tworzyć szybkie i skalowalne aplikacje ukierunkowane na scenariusze oparte na danych. Na przykład Uczenie maszynowe (ML), extract-transform-load (ETL) i Log Analytics. Za pomocą łącznika platforma Azure Eksplorator danych stać się prawidłowym magazynem danych dla standardowych operacji źródłowych i ujścia platformy Spark, takich jak Write, Read i writeStream.

Możesz zapisywać na platformie Azure Eksplorator danych w trybie wsadowym lub w ramach przesyłania strumieniowego. Odczyt z usługi Azure Eksplorator danych obsługuje funkcję oczyszczania kolumn i przekazywanie predykatów, które filtrują dane na platformie Azure Eksplorator danych, zmniejszając ilość transferowanych danych.

W tym temacie opisano sposób instalowania i konfigurowania łącznika usługi Azure Eksplorator danych Spark oraz przenoszenia danych między klastrami Eksplorator danych i Apache Spark platformy Azure.

> [!NOTE]
> Mimo że niektóre z poniższych przykładów odnoszą się do klastra platformy Spark [Azure Databricks](https://docs.azuredatabricks.net/) , łącznik usługi Azure Eksplorator danych Spark nie przyjmuje bezpośrednich zależności od elementów datakostki ani żadnej innej dystrybucji platformy Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra usługi Azure Eksplorator danych i bazy danych](/azure/data-explorer/create-cluster-database-portal) 
* Tworzenie klastra Spark
* Zainstaluj bibliotekę łącznika usługi Azure Eksplorator danych:
    * Wstępnie skompilowane biblioteki dla [platformy Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Repozytorium Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) zainstalowana

> [!TIP]
> Obsługiwane są również wersje 2.3. x, ale mogą wymagać pewnych zmian w zależnościach pliku pom. XML.

## <a name="how-to-build-the-spark-connector"></a>Jak skompilować łącznik Spark

> [!NOTE]
> Ten krok jest opcjonalny. Jeśli używasz wstępnie skompilowanych bibliotek, przejdź do [konfiguracji klastra Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Wymagania wstępne dotyczące kompilacji

1. Zainstaluj biblioteki wymienione w [zależnościach](https://github.com/Azure/azure-kusto-spark#dependencies) , w tym następujące biblioteki [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) :
    * [Klient danych Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Klient pozyskiwania Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Zapoznaj się z [tym źródłem](https://github.com/Azure/azure-kusto-spark) , aby skompilować łącznik Spark.

1. W przypadku aplikacji Scala/Java przy użyciu definicji projektu Maven Połącz aplikację z następującym artefaktem (Najnowsza wersja może się różnić):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Polecenia kompilacji

Aby skompilować i uruchomić wszystkie testy:

```
mvn clean package
```

Aby skompilować plik JAR, uruchom wszystkie testy i Zainstaluj plik JAR w lokalnym repozytorium Maven:

```
mvn clean install
```

Aby uzyskać więcej informacji, zobacz [użycie łącznika](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Konfiguracja klastra Spark

> [!NOTE]
> Zalecane jest użycie najnowszej wersji łącznika usługi Azure Eksplorator danych Spark podczas wykonywania poniższych kroków.

1. Skonfiguruj następujące ustawienia klastra Spark na podstawie Azure Databricks klastra przy użyciu platformy Spark 2.4.4 i scala 2,11:

    ![Ustawienia klastra datakostki](media/spark-connector/databricks-cluster.png)
    
1. Zainstaluj najnowszą bibliotekę Spark-Kusto-Connector z poziomu usługi Maven:
    
    ![importować biblioteki](media/spark-connector/db-libraries-view.png) ![wybierz pozycję Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Sprawdź, czy wszystkie wymagane biblioteki są zainstalowane:

    ![Weryfikowanie zainstalowanych bibliotek](media/spark-connector/db-libraries-view.png)

1. W przypadku instalacji przy użyciu pliku JAR Sprawdź, czy zostały zainstalowane dodatkowe zależności:

    ![Dodaj zależności](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Authentication

Łącznik usługi Azure Eksplorator danych Spark umożliwia uwierzytelnianie za pomocą Azure Active Directory (Azure AD) przy użyciu jednej z następujących metod:
* [Aplikacja usługi Azure AD](#azure-ad-application-authentication)
* [Token dostępu usługi Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Uwierzytelnianie urządzenia](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (dla scenariuszy innych niż produkcyjne)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) , aby uzyskać dostęp do zasobu Key Vault, zainstalować pakiet magazynu kluczy platformy Azure i podać poświadczenia aplikacji.

### <a name="azure-ad-application-authentication"></a>Uwierzytelnianie aplikacji usługi Azure AD

Uwierzytelnianie aplikacji usługi Azure AD to najprostsza i najbardziej Typowa metoda uwierzytelniania zalecana dla łącznika usługi Azure Eksplorator danych Spark.

|Właściwości  |Opis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identyfikator aplikacji usługi Azure AD (klienta).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Urząd uwierzytelniania usługi Azure AD. Identyfikator katalogu usługi Azure AD (dzierżawa).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Klucz aplikacji usługi Azure AD dla klienta.     |

### <a name="azure-data-explorer-privileges"></a>Uprawnienia Eksplorator danych platformy Azure

Przyznaj następujące uprawnienia w klastrze usługi Azure Eksplorator danych:

* W przypadku odczytywania (źródła danych) tożsamość usługi Azure AD musi mieć uprawnienia *przeglądarki* w docelowej bazie danych lub uprawnienia *administratora* w tabeli docelowej.
* Do zapisu (ujścia danych) tożsamość usługi Azure AD musi *mieć uprawnienia do* pozyskiwania w docelowej bazie danych. Ponadto musi mieć uprawnienia *użytkownika* w docelowej bazie danych, aby utworzyć nowe tabele. Jeśli tabela docelowa już istnieje, należy skonfigurować uprawnienia *administratora* w tabeli docelowej.
 
Aby uzyskać więcej informacji na temat ról podmiotu zabezpieczeń platformy Azure Eksplorator danych, zobacz [Autoryzacja oparta na rolach](/azure/kusto/management/access-control/role-based-authorization). Aby zarządzać rolami zabezpieczeń, zobacz [Zarządzanie rolami zabezpieczeń](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Ujścia Spark: zapisywanie do Eksplorator danych platformy Azure

1. Skonfiguruj parametry ujścia:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Zapisz ramkę danych Spark w klastrze Eksplorator danych platformy Azure jako partię:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Lub użyj uproszczonej składni:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Zapisz dane przesyłane strumieniowo:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Źródło platformy Spark: odczytywanie z usługi Azure Eksplorator danych

1. Podczas odczytywania [niewielkich ilości danych](/azure/kusto/concepts/querylimits)Zdefiniuj zapytanie o dane:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Opcjonalnie **: Jeśli** udostępniasz przejściowy magazyn obiektów BLOB (a nie Eksplorator danych platformy Azure), obiekty blob są tworzone w obszarze odpowiedzialności obiektu wywołującego. Obejmuje to Inicjowanie obsługi magazynu, rotację kluczy dostępu i usuwanie przejściowych artefaktów. 
    Moduł KustoBlobStorageUtils zawiera funkcje pomocnika do usuwania obiektów BLOB na podstawie współrzędnych konta i kontenera oraz poświadczenia konta, albo pełny adres URL SAS z uprawnieniami do zapisu, odczytu i wyświetlania listy. Gdy odpowiadające RDD nie są już potrzebne, każda transakcja przechowuje przejściowe artefakty obiektów BLOB w osobnym katalogu. Ten katalog jest przechwytywany jako część dzienników informacyjnych odczytu i transakcji raportowanych w węźle sterownika Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    W powyższym przykładzie nie można uzyskać dostępu do Key Vault przy użyciu interfejsu łącznika; używana jest prostsza Metoda użycia wpisów tajnych datakostki.

1. Przeczytaj Eksplorator danych platformy Azure.

    * Jeśli **udostępniasz** przejściowy magazyn obiektów blob, przeczytaj artykuł Azure Eksplorator danych w następujący sposób:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Jeśli **Usługa azure Eksplorator danych** zapewnia przejściowy magazyn obiektów blob, przeczytaj Eksplorator danych Azure w następujący sposób:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [łącznika usługi Azure Eksplorator danych Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Przykładowy kod dla języków Java i Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
