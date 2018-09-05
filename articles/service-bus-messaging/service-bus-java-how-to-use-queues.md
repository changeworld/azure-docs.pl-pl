---
title: Jak używać kolejek usługi Azure Service Bus przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure. Przykłady kodu napisane w języku Java.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: e4099c8228e9434276242a3c49ebcb4fc2e995b2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696469"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Jak używać kolejek usługi Service Bus przy użyciu języka Java
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym artykule opisano sposób używania kolejek usługi Service Bus. Przykłady są zapisywane w środowiskach Java i użyj [zestawu Azure SDK dla języka Java][Azure SDK for Java]. Omówione scenariusze obejmują **tworzenie kolejek**, **wysyłanie i odbieranie komunikatów**, i **usuwanie kolejek**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Upewnij się, że zainstalowano [zestawu Azure SDK dla języka Java] [ Azure SDK for Java] przed kompilacją w tym przykładzie. Jeśli używasz środowiska Eclipse, możesz zainstalować [Azure Toolkit for Eclipse] [ Azure Toolkit for Eclipse] zawierającego zestaw Azure SDK dla języka Java. Następnie można dodać **biblioteki systemu Microsoft Azure dla języka Java** do projektu:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Dodaj następujący kod `import` instrukcji na początku pliku Java:

```java
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Tworzenie kolejki
Operacje zarządzania kolejkami usługi Service Bus można wykonywać za pomocą **ServiceBusContract** klasy. A **ServiceBusContract** obiekt jest konstruowany przy użyciu prawidłowej konfiguracji, który hermetyzuje tokenu sygnatury dostępu Współdzielonego z uprawnieniami do zarządzania nim, a **ServiceBusContract** klasy jest jedynym punktem Komunikacja z platformą Azure.

**ServiceBusService** klasa dostarcza metody do tworzenia, wyliczania i usuwania kolejek. Poniżej przedstawiono przykład sposobu **ServiceBusService** obiektu można utworzyć kolejkę o nazwie `TestQueue`, za pomocą przestrzeni nazw o nazwie `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Istnieją metody na `QueueInfo` umożliwiające właściwości kolejki, aby dostroić (na przykład: można ustawić czasu wygaśnięcia (TTL) wartość domyślna ma zostać zastosowany do komunikatów wysłanych do kolejki). Poniższy przykład pokazuje, jak utworzyć kolejkę o nazwie `TestQueue` o maksymalnym rozmiarze 5 GB:

````java
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Należy zauważyć, że można użyć `listQueues` metody **ServiceBusContract** obiekty do sprawdzenia, czy kolejka o określonej nazwie już istnieje w przestrzeni nazw usługi.

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki usługi Service Bus, aplikacja uzyskuje **ServiceBusContract** obiektu. Poniższy kod przedstawia sposób wysłania komunikatu `TestQueue` utworzone wcześniej w kolejce `HowToSample` przestrzeni nazw:

```java
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Komunikaty wysyłane do i odbierane z usługi Service Bus są kolejki wystąpień [BrokeredMessage] [ BrokeredMessage] klasy. [BrokeredMessage] [ BrokeredMessage] obiekty mają zestaw właściwości standardowych (takich jak [etykiety](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) i [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.timetolive#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja możne ustawić treść komunikatu przez przekazanie dowolnego obiektu podlegającego serializacji do konstruktora obiektu [BrokeredMessage][BrokeredMessage], a odpowiedni element serializujący zostanie następnie użyte do serializacji obiektu. Alternatywnie można udostępnić **języka java. WE/WY. InputStream** obiektu.

Poniższy przykład pokazuje sposób wysyłania pięciu testowych komunikatów do `TestQueue` **MessageSender** firma Microsoft uzyskanego w poprzednim fragmencie kodu:

```java
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Podstawowym sposobem na odbieranie komunikatów z kolejki jest użycie **ServiceBusContract** obiektu. Odebrane komunikaty mogą pracować w dwóch różnych trybach: **ReceiveAndDelete** i **PeekLock**.

Korzystając z **ReceiveAndDelete** trybie odbieranie jest operacją pojedynczego zrzutu — oznacza to, gdy Usługa Service Bus odbiera żądanie odczytu komunikatu w kolejce, jego oznacza komunikat jako wykorzystany i zwraca go do aplikacji. **ReceiveAndDelete** trybu (jest to domyślny tryb) jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem.
Ponieważ usługi Service Bus będą oznaczyła komunikat jako wykorzystany, a następnie, gdy aplikacja ponownie uruchamia i ponownie rozpocznie korzystanie z komunikatów, pominie utracony komunikat, który został wykorzystany przed awarią.

W **PeekLock** trybie odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody **Usuń** na odebranym komunikacie. Gdy Usługa Service Bus widzi **Usuń** wywołanie, wówczas Oznacz komunikat jako wykorzystany i usuń go z kolejki.

W poniższym przykładzie pokazano, jak mogą być odbierane wiadomości i przetworzone przy użyciu **PeekLock** tryb (nie tryb domyślny). W poniższym przykładzie nie nieskończonej pętli i przetwarzania wiadomości przychodzące do naszych `TestQueue`:

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                 service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać **unlockMessage** metody dla odebranego komunikatu (zamiast **deleteMessage** metody). Powoduje to odblokowanie komunikatu w kolejce przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i sprawia, że dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed **deleteMessage** wystawić żądania, a następnie wiadomości są przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*; oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **getMessageId** metoda wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz [kolejki, tematy i subskrypcje] [ Queues, topics, and subscriptions] Aby uzyskać więcej informacji.

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
