---
title: Azure Event Hubs — przetwarzanie zdarzeń Apache Kafka
description: 'Samouczek: w tym artykule przedstawiono sposób przetwarzania zdarzeń Kafka, które są pozyskiwane za pośrednictwem centrów zdarzeń przy użyciu Azure Stream Analytics'
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
ms.openlocfilehash: 8047ac5d3db2db230a00583e888a4afea3b282b9
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969369"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Samouczek: proces Apache Kafka dla zdarzeń Event Hubs za pomocą usługi Stream Analytics 
W tym artykule przedstawiono sposób przesyłanie strumieniowe danych do komputerów z obsługą platformy Kafka z usługi Event Hubs i przetworzyć te dane za pomocą usługi Azure Stream Analytics. Przeprowadza użytkownika przez następujące kroki: 

1. Tworzenie platformy Kafka włączona przestrzeń nazw usługi Event Hubs.
2. Tworzenie klienta platformy Kafka, która wysyła komunikaty do Centrum zdarzeń.
3. Utwórz zadanie usługi Stream Analytics, które kopiuje dane z Centrum zdarzeń do usługi Azure blob storage. 

Nie trzeba zmienić klientów protokołu lub uruchamiaj swoje własne klastry, korzystając z punktu końcowego platformy Kafka, udostępnianego przez Centrum zdarzeń. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html) i powyżej. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Usługa Git](https://www.git-scm.com/)
* **Konto usługi Azure Storage**. Jeśli go nie masz, [Utwórz go](../storage/common/storage-account-create.md) przed kontynuowaniem. Zadanie usługi Stream Analytics, w tym przewodniku przechowuje dane wyjściowe w usłudze Azure blob storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka
Podczas tworzenia warstwy **standardowa** Event Hubs przestrzeń nazw, punkt końcowy Kafka dla przestrzeni nazw jest automatycznie włączany. Zdarzenia z aplikacji korzystających z protokołu Kafka można przesyłać strumieniowo do warstwy Standardowa Event Hubs. Postępuj zgodnie z instrukcjami krok po kroku w temacie [Tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md) , aby utworzyć Event Hubs przestrzeni nazw w warstwie **standardowa** . 

> [!NOTE]
> Event Hubs dla Kafka jest dostępny tylko w warstwach **standardowa** i **dedykowana** . Warstwa **podstawowa** nie obsługuje Kafka na Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Wysyłanie komunikatów z platformą Kafka w usłudze Event Hubs

1. Sklonuj [Event Hubs Azure dla repozytorium Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) na komputerze.
2. Przejdź do folderu: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Zaktualizuj szczegóły konfiguracji producenta w `src/main/resources/producer.config`. Określ **nazwę** i **Parametry połączenia** dla **przestrzeni nazw centrum zdarzeń**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Przejdź do `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`i Otwórz plik **TestDataReporter. Java** w wybranym edytorze. 
6. Komentarz następujący wiersz kodu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Dodaj następujący wiersz kodu, zamiast skomentowanej kodu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ten kod wysyła dane zdarzenia w formacie **JSON** . Po skonfigurowaniu dane wejściowe dla zadania usługi Stream Analytics, należy określić w formacie dla danych wejściowych JSON. 
7. **Uruchom producenta** i prześlij strumień do Event Hubs z włączoną obsługą Kafka. Na komputerze z systemem Windows, w przypadku korzystania z **wiersza polecenia środowiska Node. js**, przed uruchomieniem tych poleceń przejdź do folderu `azure-event-hubs-for-kafka/quickstart/java/producer`. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Sprawdź, czy tego Centrum zdarzeń odbiera dane

1. Wybierz **Event Hubs** w obszarze **jednostki**. Upewnij się, że widzisz centrum zdarzeń o nazwie **test**. 

    ![Centrum zdarzeń — testowanie](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Upewnij się, że widzisz wiadomości przychodzących Centrum zdarzeń. 

    ![Centrum zdarzeń — komunikaty](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Przetwarzanie danych zdarzeń za pomocą zadania usługi Stream Analytics
W tej sekcji opisano tworzenie zadania usługi Azure Stream Analytics. Klient platformy Kafka wysyła zdarzenia do Centrum zdarzeń. Możesz utworzyć zadanie usługi Stream Analytics, która pobiera dane zdarzeń jako dane wejściowe i wysyła go do usługi Azure blob storage. Jeśli nie masz **konta usługi Azure Storage**, [Utwórz je](../storage/common/storage-account-create.md).

Zapytanie zadania usługi Stream Analytics przechodzi przez dane bez przeprowadzania żadnych analizy. Można utworzyć zapytanie, które przekształca dane wejściowe, aby wygenerować dane wyjściowe w innym formacie lub za pomocą zyskały szczegółowych informacji.  

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics 

1. Wybierz pozycję **+ Utwórz zasób** w [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Analiza** w menu **portalu Azure Marketplace** , a następnie wybierz pozycję **Stream Analytics zadanie**. 
3. Na stronie **nowy Stream Analytics** wykonaj następujące czynności: 
    1. Wprowadź **nazwę** zadania. 
    2. Wybierz swoją **subskrypcję**.
    3. Wybierz pozycję **Utwórz nowy** dla **grupy zasobów** , a następnie wprowadź nazwę. Można również **użyć istniejącej** grupy zasobów. 
    4. Wybierz **lokalizację** dla zadania.
    5. Wybierz pozycję **Utwórz** , aby utworzyć zadanie. 

        ![Nowe zadanie usługi Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. W komunikacie powiadomienia wybierz pozycję **Przejdź do zasobu** , aby wyświetlić stronę **zadania Stream Analytics** . 
2. Wybierz pozycję **dane wejściowe** w sekcji **topologia zadania** w menu po lewej stronie.
3. Wybierz pozycję **Dodaj strumień wejściowy**, a następnie wybierz pozycję **centrum zdarzeń**. 

    ![Dodaj Centrum zdarzeń jako dane wejściowe](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stronie Konfiguracja **danych wejściowych centrum zdarzeń** wykonaj następujące czynności: 

    1. Określ **alias** dla danych wejściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Wybierz utworzoną wcześniej **przestrzeń nazw centrum zdarzeń** . 
    4. Wybierz **test** **centrum zdarzeń**. 
    5. Wybierz pozycję **Zapisz**. 

        ![Konfiguracja danych wejściowych w Centrum zdarzeń](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania 

1. Wybierz pozycję dane **wyjściowe** w sekcji **topologia zadania** w menu. 
2. Wybierz pozycję **+ Dodaj** na pasku narzędzi i wybierz pozycję **BLOB Storage**
3. Na stronie Ustawienia danych wyjściowych magazynu obiektów Blob wykonaj następujące czynności: 
    1. Określ **alias** dla danych wyjściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure. 
    3. Wybierz **konto usługi Azure Storage**. 
    4. Wprowadź **nazwę kontenera** , w którym są przechowywane dane wyjściowe z kwerendy Stream Analytics.
    5. Wybierz pozycję **Zapisz**.

        ![Konfiguracja danych wyjściowych magazynu obiektów blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiowanie zapytania
Po skonfigurowaniu zadania usługi Stream Analytics do odczytu przychodzącego strumienia danych następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). W tym przewodniku należy zdefiniować zapytanie, które przechodzą przez dane bez przeprowadzania dowolnego przekształcenia.

1. Wybierz **zapytanie**.
2. W oknie zapytania Zastąp `[YourOutputAlias]` utworzonym wcześniej aliasem wyjściowym.
3. Zastąp `[YourInputAlias]` utworzonym wcześniej aliasem wejściowym. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Zapytanie](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Z menu po lewej stronie wybierz pozycję **Przegląd** . 
2. Wybierz pozycję **Uruchom**. 

    ![Start menu](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stronie **Uruchamianie zadania** wybierz pozycję **Rozpocznij**. 

    ![Zadanie strony początkowej](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Poczekaj, aż stan zadania zmieni się z **Rozpoczynanie** na **uruchomione**. 

    ![Stan zadania — uruchamianie](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testowanie scenariusza
1. Uruchom ponownie **producenta Kafka** , aby wysłać zdarzenia do centrum zdarzeń. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Upewnij się, że w **usłudze Azure Blob Storage**są generowane **dane wyjściowe** . Zostanie wyświetlony plik w formacie JSON w kontenerze przy użyciu 100 wierszy, które mają postać kolejnych wierszach przykładowe: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Zadanie usługi Azure Stream Analytics otrzymanych danych wejściowych z Centrum zdarzeń i zapisana w usłudze Azure blob storage, w tym scenariuszu. 



## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego usługi Event Hubs z obsługą platformy Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create-kafka-enabled.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie narzędzia Kafka Connect z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-connect-tutorial.md)
- [Łączenie biblioteki Akka Streams z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) 
