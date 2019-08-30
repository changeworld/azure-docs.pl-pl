---
title: Użyj łącznika usługi Azure Eksplorator danych, aby uzyskać Apache Spark przenoszenia danych między Eksplorator danych platformy Azure i klastrami platformy Spark.
description: W tym temacie pokazano, jak przenosić dane między usługą Azure Eksplorator danych i klastrami Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 0fe81926327bcccac56718cc0d06e336e1af17fe
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165086"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Łącznik usługi Azure Eksplorator danych dla Apache Spark (wersja zapoznawcza)

[Apache Spark](https://spark.apache.org/) to ujednolicony aparat analityczny do przetwarzania danych na dużą skalę. Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym w przypadku dużych ilości danych. 

Łącznik usługi Azure Eksplorator danych dla platformy Spark implementuje źródło danych i ujścia danych na potrzeby przesyłania danych między usługą Azure Eksplorator danych i klastrami Spark w celu korzystania z obu tych możliwości. Korzystając z usługi Azure Eksplorator danych i Apache Spark, możesz tworzyć szybkie i skalowalne aplikacje ukierunkowane na scenariusze oparte na danych, takie jak uczenie maszynowe (ML), extract-transform-load (ETL) i Log Analytics. Zapisywanie w usłudze Azure Eksplorator danych można przeprowadzić w trybie wsadowym i przesyłania strumieniowego.
Odczyt z usługi Azure Eksplorator danych obsługuje funkcję oczyszczania kolumn i przekazywanie predykatów, co zmniejsza ilość transferowanych danych przez filtrowanie danych w usłudze Azure Eksplorator danych.

Łącznik usługi Azure Eksplorator danych Spark to [projekt Open Source](https://github.com/Azure/azure-kusto-spark) , który można uruchomić w dowolnym klastrze Spark.

> [!NOTE]
> Mimo że niektóre z poniższych przykładów odnoszą się do klastra platformy Spark [Azure Databricks](https://docs.azuredatabricks.net/) , łącznik usługi Azure Eksplorator danych Spark nie przyjmuje bezpośrednich zależności od elementów datakostki ani żadnej innej dystrybucji platformy Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra usługi Azure Eksplorator danych i bazy danych](/azure/data-explorer/create-cluster-database-portal) 
* Tworzenie klastra Spark
* Zainstaluj bibliotekę łączników usługi Azure Eksplorator danych i biblioteki wymienione [](https://github.com/Azure/azure-kusto-spark#dependencies) w zależnościach, w tym następujące biblioteki [zestawu SDK Java Kusto](/azure/kusto/api/java/kusto-java-client-library) :
    * [Klient danych Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Klient pozyskiwania Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Wstępnie skompilowane biblioteki dla [platformy Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Jak skompilować łącznik Spark

Łącznik platformy Spark można skompilować ze [źródeł](https://github.com/Azure/azure-kusto-spark) , jak pokazano poniżej.

> [!NOTE]
> Ten krok jest opcjonalny. Jeśli używasz wstępnie skompilowanych bibliotek, przejdź do [konfiguracji klastra Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Wymagania wstępne dotyczące kompilacji

* Zainstalowano zestaw SDK dla języka Java 1,8
* [Maven 3. x](https://maven.apache.org/download.cgi) zainstalowana
* Apache Spark w wersji 2.4.0 lub nowszej

> [!TIP]
> Obsługiwane są również wersje 2.3. x, ale mogą wymagać pewnych zmian w zależnościach pliku pom. XML.

W przypadku aplikacji Scala/Java przy użyciu definicji projektu Maven Połącz aplikację z następującym artefaktem (Najnowsza wersja może się różnić):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
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
> Zalecane jest użycie najnowszej wersji łącznika usługi Azure Eksplorator danych Spark podczas wykonywania następujących czynności:

1. Ustaw następujące ustawienia klastra Spark na podstawie Azure Databricks klastra przy użyciu platformy Spark 2,4 i scala 2,11: 

    ![Ustawienia klastra datakostki](media/spark-connector/databricks-cluster.png)

1. Zaimportuj bibliotekę łącznika usługi Azure Eksplorator danych:

    ![Importuj bibliotekę Eksplorator danych platformy Azure](media/spark-connector/db-create-library.png)

1. Dodaj dodatkowe zależności (niepotrzebne, jeśli są używane z Maven):

    ![Dodaj zależności](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > W [tym miejscu](https://github.com/Azure/azure-kusto-spark#dependencies)znajduje się prawidłowa wersja języka Java dla każdej wersji platformy Spark.

1. Sprawdź, czy wszystkie wymagane biblioteki są zainstalowane:

    ![Weryfikowanie zainstalowanych bibliotek](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Łącznik usługi Azure Eksplorator danych Spark umożliwia uwierzytelnianie za pomocą usługi Azure Active Directory (Azure AD) przy użyciu [aplikacji usługi Azure AD](#azure-ad-application-authentication), [tokenu dostępu usługi Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [uwierzytelniania urządzeń](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (dla scenariuszy nieprodukcyjnych) lub [klucza platformy Azure Magazyn](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Użytkownik musi zainstalować pakiet magazynu kluczy Azure i podać poświadczenia aplikacji, aby uzyskać dostęp do zasobu Key Vault.

### <a name="azure-ad-application-authentication"></a>Uwierzytelnianie aplikacji usługi Azure AD

Najbardziej prosta i wspólna metoda uwierzytelniania. Ta metoda jest zalecana w przypadku użycia łącznika usługi Azure Eksplorator danych Spark.

|Właściwości  |Opis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identyfikator aplikacji usługi Azure AD (klienta).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Urząd uwierzytelniania usługi Azure AD. Identyfikator katalogu usługi Azure AD (dzierżawa).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Klucz aplikacji usługi Azure AD dla klienta.     |

### <a name="azure-data-explorer-privileges"></a>Uprawnienia Eksplorator danych platformy Azure

Następujące uprawnienia muszą zostać przyznane w klastrze usługi Azure Eksplorator danych:

* W przypadku odczytywania (źródła danych) aplikacja usługi Azure AD musi mieć uprawnienia *przeglądarki* w docelowej bazie danych lub uprawnienia *administratora* w tabeli docelowej.
* Do zapisu (ujścia danych) aplikacja usługi Azure AD musi mieć uprawnienia do pozyskiwania w docelowej bazie danych. Ponadto musi mieć uprawnienia *użytkownika* w docelowej bazie danych, aby utworzyć nowe tabele. Jeśli tabela docelowa już istnieje, można skonfigurować uprawnienia *administratora* w tabeli docelowej.
 
Aby uzyskać więcej informacji na temat ról podmiotu zabezpieczeń platformy Azure Eksplorator danych, zobacz [Autoryzacja oparta na rolach](/azure/kusto/management/access-control/role-based-authorization). Aby zarządzać rolami zabezpieczeń, zobacz [Zarządzanie rolami zabezpieczeń](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Ujścia Spark: Zapisywanie w usłudze Azure Eksplorator danych

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
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
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

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Źródło platformy Spark: Odczytywanie z usługi Azure Eksplorator danych

1. Podczas odczytywania niewielkich ilości danych Zdefiniuj zapytanie o dane:

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

1. W przypadku odczytywania dużych ilości danych należy zapewnić przejściowy magazyn obiektów BLOB. Podaj klucz sygnatury dostępu współdzielonego kontenera magazynu lub nazwę konta magazynu, klucz konta i nazwę kontenera. Ten krok jest wymagany tylko w przypadku bieżącej wersji zapoznawczej łącznika Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    W powyższym przykładzie nie możemy uzyskać dostępu do Key Vault przy użyciu interfejsu łącznika. Alternatywnie korzystamy z prostszej metody używania wpisów tajnych datakostki.

1. Przeczytaj Eksplorator danych platformy Azure:

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
