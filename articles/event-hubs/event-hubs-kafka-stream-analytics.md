---
title: Usługi Azure Event Hubs — proces zdarzeń apache platformy Kafka
description: 'Samouczek: W tym artykule pokazano, jak przetwarzać zdarzenia platformy Kafka, które są pozyskiwania za pośrednictwem centrów zdarzeń przy użyciu usługi Azure Stream Analytics'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 054b5c7d13df39de4fa57167e0363c0b4acf9b08
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80280749"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Samouczek: Przetwarzanie apache kafka dla zdarzeń centrum zdarzeń przy użyciu analizy strumienia 
W tym artykule pokazano, jak przesyłać strumieniowo dane do centrów zdarzeń i przetwarzać go za pomocą usługi Azure Stream Analytics. Przeprowadzi Cię przez następujące kroki: 

1. Tworzenie obszaru nazw centrów zdarzeń z włączoną platformą Kafka.
2. Utwórz klienta platformy Kafka, który wysyła wiadomości do centrum zdarzeń.
3. Utwórz zadanie usługi Stream Analytics, które kopiuje dane z Centrum zdarzeń do magazynu obiektów blob platformy Azure. 

Nie trzeba zmieniać klientów protokołu ani uruchamiać własnych klastrów podczas korzystania z punktu końcowego platformy Kafka udostępnianego przez centrum zdarzeń. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html) i powyżej. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Git](https://www.git-scm.com/)
* **Konto usługi Azure Storage**. Jeśli go nie masz, [utwórz go](../storage/common/storage-account-create.md) przed kontynuowaniem. Zadanie usługi Stream Analytics w tym instruktażu przechowuje dane wyjściowe w magazynie obiektów blob platformy Azure. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka
Podczas tworzenia obszaru nazw centrum zdarzeń warstwy **standardowej** punkt końcowy platformy Kafka dla obszaru nazw jest automatycznie włączony. Można przesyłać strumieniowo zdarzenia z aplikacji korzystających z protokołu Kafka do centrum zdarzeń warstwy standardowej. Postępuj zgodnie z instrukcjami krok po kroku w [Centrum tworzenia zdarzeń przy użyciu witryny Azure Portal,](event-hubs-create.md) aby utworzyć obszar nazw centrum zdarzeń warstwy **standardowej.** 

> [!NOTE]
> Centra zdarzeń dla platformy Kafka są dostępne tylko w **warstwach standardowych** i **dedykowanych.** Warstwa **podstawowa** nie obsługuje platformy Kafka w centrach zdarzeń.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Wysyłanie wiadomości z platformą Kafka w Centrach zdarzeń

1. Klonuj [usługi Azure Event Hubs dla repozytorium platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) na komputerze.
2. Przejdź do folderu: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Zaktualizuj szczegóły `src/main/resources/producer.config`konfiguracji producenta w programie . Określ **nazwę** i **parametry połączenia** obszaru nazw centrum **zdarzeń**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Przejdź `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`do pliku **TestDataReporter.java** w wybranym edytorze. 
6. Skomentuj następujący wiersz kodu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Dodaj następujący wiersz kodu zamiast komentowanego kodu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ten kod wysyła dane zdarzenia w formacie **JSON.** Podczas konfigurowania danych wejściowych dla zadania usługi Stream Analytics, należy określić JSON jako format danych wejściowych. 
7. **Uruchom producenta** i strumień do Centrów zdarzeń. Na komputerze z systemem Windows podczas korzystania z **wiersza polecenia Node.js**przełącz się do folderu `azure-event-hubs-for-kafka/quickstart/java/producer` przed uruchomieniem tych poleceń. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Sprawdź, czy Centrum zdarzeń odbiera dane

1. Wybierz **centra zdarzeń** w obszarze **JEDNOSTKI**. Upewnij się, że widzisz centrum zdarzeń o nazwie **test**. 

    ![Centrum zdarzeń — test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Upewnij się, że do centrum zdarzeń przychodzą komunikaty. 

    ![Centrum zdarzeń - wiadomości](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Przetwarzanie danych zdarzeń przy użyciu zadania usługi Stream Analytics
W tej sekcji utworzysz zadanie usługi Azure Stream Analytics. Klient platformy Kafka wysyła zdarzenia do centrum zdarzeń. Utwórz zadanie usługi Stream Analytics, które przyjmuje dane zdarzeń jako dane wejściowe i wyprowadza je do magazynu obiektów blob platformy Azure. Jeśli nie masz **konta usługi Azure Storage,** [utwórz je](../storage/common/storage-account-create.md).

Kwerenda w zadaniu usługi Stream Analytics przekazuje dane bez przeprowadzania żadnych analiz. Można utworzyć kwerendę, która przekształca dane wejściowe do tworzenia danych wyjściowych w innym formacie lub z uzyskanych szczegółowych informacji.  

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics 

1. Wybierz **+ Utwórz zasób** w [witrynie Azure portal](https://portal.azure.com).
2. Wybierz **pozycję Analytics** w menu Portalu Azure **Marketplace** i wybierz zadanie usługi **Stream Analytics**. 
3. Na stronie **Nowa analiza strumienia** wykonaj następujące czynności: 
    1. Wprowadź **nazwę** zadania. 
    2. Wybierz **subskrypcję**.
    3. Wybierz **pozycję Utwórz nowy** dla grupy **zasobów** i wprowadź jej nazwę. Można również **użyć istniejącej** grupy zasobów. 
    4. Wybierz **lokalizację** zadania.
    5. Wybierz **pozycję Utwórz,** aby utworzyć zadanie. 

        ![Nowe zadanie usługi Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. W komunikacie z powiadomieniem wybierz pozycję **Przejdź do zasobu,** aby wyświetlić stronę **zadania usługi Stream Analytics.** 
2. **Wybierz wejścia** w sekcji **TOPOLOGIA ZADANIA** w menu po lewej stronie.
3. Wybierz **pozycję Dodaj dane wejściowe strumienia**, a następnie wybierz pozycję Centrum **zdarzeń**. 

    ![Dodawanie centrum zdarzeń jako danych wejściowych](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stronie konfiguracji **wejściowej Centrum zdarzeń** wykonaj następujące czynności: 

    1. Określ **alias** danych wejściowych. 
    2. Wybierz swoją **subskrypcję platformy Azure**.
    3. Wybierz **obszar nazw centrum zdarzeń** utworzony wcześniej. 
    4. Wybierz **test** dla **centrum zdarzeń**. 
    5. Wybierz **pozycję Zapisz**. 

        ![Konfiguracja wprowadzania centrum zdarzeń](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania 

1. Wybierz **pozycję Wyjścia** w sekcji **TOPOLOGIA ZADAŃ** w menu. 
2. Wybierz **pozycję + Dodaj** na pasku narzędzi i wybierz pozycję Magazyn obiektów **Blob**
3. Na stronie Ustawienia wyjściowe magazynu obiektów blob wykonaj następujące czynności: 
    1. Określ **alias** dla danych wyjściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure. 
    3. Wybierz swoje **konto usługi Azure Storage**. 
    4. Wprowadź **nazwę kontenera,** który przechowuje dane wyjściowe z kwerendy usługi Stream Analytics.
    5. Wybierz **pozycję Zapisz**.

        ![Konfiguracja wyjściowa magazynu obiektów blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiowanie kwerendy
Po skonfigurowaniu zadania usługi Stream Analytics do odczytu przychodzącego strumienia danych następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). W tym instruktażu można zdefiniować kwerendę, która przechodzi przez dane bez wykonywania transformacji.

1. Wybierz **opcję Kwerenda**.
2. W oknie kwerendy `[YourOutputAlias]` zastąp aliasem wyjściowym utworzonym wcześniej.
3. Zastąp `[YourInputAlias]` wcześniej utworzonym aliasem wejściowym. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Zapytanie](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Wybierz **pozycję Przegląd** w menu po lewej stronie. 
2. Wybierz pozycję **Uruchom**. 

    ![Menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stronie **Rozpocznij pracę** wybierz pozycję **Start**. 

    ![Strona uruchamiania zadania](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Poczekaj, aż stan zadania zmieni się z **Rozpoczęcie** do **uruchomienia**. 

    ![Stan zadania — uruchamianie](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testowanie scenariusza
1. Uruchom **ponownie producenta platformy Kafka,** aby wysłać zdarzenia do centrum zdarzeń. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Upewnij się, że dane **wyjściowe** są generowane w **magazynie obiektów blob platformy Azure**. W kontenerze jest widoczny plik JSON ze 100 wierszami, które wyglądają jak następujące przykładowe wiersze: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Zadanie usługi Azure Stream Analytics odebrało dane wejściowe z Centrum zdarzeń i przechowywać je w magazynie obiektów blob platformy Azure w tym scenariuszu. 



## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak przesyłać strumieniowo do centrów zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integracja platformy Kafka Connect z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Łączenie strumieni Akka z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) 
