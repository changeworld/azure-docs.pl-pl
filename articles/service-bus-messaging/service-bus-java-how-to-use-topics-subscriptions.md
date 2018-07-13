---
title: Jak używać tematów usługi Azure Service Bus przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Użyj subskrypcji i tematów usługi Service Bus na platformie Azure.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/17/2017
ms.author: sethm
ms.openlocfilehash: 9c2501840b3c00a63b0344d48e3225fd2c9d1620
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701995"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Jak korzystać z subskrypcji i tematów usługi Service Bus przy użyciu języka Java

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ten przewodnik opisuje sposób używania tematów usługi Service Bus i subskrypcje. Przykłady są zapisywane w środowiskach Java i użyj [zestawu Azure SDK dla języka Java][Azure SDK for Java]. Omówione scenariusze obejmują **tworzenie tematów i subskrypcji**, **tworzenie filtrów subskrypcji**, **wysyłanie komunikatów do tematu**, **odbierania komunikaty z subskrypcji**, i **usuwanie tematów i subskrypcji**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do kolejek usługi Service Bus, w których każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji „jeden do wielu” z użyciem wzorca publikowania/subskrypcji. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia.

Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtrów dla tematu na podstawie każdej subskrypcji, co pozwala na filtr/ograniczyć zakres komunikatów w temacie odbieranych przez określone subskrypcje tematów.

Subskrypcji i tematów usługi Service Bus umożliwiają skalowanie do przetwarzania dużej liczby komunikatów na dużą liczbę użytkowników i aplikacji.

## <a name="create-a-service-namespace"></a>Tworzenie przestrzeni nazw usługi
Aby rozpocząć korzystanie z subskrypcji i tematów usługi Service Bus na platformie Azure, należy najpierw utworzyć *przestrzeni nazw*, który zapewnia kontener określania zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji.

Aby utworzyć przestrzeń nazw:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do użycia usługi Service Bus
Upewnij się, że zainstalowano [zestawu Azure SDK dla języka Java] [ Azure SDK for Java] przed kompilacją w tym przykładzie. Jeśli używasz środowiska Eclipse, możesz zainstalować [Azure Toolkit for Eclipse] [ Azure Toolkit for Eclipse] zawierającego zestaw Azure SDK dla języka Java. Następnie można dodać **biblioteki systemu Microsoft Azure dla języka Java** do projektu:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Dodaj następujący kod `import` instrukcji na początku pliku Java:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Dodawanie bibliotek platformy Azure dla języka Java do swojej ścieżki kompilacji i uwzględnić go w zestaw wdrażania projektu.

## <a name="create-a-topic"></a>Tworzenie tematu
Operacje zarządzania dla tematów usługi Service Bus można wykonywać za pomocą **ServiceBusContract** klasy. A **ServiceBusContract** obiekt jest konstruowany przy użyciu prawidłowej konfiguracji, który hermetyzuje tokenu sygnatury dostępu Współdzielonego z uprawnieniami do zarządzania nim, a **ServiceBusContract** klasy jest jedynym punktem Komunikacja z platformą Azure.

**ServiceBusService** klasa dostarcza metody do tworzenia, wyliczania i usuwania tematów. W poniższym przykładzie przedstawiono sposób **ServiceBusService** obiekt może służyć do utworzenia tematu o nazwie `TestTopic`, za pomocą przestrzeni nazw o nazwie `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
      "HowToSample",
      "RootManageSharedAccessKey",
      "SAS_key_value",
      ".servicebus.windows.net"
      );

ServiceBusContract service = ServiceBusService.create(config);
TopicInfo topicInfo = new TopicInfo("TestTopic");
try  
{
    CreateTopicResult result = service.createTopic(topicInfo);
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Istnieją metody na **TopicInfo** , dzięki którym właściwości tematu, należy ustawić (na przykład: można ustawić czasu wygaśnięcia (TTL) wartość domyślna ma zostać zastosowany do komunikatów wysłanych do tematu). Poniższy przykład pokazuje, jak utworzyć temat o nazwie `TestTopic` o maksymalnym rozmiarze 5 GB:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Możesz użyć **listTopics** metody **ServiceBusContract** obiekty do sprawdzenia, czy temat o określonej nazwie już istnieje w przestrzeni nazw usługi.

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje do tematów, również są tworzone za pomocą **ServiceBusService** klasy. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów przesyłany do wirtualnej kolejki subskrypcji.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Filtr **MatchAll** jest filtrem domyślnym, który jest używany, gdy podczas tworzenia nowej subskrypcji nie został określony żaden filtr. Gdy **MatchAll** filtr jest stosowany, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie „AllMessages” i używa domyślnego filtru **MatchAll**.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również utworzyć filtry, które pozwalają na zakresie, które komunikaty wysyłane do tematu powinny być widoczne w subskrypcji określonego tematu.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest [SqlFilter][SqlFilter], która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, przejrzyj [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] składni.

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` z [SqlFilter] [ SqlFilter] obiekt, który wybiera tylko komunikaty o niestandardowej **MessageNumber** Właściwość jest większy niż 3:

```java
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Podobnie poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z [SqlFilter] [ SqlFilter] obiektu, który wybiera tylko komunikaty, które mają **MessageNumber** Właściwość mniejszą lub równą 3:

```java
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Kiedy wiadomość jest teraz wysyłane do `TestTopic`, zawsze jest dostarczany do odbiorców mających subskrypcję `AllMessages` subskrypcji i selektywnie dostarczany do odbiorców mających subskrypcję `HighMessages` i `LowMessages` subskrypcje (w zależności od zawartość komunikatu).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do tematu usługi Service Bus, aplikacja uzyskuje **ServiceBusContract** obiektu. Poniższy kod ilustruje sposób wysłać komunikat `TestTopic` utworzonego wcześniej w ramach tematu `HowToSample` przestrzeni nazw:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami [BrokeredMessage] [ BrokeredMessage] klasy. [BrokeredMessage][BrokeredMessage]* obiekty mają zestaw metod standardowych (takich jak **setLabel** i **TimeToLive**), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja możne ustawić treść komunikatu przez przekazanie dowolnego obiektu podlegającego serializacji do konstruktora obiektu [BrokeredMessage][BrokeredMessage], a odpowiedni **DataContractSerializer** jest następnie używany do serializacji obiektu. Alternatywnie **java.io.InputStream** można podać.

Poniższy przykład pokazuje sposób wysyłania pięciu testowych komunikatów do `TestTopic` **MessageSender** firma Microsoft uzyskanego w poprzednim fragmencie kodu.
Uwaga jak **MessageNumber** wartości właściwości każdego komunikatu różni się w iteracji pętli (Ta wartość określa subskrypcje, które go otrzymają):

```java
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Jak odbierać komunikaty z subskrypcji
Aby odbierać komunikaty z subskrypcji, użyj **ServiceBusContract** obiektu. Odebrane komunikaty mogą pracować w dwóch różnych trybach: **ReceiveAndDelete** i **PeekLock** (ustawienie domyślne).

Korzystając z **ReceiveAndDelete** trybie odbieranie jest operacją pojedynczego zrzutu — oznacza to, gdy Usługa Service Bus odbiera żądanie odczytu komunikatu, jego oznacza komunikat jako wykorzystany i zwraca go do aplikacji. **ReceiveAndDelete** tryb jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w przypadku awarii. Na przykład Rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Usługa Service Bus oznaczyła komunikat jako wykorzystany, następnie gdy aplikacja zostanie ponownie uruchomiona i ponownie rozpocznie korzystanie z komunikatów, jego ma utracony komunikat, który został wykorzystany przed awarią.

W **PeekLock** trybie odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody **Usuń** na odebranym komunikacie. Gdy Usługa Service Bus widzi **Usuń** wywołanie, jego oznacza komunikat jako wykorzystany i usuwa go z tematu.

W poniższym przykładzie pokazano, jak mogą być odbierane wiadomości i przetworzone przy użyciu **PeekLock** (tryb domyślny). W przykładzie wykonuje pętlę i przetwarza wiadomości w `HighMessages` subskrypcji, a następnie wyjścia w przypadku dalszych komunikatów (ewentualnie ją można ustawić oczekiwania dla nowych komunikatów).

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
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
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
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
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać **unlockMessage** metody dla odebranego komunikatu (zamiast **deleteMessage** metody). Powoduje to odblokowanie komunikatu w ramach tematu i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w ramach tematu, a jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i sprawia, że dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed **deleteMessage** wystawić żądania, a następnie wiadomości są przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Ten proces jest często nazywany **przetwarzaniem co najmniej raz**; oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **getMessageId** metoda wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Podstawowym sposobem usuwanie tematów i subskrypcji jest użycie **ServiceBusContract** obiektu. Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz [kolejek usługi Service Bus, tematy i subskrypcje] [ Service Bus queues, topics, and subscriptions] Aby uzyskać więcej informacji.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
