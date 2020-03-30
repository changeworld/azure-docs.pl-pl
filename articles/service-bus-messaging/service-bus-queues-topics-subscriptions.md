---
title: Wiadomości usługi Azure Service Bus — kolejki, tematy i subskrypcje
description: Ten artykuł zawiera omówienie jednostek obsługi wiadomości usługi Azure Service Bus (kolejki, tematów i subskrypcji).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259515"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Kolejki, tematy i subskrypcje usługi Service Bus

Usługa Microsoft Azure Service Bus obsługuje zestaw opartych na chmurze, zorientowanych na wiadomości technologii oprogramowania pośredniczącego, w tym niezawodne kolejkowanie wiadomości i trwałe publikowanie/subskrybowanie wiadomości. Te "brokered" możliwości obsługi wiadomości można traktować jako oddzielone funkcje obsługi obsługi wiadomości, które obsługują publikowanie subskrybować, czasowe oddzielenie i równoważenia obciążenia scenariuszy przy użyciu obciążenia obsługi wiadomości usługi Service Bus. Komunikacja oddzielona ma wiele zalet, na przykład klienci i serwery mogą nawiązywać połączenie zgodnie z potrzebami i wykonywać ich operacje w sposób asynchroniczny.

Jednostki obsługi wiadomości, które tworzą rdzeń możliwości obsługi wiadomości w usłudze Service Bus są kolejki, tematy i subskrypcje oraz reguły/akcje.

## <a name="queues"></a>Kolejki

Kolejki oferują dostarczanie wiadomości *FIRST In, First Out* (FIFO) jednemu lub większej liczbie konkurujących konsumentów. Oznacza to, że odbiorniki zazwyczaj odbierają i przetwarzają wiadomości w kolejności, w jakiej zostały dodane do kolejki, a tylko jeden konsument odbiera i przetwarza każdą wiadomość. Kluczową zaletą korzystania z kolejek jest osiągnięcie "czasowego oddzielenia" składników aplikacji. Innymi słowy, producenci (nadawcy) i konsumenci (odbiorcy) nie muszą wysyłać i odbierać wiadomości w tym samym czasie, ponieważ wiadomości są przechowywane trwale w kolejce. Ponadto producent nie musi czekać na odpowiedź od konsumenta, aby kontynuować przetwarzanie i wysyłanie wiadomości.

Powiązaną korzyścią jest "bilansowanie obciążenia", które umożliwia producentom i konsumentom wysyłanie i odbieranie wiadomości po różnych stawkach. W wielu aplikacjach obciążenie systemu zmienia się w czasie; jednak czas przetwarzania wymagany dla każdej jednostki pracy jest zazwyczaj stały. Pośredniczenie producentów komunikatów i konsumentów z kolejką oznacza, że aplikacja zużywająca tylko musi być aprowizowana, aby móc obsługiwać średnie obciążenie zamiast obciążenia szczytowego. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. Ta funkcja bezpośrednio oszczędza pieniądze w odniesieniu do ilości infrastruktury wymaganej do obsługi obciążenia aplikacji. Wraz ze wzrostem obciążenia można dodać więcej procesów roboczych do odczytu z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto to równoważenie obciążenia oparte na ściąganiu pozwala na optymalne wykorzystanie komputerów roboczych, nawet jeśli komputery robocze różnią się pod względem mocy obliczeniowej, ponieważ ściągają wiadomości z maksymalną szybkością. Ten wzorzec jest często nazywany "konkurencyjnym konsumentem" wzorzec.

Korzystanie z kolejek do pośrednich między producentami wiadomości i konsumentami zapewnia nieodłączne luźne sprzężenie między składnikami. Ponieważ producenci i konsumenci nie są sobie świadomi, konsument może zostać uaktualniony bez wywierania wpływu na producenta.

### <a name="create-queues"></a>Tworzenie kolejek

Kolejki są tworzone przy użyciu [szablonów](service-bus-resource-manager-namespace-queue.md) [Azure portal,](service-bus-quickstart-portal.md) [PowerShell,](service-bus-quickstart-powershell.md) [CLI](service-bus-quickstart-cli.md)lub Resource Manager . Następnie wysyłasz i odbierasz wiadomości przy użyciu obiektu [QueueClient.](/dotnet/api/microsoft.azure.servicebus.queueclient)

Aby szybko dowiedzieć się, jak utworzyć kolejkę, a następnie wysyłać i odbierać wiadomości do i z kolejki, zobacz [szybki start](service-bus-quickstart-portal.md) dla każdej metody. Aby uzyskać bardziej szczegółowy samouczek dotyczący używania kolejek, zobacz Wprowadzenie do [kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md).

W przypadku przykładu roboczego zobacz [BasicSendReceiveUsingQueueClient próbki](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) w usłudze GitHub.

### <a name="receive-modes"></a>Tryby odbioru

Można określić dwa różne tryby, w których usługa Service Bus odbiera wiadomości: *ReceiveAndDelete* lub *PeekLock*. W trybie [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) operacja odbierania jest pojedynczym strzałem; oznacza to, że gdy usługa Service Bus odbiera żądanie od konsumenta, oznacza komunikat jako zużyty i zwraca ją do aplikacji konsumenckiej. **ReceiveAndDelete** tryb jest najprostszym modelu i działa najlepiej dla scenariuszy, w których aplikacja może tolerować nie przetwarzania wiadomości, jeśli wystąpi błąd. Aby zrozumieć ten scenariusz, należy wziąć pod uwagę scenariusz, w którym konsument wystawia żądanie odbierania, a następnie ulega awarii przed przetworzeniem. Ponieważ usługa Service Bus oznacza komunikat jako zużyty, gdy aplikacja zostanie ponownie uruchomiona i zacznie ponownie zużywać wiadomości, zostanie wyświetlony komunikat, który został zużyty przed awarią.

W trybie [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) operacja odbierania staje się dwuetapowa, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajdzie następną wiadomość do zużywania, blokuje go, aby uniemożliwić innym odbiorcom odbieranie go, a następnie zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości (lub magazynuje ją niezawodnie do przetwarzania w przyszłości), kończy drugi etap procesu odbierania, wywołując [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) w odebranej wiadomości. Gdy usługa Service Bus widzi **CompleteAsync** wywołanie, oznacza komunikat jako używane.

Jeśli aplikacja nie jest w stanie przetworzyć wiadomości z jakiegoś powodu, może wywołać [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metody na odebranej wiadomości (zamiast [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Ta metoda umożliwia usługi Service Bus, aby odblokować wiadomość i udostępnić go do odbioru ponownie, przez tego samego konsumenta lub przez innego konsumenta konkurencyjnych. Po drugie istnieje limit czasu skojarzony z blokadą i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), a następnie usługa Service Bus odblokowuje komunikat i udostępnia ją do odebraniu ponownie (zasadniczo wykonując operację [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) domyślnie).

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed **completeAsync** żądanie zostanie wydane, komunikat jest ponownie dostarczony do aplikacji po ponownym uruchomieniu. Proces ten jest często nazywany *co najmniej raz* przetwarzania; oznacza to, że każda wiadomość jest przetwarzana co najmniej raz. Jednak w niektórych sytuacjach ten sam komunikat może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowane przetwarzania, a następnie dodatkową logikę jest wymagane w aplikacji do wykrywania duplikatów, które można osiągnąć na podstawie [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) właściwości wiadomości, która pozostaje stała w próbach dostarczenia. Ta funkcja jest znana jako *dokładnie raz* przetwarzania.

## <a name="topics-and-subscriptions"></a>Tematy i subskrypcje

W przeciwieństwie do kolejek, w których każda wiadomość jest przetwarzana przez jednego konsumenta, *tematy* i *subskrypcje* zapewniają jedną do wielu form komunikacji, w wzorzec *publikowania/subskrybowania.* Przydatne do skalowania do dużej liczby adresatów, każda opublikowana wiadomość jest udostępniana każdej subskrypcji zarejestrowanej w temacie. Wiadomości są wysyłane do tematu i dostarczane do jednej lub więcej skojarzonych subskrypcji, w zależności od reguł filtrowania, które można ustawić na podstawie subskrypcji. Subskrypcje można użyć dodatkowych filtrów, aby ograniczyć wiadomości, które mają być odbierane. Wiadomości są wysyłane do tematu w taki sam sposób, w jaki są wysyłane do kolejki, ale wiadomości nie są odbierane bezpośrednio z tematu. Zamiast tego są one odbierane z subskrypcji. Subskrypcja tematu przypomina kolejkę wirtualną, która odbiera kopie wiadomości, które są wysyłane do tematu. Wiadomości są odbierane z subskrypcji identycznie do sposobu ich odełowienia z kolejki.

Dla porównania funkcje wysyłania wiadomości kolejki są mapowane bezpośrednio do tematu, a jego funkcje odbierania wiadomości są mapowane na subskrypcję. Między innymi ta funkcja oznacza, że subskrypcje obsługują te same wzorce opisane wcześniej w tej sekcji w odniesieniu do kolejek: konkurencyjnych konsumentów, czasowe oddzielenie, bilansowanie obciążenia i równoważenie obciążenia.

### <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Tworzenie tematu jest podobne do tworzenia kolejki, jak opisano w poprzedniej sekcji. Następnie wysyłasz wiadomości przy użyciu [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) klasy. Aby odbierać wiadomości, należy utworzyć jedną lub więcej subskrypcji do tematu. Podobnie jak w przypadku kolejek, wiadomości są odbierane z subskrypcji przy użyciu [obiektu SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) zamiast obiektu [QueueClient.](/dotnet/api/microsoft.azure.servicebus.queueclient) Utwórz klienta subskrypcji, przekazując nazwę tematu, nazwę subskrypcji i (opcjonalnie) tryb odbierania jako parametry.

Aby uzyskać pełny przykład pracy, zobacz [BasicSendReceiveUsingTopicSubscriptionClient próbki](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) w usłudze GitHub.

### <a name="rules-and-actions"></a>Reguły i akcje

W wielu scenariuszach wiadomości, które mają określone cechy muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie, można skonfigurować subskrypcje, aby znaleźć komunikaty, które mają żądane właściwości, a następnie wykonać pewne modyfikacje tych właściwości. Podczas subskrypcji usługi Service Bus zobacz wszystkie wiadomości wysyłane do tematu, można skopiować tylko podzbiór tych wiadomości do kolejki subskrypcji wirtualnej. To filtrowanie jest realizowane przy użyciu filtrów subskrypcji. Takie modyfikacje są nazywane *akcjami filtrowania*. Po utworzeniu subskrypcji można podać wyrażenie filtru działające we właściwościach wiadomości, zarówno właściwości systemu (na przykład **Etykieta)** i właściwości aplikacji niestandardowej (na przykład **StoreName**.) Wyrażenie filtru SQL jest opcjonalne w tym przypadku; bez wyrażenia filtru SQL wszystkie działania filtru zdefiniowane w ramach subskrypcji będą wykonywane na wszystkich komunikatach dla tej subskrypcji.

Aby uzyskać pełny przykład pracy, zobacz [TopicSubscriptionWithRuleOperationsPróbuj próbkę](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) w usłudze GitHub.

Aby uzyskać więcej informacji na temat możliwych wartości filtrów, zobacz dokumentację dla [sqlfilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) i [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) klas.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykłady korzystania z obsługi wiadomości usługi Service Bus, zobacz następujące zaawansowane tematy:

* [Omówienie obsługi wiadomości usługi Service Bus](service-bus-messaging-overview.md)
* [Szybki start: wysyłanie i odbieranie komunikatów przy użyciu witryny Azure Portal i platformy .NET](service-bus-quickstart-portal.md)
* [Samouczek: aktualizowanie magazynu przy użyciu witryny Azure Portal oraz tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)


