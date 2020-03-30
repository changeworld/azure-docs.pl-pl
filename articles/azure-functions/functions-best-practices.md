---
title: Najważniejsze wskazówki dotyczące funkcji platformy Azure
description: Poznaj najlepsze rozwiązania i wzorce usług Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277780"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optymalizowanie wydajności i niezawodności usługi Azure Functions

Ten artykuł zawiera wskazówki, aby zwiększyć wydajność i niezawodność aplikacji [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) funkcji.  

## <a name="general-best-practices"></a>Ogólne najlepsze praktyki

Poniżej przedstawiono najlepsze rozwiązania dotyczące tworzenia i zarządzania rozwiązaniami bezserwerowymi przy użyciu funkcji platformy Azure.

### <a name="avoid-long-running-functions"></a>Unikaj długotrwałych funkcji

Duże, długotrwałe funkcje mogą powodować nieoczekiwane problemy z limitem czasu. Aby dowiedzieć się więcej o limitach czasu dla danego planu hostingu, zobacz [czas limitu czasu aplikacji funkcji](functions-scale.md#timeout). 

Funkcja może stać się duża ze względu na wiele zależności Node.js. Importowanie zależności może również spowodować wydłużenie czasu ładowania, które powodują nieoczekiwane limity czasu. Zależności są ładowane jawnie i niejawnie. Pojedynczy moduł załadowany przez kod może załadować własne dodatkowe moduły. 

Jeśli to możliwe, refaktoryzuje duże funkcje w mniejszych zestawach funkcji, które współpracują ze sobą i szybko zwracają odpowiedzi. Na przykład element webhook lub funkcja wyzwalacza HTTP może wymagać odpowiedzi potwierdzenia w określonym terminie; często zdarza się, że elementki webhook wymagają natychmiastowej odpowiedzi. Ładunek wyzwalacza HTTP można przekazać do kolejki, która ma być przetwarzana przez funkcję wyzwalacza kolejki. Takie podejście pozwala odroczyć rzeczywistą pracę i zwrócić natychmiastową odpowiedź.


### <a name="cross-function-communication"></a>Komunikacja między funkcjami

[Funkcje trwałe](durable/durable-functions-overview.md) i [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) są tworzone do zarządzania przejściami stanu i komunikacji między wieloma funkcjami.

Jeśli nie używasz funkcji trwałych lub aplikacji logiki do integracji z wieloma funkcjami, najlepiej jest użyć kolejek magazynu do komunikacji między funkcjami. Głównym powodem jest to, że kolejki magazynu są tańsze i znacznie łatwiejsze do udostępnienia niż inne opcje magazynu. 

Poszczególne wiadomości w kolejce magazynu są ograniczone rozmiarem do 64 KB. Jeśli chcesz przekazać większe wiadomości między funkcjami, kolejka usługi Azure Service Bus może służyć do obsługi rozmiarów wiadomości do 256 KB w warstwie Standardowa i do 1 MB w warstwie Premium.

Tematy usługi Service Bus są przydatne, jeśli przed przetworzeniem wymagane jest filtrowanie wiadomości.

Centra zdarzeń są przydatne do obsługi komunikacji o dużej objętości.


### <a name="write-functions-to-be-stateless"></a>Funkcje zapisu są bezstanowe 

Funkcje powinny być bezstanowe i idempotentne, jeśli to możliwe. Skojarz wszelkie wymagane informacje o stanie z danymi. Na przykład przetwarzane zamówienie prawdopodobnie miałoby skojarzonego `state` członka. Funkcja może przetwarzać kolejność na podstawie tego stanu, podczas gdy sama funkcja pozostaje bezstanowy. 

Funkcje Idempotentne są szczególnie zalecane w przypadku wyzwalaczy czasomierza. Na przykład jeśli masz coś, co absolutnie musi działać raz dziennie, napisz go, aby można go uruchomić w dowolnym momencie w ciągu dnia z tymi samymi wynikami. Funkcja może zakończyć pracę, gdy nie ma pracy dla określonego dnia. Również jeśli poprzedni bieg nie został ukończony, następny bieg powinien odebrać miejsce, w którym zostało przerwane.


### <a name="write-defensive-functions"></a>Pisanie funkcji obronnych

Załóżmy, że funkcja może wystąpić wyjątek w dowolnym momencie. Zaprojektuj swoje funkcje z możliwością kontynuowania z poprzedniego punktu niepowodzenia podczas następnego wykonywania. Należy wziąć pod uwagę scenariusz, który wymaga następujących akcji:

1. Kwerenda dla 10 000 wierszy w bazie danych.
2. Utwórz komunikat kolejki dla każdego z tych wierszy, aby przetworzyć dalej w dół wiersza.
 
W zależności od złożonego systemu może być: zaangażowane usługi podrzędne zachowują się źle, awarie sieci lub limity przydziału osiągnięte itp. Wszystko to może mieć wpływ na funkcję w dowolnym momencie. Musisz zaprojektować swoje funkcje, aby być na to przygotowanym.

Jak reaguje kod, jeśli wystąpi błąd po wstawieniu 5000 tych elementów do kolejki do przetworzenia? Śledź elementy w zestawie, który został ukończony. W przeciwnym razie można wstawić je ponownie następnym razem. To podwójne wstawianie może mieć poważny wpływ na przepływ pracy, więc [sprawiają, że funkcje idempotentne](functions-idempotent.md). 

Jeśli element kolejki został już przetworzony, zezwalaj na funkcję jako no-op.

Skorzystaj ze środków obronnych już dostarczonych dla składników używanych na platformie Azure Functions. Na przykład zobacz **Obsługa wiadomości kolejki zatruć** w dokumentacji dla [wyzwalaczy i powiązań kolejki usługi Azure Storage.](functions-bindings-storage-queue-trigger.md#poison-messages) 

## <a name="scalability-best-practices"></a>Najważniejsze wskazówki dotyczące skalowalności

Istnieje wiele czynników, które wpływają na sposób skalowania wystąpień aplikacji funkcji. Szczegóły znajdują się w dokumentacji [skalowania funkcji](functions-scale.md).  Poniżej przedstawiono kilka najlepszych rozwiązań, aby zapewnić optymalną skalowalność aplikacji funkcji.

### <a name="share-and-manage-connections"></a>Udostępnianie połączeń i zarządzanie nimi

Ponowne używanie połączeń z zasobami zewnętrznymi, gdy tylko jest to możliwe. Zobacz, [jak zarządzać połączeniami w usłudze Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Unikaj udostępniania kont magazynu

Podczas tworzenia aplikacji funkcji, należy skojarzyć go z kontem magazynu. Połączenie z kontem magazynu jest obsługiwane w [ustawieniu aplikacji AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nie mieszaj kodu testowego i produkcyjnego w tej samej aplikacji funkcyjnej

Funkcje w aplikacji funkcji współużytkują zasoby. Na przykład pamięć jest współużytkowana. Jeśli używasz aplikacji funkcji w produkcji, nie należy dodawać funkcji związanych z testami i zasobów do niego. Może to spowodować nieoczekiwane obciążenie podczas wykonywania kodu produkcyjnego.

Należy zachować ostrożność, co można załadować w aplikacjach funkcji produkcji. Pamięć jest uśredniona dla każdej funkcji w aplikacji.

Jeśli masz zestaw udostępniony, do którego odwołuje się wiele funkcji platformy .NET, umieść go we wspólnym folderze udostępnionym. W przeciwnym razie można przypadkowo wdrożyć wiele wersji tego samego pliku binarnego, które zachowują się inaczej między funkcjami.

Nie należy używać pełne rejestrowanie w kodzie produkcyjnym, który ma negatywny wpływ na wydajność.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Użyj kodu asynchronizowego, ale unikaj blokowania połączeń

Programowanie asynchroniczne jest zalecaną najlepszą praktyką, szczególnie podczas blokowania operacji we/wy.

W języku C#, zawsze `Result` unikać `Wait` odwoływania `Task` się do właściwości lub wywoływania metody w wystąpieniu. Takie podejście może prowadzić do wyczerpania wątków.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Używanie wielu procesów roboczych

Domyślnie każde wystąpienie hosta dla funkcji używa procesu pojedynczego procesu roboczego. Aby zwiększyć wydajność, szczególnie w przypadku jednostwowych środowiskach uruchomieniowych, takich jak Python, użyj [FUNCTIONS_WORKER_PROCESS_COUNT,](functions-app-settings.md#functions_worker_process_count) aby zwiększyć liczbę procesów roboczych na hoście (do 10). Usługa Azure Functions następnie próbuje równomiernie rozpowszechniać wywołania funkcji równoczesnych między tymi pracownikami. 

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który funkcja tworzy podczas skalowania w poziomie aplikacji w celu zaspokojenia popytu. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Odbieranie wiadomości w partii, gdy tylko jest to możliwe

Niektóre wyzwalacze, takie jak Centrum zdarzeń umożliwiają odbieranie partii wiadomości na jednym wywołaniu.  Wsadowanie wiadomości ma znacznie lepszą wydajność.  Maksymalny rozmiar partii w pliku `host.json` można skonfigurować zgodnie z opisem w [dokumentacji referencyjnej host.json](functions-host-json.md)

Dla funkcji języka C# można zmienić typ na tablicy silnie typizowane.  Na przykład zamiast `EventData sensorEvent` podpisu metody `EventData[] sensorEvent`może być .  W przypadku innych języków należy jawnie ustawić właściwość kardynalność w to, `function.json` aby `many` włączyć przetwarzanie wsadowe, jak pokazano w tym [miejscu](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurowanie zachowań hosta w celu lepszego obchodzenia się z współbieżnością

Plik `host.json` w aplikacji funkcji umożliwia konfigurację zachowania hosta i wyzwalacza.  Oprócz zachowań przetwarzania wsadowego można zarządzać współbieżności dla wielu wyzwalaczy. Często dostosowując wartości w tych opcjach może pomóc każde wystąpienie skalować odpowiednio dla wymagań wywoływanych funkcji.

Ustawienia w pliku host.json mają zastosowanie do wszystkich funkcji w aplikacji w *ramach jednego wystąpienia* funkcji. Na przykład jeśli masz aplikację funkcji z [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) dwóch funkcji HTTP i żądań ustawionych na 25, żądanie albo wyzwalacz HTTP będzie wliczany do udostępnionych 25 równoczesnych żądań.  Gdy ta aplikacja funkcji jest skalowany do 10 wystąpień, dwie funkcje skutecznie zezwalają na 250 równoczesnych żądań (10 wystąpień * 25 równoczesnych żądań na wystąpienie). 

Inne opcje konfiguracji hosta znajdują się w [artykule konfiguracji host.json](functions-host-json.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Jak zarządzać połączeniami w usłudze Azure Functions](manage-connections.md)
* [Najważniejsze wskazówki dotyczące usługi Azure App Service](../app-service/app-service-best-practices.md)
