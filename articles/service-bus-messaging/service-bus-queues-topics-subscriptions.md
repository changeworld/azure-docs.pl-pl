---
title: Omówienie obsługi komunikatów kolejek, tematów i subskrypcji usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Omówienie usługi Service Bus jednostki obsługi komunikatów.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 7cacabf4f171189810e943043b5513e20113d962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125818"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Kolejki, tematy i subskrypcje usługi Service Bus

Microsoft Azure Service Bus obsługuje zestaw technologii oprogramowanie pośredniczące oparte na chmurze, zorientowanych na wiadomości, w tym usługi kolejkowania komunikatów niezawodne i trwałe publikowania/subskrybowania komunikatów. "Obsługiwane przez brokera" możliwości obsługi komunikatów można traktować jako niezależne funkcje obsługi komunikatów, które Obsługa publikowania/subskrybowania, oddzielenia czasowego i scenariusze przy użyciu obciążenia obsługi komunikatów usługi Service Bus równoważenia obciążenia. Komunikacja oddzielona ma wiele zalet, na przykład klienci i serwery mogą nawiązywać połączenie zgodnie z potrzebami i wykonywać ich operacje w sposób asynchroniczny.

Jednostki do obsługi komunikatów, które tworzą podstawowe możliwości obsługi komunikatów w usłudze Service Bus są kolejki, tematy i subskrypcje i reguły/akcji.

## <a name="queues"></a>Kolejki

Kolejki oferują *pierwszy na wejściu, pierwszy limit* dostarczania komunikatów (FIFO) do co najmniej jeden konkurujący odbiorcy. Oznacza to odbiorcy zwykle odbieranie i przetwarzanie wiadomości w kolejności, w którym zostały one dodane do kolejki, a tylko jednego odbiorcę komunikatów odbiera i przetwarza każdy komunikat. Najważniejszą korzyścią z używania kolejek jest osiągnięcie "oddzielenia czasowego" składników aplikacji. Innymi słowy producenci (nadawcy) i konsumenci (odbiorcy) nie ma wysyłać i odbierać komunikatów w tym samym czasie, ponieważ komunikaty są trwale przechowywane w kolejce. Ponadto producent nie ma czekać na odpowiedź od konsumenta, aby można było kontynuować przetwarzanie i wysyłanie komunikatów.

Pokrewną korzyścią jest "Wyrównywanie obciążenia," które umożliwia producentom i odbiorcom wysyłanie i odbieranie wiadomości w różnym tempie. W wielu aplikacjach obciążenie systemu różni się wraz z upływem czasu; jednak czas przetwarzania wymagany dla każdej jednostki pracy jest zwykle stały. Pośredniczenie a konsumentami komunikatów z kolejki oznacza, że aplikacja odbierająca komunikaty tylko ma być przygotowana do mogło obsłużyć średnie obciążenie zamiast szczytowego obciążenia. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. Ta funkcja pozwala bezpośrednio oszczędzać pieniądze w odniesieniu do wielkości infrastruktury do obsługi obciążenia aplikacji. W miarę zwiększania obciążenia, można dodać więcej procesów roboczych i odczytany z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto ta równoważenia obciążenia oparte na ściąganiu pozwala na optymalne wykorzystanie komputerów procesu roboczego nawet wtedy, gdy komputery procesów roboczych różnią się w zakresie mocy obliczeniowej, zgodnie z ich własnych maksymalna szybkość ich ściągają komunikaty. Ten wzorzec jest często nazywany wzorcem "konkurujących konsumentów".

Za pomocą kolejek w celu pośrednich między a konsumentami komunikatów zapewnia nieprzerwaną pracę luźne powiązanie między składnikami. Ponieważ producenci i konsumenci nie rozpoznają od siebie, można uaktualnić konsumenta bez żadnego wpływu na producenta.

### <a name="create-queues"></a>Tworzenie kolejki

Tworzenie kolejki za pomocą [witryny Azure portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [interfejsu wiersza polecenia](service-bus-quickstart-cli.md), lub [szablonów usługi Resource Manager](service-bus-resource-manager-namespace-queue.md). Następnie wysyłanie i odbieranie wiadomości przy użyciu [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) obiektu.

Aby szybko dowiedzieć się, jak utworzyć kolejkę, a następnie wysyłać i odbierać komunikaty z kolejki, zobacz [przewodników Szybki Start](service-bus-quickstart-portal.md) dla każdej metody. Więcej informacji na temat samouczek dotyczący sposobu użycia kolejek, zobacz [Rozpoczynanie pracy z kolejkami usługi Service Bus](service-bus-dotnet-get-started-with-queues.md).

Przykładowy pracy [przykładowe BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) w witrynie GitHub.

### <a name="receive-modes"></a>Odbieranie tryby

Można określić dwa różne tryby, w których Usługa Service Bus odbiera wiadomości: *ReceiveAndDelete* lub *PeekLock*. W [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) trybu operacji odbierania pojedynczego zrzutu; oznacza to, kiedy usługa Service Bus odbiera żądanie, jego oznacza komunikat jako wykorzystany i zwraca go do aplikacji. **ReceiveAndDelete** tryb jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w przypadku awarii. Aby dowiedzieć się, w tym scenariuszu, rozważ scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus oznacza komunikat jako wykorzystany, gdy aplikacja zostanie ponownie uruchomiona i ponownie rozpocznie korzystanie z komunikatów, pominie utracony komunikat, który został wykorzystany przed awarią.

W [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) trybu operacji odbierania staje się dwuetapowego, która umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy Usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go, aby uniemożliwić innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na odebranym komunikacie. Gdy Usługa Service Bus widzi **CompleteAsync** wywołanie, oznacza komunikat jako wykorzystany.

Jeśli aplikacja nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metody dla odebranego komunikatu (zamiast [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Ta metoda umożliwia to odblokowanie komunikatu i udostępnić go do ponownego odbioru, przez odbiorcę tego samego lub innego konkurujących konsumentów usługę Service Bus. Po drugie ma skojarzonego z blokadą limitu czasu i jeśli aplikacja nie może przetworzyć komunikatu, wygaśnięcia limitu czasu blokady (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus odblokowuje komunikat i sprawia, że dostępny do ponownego odbioru) zasadniczo wykonywania [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) operacji domyślnie).

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed **CompleteAsync** wystawić żądania, komunikat jest przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Ten proces jest często nazywany *co najmniej raz* przetwarzania; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz. Jednak w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie Toleruje zduplikowane przetwarzania, a następnie dodatkowej logiki jest wymagany w aplikacji, aby wykrywanie duplikatów, który można osiągnąć na podstawie [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) właściwości komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia. Ta funkcja jest znany jako *dokładnie jeden raz* przetwarzania.

## <a name="topics-and-subscriptions"></a>Tematy i subskrypcje

W przeciwieństwie do kolejek, w których każdy komunikat jest przetwarzany przez jednego konsumenta *tematy* i *subskrypcje* zapewniają formę komunikacji, jeden do wielu w *publikowania/subskrybowania* wzorca. Przydatne w przypadku skalowania do dużej liczby odbiorców, każdy opublikowany komunikat jest udostępniane dla każdej subskrypcji zarejestrowanej w temacie. Komunikaty są wysyłane do tematu i dostarczane do co najmniej jeden skojarzonych subskrypcji, w zależności od reguł filtrowania, które można ustawić w każdej subskrypcji. Subskrypcje można użyć dodatkowych filtrów do ograniczania wiadomości, które chcą otrzymywać. Komunikaty są wysyłane do tematu w taki sam sposób są wysyłane do kolejki, ale nie odbiera wiadomości z tematu bezpośrednio. Zamiast tego są odbierane z subskrypcji. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Komunikaty są odbierane z subskrypcji, tak samo jak otrzymany z kolejki.

Dla porównania funkcji wysyłania komunikatu kolejki mapuje bezpośrednio na temat i jego działanie odbieranie komunikatu mapuje do subskrypcji. Między innymi tej funkcji oznacza, że subskrypcje obsługiwać te same wzory opisanych powyżej w tej sekcji w odniesieniu do kolejki: konkurujących konsumentów, oddzielenia czasowego, wyrównywania i równoważenia obciążenia.

### <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Tworzenie tematu przypomina Tworzenie kolejki, zgodnie z opisem w poprzedniej sekcji. Następnie wysyłania komunikatów przy użyciu [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) klasy. Aby odbierać komunikaty, należy utworzyć co najmniej jedną subskrypcję tematu. Podobnie jak w kolejkach, komunikaty są odbierane z subskrypcji przy użyciu [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) zamiast obiektu [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) obiektu. Tworzenie subskrypcji klienta, przekazując nazwę tematu, Nazwa subskrypcji i (opcjonalnie) tryb odbierania jako parametry.

Aby uzyskać pełną Praca przykładu, zobacz [przykładowe BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) w witrynie GitHub.

### <a name="rules-and-actions"></a>Reguły i akcje

W wielu scenariuszach wiadomości, które mają szczególne cechy muszą zostać przetworzone w różny sposób. Aby włączyć to przetwarzanie, można skonfigurować subskrypcje, aby znaleźć wiadomości, które mają być żądane właściwości, a następnie wykonaj pewne zmiany do tych właściwości. Natomiast subskrypcje usługi Service Bus, zobacz wszystkie wiadomości wysyłane do tematu, można kopiować tylko podzbiór tych komunikatów do kolejki wirtualne subskrypcji. Filtrowanie odbywa się przy użyciu filtrów subskrypcji. Takie modyfikacje są nazywane *akcji filtrowania*. Po utworzeniu subskrypcji można podać wyrażenie filtru, który działa we właściwościach wiadomości, w oknie Właściwości systemu (na przykład **etykiety**) i właściwości niestandardowych aplikacji (na przykład  **StoreName**.) Wyrażenie filtru SQL jest opcjonalny w tym przypadku; bez wyrażenie filtru SQL żadnych akcji filtrowania zdefiniowanych w ramach subskrypcji zostanie przeprowadzone wszystkie komunikaty dla tej subskrypcji.

Aby uzyskać pełną Praca przykładu, zobacz [przykładowe TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) w witrynie GitHub.

Aby uzyskać więcej informacji na temat wartości filtru możliwe dokumentacji dla [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) i [elementu SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) klasy.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji i przykłady użycia komunikatów usługi Service Bus zobacz następujące tematy zaawansowane:

* [Omówienie obsługi komunikatów w usłudze Service Bus](service-bus-messaging-overview.md)
* [Szybki start: Wysyłanie i odbieranie wiadomości przy użyciu witryny Azure portal i platformy .NET](service-bus-quickstart-portal.md)
* [Samouczek: Aktualizowanie spisu przy użyciu witryny Azure portal i tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)


