---
title: Omówienie obsługi komunikatów kolejek, tematów i subskrypcji usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Przegląd jednostki do obsługi komunikatów usługi Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/18/2018
ms.author: sethm
ms.openlocfilehash: 424004a2a39bd0d05bce515dc17685e60f7a0c9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231580"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Kolejki, tematy i subskrypcje usługi Service Bus

Microsoft Azure Service Bus obsługuje zestaw technologii chmurowych, zorientowany oprogramowanie pośredniczące, takich jak usługi kolejkowania komunikatów niezawodnej i trwałe publikowania/subskrybowania komunikatów. Te możliwości obsługi komunikatów "obsługiwanych przez brokera" można traktować jako całkowicie niezależna funkcji obsługi komunikatów, które publikacji / subskrypcji, oddzielenia czasowego i scenariuszy przy użyciu obciążenie obsługi komunikatów usługi Service Bus równoważenia obciążenia. Komunikacja oddzielona ma wiele zalet, na przykład klienci i serwery mogą nawiązywać połączenie zgodnie z potrzebami i wykonywać ich operacje w sposób asynchroniczny.

Jednostek obsługi komunikatów, które tworzą podstawowe możliwości obsługi komunikatów w usłudze Service Bus są kolejki, tematy i subskrypcje i reguły/akcji.

## <a name="queues"></a>Kolejki

Kolejki oferują *pierwszy na wejściu — pierwszy limit* dostarczanie komunikatów (FIFO) do co najmniej jeden konkurujących konsumentów. Oznacza to, że odbiorcy zwykle odbierają i przetwarzanie wiadomości w kolejności, w której zostały dodane do kolejki, a tylko jednego odbiorcę komunikatów odbiera i przetwarza każdy komunikat. Najważniejszą korzyścią z używania kolejek jest osiągnięcie "oddzielenia czasowego" składników aplikacji. Innymi słowy producenci (nadawcy) i konsumenci (odbiorcy) nie ma wysyłać i odbierać komunikatów w tym samym czasie, ponieważ komunikaty są trwale przechowywane w kolejce. Ponadto producent nie ma czekać na odpowiedź od konsumenta, aby kontynuować przetwarzanie i wysyłanie komunikatów.

Pokrewną korzyścią jest "Wyrównywanie obciążenia," które umożliwia producentom i odbiorcom wysyłanie i odbieranie wiadomości w różnym tempie. W wielu aplikacjach obciążenie systemu różni się wraz z upływem czasu; Jednak gdy czas przetwarzania wymagany dla każdej jednostki pracy jest zwykle stały. Pośredniczenie producentami a konsumentami komunikatów z kolejki oznacza, że aplikacja odbierająca komunikaty tylko ma być przygotowana do być w stanie obsłużyć obciążenia średni zamiast obciążenia szczytowego. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. Ta funkcja jest zapisywany bezpośrednio oszczędność pieniędzy w odniesieniu do kwoty infrastruktury wymaganej do obsługi obciążenia aplikacji. Miarę wzrostu obciążenia, można dodać więcej procesów roboczych do odczytu z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto ta Równoważenie obciążenia oparte na ściąganiu umożliwia optymalne wykorzystanie komputerów roboczych nawet wtedy, gdy komputery procesu roboczego różnią się mocy obliczeniowej, zgodnie z ich ściągać komunikaty z ich maksymalna szybkość. Ten wzorzec jest często nazywany wzorcem "konkurujących konsumentów".

Kolejki, aby między producentami a konsumentami komunikatów zapewnia możliwość dostępu do właściwych luźne powiązanie między składnikami. Ponieważ producenci i konsumenci nie są wzajemnie świadomi, można uaktualnić konsumenta bez wpływu na producenta.

### <a name="create-queues"></a>Tworzenie kolejki

Tworzenie kolejki przy użyciu [portalu Azure](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md), lub [szablonów Resource Manager](service-bus-resource-manager-namespace-queue.md). Następnie wysyłać i odbierać wiadomości przy użyciu [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) obiektu. 

Aby szybko Dowiedz się, jak utworzyć kolejkę, a następnie wysyłać i odbierać komunikaty z kolejki, zobacz [quickstarts](service-bus-quickstart-portal.md) dla każdej metody. Więcej informacji na temat Samouczek o tym, jak używać kolejek, zobacz [Rozpoczynanie pracy z kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md). 

Dla przykładu pracy, zobacz [próbki BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) w witrynie GitHub.

### <a name="receive-modes"></a>Tryby odbierania

Możesz określić dwa różne tryby, w których Usługa Service Bus odbiera wiadomości: *ReceiveAndDelete* lub *PeekLock*. W [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) trybie operacji odbierania pojedynczego zrzutu; oznacza to, kiedy usługa Service Bus odbiera żądanie, oznacza komunikat jako wykorzystany i zwraca go do aplikacji. **ReceiveAndDelete** tryb jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w przypadku niepowodzenia. Aby zrozumieć, w tym scenariuszu, należy wziąć pod uwagę scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus oznacza komunikat jako wykorzystany, gdy aplikacja ponownego uruchomienia i rozpocznie korzystanie z komunikatów ponownie, pominie utracony komunikat, który został wykorzystany przed awarią.

W [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) trybie operacji odbierania staje się dwuetapowa, który umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy Usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go, aby uniemożliwić innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na odebranym komunikacie. Kiedy Usługa Service Bus widzi **CompleteAsync** wywołanie, oznacza komunikat jako wykorzystany.

Jeśli aplikacja nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metody dla odebranego komunikatu (zamiast [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Ta metoda umożliwia usługi Service Bus odblokować wiadomości i udostępnienie go do ponownego odbioru, przez tego samego użytkownika lub innego konkurujących konsumentów. Po drugie istnieje limit czasu skojarzony z blokadą i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus odblokowuje komunikat i ułatwia dostępny do odbioru) zasadniczo wykonywania [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) operacji domyślnie).

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem **CompleteAsync** żądania, wiadomość jest dostarczony do aplikacji po jej ponownym uruchomieniu. Ten proces jest często nazywany *co najmniej raz* przetwarzania; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz. Jednak w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje zduplikowane przetwarzania, a następnie dodatkową logikę jest wymagany w aplikacji na wykrywanie duplikatów, który można uzyskać na podstawie [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia. Funkcja ta jest określana jako *dokładnie raz* przetwarzania.

## <a name="topics-and-subscriptions"></a>Tematy i subskrypcje

W przeciwieństwie do kolejek, w których każdy komunikat jest przetwarzany przez jednego konsumenta, *tematy* i *subskrypcje* Podaj jeden do wielu formę komunikacji w *publikowania/subskrypcji* wzorca. Przydatne w przypadku skalowania do dużej liczby adresatów, każdy opublikowany komunikat jest udostępniana do każdej subskrypcji zarejestrowanej w temacie. Komunikaty są wysyłane do tematu i dostarczane do przynajmniej jednej subskrypcji skojarzone, w zależności od reguły filtrowania, które można ustawić na podstawie na subskrypcję. Subskrypcje użyć dodatkowych filtrów do ograniczenia wiadomości, które mają otrzymywać. Komunikaty są wysyłane do tematu w taki sam sposób jak są wysyłane do kolejki, ale nie odbiera wiadomości z tematu bezpośrednio. Zamiast tego są odbierane z subskrypcji. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów, które są wysyłane do tematu. Komunikaty są odbierane z subskrypcji, tak samo jak, które zostały odebrane z kolejki.

Porównania funkcji wysyłanie komunikatu z kolejki mapuje bezpośrednio na temat i jego działanie odbieranie komunikatu mapy do subskrypcji. Między innymi tej funkcji oznacza, że subskrypcje obsługiwać te same wzory opisany wcześniej w tej sekcji, w odniesieniu do kolejki: konkurujących konsumentów, oddzielenia czasowego wyrównywanie obciążenia i równoważenia obciążenia.

### <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Tworzenie tematu przypomina tworzenia kolejki, zgodnie z opisem w poprzedniej sekcji. Następnie wysyłanie wiadomości przy użyciu [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) klasy. Aby odbierać wiadomości, należy utworzyć co najmniej jedna Subskrypcja do tematu. Podobnie jak kolejki, komunikaty są odbierane z subskrypcją za pomocą [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) obiekt zamiast [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) obiektu. Tworzenie klienta subskrypcji, przekazywanie nazwę tematu, nazwę subskrypcji i (opcjonalnie) tryb odbierania jako parametrów. 

Dla pełnej pracy przykład zobacz [próbki BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) w witrynie Github.

### <a name="rules-and-actions"></a>Reguły i akcje

W wielu scenariuszach wiadomości, które mają określone parametry muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie, można skonfigurować subskrypcje, aby znaleźć komunikaty, które ma potrzebne właściwości, a następnie wykonaj pewne zmiany do tych właściwości. Natomiast subskrypcje usługi Service Bus, zobacz wszystkich wiadomości wysłanych do tematu, można kopiować tylko podzbiór tych wiadomości do kolejki subskrypcji wirtualnego. Filtrowanie to odbywa się przy użyciu filtrów subskrypcji. Takie zmiany są nazywane *akcje filtru*. Po utworzeniu subskrypcji można podać wyrażenie filtru, który działa we właściwościach komunikatu właściwości systemu (na przykład **etykiety**) i właściwości niestandardowych aplikacji (na przykład  **StoreName**.) Wyrażenie filtru SQL jest opcjonalne w takim przypadku; bez wyrażenie filtru SQL żadnych działań filtru zdefiniowane w przypadku subskrypcji zostaną wykonane dla wszystkich wiadomości dla tej subskrypcji.

Dla pełnej pracy przykład zobacz [próbki TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) w witrynie GitHub.

Aby uzyskać więcej informacji o wartości filtru możliwości, zobacz dokumentację dla [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) i [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) klasy. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji i przykłady użycia komunikatów usługi Service Bus zobacz następujące tematy zaawansowane:

* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Szybki Start: Wysyłanie i odbieranie wiadomości przy użyciu portalu Azure i .NET](service-bus-quickstart-portal.md)
* [Samouczek: Aktualizacja spisu przy użyciu portalu Azure i tematy/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)


