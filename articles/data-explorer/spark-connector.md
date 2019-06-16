---
title: Łącznik Eksplorator danych platformy Azure dla platformy Apache Spark umożliwia przenoszenie danych między klastrami Eksplorator danych platformy Azure i platformy Spark.
description: W tym temacie przedstawiono sposób przenoszenia danych między klastrami Eksplorator danych platformy Azure i Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65441353"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Łącznik Eksploratora danych platformy Azure dla platformy Apache Spark (wersja zapoznawcza)

[Platforma Apache Spark](https://spark.apache.org/) jest aparatem ujednoliconego analizy do przetwarzania danych na dużą skalę. Eksplorator usługi Azure Data to szybki, w pełni zarządzana Usługa do analizowania danych do analizy w czasie rzeczywistym na bardzo dużych ilości danych. 

Łącznik Eksplorator danych usługi Azure dla platformy Spark implementuje danych źródła i ujścia danych do przenoszenia danych w klastrach Eksploratora danych platformy Azure i platformy Spark do użycia zarówno ich możliwości. Za pomocą Eksploratora danych platformy Azure i Apache Spark, można tworzyć szybkich i skalowalnych aplikacji, przeznaczone dla scenariuszy, takich jak machine learning (ML), wyodrębnianie-transformacja-ładowanie (ETL) i usługi Log Analytics w oparciu o dane. Zapisywanie do Eksploratora danych usługi Azure może odbywać się w trybie przesyłania strumieniowego i usługi batch.
Odczytywanie ich z Eksploratora danych usługi Azure obsługuje Oczyszczanie kolumn i przekazywanie predykatu, co zmniejsza ilość danych przekazywanych przez filtrowanie danych w Eksploratorze danych platformy Azure.

Łącznik Spark Eksploratora danych usługi Azure jest [projekt typu open source](https://github.com/Azure/azure-kusto-spark) , można uruchomić w dowolnym klastrze Spark.

> [!NOTE]
> Mimo że niektóre z poniższych przykładów można znaleźć [usługi Azure Databricks](https://docs.azuredatabricks.net/) klastra Spark łącznika usługi Spark Eksploratora danych usługi Azure nie ma zależności bezpośrednich w usłudze Databricks lub innych dystrybucji Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](/azure/data-explorer/create-cluster-database-portal) 
* Tworzenie klastra Spark
* Zainstaluj Biblioteka łącznika Eksploratora danych usługi Azure i biblioteki, na liście [zależności](https://github.com/Azure/azure-kusto-spark#dependencies) tym [zestawu SDK Java usługi Kusto](/azure/kusto/api/java/kusto-java-client-library) biblioteki:
    * [Kusto danych klienta](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto pozyskiwania klientów](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Wstępnie skompilowanych bibliotek [2.4 platformy Spark, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Jak utworzyć łącznik usługi Spark

Łącznik platformy Spark, może być zbudowane z [źródeł](https://github.com/Azure/azure-kusto-spark) jak wyjaśniono poniżej.

> [!NOTE]
> Ten krok jest opcjonalny. Jeśli używasz wstępnie skompilowanych bibliotek przejdź do [Konfiguracja klastra Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Wymagania wstępne dotyczące kompilacji

* Zainstalowany zestaw SDK 1.8 języka Java
* [Narzędzie maven 3.x](https://maven.apache.org/download.cgi) zainstalowane
* Platforma Apache Spark w wersji 2.4.0 lub nowszej

> [!TIP]
> wersje 2.3.x są również obsługiwane, ale mogą wymagać pewnych zmian w zależnościach pom.xml.

Dla aplikacji Scala/Java przy użyciu definicji Projekt narzędzia Maven, Połącz swoją aplikację przy użyciu następującego artefaktu (Najnowsza wersja mogą się różnić):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Tworzenie poleceń

Aby skompilować plik jar i uruchomić wszystkie testy:

```
mvn clean package
```

Aby utworzyć plik jar, uruchom wszystkie testy i zainstaluj plik jar do lokalnego repozytorium Maven:

```
mvn clean install
```

Aby uzyskać więcej informacji, zobacz [użycie łącznika](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Konfiguracja klastra Spark

> [!NOTE]
> Zalecane jest użyj najnowszej wersji łącznika usługi Spark Eksploratora danych, wykonując następujące czynności:

1. Ustaw następujące platformy Spark ustawienia klastra, w oparciu o usługi Azure Databricks w klastrze przy użyciu platformy Spark 2.4 i Scala 2.11: 

    ![Ustawienia klastra usługi Databricks](media/spark-connector/databricks-cluster.png)

1. Biblioteka łącznika Eksploratora danych usługi Azure import:

    ![Importuj biblioteki Eksplorator danych platformy Azure](media/spark-connector/db-create-library.png)

1. Dodaj dodatkowe zależności:

    ![Dodaj zależności](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Java poprawną wersję dla każdej wersji platformy Spark zostanie znaleziony [tutaj](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Sprawdź, czy wszystkie wymagane biblioteki są zainstalowane:

    ![Sprawdź biblioteki zainstalowane](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Łącznik Spark Eksploratora danych usługi Azure pozwala na uwierzytelnianie za pomocą usługi Azure Active Directory (Azure AD) przy użyciu [aplikacji usługi Azure AD](#azure-ad-application-authentication), [token dostępu usługi Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [uwierzytelnianie urządzeń ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (dla scenariuszy nieprodukcyjnych) lub [usługi Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Użytkownik musi zainstalować pakiet azure keyvault i podaj poświadczenia aplikacji, aby uzyskać dostęp do zasobu usługi Key Vault.

### <a name="azure-ad-application-authentication"></a>Uwierzytelnianie aplikacji w usłudze Azure AD

Większość metoda uwierzytelniania proste i wspólnej. Ta metoda jest zalecana dla użycia łącznika usługi Spark Eksploratora danych.

|Właściwości  |Opis  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Usługa Azure AD identyfikator aplikacji (klienta).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Urząd uwierzytelniania usługi Azure AD. Usługa Azure identyfikatora katalogu usługi AD (dzierżawcy)        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Klucz aplikacji Azure AD dla klienta.     |

### <a name="azure-data-explorer-privileges"></a>Uprawnienia do Eksploratora danych platformy Azure

Następujące uprawnienia muszą być przyznawane w klastrze usługi Azure Data Explorer:

* Do odczytywania (źródła danych), musi mieć aplikację usługi Azure AD *podglądu* uprawnienia do docelowej bazy danych lub *administratora* uprawnień w tabeli docelowej.
* Do pisania (ujście danych), musi mieć aplikację usługi Azure AD *pozwalająca* uprawnienia do docelowej bazy danych. Musisz również posiadać *użytkownika* uprawnienia do docelowej bazy danych, aby utworzyć nowe tabele. Jeśli tabela docelowa już istnieje, *administratora* można skonfigurować uprawnienia w tabeli docelowej.
 
Aby uzyskać więcej informacji na temat ról jednostki Eksploratora danych usługi Azure, zobacz [autoryzacji opartej na rolach](/azure/kusto/management/access-control/role-based-authorization). Aby zarządzać rolami zabezpieczeń, zobacz [Zarządzanie rolami zabezpieczeń](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Obiekt sink Spark: Zapisywanie do Eksploratora danych platformy Azure

1. Skonfiguruj parametry ujścia:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Pisanie Spark DataFrame klastrowi Eksploratora usługi Azure Data jako usługi batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Dane przesyłane strumieniowo zapisu:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Źródło Spark: Odczytywanie z Eksploratora danych platformy Azure

1. Podczas odczytywania małe ilości danych, należy zdefiniować zapytanie danych:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Podczas odczytywania dużych ilości danych, magazynu przejściowego obiektu blob musi być podany. Podaj klucz sygnatury dostępu Współdzielonego kontenera magazynu, lub nazwy konta magazynu, klucz konta i nazwę kontenera. Ten krok jest tylko wymagane dla bieżącej wersji zapoznawczej łącznika Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    W powyższym przykładzie firma Microsoft nie uzyskać dostęp do usługi Key Vault, przy użyciu łącznika interfejsu. Można również użyć prostszą metodę niż użycia wpisów tajnych usługi Databricks.

1. Przeczytaj, w Eksploratorze danych platformy Azure:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
