---
title: Usługa Service Bus cennik i rozliczenia | Dokumentacja firmy Microsoft
description: Omówienie struktury cennik usługi Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 8ccb44b5009588c28bc79bb45e1a7640ead6c817
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159790"
---
# <a name="service-bus-pricing-and-billing"></a>Usługa Service Bus cennik i rozliczenia

Usługa Azure Service Bus jest oferowana w standardzie i [Premium](service-bus-premium-messaging.md) warstw. Można wybrać dla każdej przestrzeni nazw usługi Service Bus, który utworzono warstwy usług, i stosuje ten wybór warstwy dla wszystkich obiektów utworzonych w tej przestrzeni nazw.

> [!NOTE]
> Aby uzyskać szczegółowe informacje o cenach bieżącej usługi Service Bus, zobacz [cennik usługi Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/)i [— często zadawane pytania dla magistrali usługi](service-bus-faq.md#pricing).
>
>

Usługa Service Bus używa następujących liczników 2 dla kolejek i tematów/subskrypcji:

1. **Operacje do obsługi komunikatów**: zdefiniowany jako wywołania interfejsu API z kolejki i tematu/subskrypcji usługi punktów końcowych. Ten licznik zastępuje wiadomości wysyłane lub odbierane jako podstawową jednostkę rozliczeniowy użycie kolejek i tematów/subskrypcji.
2. **Obsługiwane przez brokera połączeń**: zdefiniowany jako Szczytowa liczba połączeń trwałych Otwórz przed kolejki, tematy i subskrypcje w danego próbkowania jednogodzinnym przedziale czasu. Ten licznik ma zastosowanie tylko w warstwie standardowa, w którym można otwierać dodatkowych połączeń (wcześniej połączenia były ograniczone do 100 na kolejki/tematu/subskrypcji) do opłaty za połączenia nominalnego.

**Standardowe** warstwy wprowadza pomiarowej ceny operacji z kolejki i tematy/subskrypcji, co wolumin na podstawie rabaty maksymalnie 80% na najwyższy poziom użycia. Brak opłat podstawowej warstwy standardowa $ 10 na miesiąc, co pozwala na wykonywanie operacji do milionów 12,5 miesięcznie bez ponoszenia dodatkowych kosztów.

**Premium** warstwę zapewniają izolację zasobów w warstwie Procesora i pamięci, dzięki czemu obciążenia poszczególnych klientów uruchamia się w izolacji. Ten kontener zasobów jest nazywany *jednostką obsługi komunikatów*. Każda przestrzeń nazw w warstwie Premium ma przydzieloną co najmniej jedną jednostkę obsługi komunikatów. Możesz kupić 1, 2 lub 4 jednostki obsługi komunikatów dla każdej przestrzeni nazw usługi Service Bus w warstwie Premium. Pojedyncze obciążenie lub jednostka może obejmować wiele jednostek obsługi komunikatów, a ich liczbę można dowolnie zmieniać, chociaż opłaty są naliczane w cyklu 24-godzinnym lub codziennie. Pozwala to uzyskać przewidywalną i powtarzalną wydajność dla rozwiązania opartego na usłudze Service Bus. Poprawa dotyczy nie tylko przewidywalności i dostępności, ale również szybkości działania.

Należy pamiętać, że warstwy standardowa opłata podstawowa jest rozliczana tylko raz na miesiąc na subskrypcję platformy Azure. Oznacza to, że po utworzeniu przestrzeni nazw usługi Service Bus warstwy jeden Standard, można utworzyć dowolną liczbę dodatkowych standardowych przestrzeni nazw można dowolnie w tej samej subskrypcji platformy Azure, bez ponoszenia dodatkowych kosztów podstawowej.

[Cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabeli przedstawiono różnice funkcjonalne między warstwy standardowa i Premium.

## <a name="messaging-operations"></a>Operacje obsługi komunikatów

Kolejki i tematy/subskrypcje są rozliczane na "operacji", nie dla każdego komunikatu. Operacja odwołuje się do dowolnego wywołania interfejsu API przed punkt końcowy usługi kolejki i tematu/subskrypcji. Dotyczy to również operacji zarządzania, wysyłania/odbierania i obsługi stanu sesji.

| Typ operacji | Opis |
| --- | --- |
| Zarządzanie |Tworzenia, odczytu, aktualizacji, usuwania (CRUD) z kolejki i tematy/subskrypcji. |
| Obsługa komunikatów |Wysyłanie i odbieranie wiadomości z kolejki i tematy/subskrypcji. |
| Stan sesji |Pobierania lub ustawiania stanu sesji na kolejki i tematu/subskrypcji. |

Szczegółowe koszt, można znaleźć ceny wymienione na [cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) strony.

## <a name="brokered-connections"></a>Połączenia obsługiwane przez brokera

*Obsługiwane przez brokera połączeń* pomieścić wzorce użycia, obejmujących wiele "stale połączonych" nadawców/odbiorcy przed kolejki, tematy i subskrypcje. Stale połączonych nadawców/odbiorcy są te, które łączą się przy użyciu protokołu AMQP lub HTTP z inną niż zero uzyskują limitu czasu (na przykład HTTP długi sondowania). HTTP nadawcami a odbiornikami natychmiastowego limitu czasu nie generują obsługiwanych przez brokera połączeń.

Dla połączenia przydziały i limity inne usługi, zobacz [przydziały usługi Service Bus](service-bus-quotas.md) artykułu. Aby uzyskać więcej informacji na temat obsługiwanych przez brokera połączeń, zobacz [— często zadawane pytania](#faq) sekcji w dalszej części tego artykułu.

Warstwy standardowa liczby użycie agregacji obsługiwanych przez brokera połączeń między subskrypcją platformy Azure i usuwa limit połączeń obsługiwanych przez brokera na przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [obsługiwanych przez brokera połączeń](https://azure.microsoft.com/pricing/details/service-bus/) tabeli.

> [!NOTE]
> 1000 połączeń obsługiwanych przez brokera są dołączone do planu Standard obsługi komunikatów (za pośrednictwem opłata podstawowa) i może być współużytkowana przez wszystkie kolejki, tematy i subskrypcje w ramach subskrypcji platformy Azure skojarzone.
>
>

<br />

> [!NOTE]
> Opłata zależy do szczytowej liczby jednoczesnych połączeń i jest naliczana proporcjonalnie w oparciu o godziny przy założeniu 744 godzin na miesiąc.
>
>

### <a name="premium-tier"></a>Warstwa Premium

W warstwie Premium nie są naliczane opłaty za połączenia obsługiwane przez brokera.

## <a name="faq"></a>Często zadawane pytania

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Co to są obsługiwane przez brokera połączeń i jak pobrać daną opłatę dla nich?

Połączenie obsługiwane przez brokera jest definiowane jako jedna z następujących sytuacji:

1. Protokół AMQP połączenie z klienta do kolejki usługi Service Bus lub tematu/subskrypcji.
2. Wywołanie HTTP mające na celu odebranie komunikatu z tematu lub kolejki usługi Service Bus, dla którego wartość limitu czasu odbierania jest większa od zera.

Usługa Service Bus opłaty za szczytowa liczba równoczesnych połączeń obsługiwanych przez brokera, które przekraczają uwzględnionej ilości (w 1000 w warstwie standardowej). Wartości szczytowe są mierzone godzinowo, proporcjonalnie dzielone przez 744 godziny w miesiącu i dodawane w ciągu miesięcznego okresu rozliczeniowego. Uwzględniona liczba (1000 połączeń obsługiwanych przez brokera na miesiąc) jest stosowana na koniec okresu rozliczeniowego względem sumy proporcjonalnie naliczonych szczytów wyrażonych w godzinach.

Na przykład:

1. Każda 10 000 urządzeń łączy się za pomocą pojedynczego połączenia protokołu AMQP i odbiera poleceń z tematu usługi Service Bus. Urządzenia wysyłają dane telemetryczne zdarzeń do Centrum zdarzeń. Jeśli wszystkie urządzenia podłączone 12 godzin każdego dnia, następujące połączenia opłaty (jako uzupełnienie innych usługi Service Bus temat opłat): połączenia o szybkości 10 000 * 12 godzin * 31 dni / 744 = 5000 obsługiwanych przez brokera połączeń. Po miesięczne dodatku 1000 połączeń obsługiwanych przez brokera będą naliczane za 4000 połączenia obsługiwane przez brokera w wysokości 0,03 USD na połączenie obsługiwanych przez brokera, łącznie z $120.
2. 10 000 urządzeń odbierać komunikaty z kolejki usługi Service Bus za pośrednictwem protokołu HTTP, określając limit czasu równy zero. Jeśli wszystkie urządzenia podłączone 12 godzin codziennie, pojawi się następujące opłaty za połączenia (jako uzupełnienie innych opłat usługi Service Bus): 10 000 HTTP odbierania połączeń * 12 godzin dziennie * 31 dni / godziny 744 = 5000 obsługiwanych przez brokera połączeń.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Czy opłaty za połączenia obsługiwane przez brokera mają zastosowanie w przypadku kolejek i tematów/subskrypcji?

Tak. Nie ma opłat za połączenia związane z przesyłaniem zdarzeń przez protokół HTTP niezależnie od liczby systemów lub urządzeń wysyłających. Odbieranie zdarzeń z protokołem HTTP przy użyciu przekroczenie limitu czasu większą niż zero, nazywane czasem "long sondowania," generuje opłaty za połączenia obsługiwanych przez brokera. Połączenia AMQP generują opłaty za połączenia obsługiwane przez brokera bez względu na to, czy połączenia są używane do wysyłania czy odbierania. Pierwszych 1000 obsługiwanych przez brokera połączeń we wszystkich standardowych przestrzeni nazw w subskrypcji platformy Azure są uwzględniane bez dodatkowych opłat (poza opłata podstawowa). Ponieważ te dodatki pokrywać wiele scenariuszy wysyłania komunikatów do usługi, opłaty za połączenia obsługiwanych przez brokera zwykle tylko staną się ważne, jeśli planujesz używać protokołu AMQP i HTTP sondowania long z dużej liczby klientów. na przykład, aby osiągnąć większą wydajność przesyłania strumieniowego zdarzeń lub Włącz komunikację dwukierunkową z wieloma urządzeniami lub wystąpień aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe informacje o cenach usługi Service Bus, zobacz [cennikiem usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Zobacz [— często zadawane pytania dla magistrali usługi](service-bus-faq.md#pricing) dla niektórych typowych — często zadawane pytania dotyczące usługi Service bus cennika i rozliczeń.

[Azure portal]: https://portal.azure.com
