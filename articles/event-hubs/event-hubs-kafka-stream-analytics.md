---
title: Przetwarzanie zdarzeń platformy Apache Kafka za pomocą usługi Stream analytics — usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak przetwarzać zdarzenia platformy Kafka, które są pozyskiwane za pośrednictwem usługi event hubs za pomocą usługi Azure Stream Analytics
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: b6bba4ed45530ba66a1adde274022a80091cd199
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748141"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Przetwarzanie zdarzeń platformy Apache Kafka dla usługi Event Hubs za pomocą usługi Stream Analytics 
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
* **Konta usługi Azure Storage**. Jeśli nie masz, [utworzyć](../storage/common/storage-quickstart-create-account.md) przed kontynuacją. Zadanie usługi Stream Analytics, w tym przewodniku przechowuje dane wyjściowe w usłudze Azure blob storage. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs z obsługą platformy Kafka

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.
2. Wyszukaj **usługi Event Hubs** i wybrać opcje pokazano poniżej:
    
    ![Wyszukiwanie usługi Event Hubs w portalu](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Na **usługi Event Hubs** wybierz opcję **Utwórz**.
4. Na **tworzenie Namespace** wykonaj następujące czynności: 
    1. Podaj unikatową **nazwa** dla przestrzeni nazw. 
    2. Wybierz **warstwy cenowej**. 
    3. Wybierz **Włącz Kafka**. Ten krok jest **ważne** kroku. 
    4. Wybierz swoje **subskrypcji** w którym ma przestrzeń nazw Centrum zdarzeń ma zostać utworzony. 
    5. Utwórz nową **grupy zasobów** lub wybierz istniejącą grupę zasobów. 
    6. Wybierz **lokalizacji**. 
    7. Kliknij pozycję **Utwórz**.
    
        ![Tworzenie przestrzeni nazw](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. W **wiadomość z powiadomieniem**, wybierz opcję **nazwy grupy zasobów**. 

    ![Tworzenie przestrzeni nazw](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Wybierz **przestrzeń nazw Centrum zdarzeń** w grupie zasobów. 
2. Po utworzeniu przestrzeni nazw wybierz **zasady dostępu współdzielonego** w obszarze **ustawienia**.

    ![Klikanie pozycji Zasady dostępu współużytkowanego](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Można wybrać domyślną wartość **RootManageSharedAccessKey** lub dodać nowe zasady. Kliknij nazwę zasad, a kopia **parametry połączenia**. Parametry połączenia umożliwiają konfigurowanie klienta platformy Kafka. 
    
    ![Wybieranie zasad](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Teraz możesz przesyłać strumieniowo zdarzenia z aplikacji, które używają protokołu platformy Kafka, do usługi Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Wysyłanie komunikatów z platformą Kafka w usłudze Event Hubs

1. Klonuj [usługi Azure Event Hubs dla platformy Kafka repozytorium](https://github.com/Azure/azure-event-hubs-for-kafka) na komputer.
2. Przejdź do folderu: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Aktualizowanie szczegółów konfiguracji dla producentów w `src/main/resources/producer.config`. Określ **nazwa** i **parametry połączenia** dla **przestrzeń nazw Centrum zdarzeń**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Przejdź do `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`, a następnie otwórz **TestDataReporter.java** plik w wybranym edytorze. 
6. Komentarz następujący wiersz kodu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Dodaj następujący wiersz kodu, zamiast skomentowanej kodu: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ten kod wysyła dane zdarzeń w **JSON** formatu. Po skonfigurowaniu dane wejściowe dla zadania usługi Stream Analytics, należy określić w formacie dla danych wejściowych JSON. 
7. **Uruchom producenta** i dane ze strumienia do komputerów z obsługą platformy Kafka z usługi Event Hubs. Na komputerze Windows, korzystając z **wiersza polecenia Node.js**, przełącz się do `azure-event-hubs-for-kafka/quickstart/java/producer` folderu przed uruchomieniem tych poleceń. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Sprawdź, czy tego Centrum zdarzeń odbiera dane

1. Wybierz **usługi Event Hubs** w obszarze **jednostek**. Upewnij się, że Centrum zdarzeń o nazwie **testu**. 

    ![Centrum zdarzeń — testowanie](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Upewnij się, że widzisz wiadomości przychodzących Centrum zdarzeń. 

    ![Centrum zdarzeń — komunikaty](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Przetwarzanie danych zdarzeń za pomocą zadania usługi Stream Analytics
W tej sekcji opisano tworzenie zadania usługi Azure Stream Analytics. Klient platformy Kafka wysyła zdarzenia do Centrum zdarzeń. Możesz utworzyć zadanie usługi Stream Analytics, która pobiera dane zdarzeń jako dane wejściowe i wysyła go do usługi Azure blob storage. Jeśli nie masz **konta usługi Azure Storage**, [utworzyć](../storage/common/storage-quickstart-create-account.md).

Zapytanie zadania usługi Stream Analytics przechodzi przez dane bez przeprowadzania żadnych analizy. Można utworzyć zapytanie, które przekształca dane wejściowe, aby wygenerować dane wyjściowe w innym formacie lub za pomocą zyskały szczegółowych informacji.  

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics 

1. Wybierz **+ Utwórz zasób** w [witryny Azure portal](https://portal.azure.com).
2. Wybierz **Analytics** w **portalu Azure Marketplace** menu, a następnie wybierz **zadania usługi Stream Analytics**. 
3. Na **nowe Stream Analytics** wykonaj następujące czynności: 
    1. Wprowadź **nazwa** dla zadania. 
    2. Wybierz swoje **subskrypcji**.
    3. Wybierz **Utwórz nową** dla **grupy zasobów** i wprowadź nazwę. Możesz również **Użyj istniejącego** grupy zasobów. 
    4. Wybierz **lokalizacji** dla zadania.
    5. Wybierz **Utwórz** do utworzenia zadania. 

        ![Nowe zadanie usługi Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. W komunikacie powiadomienia wybierz **przejdź do zasobu** się **zadania usługi Stream Analytics** strony. 
2. Wybierz **dane wejściowe** w **TOPOLOGIA zadań** sekcji, w menu po lewej stronie.
3. Wybierz **Dodaj wejście strumienia**, a następnie wybierz pozycję **Centrum zdarzeń**. 

    ![Dodaj Centrum zdarzeń jako dane wejściowe](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na **danych wejściowych z Centrum zdarzeń** konfiguracji wykonaj następujące czynności: 

    1. Określ **alias** dla danych wejściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Wybierz **przestrzeń nazw Centrum zdarzeń** utworzonej wcześniej. 
    4. Wybierz **test** dla **Centrum zdarzeń**. 
    5. Wybierz pozycję **Zapisz**. 

        ![Konfiguracja danych wejściowych w Centrum zdarzeń](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania 

1. Wybierz **dane wyjściowe** w **TOPOLOGIA zadań** sekcji, w menu. 
2. Wybierz **+ Dodaj** na pasku narzędzi i wybierz **magazynu obiektów Blob**
3. Na stronie Ustawienia danych wyjściowych magazynu obiektów Blob wykonaj następujące czynności: 
    1. Określ **alias** dla danych wyjściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure. 
    3. Wybierz swoje **konta usługi Azure Storage**. 
    4. Wprowadź **nazwa kontenera** która przechowuje dane wyjściowe z zapytań usługi Stream Analytics.
    5. Wybierz pozycję **Zapisz**.

        ![Konfiguracja danych wyjściowych magazynu obiektów blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiowanie zapytania
Po skonfigurowaniu zadania usługi Stream Analytics do odczytu przychodzącego strumienia danych następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). W tym przewodniku należy zdefiniować zapytanie, które przechodzą przez dane bez przeprowadzania dowolnego przekształcenia.

1. Wybierz **zapytania**.
2. W oknie zapytania Zastąp `[YourOutputAlias]` za pomocą aliasu danych wyjściowych, została utworzona wcześniej.
3. Zastąp `[YourInputAlias]` z alias danych wejściowych, została utworzona wcześniej. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Zapytanie](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Wybierz **Przegląd** w menu po lewej stronie. 
2. Wybierz **Start**. 

    ![Start menu](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na **zadanie rozpoczęcia** wybierz opcję **Start**. 

    ![Zadanie strony początkowej](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Zaczekaj, aż stan zadania zmieni się z **od** do **systemem**. 

    ![Stan zadania — uruchamianie](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testowanie scenariusza
1. Uruchom **Kafka producer** ponownie, aby wysyłać zdarzenia do Centrum zdarzeń. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Upewnij się, że **dane wyjściowe** jest generowany w **usługi Azure blob storage**. Zostanie wyświetlony plik w formacie JSON w kontenerze przy użyciu 100 wierszy, które mają postać kolejnych wierszach przykładowe: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Zadanie usługi Azure Stream Analytics otrzymanych danych wejściowych z Centrum zdarzeń i zapisana w usłudze Azure blob storage, w tym scenariuszu. 



## <a name="next-steps"></a>Kolejne kroki
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
