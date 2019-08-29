---
title: Przechowywanie wersji w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować obsługę wersji w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: ef64a43cbed7f033a938351506b7f78142ff044c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097619"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Przechowywanie wersji w Durable Functions (Azure Functions)

Jest to nieuniknione, że funkcje zostaną dodane, usunięte i zmienione w okresie istnienia aplikacji. [Durable Functions](durable-functions-overview.md) umożliwia łączenie się z funkcjami łańcucha w sposób, który nie był wcześniej możliwy i ten łańcuch ma wpływ na sposób obsługi wersji.

## <a name="how-to-handle-breaking-changes"></a>Jak obsłużyć istotne zmiany

Istnieje kilka przykładów istotnych zmian, o których należy wiedzieć. W tym artykule omówiono najczęstsze. Głównym motywem za wszystkie z nich jest to, że w przypadku nowych i istniejących aranżacji funkcji wpływają zmiany kodu funkcji.

### <a name="changing-activity-function-signatures"></a>Zmienianie sygnatur funkcji działania

Zmiana podpisu odwołuje się do zmiany w nazwie, danych wejściowych lub wyjściowych funkcji. Jeśli ten rodzaj zmiany jest wprowadzany do funkcji działania, może to spowodować uszkodzenie funkcji programu Orchestrator, która jest od niej zależna. Jeśli zaktualizujesz funkcję programu Orchestrator w celu uwzględnienia tej zmiany, możesz przerwać istniejące wystąpienia w locie.

Załóżmy na przykład, że mamy następującą funkcję.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta funkcja uproszczony pobiera wyniki **foo** i przekazuje je do **paska**. Załóżmy, że musimy zmienić wartość zwracaną **foo** z `bool` na `int` , aby obsługiwać szersze różne wartości wynikowe. Wynik będzie wyglądać następująco:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta zmiana działa prawidłowo dla wszystkich nowych wystąpień funkcji programu Orchestrator, ale przerywa wszystkie wystąpienia w locie. Rozważmy na przykład przypadek, w którym wystąpienie aranżacji wywołuje **foo**, pobiera wartość logiczną, a następnie punkty kontrolne. Jeśli w tym momencie zostanie wdrożona zmiana podpisu, wystąpienie z punktem kontrolnym zakończy się niepowodzeniem natychmiast po jego wznowieniu i odtwarza wywołanie `context.CallActivityAsync<int>("Foo")`. Wynika to z faktu, że wynik w tabeli `bool` historii jest, ale nowy kod próbuje zdeserializować `int`go do.

Jest to tylko jeden z wielu różnych sposobów, w których zmiana podpisu może przerwać istniejące wystąpienia. Ogólnie rzecz biorąc, jeśli program Orchestrator musi zmienić sposób wywoływania funkcji, zmiana będzie prawdopodobnie powodować problemy.

### <a name="changing-orchestrator-logic"></a>Zmiana logiki programu Orchestrator

Druga klasa problemów z obsługą wersji polega na zmianie kodu funkcji programu Orchestrator w taki sposób, który odmówi logiki powtarzania dla wystąpień w locie.

Weź pod uwagę następujące funkcje programu Orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Teraz Załóżmy, że chcemy pozornie nieszkodliwe zmienić, aby dodać kolejne wywołanie funkcji.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Ta zmiana powoduje dodanie nowego wywołania funkcji do **SendNotification** między **foo** a **paskiem**. Brak zmian sygnatur. Problem występuje, gdy istniejące wystąpienie zostanie wznowione z wywołania do **paska**. Podczas powtarzania, jeśli oryginalne wywołanie metody **foo** zwróci `true`wartość, ponowne uruchomienie programu Orchestrator wywoła wywołanie do **SendNotification** , które nie znajduje się w jego historii wykonywania. W związku z tym trwała struktura zadań kończy się niepowodzeniem z `NonDeterministicOrchestrationException` powodu wystąpienia **SendNotification** , gdy oczekiwano na wyświetlenie wywołania do **paska**.

## <a name="mitigation-strategies"></a>Strategie ograniczenia

Poniżej przedstawiono niektóre strategie postępowania z wyzwaniami dotyczącymi wersji:

* Nic nie rób
* Zatrzymaj wszystkie wystąpienia w locie
* Wdrożenia równoległe

### <a name="do-nothing"></a>Nic nie rób

Najprostszym sposobem obsługi istotnej zmiany jest umożliwienie awarii wystąpienia aranżacji w locie. Nowe wystąpienia pomyślnie uruchomiły zmieniony kod.

To, czy jest to problem, zależy od ważności wystąpień w locie. Jeśli pracujesz w aktywnym programowaniu i nie Zadbaj o wystąpienia w locie, może to być wystarczające. Należy jednak zająć się wyjątkami i błędami w potoku diagnostyki. Aby uniknąć tych problemów, należy wziąć pod uwagę inne opcje przechowywania wersji.

### <a name="stop-all-in-flight-instances"></a>Zatrzymaj wszystkie wystąpienia w locie

Innym rozwiązaniem jest zatrzymanie wszystkich wystąpień w locie. Można to zrobić przez wyczyszczenie zawartości kolejek wewnętrznej **kontroli** i kolejek elementów **roboczych** . Wystąpienia staną się nieograniczona, gdy są, ale nie będą zasłaniać danych telemetrycznych i komunikatów o błędach. Jest to idealne rozwiązanie w szybkim tworzeniu prototypów.

> [!WARNING]
> Szczegóły tych kolejek mogą ulec zmianie z upływem czasu, dlatego nie należy polegać na tej metodzie w przypadku obciążeń produkcyjnych.

### <a name="side-by-side-deployments"></a>Wdrożenia równoległe

Najbardziej nieudaną próbą zapewnienia, że krytyczne zmiany są wdrażane bezpiecznie, wdrażając je równolegle ze starszymi wersjami. Można to zrobić przy użyciu dowolnej z następujących technik:

* Wdróż wszystkie aktualizacje jako całkowicie nowe funkcje (nowe nazwy).
* Wdróż wszystkie aktualizacje jako nową aplikację funkcji przy użyciu innego konta magazynu.
* Wdróż nową kopię aplikacji funkcji, ale ze zaktualizowaną `TaskHub` nazwą. Jest to zalecana technika.

### <a name="how-to-change-task-hub-name"></a>Jak zmienić nazwę centrum zadań

Centrum zadań można skonfigurować w pliku *host. JSON* w następujący sposób:

#### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Functions w wersji 2.x

Wartość domyślna to `DurableFunctionsHub`.

Wszystkie jednostki usługi Azure Storage są nazwane na podstawie `HubName` wartości konfiguracji. Nadając centrum zadań nową nazwę, upewnij się, że dla nowej wersji aplikacji zostanie utworzona osobna kolejka i tabela historii.

Zalecamy wdrożenie nowej wersji aplikacji funkcji w nowym [miejscu wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Miejsca wdrożenia umożliwiają uruchamianie wielu kopii aplikacji funkcji obok siebie tylko z jednym z nich jako aktywnym miejscem *produkcyjnym* . Gdy wszystko jest gotowe do uwidocznienia nowej logiki aranżacji do istniejącej infrastruktury, może to być proste, ponieważ zamienia nową wersję na miejsce produkcyjne.

> [!NOTE]
> Ta strategia działa najlepiej, gdy używane są wyzwalacze protokołu HTTP i elementu webhook dla funkcji programu Orchestrator. W przypadku wyzwalaczy innych niż HTTP, takich jak kolejki lub Event Hubs, definicja wyzwalacza powinna [pochodzić od ustawienia aplikacji](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , które jest aktualizowane w ramach operacji zamiany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać problemy dotyczące wydajności i skalowania](durable-functions-perf-and-scale.md)
