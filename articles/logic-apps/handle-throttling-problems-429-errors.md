---
title: Obsługa problemów z ograniczaniem przepustowości lub błędów "429 — zbyt wiele żądań"
description: Jak obejść problemy z ograniczaniem przepustowości lub błędy "HTTP 429 Zbyt wiele żądań" w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272682"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Obsługa problemów z ograniczaniem przepustowości (429 — błędy "Zbyt wiele żądań" w usłudze Azure Logic Apps

W [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md)aplikacja logiki zwraca błąd ["HTTP 429 Zbyt wiele żądań"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) podczas występowania ograniczania przepustowości, co ma miejsce, gdy liczba żądań przekracza szybkość, z jaką miejsce docelowe może obsłużyć przez określony czas. Ograniczanie może powodować problemy, takie jak opóźnione przetwarzanie danych, zmniejszona szybkość wydajności i błędy, takie jak przekroczenie określonych zasad ponawiania.

![Ograniczanie przepustowości w łączniku programu SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Oto kilka typowych typów ograniczania przepustowości, które mogą wystąpić w aplikacji logiki:

* [Aplikacja logika](#logic-app-throttling)
* [Złącze](#connector-throttling)
* [Usługa lub system docelowy](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Ograniczanie przepustowości aplikacji logiki

Usługa Azure Logic Apps ma własne [limity przepływności.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Tak jeśli aplikacja logiki przekracza te limity, zasób aplikacji logiki zostanie ograniczona, a nie tylko określonego wystąpienia lub uruchomić.

Aby znaleźć zdarzenia ograniczania przepustowości na tym poziomie, sprawdź okienko **metryki** aplikacji logiki w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Monitorowanie**wybierz pozycję **Metryki**.

1. W obszarze **Tytuł wykresu**wybierz pozycję **Dodaj metrykę,** aby dodać kolejną metrykę do istniejącej.

1. Na pierwszym pasku metryki z listy **METRYK** wybierz pozycję **Action Throttled Events**. Na drugim pasku metryki z listy **METRYK** wybierz **pozycję Wyzwalaj zdarzenia ograniczone.**

Aby obsłużyć ograniczanie przepustowości na tym poziomie, dostępne są następujące opcje:

* Ogranicz liczbę wystąpień aplikacji logiki, które można uruchomić w tym samym czasie.

  Domyślnie, jeśli warunek wyzwalania aplikacji logiki jest spełniony więcej niż raz w tym samym czasie, wiele wystąpień wyzwalaczy dla aplikacji logiki jest uruchamianych jednocześnie lub *równolegle.* To zachowanie oznacza, że każde wystąpienie wyzwalacza jest uruchamiane przed zakończeniem pracy poprzedniego wystąpienia przepływu pracy.

  Mimo że domyślna liczba wystąpień wyzwalaczy, które można jednocześnie uruchamiać, jest [nieograniczona,](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)można ograniczyć tę liczbę, [włączając ustawienie współbieżności wyzwalacza](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)i w razie potrzeby wybierz limit inny niż wartość domyślna.

* Włącz tryb wysokiej przepustowości.

  Aplikacja logiki ma [domyślny limit liczby akcji, które można uruchomić w ciągu 5-minutowego interwału toczenia](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Aby podnieść ten limit do maksymalnej liczby akcji, włącz [tryb wysokiej przepływności](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) w aplikacji logiki.

* Wyłącz zachowanie dyskusyjne tablicowe ("podzielone na") w wyzwalaczach.

  Jeśli wyzwalacz zwraca tablicę dla pozostałych akcji przepływu pracy do przetworzenia, ustawienie [ **podziel na** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) wyzwalaczu dzieli elementy tablicy i uruchamia wystąpienie przepływu pracy dla każdego elementu tablicy, skutecznie wyzwalając wiele równoczesnych przebiegów do limitu Podziału [ **na** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Aby kontrolować ograniczanie przepustowości, należy wyłączyć zachowanie **Podziału na** i mieć aplikację logiki przetwarzania całej tablicy za pomocą jednego wywołania, a nie obsługi pojedynczego elementu na wywołanie.

* Akcje refaktoryzacyjne w mniejszych aplikacjach logiki.

  Jak wspomniano wcześniej, aplikacja logiki jest ograniczona do [domyślnej liczby akcji, które można uruchomić w okresie 5 minut](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chociaż można zwiększyć ten limit, włączając [tryb wysokiej przepływności,](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)można również rozważyć, czy chcesz podzielić akcje aplikacji logiki na mniejsze aplikacje logiki, tak aby liczba akcji uruchamianych w każdej aplikacji logiki pozostaje w granicach. W ten sposób można zmniejszyć obciążenie dla pojedynczego zasobu aplikacji logiki i rozdzielić obciążenie w wielu aplikacjach logiki. To rozwiązanie działa lepiej dla akcji, które obsługują duże zestawy danych lub rozkręcają się tak wiele równocześnie uruchomionych akcji, iteracji pętli lub akcji wewnątrz każdej iteracji pętli, które przekraczają limit wykonywania akcji.

  Na przykład ta aplikacja logiki wykonuje całą pracę, aby uzyskać tabele z bazy danych programu SQL Server i pobiera wiersze z każdej tabeli. **For each** loop równocześnie iteruje za pośrednictwem każdej tabeli, tak aby akcja **Pobierz wiersze** zwraca wiersze dla każdej tabeli. Na podstawie ilości danych w tych tabelach te akcje mogą przekroczyć limit wykonywania akcji.

  ![Refaktoryzowanie aplikacji logiki "przed"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Po refaktoryzacji aplikacja logiki jest teraz aplikacją logiki nadrzędnej i podrzędnej. Element nadrzędny pobiera tabele z programu SQL Server, a następnie wywołuje podrzędną aplikację logiki dla każdej tabeli, aby uzyskać wiersze:

  ![Tworzenie aplikacji logiki dla jednej akcji](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Oto podrzędna aplikacja logiki, która jest wywoływana przez nadrzędną aplikację logiki, aby uzyskać wiersze dla każdej tabeli:

  ![Tworzenie innej aplikacji logiki dla drugiej akcji](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Ograniczanie łączników

Każdy łącznik ma swoje własne limity ograniczania przepustowości, które można znaleźć na stronie odniesienia technicznego łącznika. Na przykład [łącznik usługi Azure Service Bus](https://docs.microsoft.com/connectors/servicebus/) ma limit ograniczania przepustowości, który pozwala na maksymalnie 6000 wywołań na minutę, podczas gdy łącznik programu SQL Server ma [limity ograniczania przepustowości, które różnią się w zależności od typu operacji.](https://docs.microsoft.com/connectors/sql/)

Niektóre wyzwalacze i akcje, takie jak HTTP, mają ["zasady ponawiania próby",](../logic-apps/logic-apps-exception-handling.md#retry-policies) które można dostosować na podstawie [limitów zasad ponawiania,](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) aby zaimplementować obsługę wyjątków. Ta zasada określa, czy i jak często wyzwalacz lub akcja ponawia żądanie, gdy oryginalne żądanie kończy się niepowodzeniem lub przesunie się w czasie i powoduje odpowiedź 408, 429 lub 5xx. Tak, gdy ograniczanie rozpoczyna się i zwraca błąd 429, aplikacje logiki zgodnie z zasadami ponawiania, gdzie obsługiwane.

Aby dowiedzieć się, czy wyzwalacz lub akcja obsługuje zasady ponawiania, sprawdź ustawienia wyzwalacza lub akcji. Aby wyświetlić wyzwalacz lub próbę ponawiania akcji, przejdź do historii przebiegów aplikacji logiki, wybierz przebieg, który chcesz przejrzeć, i rozwiń ten wyzwalacz lub akcję, aby wyświetlić szczegółowe informacje o danych wejściowych, wyjściach i próbach ponownych prób, na przykład:

![Wyświetlanie historii uruchamiania akcji, ponownych prób, wejść i wyjść](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Chociaż historia ponownych prób zawiera informacje o błędach, mogą wystąpić problemy z rozróżnieniem między ograniczaniem połączeń a [ograniczaniem miejsca docelowego.](#destination-throttling) W takim przypadku może być trzeba przejrzeć szczegóły odpowiedzi lub wykonać niektóre obliczenia interwału ograniczania przepustowości w celu zidentyfikowania źródła.

W przypadku aplikacji logiki w globalnej, wielodostępne usługi Azure Logic Apps ograniczanie odbywa się na poziomie *połączenia.* Tak, na przykład dla aplikacji logiki, które działają w [środowisku usługi integracji (ISE) ograniczania](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)nadal dzieje się dla połączeń innych niż ISE, ponieważ są one uruchamiane w globalnej, wielu dzierżawców usługi logic apps. Jednak połączenia ISE, które są tworzone przez łączniki ISE, nie są ograniczane, ponieważ są uruchamiane w użymie.

Aby obsłużyć ograniczanie przepustowości na tym poziomie, dostępne są następujące opcje:

* Skonfiguruj wiele połączeń dla pojedynczej akcji, tak aby aplikacja logiki partycjonuje dane do przetwarzania.

  W przypadku tej opcji należy rozważyć, czy można dystrybuować obciążenie, dzieląc żądania akcji na wiele połączeń z tym samym miejscem docelowym przy użyciu tych samych poświadczeń.

  Załóżmy na przykład, że aplikacja logiki pobiera tabele z bazy danych programu SQL Server, a następnie pobiera wiersze z każdej tabeli. Na podstawie liczby wierszy, które trzeba przetworzyć, można użyć wielu połączeń i wiele **Dla każdej** pętli, aby podzielić całkowitą liczbę wierszy na mniejsze zestawy do przetwarzania. W tym scenariuszu używa dwóch **Dla każdej** pętli, aby podzielić całkowitą liczbę wierszy na pół. Pierwszy **Dla każdej** pętli używa wyrażenia, które pobiera pierwszą połowę. Druga **dla każdej** pętli używa innego wyrażenia, które pobiera drugą połowę, na przykład:<p>

    * Wyrażenie 1: `take()` Funkcja pobiera z przodu kolekcji. Aby uzyskać więcej informacji, zobacz [ **`take()`** funkcję](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Wyrażenie 2: `skip()` Funkcja usuwa przód kolekcji i zwraca wszystkie inne elementy. Aby uzyskać więcej informacji, zobacz [ **`skip()`** funkcję](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Oto przykład wizualny, który pokazuje, jak można użyć tych wyrażeń:

    ![Tworzenie i używanie wielu połączeń dla pojedynczej akcji](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Skonfiguruj inne połączenie dla każdej akcji.

  W przypadku tej opcji należy rozważyć, czy można dystrybuować obciążenie, rozprowadzając żądania każdej akcji za pomocą własnego połączenia, nawet gdy akcje łączą się z tą samą usługą lub systemem i używają tych samych poświadczeń.

  Załóżmy na przykład, że aplikacja logiki pobiera tabele z bazy danych programu SQL Server i pobiera każdy wiersz w każdej tabeli. Można użyć oddzielnych połączeń, tak aby coraz tabele używać jednego połączenia, podczas gdy coraz każdy wiersz używa innego połączenia.

  ![Tworzenie i używanie różnych połączeń dla każdej akcji](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Zmień współbieżność lub równoległość w [pętli "Dla każdego"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Domyślnie iteracje pętli "Dla każdego" są uruchamiane w tym samym czasie do [limitu współbieżności](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Jeśli masz łącznika, który jest coraz ograniczone wewnątrz "Dla każdego" pętli, można zmniejszyć liczbę iteracji pętli, które są uruchamiane równolegle. Więcej informacji można znaleźć w następujących tematach:
  
  * ["Dla każdej" pętli - zmień współbieżność lub uruchom sekwencyjnie](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schemat języka definicji przepływu pracy — dla każdej pętli](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schemat języka definicji przepływu pracy — zmiana współbieżności pętli "Dla każdego"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schemat języka definicji przepływu pracy — uruchom kolejno pętle "Dla każdego"](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Usługa docelowa lub ograniczanie przepustowości systemu

Gdy łącznik ma własne limity ograniczania przepustowości, usługa docelowa lub system, który jest wywoływany przez łącznik może mieć również limity ograniczania przepustowości. Na przykład niektóre interfejsy API w programie Microsoft Exchange Server mają bardziej rygorystyczne limity ograniczania przepustowości niż łącznik programu Office 365 Outlook.

Domyślnie wystąpienia aplikacji logiki i wszelkie pętle lub gałęzie wewnątrz tych wystąpień, działają *równolegle*. To zachowanie oznacza, że wiele wystąpień można wywołać tego samego punktu końcowego w tym samym czasie. Każde wystąpienie nie wie o istnieniu drugiej strony, więc próby ponowienia próby nieudanych akcji mogą tworzyć [warunki wyścigu,](https://en.wikipedia.org/wiki/Race_condition) w których wiele wywołań próbuje uruchomić w tym samym czasie, ale aby zakończyć się pomyślnie, te wywołania muszą dotrzeć do usługi docelowej lub systemu przed rozpoczęciem ograniczania przepustowości.

Załóżmy na przykład, że masz tablicę, która ma 100 elementów. Użyj pętli "dla każdego" do iteracji za pośrednictwem tablicy i włączyć kontrolkę współbieżności pętli, dzięki czemu można ograniczyć liczbę iteracji równoległych do 20 lub [bieżącego domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Wewnątrz tej pętli akcja wstawia element z tablicy do bazy danych programu SQL Server, która zezwala na tylko 15 wywołań na sekundę. W tym scenariuszu powoduje problem ograniczania przepustowości, ponieważ zaległości ponownych kompilacji i nigdy nie dostać się do uruchomienia.

W tej tabeli opisano oś czasu, co dzieje się w pętli, gdy interwał ponawiania akcji wynosi 1 sekundę:

| Punkt w czasie | Liczba uruchomionych akcji | Liczba akcji, które nie powiodły się | Liczba ponownych prób |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 sekund | 20 płytek | 5 nie powiedzie się, z powodu limitu SQL | 5 ponownych prób |
| T + 0,5 sekundy | 15 wstawek, ze względu na poprzednie 5 ponownych prób oczekiwania | Wszystkie 15 nie, ze względu na poprzedni limit SQL nadal w mocy przez kolejne 0,5 sekundy | 20 ponownych prób <br>(poprzednie 5 + 15 nowych) |
| T + 1 sekunda | 20 płytek | 5 nie powiedzie się plus poprzednie 20 ponownych prób, ze względu na limit SQL | 25 ponownych prób (poprzednie 20 + 5 nowych)
|||||

Aby obsłużyć ograniczanie przepustowości na tym poziomie, dostępne są następujące opcje:

* Tworzenie aplikacji logiki, tak aby każdy obsługuje jedną operację.

  * Kontynuując przykładowy scenariusz programu SQL Server w tej sekcji, można utworzyć aplikację logiki, która umieszcza elementy tablicy w kolejce, takie jak [kolejka usługi Azure Service Bus.](../connectors/connectors-create-api-servicebus.md) Następnie należy utworzyć inną aplikację logiki, która wykonuje tylko operację wstawiania dla każdego elementu w tej kolejce. W ten sposób tylko jedno wystąpienie aplikacji logiki jest uruchamiane w dowolnym określonym czasie, co kończy operację wstawiania i przechodzi do następnego elementu w kolejce lub wystąpienie pobiera 429 błędów, ale nie próbuje bezproduktywnych ponownych prób.

  * Utwórz nadrzędną aplikację logiki, która wywołuje podrzędną lub zagnieżdżoną aplikację logiki dla każdej akcji. Jeśli rodzic musi wywołać różne aplikacje podrzędne na podstawie wyniku rodzica, można użyć akcji warunek lub przełączyć akcję, która określa, która aplikacja podrzędna do wywołania. Ten wzorzec może pomóc zmniejszyć liczbę połączeń lub operacji.

    Załóżmy na przykład, że masz dwie aplikacje logiki, z których każda z wyzwalaczem sondowania, który sprawdza konto e-mail co minutę dla określonego tematu, takich jak "Sukces" lub "Niepowodzenie". Ta konfiguracja powoduje 120 połączeń na godzinę. Zamiast tego jeśli utworzysz aplikację logiki pojedynczego rodzica, która sonduje co minutę, ale wywołuje aplikację logiki podrzędnej, która jest uruchamiana na podstawie tego, czy temat jest "Sukces" lub "Niepowodzenie", można zmniejszyć liczbę kontroli sondowania do połowy lub 60 w tym przypadku.

* Konfigurowanie przetwarzania wsadowego.

  Jeśli usługa docelowa obsługuje operacje wsadowe, można rozwiązać ograniczanie przepustowości, przetwarzając towary w grupach lub partiach, a nie indywidualnie. Aby zaimplementować rozwiązanie przetwarzania wsadowego, należy utworzyć aplikację logiki "odbiornik wsadowy" i aplikację logiki "nadawca wsadowy". Nadawca wsadowy zbiera wiadomości lub elementy, dopóki nie zostanie spełnione określone kryteria, a następnie wysyła te wiadomości lub elementy w jednej grupie. Odbiornik wsadowy akceptuje tę grupę i przetwarza te wiadomości lub elementy. Aby uzyskać więcej informacji, zobacz [Przetwarzanie komunikatów w grupach](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Użyj wersji webhook dla wyzwalaczy i akcji, a nie wersji sondowania.

  Dlaczego? Wyzwalacz sondowania kontynuuje sprawdzanie usługi docelowej lub systemu w określonych odstępach czasu. Bardzo częste interwały, takie jak co sekundę, mogą powodować problemy z ograniczaniem przepustowości. Jednak wyzwalacz lub akcja elementu webhook, takie jak [HTTP Webhook,](../connectors/connectors-native-webhook.md)tworzy tylko jedno wywołanie usługi docelowej lub systemu, co dzieje się w czasie subskrypcji i żąda, aby miejsce docelowe powiadamia wyzwalacz lub akcję tylko wtedy, gdy zdarzenie się dzieje. W ten sposób wyzwalacz lub akcja nie musi stale sprawdzać miejsca docelowego.
  
  Tak więc jeśli usługa docelowa lub system obsługuje element webhook lub udostępnia łącznik, który ma wersję elementu webhook, ta opcja jest lepsza niż przy użyciu wersji sondowania. Aby zidentyfikować wyzwalacze i akcje elementu webhook, upewnij się, że mają `ApiConnectionWebhook` typ lub że nie wymagają, aby określić cykl. Aby uzyskać więcej informacji, zobacz [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i [APIConnectionWebhook akcji](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [ograniczeniach i konfiguracji aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md)
