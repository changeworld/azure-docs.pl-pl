---
title: Najlepsze rozwiązania dotyczące usługi Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, najlepsze rozwiązania i wzorce dotyczące usługi Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Usługa Azure functions, wzorców, najlepszym rozwiązaniem, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30b187676f0c1fb03b7124d93b3991b0e32d61ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104681"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optymalizuj wydajność i niezawodność usługi Azure Functions

Ten artykuł zawiera wskazówki, aby zwiększyć wydajność i niezawodność usługi [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) aplikacje funkcji. 

## <a name="general-best-practices"></a>Ogólne najlepsze rozwiązania

Poniżej przedstawiono najlepsze rozwiązania dotyczące sposobu tworzenia i architektury bezserwerowe rozwiązania przy użyciu usługi Azure Functions.

### <a name="avoid-long-running-functions"></a>Należy unikać długo działających funkcji

Duże, długo działających funkcji, mogą powodować problemy nieoczekiwany limitu czasu. Funkcja może stać się dużą ze względu na wiele zależności środowiska Node.js. Importowanie zależności może powodować zwiększone obciążenie prób spowodować nieoczekiwane przekroczeń limitu czasu. Zależności są ładowane, zarówno jawnie i niejawnie. Pojedynczy moduł ładowane przez Twój kod może spowodować załadowanie swój własny dodatkowych modułów.  

Zawsze, gdy jest to możliwe, Refaktoryzacja duże funkcje na mniejsze funkcja ustawia współpracujące ze sobą i szybko zwracania odpowiedzi. Na przykład element webhook lub funkcja wyzwalacza HTTP może wymagać odpowiedź potwierdzenia w określonym terminie. jest typowe dla elementów webhook wymagają natychmiastowej reakcji. Ładunek wyzwalacza HTTP można przekazać do kolejki do przetworzenia przez funkcję wyzwalacza kolejki. Takie podejście umożliwia odroczenie rzeczywistą pracę i zwracają natychmiastowej reakcji.


### <a name="cross-function-communication"></a>Funkcja komunikacji między

[Trwałe funkcje](durable/durable-functions-concepts.md) i [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) zostały opracowane w celu zarządzania stanami i komunikacji między wiele funkcji.

Jeśli nie używa trwałe Functions lub Logic Apps do integracji z wielu funkcji, jest zazwyczaj najlepszym rozwiązaniem, aby używać magazynu kolejek dla wielu funkcji komunikacji.  Głównym celem jest, że kolejki magazynu są tańsze i łatwiejsze do aprowizowania. 

Poszczególne wiadomości w kolejce magazynu, są ograniczone w rozmiarze do 64 KB. Jeśli musisz przekazać większe komunikaty między funkcjami, kolejka może służyć do obsługi komunikatów usługi Azure Service Bus rozmiarów do 256 KB w warstwie standardowa i do 1 MB w warstwie Premium.

Tematy usługi Service Bus są przydatne, jeśli potrzebujesz filtrowanie przed rozpoczęciem przetwarzania komunikatów.

Usługa Event hubs są przydatne do obsługi komunikacji w dużych ilościach.


### <a name="write-functions-to-be-stateless"></a>Pisanie funkcji bezstanowe 

Funkcje powinny być bezstanowe i idempotentne, jeśli jest to możliwe. Kojarzenie wszelkich informacji o stanie wymagane ze swoimi danymi. Na przykład zamówienie przetwarzany, prawdopodobnie będzie mają skojarzonego `state` elementu członkowskiego. Funkcja może przetworzyć zamówienie na podstawie tego stanu podczas gdy sama funkcja pozostaje bezstanowe. 

Funkcje Idempotentne są szczególnie zalecane przy użyciu wyzwalaczy czasomierza. Na przykład jeśli masz coś, co oczywiście należy uruchomić raz dziennie, zapisz je więc w żadnym momencie w ciągu dnia może działać przy użyciu tych samych wyników. Funkcję można zamknąć po żadnej pracy w danym dniu. Również Jeśli poprzednie uruchomienia nie można ukończyć, następnego uruchomienia powinien przejmą tam, gdzie ją przerwaliśmy.


### <a name="write-defensive-functions"></a>Pisanie funkcji obrony

Przyjęto założenie, że funkcja może wystąpić wyjątek w dowolnym momencie. Projektowanie funkcji z możliwością kontynuować z poprzedniego punktu awarii podczas następnego wykonania. Rozważmy scenariusz, który wymaga następujących czynności:

1. Zapytanie dla 10 000 wierszy w bazie danych.
2. Utworzenia komunikatu w kolejce dla każdego z tych wierszy, do dalszego przetwarzania wiersz w dół.
 
W zależności od złożoności systemu, może być: zachowuje się nieprawidłowo w inny sposób zaangażowane usługami podrzędnymi, przestojów sieci lub limitu przydziału ogranicza osiągnął itp. Wszystkie te mogą mieć wpływ na funkcję w dowolnym momencie. Należy projektować funkcje w taki sposób, aby móc go.

Jak kod reagowanie po wystąpieniu błędu po wstawieniu 5000 tych elementów w kolejce do przetworzenia? Śledzenie elementów w zestawie, który został ukończony. W przeciwnym razie może wstawić je ponownie przy następnym. Może to mieć poważny wpływ na Twój przepływ pracy. 

Jeśli element kolejki został już przetworzony, Zezwalaj na funkcję, aby być pusta.

Wykorzystaj środki obrony poprawiał dla składników, których używasz na platformie Azure Functions. Na przykład zobacz **obsługi komunikatów w kolejce skażone** w dokumentacji dotyczącej [usługi Azure Storage Queue wyzwalaczy i powiązań](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Najlepsze rozwiązania w zakresie skalowalności

Istnieje wiele czynników, mogących mieć wpływ na sposób skalować wystąpienia aplikacji funkcji. Szczegółowe informacje znajdują się w dokumentacji dotyczącej [funkcja skalowania](functions-scale.md).  Poniżej przedstawiono niektóre najlepsze rozwiązania zapewniające optymalne skalowalność aplikacji funkcji.

### <a name="share-and-manage-connections"></a>Udostępnianie połączeń i zarządzanie nimi

Ponowne używanie połączenia z zasobami zewnętrznymi, jeśli to możliwe.  Zobacz [sposób zarządzania połączeniami w usłudze Azure Functions](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nie można mieszać testowych i produkcyjnych kod w tej samej aplikacji funkcji

Funkcje w ramach aplikacji funkcji udostępniania zasobów. Na przykład pamięć jest udostępniona. Jeśli używasz aplikacji funkcji w środowisku produkcyjnym, nie należy dodawać funkcje powiązane z testem i zasoby do niego. Podczas wykonywania kodu w środowisku produkcyjnym może spowodować nieoczekiwane koszty.

Należy zachować ostrożność ładowanie w aplikacjach funkcji w środowisku produkcyjnym. Pamięci są uśredniane dla każdej funkcji w aplikacji.

Jeśli masz zestaw współużytkowany, do którego odwołuje się wiele funkcji platformy .NET, należy umieścić go we wspólnym folderze udostępnionym. Jeśli za pomocą skryptów języka C# (csx), należy odwoływać się do zestawu za pomocą instrukcji, które są podobne do poniższego przykładu: 

    #r "..\Shared\MyAssembly.dll". 

W przeciwnym razie jest łatwe przypadkowo wdrożono wiele testów wersji tej samej binarne, które zachowują się inaczej między funkcjami.

Nie należy używać pełnego rejestrowania w kodzie produkcyjnym. Ma negatywny wpływ na wydajność.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Kod asynchroniczny, ale unikania blokowania połączeń

Programowanie asynchroniczne jest zalecanym najlepszym rozwiązaniem. Jednak zawsze unikać odwoływania się do `Result` właściwość lub wywołanie `Wait` metody `Task` wystąpienia. Takie podejście może prowadzić do wyczerpania wątku.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Odbieranie komunikatów w usłudze batch, jeśli to możliwe

Niektórych wyzwalaczy, takich jak Centrum zdarzeń umożliwiają odbieranie wiadomości na pojedyncze wywołanie partii.  Tworzenie partii komunikatów ma znacznie lepszą wydajność.  Można skonfigurować rozmiar maksymalny partii w `host.json` plików zgodnie z opisem w [dokumentację referencyjną host.json](functions-host-json.md)

Dla funkcji języka C# można zmienić typ na silnie typizowaną tablicę.  Na przykład, zamiast z `EventData sensorEvent` podpis metody może być `EventData[] sensorEvent`.  W przypadku innych języków Musisz jawnie ustawić właściwość Kardynalność w swojej `function.json` do `many` w celu włączenia przetwarzania wsadowego [jak pokazano poniżej](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurowanie zachowania hosta, aby lepiej obsługiwać współbieżności

`host.json` Pliku w aplikacji funkcji umożliwia konfigurację zachowania środowiska uruchomieniowego i wyzwalacza hosta.  Oprócz partii zachowań, możesz zarządzać współbieżności wielu wyzwalaczy.  Często Dostosowywanie wartości w tych opcji może pomóc w każdej skali wystąpienia odpowiednio do potrzeb wywołanej funkcji.

Ustawienia w pliku hosts mają zastosowanie we wszystkich funkcji w ramach aplikacji w ramach *pojedyncze wystąpienie* funkcji. Na przykład jeśli masz aplikację funkcji przy użyciu 2 funkcje protokołu HTTP i wartość 25 współbieżnych żądań, żądanie albo wyzwalacza HTTP jest traktowany kierunku udostępnionego 25 współbieżnych żądań.  Ta aplikacja funkcji skalowania do 10 wystąpień, 2 funkcje efektywnie pozwoliłoby 250 równoczesnych żądań (10 wystąpień * 25 równoczesnych żądań na wystąpienie).

**Opcje hosta współbieżności HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Inne opcje konfiguracji hosta można znaleźć [w dokumencie konfiguracji hosta](functions-host-json.md).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Sposób zarządzania połączeniami w usłudze Azure Functions](manage-connections.md)
* [Najlepsze rozwiązania platformy Azure App Service](../app-service/app-service-best-practices.md)
