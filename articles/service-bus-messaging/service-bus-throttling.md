---
title: Omówienie ograniczania przepustowości usługi Azure Service Bus | Dokumenty firmy Microsoft
description: Omówienie ograniczania przepustowości usługi — warstwy Standardowa i Premium.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598293"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Ograniczanie operacji w usłudze Azure Service Bus

Rozwiązania natywne w chmurze dają pojęcie nieograniczonych zasobów, które można skalować wraz z obciążeniem. Chociaż to pojęcie jest bardziej prawdziwe w chmurze niż w systemach lokalnych, nadal istnieją ograniczenia, które istnieją w chmurze.

Te ograniczenia mogą powodować ograniczanie żądań aplikacji klienckich w warstwach Standard i Premium, jak omówiono w tym artykule. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Ograniczanie przepustowości w warstwie Standardowa usługi Azure Service Bus

Warstwa Standard usługi Azure Service Bus działa jako konfiguracja z wieloma dzierżawami z modelem cenowym płatności zgodnie z rzeczywistym użyciem. Tutaj wiele obszarów nazw w tym samym klastrze współużytkuje przydzielone zasoby. Warstwa standardowa jest zalecanym wyborem dla środowisk deweloperskich, testowych i qa wraz z systemami produkcji o niskiej przepływności.

W przeszłości usługa Azure Service Bus miała grube limity ograniczania przepustowości ściśle zależne od wykorzystania zasobów. Istnieje jednak możliwość uściślania logiki ograniczania przepustowości i zapewnienia przewidywalnego zachowania ograniczania przepustowości do wszystkich obszarów nazw, które współużytkują te zasoby.

W celu zapewnienia uczciwego użycia i dystrybucji zasobów we wszystkich obszarach nazw usługi Azure Service Bus standard, które używają tych samych zasobów, logika ograniczania przepustowości została zmodyfikowana jako oparta na kredytach.

> [!NOTE]
> Należy pamiętać, że ograniczanie przepustowości nie jest ***nowością w*** usłudze Azure Service Bus ani w dowolnej usłudze natywnej w chmurze.
>
> Ograniczanie przydziału oparte na kredytach jest po prostu udoskonalanie sposób różnych obszarów nazw współużytkować zasoby w środowisku warstwy standardowej wielu dzierżawców, a tym samym umożliwiając uczciwe użycie przez wszystkie obszary nazw udostępnianie zasobów.

### <a name="what-is-credit-based-throttling"></a>Co to jest ograniczanie przepustowości opartej na kredycie?

Ograniczanie przepustowości oparte na kredycie ogranicza liczbę operacji, które mogą być wykonywane w danym obszarze nazw w określonym okresie czasu. 

Poniżej znajduje się przepływ pracy dla ograniczania kredytów - 

  * Na początku każdego okresu udostępniamy pewną liczbę kredytów do każdej przestrzeni nazw.
  * Wszelkie operacje wykonywane przez nadawcę lub aplikacje klienckie odbiorcy będą wliczane do tych kredytów (i odejmowane od dostępnych kredytów).
  * Jeśli kredyty zostaną wyczerpane, kolejne operacje zostaną ograniczone do początku następnego okresu.
  * Kredyty są uzupełniane na początku następnego okresu.

### <a name="what-are-the-credit-limits"></a>Jakie są limity kredytowe?

Limity kredytowe są obecnie ustawione na kredyty "1000" co sekundę (na obszar nazw).

Nie wszystkie operacje są sobie równe. Oto koszty kredytu każdego z operacji - 

| Operacja | Koszt kredytu|
|-----------|-----------|
| Operacje danych (Wyślij, WyślijSync, Odbieranie, ReceiveAsync, Wgląd) |1 kredyt na wiadomość |
| Operacje zarządzania (Tworzenie, Odczyt, Aktualizacja, Usuwanie w kolejkach, Tematy, Subskrypcje, Filtry) | 10 kredytów |

> [!NOTE]
> Należy pamiętać, że podczas wysyłania do tematu każda wiadomość jest oceniana pod kątem filtrów przed udostępnieniem w Ramach Subskrypcji.
> Każda ocena filtru jest również wliczana do limitu kredytowego (tj. 1 kredyt na ocenę filtra).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Skąd będę wiedzieć, że jestem dławiony?

Gdy żądania aplikacji klienckiej są ograniczane, poniższa odpowiedź serwera zostanie odebrana przez aplikację i zarejestrowana.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Jak uniknąć ograniczania przepustowości?

W przypadku zasobów udostępnionych ważne jest wymuszanie pewnego rodzaju dozwolonego użycia w różnych przestrzeniach nazw usługi Service Bus, które współużytkują te zasoby. Ograniczanie gwarantuje, że każdy skok w jednym obciążeniu nie powoduje innych obciążeń na tych samych zasobów, które mają być ograniczone.

Jak wspomniano w dalszej części artykułu, nie ma ryzyka jest ograniczona, ponieważ zestawów SDK klienta (i innych ofert usługi Azure PaaS) mają domyślne zasady ponawiania wbudowane w nich. Wszelkie ograniczone żądania zostaną ponowione z wykładniczym backoff i ostatecznie przejść, gdy kredyty są uzupełniane.

Zrozumiałe jest, że niektóre aplikacje mogą być wrażliwe na są ograniczane. W takim przypadku zaleca się [migrację bieżącej przestrzeni nazw standardu usługi Service Bus do usługi Premium](service-bus-migrate-standard-premium.md). 

Podczas migracji można przydzielić dedykowane zasoby do obszaru nazw usługi Service Bus i odpowiednio przeskalować zasoby, jeśli występuje skok obciążenia i zmniejszyć prawdopodobieństwo ograniczenia. Ponadto gdy obciążenie zmniejsza się do normalnego poziomu, można skalować w dół zasobów przydzielonych do obszaru nazw.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Ograniczanie przepustowości w warstwie Premium usługi Azure Service Bus

Warstwa [usługi Azure Service Bus Premium](service-bus-premium-messaging.md) przydziela dedykowane zasoby, pod względem jednostek obsługi wiadomości, do każdej konfiguracji obszaru nazw przez klienta. Te dedykowane zasoby zapewniają przewidywalną przepływność i opóźnienia i są zalecane w przypadku systemów o wysokiej przepustowości lub wrażliwych klasach produkcyjnych.

Ponadto warstwa Premium umożliwia również klientom skalowanie w górę ich przepustowości, gdy występują skoki obciążenia.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Jak działa ograniczanie przepustowości w usłudze Service Bus Premium?

Z alokacji wyłącznych zasobów dla usługi Service Bus Premium, ograniczanie jest wyłącznie spowodowane ograniczenia zasobów przydzielonych do obszaru nazw.

Jeśli liczba żądań jest większa niż bieżące zasoby mogą obsługiwać, żądania zostaną ograniczone.

### <a name="how-will-i-know-that-im-being-throttled"></a>Skąd będę wiedzieć, że jestem dławiony?

Istnieją różne sposoby identyfikowania ograniczania przepustowości w usłudze Azure Service Bus Premium — 
  * **Ograniczone żądania** są wyświetlane na [metrykach żądania usługi Azure Monitor,](service-bus-metrics-azure-monitor.md#request-metrics) aby zidentyfikować liczbę żądań, które zostały ograniczone.
  * Wysokie **użycie procesora CPU** wskazuje, że bieżąca alokacja zasobów jest wysoka, a żądania mogą zostać ograniczone, jeśli bieżące obciążenie nie zmniejszy się.
  * Wysokie **użycie pamięci** wskazuje, że bieżąca alokacja zasobów jest wysoka, a żądania mogą zostać ograniczone, jeśli bieżące obciążenie nie zmniejszy się.

### <a name="how-can-i-avoid-being-throttled"></a>Jak uniknąć ograniczania przepustowości?

Ponieważ obszar nazw usługi Service Bus Premium ma już dedykowane zasoby, można zmniejszyć możliwość uzyskania ograniczania przez skalowanie liczby jednostek obsługi wiadomości przydzielonych do obszaru nazw w przypadku (lub przewidywanie) skok obciążenia.

Skalowanie w górę/w dół można osiągnąć, tworząc [runbooks,](../automation/automation-create-alert-triggered-runbook.md) które mogą być wyzwalane przez zmiany w powyższych metryki.

## <a name="faqs"></a>Często zadawane pytania

### <a name="how-does-throttling-affect-my-application"></a>Jak ograniczanie przepustowości wpływa na moją aplikację?

Gdy żądanie jest ograniczane, oznacza to, że usługa jest zajęta, ponieważ ma do czynienia z większą liczą niż pozwalają na to zasoby. Jeśli ta sama operacja zostanie wypróbowana ponownie po kilku chwilach, gdy usługa przepracowała bieżące obciążenie, żądanie może zostać uszanowane.

Ponieważ ograniczanie jest oczekiwanym zachowaniem dowolnej usługi natywnej w chmurze, stworzyliśmy logikę ponawiania próby w samym sdku usługi Azure Service Bus. Wartość domyślna jest ustawiona na automatyczne ponawianie próby przy obliczu wykładniczego wycofywania, aby upewnić się, że nie mamy tego samego żądania jest ograniczany za każdym razem.

Domyślna logika ponawiania próby będzie stosowana do każdej operacji.

### <a name="does-throttling-result-in-data-loss"></a>Czy ograniczanie przepustowości powoduje utratę danych?

Usługa Azure Service Bus jest zoptymalizowana pod kątem trwałości, zapewniamy, że wszystkie dane wysyłane do usługi Service Bus jest zobowiązana do magazynu, zanim usługa potwierdzi powodzenie żądania.

Gdy żądanie zostanie pomyślnie "ACK" (potwierdzone) przez usługę Service Bus, oznacza to, że usługa Service Bus pomyślnie przetworzyła żądanie. Jeśli usługa Service Bus zwraca "NACK" (błąd), oznacza to, że usługa Service Bus nie mogła przetworzyć żądania, a aplikacja kliencka musi ponowić próbę żądania.

Jednak gdy żądanie jest ograniczone, usługa oznacza, że nie może zaakceptować i przetworzyć żądanie teraz z powodu ograniczeń zasobów. **Nie** oznacza to utraty danych, ponieważ usługa Service Bus po prostu nie spojrzał na żądanie. W takim przypadku poleganie na domyślnej zasad ponawiania próby SDK usługi Service Bus zapewnia, że żądanie jest ostatecznie przetwarzane.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykłady korzystania z obsługi wiadomości usługi Service Bus, zobacz następujące zaawansowane tematy:

* [Omówienie obsługi wiadomości usługi Service Bus](service-bus-messaging-overview.md)
* [Szybki start: wysyłanie i odbieranie komunikatów przy użyciu witryny Azure Portal i platformy .NET](service-bus-quickstart-portal.md)
* [Samouczek: aktualizowanie magazynu przy użyciu witryny Azure Portal oraz tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)

