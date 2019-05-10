---
title: Jak używać kolejek usługi Azure Service Bus przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure. Przykłady kodu napisane w języku Java.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 032c4af0e36626881b514573cc4a5f966e8c2077
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510318"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Jak używać kolejek usługi Service Bus przy użyciu języka Java
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
W tym samouczku dowiesz się, jak tworzyć aplikacje Java do wysyłania komunikatów i odbiera komunikaty z kolejki usługi Service Bus. 

> [!NOTE]
> Przykładów w języku Java można znaleźć w witrynie GitHub w [repozytorium azure-service-bus](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Jeśli nie masz kolejki chcesz pracować, wykonaj czynności opisane w [użycia usługi Azure portal można utworzyć kolejki usługi Service Bus](service-bus-quickstart-portal.md) artykuł, aby utworzyć kolejkę.
    1. Przeczytaj szybkiego **Przegląd** usługi Service Bus **kolejek**. 
    2. Tworzenie usługi Service Bus **przestrzeni nazw**. 
    3. Pobierz **parametry połączenia**.
    4. Tworzenie usługi Service Bus **kolejki**.
3. Zainstaluj [zestaw Azure SDK dla języka Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Upewnij się, że zainstalowano [zestawu Azure SDK dla języka Java] [ Azure SDK for Java] przed kompilacją w tym przykładzie. Jeśli używasz środowiska Eclipse, możesz zainstalować [Azure Toolkit for Eclipse] [ Azure Toolkit for Eclipse] zawierającego zestaw Azure SDK dla języka Java. Następnie można dodać **biblioteki systemu Microsoft Azure dla języka Java** do projektu:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Dodaj następujący kod `import` instrukcji na początku pliku Java:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysyłać komunikaty do kolejki usługi Service Bus, aplikacja tworzy **QueueClient** obiektu, a następnie asynchronicznie wysyła wiadomości. Poniższy kod przedstawia sposób wysyłania komunikatu kolejki, który został utworzony za pośrednictwem portalu.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Komunikaty wysyłane do i odbierane z usługi Service Bus są kolejki wystąpień [komunikat](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) klasy. Obiekty wiadomości mają zestaw właściwości standardowych (takich jak etykiety i TimeToLive), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja możne ustawić treść komunikatu przez przekazanie dowolnego obiektu podlegającego serializacji do konstruktora wiadomości, a odpowiedni element serializujący zostanie następnie użyte do serializacji obiektu. Alternatywnie można udostępnić **języka java. WE/WY. InputStream** obiektu.


Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Podstawowym sposobem na odbieranie komunikatów z kolejki jest użycie **ServiceBusContract** obiektu. Odebrane komunikaty mogą pracować w dwóch różnych trybach: **ReceiveAndDelete** i **PeekLock**.

Korzystając z **ReceiveAndDelete** trybie odbieranie jest operacją pojedynczego zrzutu — oznacza to, gdy Usługa Service Bus odbiera żądanie odczytu komunikatu w kolejce, jego oznacza komunikat jako wykorzystany i zwraca go do aplikacji. **ReceiveAndDelete** trybu (jest to domyślny tryb) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem.
Usługa Service Bus oznaczyła komunikat jako wykorzystany, następnie gdy aplikacja zostanie ponownie uruchomiona i ponownie rozpocznie korzystanie z komunikatów, jego ma utracony komunikat, który został wykorzystany przed awarią.

W **PeekLock** trybie odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody **Usuń** na odebranym komunikacie. Gdy Usługa Service Bus widzi **Usuń** wywołanie, jego oznacza komunikat jako wykorzystany i usuń go z kolejki.

W poniższym przykładzie pokazano, jak mogą być odbierane wiadomości i przetworzone przy użyciu **PeekLock** tryb (nie tryb domyślny). W poniższym przykładzie nie nieskończonej pętli i przetwarzania wiadomości przychodzące do naszych `TestQueue`:

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać **unlockMessage** metody dla odebranego komunikatu (zamiast **deleteMessage** metody). Powoduje to odblokowanie komunikatu w kolejce przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i sprawia, że dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed **deleteMessage** wystawić żądania, a następnie wiadomości są przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*, co oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **getMessageId** metoda wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz [kolejki, tematy i subskrypcje] [ Queues, topics, and subscriptions] Aby uzyskać więcej informacji.

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: https://docs.microsoft.com/java/api/overview/azure/
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
