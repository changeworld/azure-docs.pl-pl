---
title: 'Szybki start: pozyskiwanie danych z platformy Kafka do usługi Azure Data Explorer'
description: Z tego przewodnika Szybki start dowiesz się, jak pozyskiwać (ładować) dane do usługi Azure Data Explorer z platformy Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: f36acb7237438848a53fdf91c291c4f67233af5d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901028"
---
# <a name="quickstart-ingest-data-from-kafka-into-azure-data-explorer"></a>Szybki start: pozyskiwanie danych z platformy Kafka do usługi Azure Data Explorer
 
Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer umożliwia pozyskiwanie (ładowanie) danych z platformy Kafka. Kafka to rozproszona platforma przesyłania strumieniowego umożliwiająca tworzenie potoków danych przesyłania strumieniowego w czasie rzeczywistym, które niezawodnie przenoszą dane między systemami lub aplikacjami.
 
## <a name="prerequisites"></a>Wymagania wstępne
 
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). 
 
* [Klaster testowy i bazy danych](create-cluster-database-portal.md).
 
* [Przykładową aplikację](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) która generuje dane i wysyła je do platformy Kafka.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) uruchamianie przykładowej aplikacji.
 
## <a name="kafka-connector-setup"></a>Konfigurowanie łącznika platformy Kafka

Kafka Connect to narzędzie służące do skalowalnego i niezawodnego przesyłania strumieniowego danych między platformą Apache Kafka i innymi systemami. Upraszcza ono szybkie definiowanie łączników, dzięki którym można przenosić duże kolekcje danych na platformę Kafka i z niej. Ujście platformy Kafka dla usługi ADX służy jako łącznik z platformy Kafka.
 
### <a name="bundle"></a>Pakiet

Platforma Kafka może załadować plik `.jar` jako wtyczkę, która będzie działać jako łącznik niestandardowy. Aby utworzyć taki plik `.jar`, sklonujemy kod lokalnie i skompilujemy go przy użyciu narzędzia Maven. 

#### <a name="clone"></a>Klonuj

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Kompilacja

Skompiluj kod lokalnie za pomocą narzędzia Maven, aby utworzyć plik `.jar` wraz z zależnościami.

* Zestaw JDK w wersji 1.8 lub nowszej — [pobierz](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie Maven — [pobierz](https://maven.apache.org/install.html)
 

W katalogu głównym *kafka-sink-azure-kusto* uruchom następujące polecenie:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Wdróż 

Załaduj wtyczkę do platformy Kafka. Przykład wdrożenia za pomocą platformy Docker można znaleźć pod adresem [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Szczegółową dokumentację łączników platformy Kafka oraz informacje na temat sposobu ich wdrażania można znaleźć na stronie [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Przykładowa konfiguracja 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Tworzenie tabeli docelowej w usłudze ADX
 
Utwórz w usłudze ADX tabelę, do której platforma Kafka będzie mogła wysyłać dane. Utwórz tabelę w klastrze i bazie danych, które były aprowizowane w sekcji **Wymagania wstępne**.
 
1. W witrynie Azure Portal przejdź do klastra, a następnie wybierz pozycję **Zapytanie**.
 
    ![Link do aplikacji Zapytanie](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Skopiuj poniższe polecenie do okna i wybierz pozycję **Uruchom**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Uruchamianie zapytania create](media/ingest-data-event-hub/run-create-query.png)
 
1. Skopiuj poniższe polecenie do okna i wybierz pozycję **Uruchom**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    To polecenie mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli (TestTable).


## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz, gdy klaster platformy Kafka jest połączony z usługą ADX, wygeneruj dane za pomocą pobranej [aplikacji przykładowej](https://github.com/Azure-Samples/event-hubs-dotnet-ingest).

### <a name="clone"></a>Klonowanie

Sklonuj aplikację przykładową lokalnie:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Uruchamianie aplikacji

1. Otwórz przykładowe rozwiązanie aplikacji w programie Visual Studio.

1. W pliku `Program.cs` zaktualizuj stałą `connectionString` tak, aby zawierała parametry połączenia platformy Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Skompiluj i uruchom aplikację. Aplikacja wysyła komunikaty do klastra platformy Kafka i co 10 sekund wyświetla swój stan.

1. Gdy aplikacja wyśle kilka komunikatów, przejdź do następnego kroku.
 
## <a name="query-and-review-the-data"></a>Zapytania o dane oraz przegląd danych

1. Aby upewnić się, że podczas pozyskiwania nie wystąpiły żadne błędy:

    ```Kusto
    .show ingestion failures
    ```

1. Aby wyświetlić nowo pozyskane dane:

    ```Kusto
    TestTable 
    | count
    ```

1. Aby wyświetlić zawartość komunikatów:
 
    ```Kusto
    TestTable
    ```
 
    Zestaw wyników powinien wyglądać podobnie do następującego:
 
    ![Zestaw wyników komunikatów](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Kolejne kroki
 
> [!div class="nextstepaction"]
> [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
