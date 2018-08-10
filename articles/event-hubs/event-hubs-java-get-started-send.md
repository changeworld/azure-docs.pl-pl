---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Wprowadzenie do wysyłania do usługi Event Hubs przy użyciu języka Java
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: shvija
ms.openlocfilehash: 6217f507b83325acb9a5062aada150fa6f8bc5d2
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006624"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka Java

Event Hubs to wysoce skalowalny system przyjmowania danych może obsługiwać miliony zdarzeń na sekundę, umożliwiając aplikacji przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Po zebraniu danych do Centrum zdarzeń, można przekształcić i przechowywanie danych przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym lub klastra magazynu.

Aby uzyskać więcej informacji, zobacz [Przegląd usługi Event Hubs][Event Hubs overview].

W tym samouczku pokazano, jak do wysyłania zdarzeń do Centrum zdarzeń za pomocą aplikacji konsolowej w języku Java. Aby odbierać zdarzenia przy użyciu biblioteki hosta procesora zdarzeń języka Java, zobacz [w tym artykule](event-hubs-java-get-started-receive-eph.md), lub kliknij odpowiedni język odbierający w spisie treści po lewej stronie.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka potrzebne są następujące wymagania wstępne:

* Środowisko projektowe Java. W tym samouczku [Eclipse](https://www.eclipse.org/).
* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].

Kod w ramach tego samouczka jest oparta na [przykładowe SimpleSend GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), który można sprawdzić, aby wyświetlić pełną działającą aplikację.

## <a name="send-events-to-event-hubs"></a>Wysyłanie zdarzeń do usługi Event Hubs

Biblioteki klienta Java dla usługi Event Hubs jest dostępna do użytku w projektach narzędzia Maven z [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Możesz odwoływać się tę bibliotekę przy użyciu poniższej deklaracji zależności wewnątrz pliku projektu Maven. Bieżąca wersja to 1.0.1:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.1</version>
</dependency>
```

Dla różnych typów środowisk kompilacji można jawnie uzyskać najnowsze plików JAR, korzystając z [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Wydawca zdarzeń prostego, można zaimportować *com.microsoft.azure.eventhubs* pakietu dotyczące klas klientów usługi Event Hubs i *com.microsoft.azure.servicebus* pakietu dla klasy narzędzi, takich jak Typowe wyjątki, które są udostępniane za pomocą klienta obsługi komunikatów usługi Azure Service Bus. 

### <a name="declare-the-send-class"></a>Deklarowanie klasy wysyłania

Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Nazwa klasy `SimpleSend`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Utworzyć parametry połączenia

Klasa ConnectionStringBuilder służy do konstruowania wartość parametrów połączenia w celu przekazania do wystąpienia klienta usługi Event Hubs. Zastąp symbole zastępcze wartości, które zostały uzyskane podczas tworzenia przestrzeni nazw i Centrum zdarzeń:

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>Wysyłanie zdarzeń

Utwórz zdarzenie pojedynczej poprzez przekształcanie ciąg do jego kodowania UTF-8 bajtów. Następnie utwórz nowe wystąpienie klienta usługi Event Hubs z parametrów połączenia i wysyłanie wiadomości:   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Odbieranie zdarzeń za pomocą klasy EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

