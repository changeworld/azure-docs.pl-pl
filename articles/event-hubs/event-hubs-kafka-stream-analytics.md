---
title: 'Samouczek: przetwarzanie Apache Kafka zdarzeń przy użyciu usługi Stream Analytics — Azure Event Hubs'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 7801b3252ab13df1f92e7aa5e0eba071195cb76c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720617"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Samouczek: proces Apache Kafka dla zdarzeń Event Hubs za pomocą usługi Stream Analytics 
W tym artykule pokazano, jak strumieniowo przesyłać dane do Event Hubs z włączoną obsługą Kafka i przetwarzać je z Azure Stream Analytics. Przeprowadzi Cię przez następujące kroki: 

1. Utwórz Kafka Event Hubs przestrzeń nazw.
2. Utwórz klienta Kafka, który wysyła komunikaty do centrum zdarzeń.
3. Utwórz zadanie Stream Analytics, które kopiuje dane z centrum zdarzeń do magazynu obiektów blob platformy Azure. 

Nie trzeba zmieniać klientów protokołu ani uruchamiać własnych klastrów, gdy używasz punktu końcowego Kafka uwidocznionego przez centrum zdarzeń. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html) i powyżej. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Git](https://www.git-scm.com/)
* **Konto usługi Azure Storage**. Jeśli go nie masz, [Utwórz go](../storage/common/storage-quickstart-create-account.md) przed kontynuowaniem. Zadanie Stream Analytics w tym instruktażu przechowuje dane wyjściowe w usłudze Azure Blob Storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com), a następnie kliknij pozycję **Utwórz zasób** w lewym górnym rogu ekranu.
2. Wyszukaj **Event Hubs** i wybierz poniższe opcje:
    
    ![Wyszukiwanie usługi Event Hubs w portalu](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Na stronie **Event Hubs** wybierz pozycję **Utwórz**.
4. Na stronie **Tworzenie przestrzeni nazw** wykonaj następujące czynności: 
    1. Podaj unikatową **nazwę** przestrzeni nazw. 
    2. Wybierz **warstwę cenową**. 
    3. Wybierz pozycję **Włącz Kafka**. Ten krok to **ważny** krok. 
    4. Wybierz swoją **subskrypcję** , w której chcesz utworzyć przestrzeń nazw centrum zdarzeń. 
    5. Utwórz nową **grupę zasobów** lub wybierz istniejącą grupę zasobów. 
    6. Wybierz **lokalizację**. 
    7. Kliknij pozycję **Utwórz**.
    
        ![Tworzenie przestrzeni nazw](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. W **komunikacie powiadomienia**wybierz **nazwę grupy zasobów**. 

    ![Tworzenie przestrzeni nazw](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Wybierz **przestrzeń nazw centrum zdarzeń** w grupie zasobów. 
2. Po utworzeniu przestrzeni nazw wybierz pozycję **zasady dostępu współdzielonego** w obszarze **Ustawienia**.

    ![Klikanie pozycji Zasady dostępu współużytkowanego](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Można wybrać domyślną wartość **RootManageSharedAccessKey** lub dodać nowe zasady. Kliknij nazwę zasad i skopiuj **Parametry połączenia**. Aby skonfigurować klienta Kafka, należy użyć parametrów połączenia. 
    
    ![Wybieranie zasad](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Teraz możesz przesyłać strumieniowo zdarzenia z aplikacji, które używają protokołu platformy Kafka, do usługi Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Wysyłanie komunikatów za pomocą Kafka w Event Hubs

1. Sklonuj [Event Hubs Azure dla repozytorium Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) na komputerze.
2. Przejdź do folderu: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Zaktualizuj szczegóły konfiguracji producenta w `src/main/resources/producer.config`. Określ **nazwę** i **Parametry połączenia** dla **przestrzeni nazw centrum zdarzeń**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Przejdź do `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`i Otwórz plik **TestDataReporter. Java** w wybranym edytorze. 
6. Skomentuj następujący wiersz kodu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Dodaj następujący wiersz kodu zamiast kodu komentarza: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ten kod wysyła dane zdarzenia w formacie **JSON** . Podczas konfigurowania danych wejściowych dla zadania Stream Analytics należy określić kod JSON jako format danych wejściowych. 
7. **Uruchom producenta** i prześlij strumień do Event Hubs z włączoną obsługą Kafka. Na komputerze z systemem Windows, w przypadku korzystania z **wiersza polecenia środowiska Node. js**, przed uruchomieniem tych poleceń przejdź do folderu `azure-event-hubs-for-kafka/quickstart/java/producer`. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Sprawdź, czy centrum zdarzeń odbiera dane

1. Wybierz **Event Hubs** w obszarze **jednostki**. Upewnij się, że widzisz centrum zdarzeń o nazwie **test**. 

    ![Centrum zdarzeń — test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Potwierdź, że zobaczysz komunikaty przychodzące do centrum zdarzeń. 

    ![Centrum zdarzeń — komunikaty](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Przetwarzanie danych zdarzeń przy użyciu zadania Stream Analytics
W tej sekcji utworzysz zadanie Azure Stream Analytics. Klient Kafka wysyła zdarzenia do centrum zdarzeń. Tworzysz zadanie Stream Analytics, które pobiera dane zdarzeń jako dane wejściowe i wyprowadza je do magazynu obiektów blob platformy Azure. Jeśli nie masz **konta usługi Azure Storage**, [Utwórz je](../storage/common/storage-quickstart-create-account.md).

Zapytanie w Stream Analytics zadania przejdzie przez dane bez wykonywania żadnej analizy. Można utworzyć zapytanie, które przekształca dane wejściowe w celu wygenerowania danych wyjściowych w innym formacie lub z uzyskaniem szczegółowych informacji.  

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics 

1. Wybierz pozycję **+ Utwórz zasób** w [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Analiza** w menu **portalu Azure Marketplace** , a następnie wybierz pozycję **Stream Analytics zadanie**. 
3. Na stronie **nowy Stream Analytics** wykonaj następujące czynności: 
    1. Wprowadź **nazwę** zadania. 
    2. Wybierz swoją **subskrypcję**.
    3. Wybierz pozycję **Utwórz nowy** dla **grupy zasobów** , a następnie wprowadź nazwę. Można również **użyć istniejącej** grupy zasobów. 
    4. Wybierz **lokalizację** dla zadania.
    5. Wybierz pozycję **Utwórz** , aby utworzyć zadanie. 

        ![Nowe zadanie Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. W komunikacie powiadomienia wybierz pozycję **Przejdź do zasobu** , aby wyświetlić stronę **zadania Stream Analytics** . 
2. Wybierz pozycję **dane wejściowe** w sekcji **topologia zadania** w menu po lewej stronie.
3. Wybierz pozycję **Dodaj strumień wejściowy**, a następnie wybierz pozycję **centrum zdarzeń**. 

    ![Dodaj centrum zdarzeń jako dane wejściowe](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stronie Konfiguracja **danych wejściowych centrum zdarzeń** wykonaj następujące czynności: 

    1. Określ **alias** dla danych wejściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Wybierz utworzoną wcześniej **przestrzeń nazw centrum zdarzeń** . 
    4. Wybierz **test** **centrum zdarzeń**. 
    5. Wybierz pozycję **Zapisz**. 

        ![Konfiguracja danych wejściowych centrum zdarzeń](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania 

1. Wybierz pozycję dane **wyjściowe** w sekcji **topologia zadania** w menu. 
2. Wybierz pozycję **+ Dodaj** na pasku narzędzi i wybierz pozycję **BLOB Storage**
3. Na stronie Ustawienia wyjściowe magazynu obiektów BLOB wykonaj następujące czynności: 
    1. Określ **alias** dla danych wyjściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure. 
    3. Wybierz **konto usługi Azure Storage**. 
    4. Wprowadź **nazwę kontenera** , w którym są przechowywane dane wyjściowe z kwerendy Stream Analytics.
    5. Wybierz pozycję **Zapisz**.

        ![Blob Storage konfiguracja wyjściowa](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiowanie zapytania
Po skonfigurowaniu zadania usługi Stream Analytics do odczytu przychodzącego strumienia danych następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). W tym instruktażu zdefiniujesz zapytanie, które przekazuje dane bez wykonywania transformacji.

1. Wybierz **zapytanie**.
2. W oknie zapytania Zastąp `[YourOutputAlias]` utworzonym wcześniej aliasem wyjściowym.
3. Zastąp `[YourInputAlias]` utworzonym wcześniej aliasem wejściowym. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Zapytanie](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Z menu po lewej stronie wybierz pozycję **Przegląd** . 
2. Wybierz pozycję **Uruchom**. 

    ![Menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stronie **Uruchamianie zadania** wybierz pozycję **Rozpocznij**. 

    ![Strona uruchamiania zadania](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Poczekaj, aż stan zadania zmieni się z **Rozpoczynanie** na **uruchomione**. 

    ![Stan zadania — uruchomiono](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testowanie scenariusza
1. Uruchom ponownie **producenta Kafka** , aby wysłać zdarzenia do centrum zdarzeń. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Upewnij się, że w **usłudze Azure Blob Storage**są generowane **dane wyjściowe** . W kontenerze zostanie wyświetlony plik JSON z 100 wierszami, które wyglądają jak w następujących przykładowych wierszach: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Zadanie Azure Stream Analytics otrzymało dane wejściowe z centrum zdarzeń i zapisano je w magazynie obiektów blob platformy Azure w tym scenariuszu. 



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
