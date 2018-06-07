---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Wprowadzenie do wysyłania do usługi Event Hubs przy użyciu języka Java
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.openlocfilehash: 6d3bf0b8ac5c5bdc7bf3deda21e800fe3cc6be2e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626415"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka Java

Event Hubs to wysoce skalowalny system przyjmowania może obsługiwać miliony zdarzeń na sekundę, włączanie aplikacji do przetwarzania i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Po zebraniu danych do Centrum zdarzeń, można przekształcić i przechowywanie danych przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym lub magazynu klastra.

Aby uzyskać więcej informacji, zobacz [Przegląd usługi Event Hubs][Event Hubs overview].

W tym samouczku przedstawiono sposób wysyłania zdarzeń do Centrum zdarzeń za pomocą aplikacji konsoli w języku Java. Aby odbierać zdarzenia przy użyciu biblioteki hosta procesora zdarzeń Java, zobacz [w tym artykule](event-hubs-java-get-started-receive-eph.md), lub kliknij odpowiedni język odbierania w tabeli po lewej stronie zawartości.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka niezbędne są następujące wymagania wstępne:

* Środowisko projektowe Java. W tym samouczku używana [Eclipse](https://www.eclipse.org/).
* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto][] przed rozpoczęciem.

Kod w tym samouczku jest oparta na [próbki SimpleSend GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), który można sprawdzić, aby wyświetlić pełny działającą aplikację.

## <a name="send-events-to-event-hubs"></a>Wysyłanie zdarzeń do usługi Event Hubs

Java biblioteki klienta usługi Event hubs jest dostępny do użytku w projekty Maven z [Maven centralnym repozytorium](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Możesz odwoływać się tę bibliotekę przy użyciu następujących deklaracji zależności w pliku projektu Maven. Bieżąca wersja to 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Dla różnych typów środowiska kompilacji, można jawnie uzyskać najnowsze wydanych JAR pliki z [Maven centralnym repozytorium](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Wydawca zdarzeń proste, można zaimportować *com.microsoft.azure.eventhubs* pakiet dla klasy klienta usługi Event Hubs i *com.microsoft.azure.servicebus* pakietu dla klasy narzędzi, takich jak Typowe wyjątki, które są współużytkowane z klienta poczty e-mail usługi Azure Service Bus. 

### <a name="declare-the-send-class"></a>Deklarowanie klasy wysyłania

Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Nazwa klasy `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Parametry połączenia konstrukcji

Klasa ConnectionStringBuilder służy do tworzenia wartości ciągu połączenia do przekazania do wystąpienia klienta usługi Event Hubs. Zastąp symbole zastępcze wartości, które uzyskane podczas tworzenia Centrum przestrzeni nazw i zdarzenia:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Wysyłanie zdarzeń

Tworzenie zdarzenia pojedynczej poprzez przekształcanie ciąg do jego kodowania UTF-8 bajtów. Następnie utwórz nowe wystąpienie klienta usługi Event Hubs z parametrów połączenia i wysłać wiadomość:   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Zdarzenia są rejestrowane za pomocą klasy EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[Bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

