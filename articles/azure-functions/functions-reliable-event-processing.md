---
title: Niezawodne przetwarzanie zdarzeń w usłudze Azure Functions
description: Unikanie brakujących komunikatów Centrum zdarzeń w usłudze Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561871"
---
# <a name="azure-functions-reliable-event-processing"></a>Niezawodne przetwarzanie zdarzeń w usłudze Azure Functions

Przetwarzanie zdarzeń jest jednym z najbardziej typowych scenariuszy związanych z architekturą bezserwerową. W tym artykule opisano sposób tworzenia niezawodnego procesora wiadomości za pomocą usługi Azure Functions, aby uniknąć utraty wiadomości.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Wyzwania związane ze strumieniami zdarzeń w systemach rozproszonych

Należy wziąć pod uwagę system, który wysyła zdarzenia ze stałą szybkością 100 zdarzeń na sekundę. Z tą szybkością w ciągu kilku minut wiele równoległych wystąpień funkcji może zużywać przychodzące 100 zdarzeń co sekundę.

Możliwe jest jednak, aby możliwe było następujące mniej optymalne warunki:

- Co zrobić, jeśli wydawca zdarzeń wyśle zdarzenie uszkodzone?
- Co zrobić, jeśli funkcja wystąpienie napotka nieobsługiwał wyjątki?
- Co zrobić, jeśli system niższego rzędu przejdzie w tryb offline?

Jak radzić sobie z tymi sytuacjami przy jednoczesnym zachowaniu przepływności aplikacji?

Z kolejkami niezawodne wiadomości przychodzi naturalnie. Po sparowaniu z wyzwalaczem funkcji funkcja tworzy blokadę komunikatu kolejki. Jeśli przetwarzanie zakończy się niepowodzeniem, blokada jest zwalniana, aby umożliwić inne wystąpienie ponowić próbę przetwarzania. Przetwarzanie następnie kontynuuje, dopóki wiadomość nie zostanie pomyślnie oceniona lub zostanie dodana do kolejki trucić.

Nawet gdy komunikat pojedynczej kolejki może pozostać w cyklu ponawiania próby, inne wykonania równoległe nadal zachować do usuwania z kolejki pozostałych wiadomości. W rezultacie ogólna przepływność pozostaje w dużej mierze nienaruszona przez jeden zły komunikat. Jednak kolejki magazynu nie gwarantują zamawiania i nie są zoptymalizowane pod kątem wysokich wymagań dotyczących przepływności wymaganych przez usługi Event Hubs.

Z drugiej strony usługi Azure Event Hubs nie zawiera koncepcji blokowania. Aby umożliwić korzystanie z funkcji, takich jak wysoka przepływność, wiele grup odbiorców i możliwość powtarzania, zdarzenia w centrach zdarzeń zachowują się bardziej jak odtwarzacz wideo. Zdarzenia są odczytywane z jednego punktu w strumieniu na partycję. Ze wskaźnika można odczytać do przodu lub do tyłu z tej lokalizacji, ale trzeba wybrać, aby przenieść wskaźnik dla zdarzeń do przetworzenia.

Gdy wystąpią błędy w strumieniu, jeśli zdecydujesz się zachować wskaźnik w tym samym miejscu, przetwarzanie zdarzeń jest blokowany, dopóki wskaźnik jest zaawansowany. Innymi słowy, jeśli wskaźnik jest zatrzymany do czynienia z problemami przetwarzania pojedynczego zdarzenia, nieprzetworzewane zdarzenia zaczynają piętrzą się.

Usługa Azure Functions pozwala uniknąć zakleszczenia, postępując wskaźnik strumienia, niezależnie od powodzenia lub niepowodzenia. Ponieważ wskaźnik stale postępuje, funkcje muszą radzić sobie z awariami odpowiednio.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Jak usługa Azure Functions zużywa zdarzenia w centrach zdarzeń

Usługa Azure Functions zużywa zdarzenia usługi Event Hub podczas przechodzenia do następujących kroków:

1. Wskaźnik jest tworzony i utrwalony w usłudze Azure Storage dla każdej partycji centrum zdarzeń.
2. Po odebraniu nowych wiadomości (w partii domyślnie), host próbuje wyzwolić funkcję z partii wiadomości.
3. Jeśli funkcja zakończy wykonywanie (z lub bez wyjątku) wskaźnik posuwa się i punkt kontrolny jest zapisywany na koncie magazynu.
4. Jeśli warunki uniemożliwiają wykonywanie funkcji z ukończenia, host nie może przejść wskaźnik. Jeśli wskaźnik nie jest zaawansowany, a następnie sprawdza kończy się przetwarzanie tych samych wiadomości.
5. Powtórz kroki 2–4

To zachowanie ujawnia kilka ważnych punktów:

- *Nieobsługiwały się wyjątkami mogą powodować utratę wiadomości.* Egzekucje, które powodują wyjątek będzie nadal postęp wskaźnika.
- *Funkcje gwarantują co najmniej raz dostawy.* Kod i systemy zależne mogą wymagać [uwzględnienia faktu, że ta sama wiadomość może zostać odebrana dwa razy.](./functions-idempotent.md)

## <a name="handling-exceptions"></a>Obsługa wyjątków

Zgodnie z ogólną zasadą każda funkcja powinna zawierać [try/catch bloku](./functions-bindings-error-pages.md) na najwyższym poziomie kodu. W szczególności wszystkie funkcje, które korzystają `catch` ze zdarzeń Usługi event hubs powinny mieć blok. W ten sposób, gdy wyjątek jest wywoływany, catch bloku obsługuje błąd przed postępy wskaźnika.

### <a name="retry-mechanisms-and-policies"></a>Mechanizmy i zasady ponawiania prób

Niektóre wyjątki mają charakter przejściowy i nie pojawiają się ponownie, gdy operacja zostanie podjęta ponownie chwilę później. Dlatego pierwszym krokiem jest zawsze ponowić próbę wykonania operacji. Możesz napisać reguły ponawiania próby przetwarzania samodzielnie, ale są one tak powszechne, że dostępnych jest wiele narzędzi. Za pomocą tych bibliotek można zdefiniować niezawodne zasady ponawiania prób, które mogą również pomóc zachować kolejność przetwarzania.

Wprowadzenie bibliotek obsługi błędów do funkcji umożliwiają definiowanie zarówno podstawowych, jak i zaawansowanych zasad ponawiania prób. Na przykład można zaimplementować zasady, które następuje przepływu pracy zilustrowane przez następujące reguły:

- Spróbuj wstawić wiadomość trzy razy (potencjalnie z opóźnieniem między ponownych prób).
- Jeśli ostateczny wynik wszystkich ponownych prób jest niepowodzeniem, a następnie dodać komunikat do kolejki, dzięki czemu przetwarzanie można kontynuować w strumieniu.
- Uszkodzone lub nieprzetworzone wiadomości są następnie obsługiwane później.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) jest przykładem biblioteki obsługi odporności i błędów przejściowych dla aplikacji języka C#.

Podczas pracy z wstępnie przestrzegane biblioteki klas języka [C#, filtry wyjątków](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) umożliwiają uruchamianie kodu, gdy wystąpi nieobsługiowany wyjątek.

Przykłady, które pokazują, jak używać filtrów wyjątków są dostępne w repozytorium [SDK usługi WebJobs platformy Azure.](https://github.com/Azure/azure-webjobs-sdk/wiki)

## <a name="non-exception-errors"></a>Błędy inne niż wyjątki

Niektóre problemy pojawiają się nawet wtedy, gdy błąd nie występuje. Na przykład należy wziąć pod uwagę błąd, który występuje w środku wykonywania. W takim przypadku jeśli funkcja nie zakończy wykonywania, wskaźnik przesunięcia nigdy nie jest postęp. Jeśli wskaźnik nie jest zaliczka, a następnie każde wystąpienie, które jest uruchamiane po wykonaniu nie powiodło się nadal odczytywać te same komunikaty. Sytuacja ta zapewnia gwarancję "co najmniej raz".

Pewność, że każda wiadomość jest przetwarzana co najmniej jeden raz oznacza, że niektóre wiadomości mogą być przetwarzane więcej niż jeden raz. Aplikacje funkcyjne muszą być świadome tej możliwości i muszą być zbudowane na [zasadach idempotency.](./functions-idempotent.md)

## <a name="stop-and-restart-execution"></a>Zatrzymywać i ponownie uruchamiać

Chociaż kilka błędów może być dopuszczalne, co zrobić, jeśli aplikacja wystąpi znaczne błędy? Możesz zatrzymać wyzwalanie zdarzeń, dopóki system nie osiągnie stanu w dobrej kondycji. Możliwość wstrzymania przetwarzania jest często osiągana za pomocą wzorca wyłącznika. Wzorzec wyłącznika umożliwia aplikacji "przerwanie obwodu" procesu zdarzenia i wznowienie w późniejszym czasie.

Istnieją dwa elementy wymagane do zaimplementowania wyłącznika w procesie zdarzenia:

- Współużytkowany stan we wszystkich wystąpieniach w celu śledzenia i monitorowania kondycji obwodu
- Proces główny, który może zarządzać stanem obwodu (otwarty lub zamknięty)

Szczegóły implementacji mogą się różnić, ale do udostępniania stanu między wystąpieniami potrzebny jest mechanizm magazynu. Możesz wybrać do przechowywania stanu w usłudze Azure Storage, pamięci podręcznej Redis lub inne konto, które jest dostępne przez kolekcję funkcji.

[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) lub [trwałe jednostki](./durable/durable-functions-overview.md) są naturalne dopasowanie do zarządzania przepływem pracy i stan obwodu. Inne usługi mogą działać równie dobrze, ale aplikacje logiki są używane w tym przykładzie. Za pomocą aplikacji logiki, można wstrzymać i ponownie uruchomić wykonanie funkcji, zapewniając formant wymagany do zaimplementowania wzorca wyłącznika.

### <a name="define-a-failure-threshold-across-instances"></a>Definiowanie progu błędu między wystąpieniami

Aby uwzględnić wiele wystąpień przetwarzania zdarzeń jednocześnie, utrwalanie współużytkowany stan zewnętrzny jest potrzebne do monitorowania kondycji obwodu.

Reguła, którą możesz zaimplementować, może wymuszć to:

- Jeśli istnieje więcej niż 100 ewentualnych błędów w ciągu 30 sekund we wszystkich wystąpieniach, a następnie przerwać obwód i zatrzymać wyzwalanie na nowe wiadomości.

Szczegóły implementacji będą się różnić, biorąc pod uwagę twoje potrzeby, ale ogólnie można utworzyć system, który:

1. Rejestrowanie błędów konta magazynu (Usługa Azure Storage, Redis itp.)
1. Po zarejestrowaniu nowego błędu sprawdź liczbę toczenia, aby sprawdzić, czy próg jest spełniony (na przykład więcej niż 100 w ciągu ostatnich 30 sekund).
1. Jeśli próg zostanie osiągnięty, emituj zdarzenie do usługi Azure Event Grid z informacją o przerwaniu obwodu.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Zarządzanie stanem obwodu za pomocą aplikacji Azure Logic Apps

Poniższy opis wyróżnia jeden ze sposobów można utworzyć aplikację logiki azure, aby zatrzymać aplikacje Functions z przetwarzania.

Usługa Azure Logic Apps jest wyposażony w wbudowane łączniki do różnych usług, funkcje aranżacji stanowych i jest naturalnym wyborem do zarządzania stan obwodu. Po wykryciu obwodu musi zostać przerwana, można utworzyć aplikację logiki, aby zaimplementować następujący przepływ pracy:

1. Wyzwalanie przepływu pracy siatki zdarzeń i zatrzymywania funkcji platformy Azure (za pomocą łącznika zasobów platformy Azure)
1. Wysyłanie wiadomości e-mail z powiadomieniem zawierającą opcję ponownego uruchomienia przepływu pracy

Odbiorca wiadomości e-mail może zbadać stan układu i, w stosownych przypadkach, ponownie uruchomić obwód za pomocą łącza w wiadomości e-mail z powiadomieniem. Gdy przepływ pracy ponownie uruchomi funkcję, wiadomości są przetwarzane z ostatniego punktu kontrolnego Centrum zdarzeń.

Przy użyciu tego podejścia żadne komunikaty nie są tracone, wszystkie wiadomości są przetwarzane w kolejności i można przerwać obwód tak długo, jak to konieczne.

## <a name="resources"></a>Resources

- [Niezawodne próbki przetwarzania zdarzeń](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Wyłącznik funkcji trwałych platformy Azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

- [Obsługa błędów usług Azure Functions](./functions-bindings-error-pages.md)
- [Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](./functions-twitter-email.md)
