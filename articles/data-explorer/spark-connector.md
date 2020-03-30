---
title: Użyj łącznika Usługi Azure Data Explorer dla platformy Apache Spark, aby przenieść dane między klastrami Usługi Azure Data Explorer i Spark.
description: W tym temacie pokazano, jak przenieść dane między klastrami Usługi Azure Data Explorer i Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208613"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer Connector for Apache Spark

[Apache Spark](https://spark.apache.org/) to zunifikowany aparat analityczny do przetwarzania danych na dużą skalę. Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym na dużych ilościach danych. 

Łącznik usługi Azure Data Explorer dla platformy Spark to [projekt typu open source,](https://github.com/Azure/azure-kusto-spark) który można uruchomić w dowolnym klastrze platformy Spark. Implementuje źródło danych i ujście danych do przenoszenia danych w klastrach Usługi Azure Data Explorer i Spark. Korzystając z usługi Azure Data Explorer i Apache Spark, można tworzyć szybkie i skalowalne aplikacje przeznaczone na scenariusze oparte na danych. Na przykład uczenie maszynowe (ML), wyodrębnianie i przekształcanie obciążenia (ETL) i analiza dzienników. Za pomocą łącznika usługa Azure Data Explorer staje się prawidłowym magazynem danych dla standardowych operacji źródła i ujścia platformy Spark, takich jak zapis, odczyt i writeStream.

Można zapisać w Eksploratorze danych platformy Azure w trybie wsadowym lub strumieniowym. Odczyt z usługi Azure Data Explorer obsługuje przycinanie kolumn i predykatu wypychania, który filtruje dane w Eksploratorze danych platformy Azure, zmniejszając ilość przesyłanych danych.

W tym temacie opisano sposób instalowania i konfigurowania łącznika platformy Azure Data Explorer Spark i przenoszenia danych między eksploratorem danych platformy Azure a klastrami Platformy Spark apache.

> [!NOTE]
> Chociaż niektóre z poniższych przykładów odnoszą się do klastra [platformy Azure Databricks](https://docs.azuredatabricks.net/) Spark, łącznik platformy Azure Data Explorer Spark nie przyjmuje bezpośrednich zależności od databricks lub innej dystrybucji platformy Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra i bazy danych usługi Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Tworzenie klastra Spark
* Zainstaluj bibliotekę łączników Usługi Azure Data Explorer:
    * Wstępnie zbudowane biblioteki dla [spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Repo Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) zainstalowany

> [!TIP]
> Obsługiwane są również wersje 2.3.x, ale mogą wymagać pewnych zmian w zależnościach pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Jak zbudować łącznik Spark

> [!NOTE]
> Ten krok jest opcjonalny. Jeśli używasz wstępnie utworzonych bibliotek, przejdź do [konfiguracji klastra platformy Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Wymagania wstępne kompilacji

1. Zainstaluj biblioteki wymienione w [zależnościach,](https://github.com/Azure/azure-kusto-spark#dependencies) w tym następujące biblioteki [Kusto Java SDK:](/azure/kusto/api/java/kusto-java-client-library)
    * [Klient danych Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Klient Kusto Ingest](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Zapoznaj się [z tym źródłem](https://github.com/Azure/azure-kusto-spark) do tworzenia łącznika platformy Spark.

1. W przypadku aplikacji Scala/Java przy użyciu definicji projektu Maven połącz aplikację z następującym artefaktem (najnowsza wersja może się różnić):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Polecenia kompilacji

Aby zbudować słoik i uruchomić wszystkie testy:

```
mvn clean package
```

Aby utworzyć słoik, uruchom wszystkie testy i zainstaluj słoik do lokalnego repozytorium Maven:

```
mvn clean install
```

Aby uzyskać więcej informacji, zobacz [użycie łącznika](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Konfiguracja klastra platformy Spark

> [!NOTE]
> Podczas wykonywania następujących kroków zaleca się użycie najnowszej wersji łącznika usługi Azure Data Explorer Spark.

1. Skonfiguruj następujące ustawienia klastra platformy Spark na podstawie klastra Usługi Azure Databricks przy użyciu programów Spark 2.4.4 i Scala 2.11:

    ![Ustawienia klastra Databricks](media/spark-connector/databricks-cluster.png)
    
1. Zainstaluj najnowszą bibliotekę złączy spark-kusto firmy Maven:
    
    ![Importowanie](media/spark-connector/db-libraries-view.png) ![bibliotek Wybierz złącze Spark-Kusto](media/spark-connector/db-dependencies.png)

1. Sprawdź, czy wszystkie wymagane biblioteki są zainstalowane:

    ![Weryfikowanie zainstalowanych bibliotek](media/spark-connector/db-libraries-view.png)

1. W przypadku instalacji przy użyciu pliku JAR sprawdź, czy zainstalowano dodatkowe zależności:

    ![Dodawanie zależności](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Uwierzytelnianie

Łącznik Platformy Azure Data Explorer Spark umożliwia uwierzytelnienie za pomocą usługi Azure Active Directory (Azure AD) przy użyciu jednej z następujących metod:
* [Aplikacja usługi Azure AD](#azure-ad-application-authentication)
* [Token dostępu usługi Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Uwierzytelnianie urządzeń](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (w scenariuszach nieprodukcyjnych)
* [Usługa Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) Aby uzyskać dostęp do zasobu usługi Key Vault, zainstaluj pakiet usługi azure-keyvault i podaj poświadczenia aplikacji.

### <a name="azure-ad-application-authentication"></a>Uwierzytelnianie aplikacji usługi Azure AD

Uwierzytelnianie aplikacji usługi Azure AD jest najprostszą i najbardziej typową metodą uwierzytelniania i jest zalecane dla łącznika platformy Azure Data Explorer Spark.

|Właściwości  |Opis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identyfikator aplikacji (klienta) usługi Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Urząd uwierzytelniania usługi Azure AD. Identyfikator usługi Azure AD Directory (dzierżawy).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Klucz aplikacji usługi Azure AD dla klienta.     |

### <a name="azure-data-explorer-privileges"></a>Uprawnienia Eksploratora danych platformy Azure

Przyznaj następujące uprawnienia w klastrze usługi Azure Data Explorer:

* Do odczytu (źródło danych), tożsamość usługi Azure AD musi mieć uprawnienia *przeglądarki* w docelowej bazie danych lub uprawnienia *administratora* w tabeli docelowej.
* Do zapisu (ujście danych), tożsamość usługi Azure AD musi mieć wbudowane uprawnienia *w* docelowej bazie danych. Musi również mieć uprawnienia *użytkownika* w docelowej bazie danych, aby utworzyć nowe tabele. Jeśli tabela docelowa już istnieje, należy skonfigurować uprawnienia *administratora* w tabeli docelowej.
 
Aby uzyskać więcej informacji na temat głównych ról usługi Azure Data Explorer, zobacz [autoryzacja oparta na rolach](/azure/kusto/management/access-control/role-based-authorization). Aby zarządzać rolami zabezpieczeń, zobacz [Zarządzanie rolami zabezpieczeń](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark sink: zapisywanie w Eksploratorze danych platformy Azure

1. Konfigurowanie parametrów ujścia:

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

1. Zapisz program Spark DataFrame w klastrze usługi Azure Data Explorer jako partia:

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
    
   Możesz też użyć uproszczonej składni:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Zapisuj dane przesyłane strumieniowo:

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Źródło iskry: odczyt z Eksploratora danych platformy Azure

1. Podczas [odczytywania niewielkich ilości danych](/azure/kusto/concepts/querylimits)należy zdefiniować kwerendę danych:

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

1. Opcjonalnie: **Jeśli** podasz przejściowy magazyn obiektów blob (a nie Usługi Azure Data Explorer) obiekty blob są tworzone są na odpowiedzialność wywołującego. Obejmuje to inicjowanie obsługi administracyjnej magazynu, obracanie kluczy dostępu i usuwanie artefaktów przejściowych. 
    Moduł KustoBlobStorageUtils zawiera funkcje pomocnika do usuwania obiektów blob na podstawie współrzędnych konta i kontenera oraz poświadczeń konta lub pełnego adresu URL sygnatury dostępu Współdzielonego z uprawnieniami do zapisu, odczytu i listy. Gdy odpowiednie RDD nie jest już potrzebne, każda transakcja przechowuje przejściowe artefakty obiektów blob w oddzielnym katalogu. Ten katalog jest przechwytywany jako część dzienników informacji o transakcjach odczytu zgłoszonych w węźle Sterownik platformy Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    W powyższym przykładzie przechowalnia kluczy nie jest dostępna przy użyciu interfejsu łącznika; używana jest prostsza metoda korzystania z wpisów tajnych Databricks.

1. Odczyt z Eksploratora danych platformy Azure.

    * Jeśli **you** podasz magazynu obiektów blob przejściowe, odczyt z Usługi Azure Data Explorer w następujący sposób:

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

    * Jeśli **Usługa Azure Data Explorer** udostępnia magazyn obiektów blob przejściowy, odczyt z Usługi Azure Data Explorer w następujący sposób:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o usłudze [Azure Data Explorer Spark Connector](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Przykładowy kod dla języka Java i Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
