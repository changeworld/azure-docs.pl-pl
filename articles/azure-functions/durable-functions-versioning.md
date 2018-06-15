---
title: Przechowywanie wersji w funkcjach trwałe - Azure
description: Dowiedz się, jak do implementacji przechowywania wersji w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23838555"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Przechowywanie wersji w funkcji trwałe (funkcje platformy Azure)

Jest nieuniknione, że funkcje zostaną dodane, usunięte i zmianie w okresie istnienia aplikacji. [Funkcje trwałe](durable-functions-overview.md) umożliwia tworzenie łańcuchów razem działa w sposób, w którym nie zostały wcześniej możliwe i łańcucha tej wpływa na sposób może obsłużyć przechowywanie wersji.

## <a name="how-to-handle-breaking-changes"></a>Jak obsługiwać zmiany podziału

Brak zmian, które psuły pod uwagę kilka przykładów. W tym artykule opisano najbardziej typowe. Motywu głównego za wszystkich z nich jest zarówno orchestrations nowych i istniejących funkcji mają wpływ zmiany kodu funkcji.

### <a name="changing-activity-function-signatures"></a>Zmiana sygnatury funkcji działania

Zmiana podpisu odwołuje się do zmiany nazwy, danych wejściowych lub wyjściowych funkcji. Tego rodzaju zmiany dotyczące funkcji działania, może spowodować przerwanie funkcji programu orchestrator, która zależy od niego. Po zaktualizowaniu funkcji programu orchestrator w celu uwzględnienia tej zmiany może spowodować przerwanie istniejących wystąpień locie.

Na przykład załóżmy, że mamy następująca funkcja.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta funkcja simplistic przyjmuje wyniki **Foo** i przekazuje je do **paska**. Załóżmy, że należy zmienić wartość zwracaną **Foo** z `bool` do `int` do obsługi różnych wartości wyników. Wynik wygląda następująco:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta zmiana działa prawidłowo dla wszystkich wystąpień nowych funkcji programu orchestrator, ale dzieli wystąpienia locie. Rozważmy na przykład sytuacji, gdy wywołuje wystąpienie aranżacji **Foo**, pobiera ponownie wartość logiczną, a następnie punkty kontrolne. Jeśli zmiana podpisu jest wdrażana w tym momencie, użyciu wystąpienia zakończy się niepowodzeniem, natychmiast po wznowieniu działania i odtwarzaniem wywołanie `context.CallActivityAsync<int>("Foo")`. Jest to spowodowane jest wynik w tabeli historii `bool` , ale nowy kod spróbuje go do deserializacji `int`.

Jest to tylko jeden wiele różnych sposobów, że zmiana podpisu mogą być dzielone istniejących wystąpień. Ogólnie rzecz biorąc Jeśli trzeba zmienić sposób orchestrator wywołuje funkcję, a następnie ta zmiana jest przyczyną problemów.

### <a name="changing-orchestrator-logic"></a>Zmiana logiki programu orchestrator

Klasa problemom z wersjami pochodzą z zmiana kodu funkcji programu orchestrator w taki sposób, aby confuses logikę powtarzania locie wystąpień.

Należy wziąć pod uwagę następujące funkcji programu orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Teraz załóżmy, że chcesz dokonania zmiany pozornie nieszkodliwie, aby dodać inne wywołanie funkcji.

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

Ta zmiana dodaje nowe wywołanie funkcji w celu **SendNotification** między **Foo** i **paska**. Nie wprowadzono żadnych zmian podpisu. Problem występuje, gdy istniejące wystąpienie zostanie wznowione po wywołaniu **paska**. Podczas powtarzania, jeśli oryginalny wywołanie **Foo** zwrócił `true`, a następnie zostanie wywołany powtarzania orchestrator **SendNotification** który nie znajduje się w jego historii wykonywania. W związku z tym trwałe Framework zadań kończy się niepowodzeniem z `NonDeterministicOrchestrationException` ponieważ napotkała wywołanie **SendNotification** po oczekuje Zobacz wywołanie **paska**.

## <a name="mitigation-strategies"></a>Środki zaradcze strategie

Oto kilka strategii zajmujących się wyzwania przechowywanie wersji:

* Nic nie rób
* Zatrzymaj wszystkie wystąpienia aktywny
* Side-by-side wdrożenia

### <a name="do-nothing"></a>Nic nie rób

Najprostszym sposobem obsługi na istotne zmiany jest aby umożliwić locie aranżacji, niepowodzenia wystąpień. Nowe wystąpienia, pomyślny zmienionego kodu.

Czy jest to problem, zależy od ważności wystąpień locie. Jeśli w rozwoju aktywne i nie zależy Ci na zachowaniu locie wystąpień, może to być wystarczająca. Jednak należy uwzględniać wyjątków i błędów w potoku diagnostyki sieci. Aby uniknąć tych problemów, należy rozważyć inne opcje przechowywania wersji.

### <a name="stop-all-in-flight-instances"></a>Zatrzymaj wszystkie wystąpienia aktywny

Innym rozwiązaniem jest zatrzymanie wszystkich wystąpień w locie. Można to zrobić przez wyczyszczenie zawartości wewnętrznego **kolejki kontroli** i **kolejki elementów roboczych** kolejek. Wystąpienia zostanie trwale zablokowane, gdy są one, ale nie będą one zajmowały telemetrii z komunikatów o błędach. Jest to idealne rozwiązanie w prototypu szybkie programowanie.

> [!WARNING]
> Szczegóły te kolejki mogą ulec zmianie w czasie, dlatego nie należy polegać na tej techniki w przypadku obciążeń produkcyjnych.

### <a name="side-by-side-deployments"></a>Side-by-side wdrożenia

Sposobem najbardziej dowód błędów upewnij się, że istotne zmiany są wdrażane bezpiecznie jest wdrożenie ich side-by-side Twojej starszych wersji. Można to zrobić przy użyciu dowolnej z następujących metod:

* Wdrażanie wszystkich aktualizacji jako całkowicie nowe funkcje (nowe nazwy).
* Wszystkie aktualizacje należy wdrożyć jako nową aplikację funkcji z innego konta magazynu.
* Wdrażanie nowej kopii aplikacji funkcji, ale zaktualizowaną `TaskHub` nazwy. Jest to zalecana metoda.

### <a name="how-to-change-task-hub-name"></a>Jak zmienić nazwę Centrum zadań

Centrum zadań można skonfigurować w *host.json* plików w następujący sposób:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

Wartość domyślna to `DurableFunctionsHub`.

Wszystkie jednostki usługi Azure Storage są nazywane na podstawie `HubName` wartości konfiguracji. Podając nową nazwę koncentratora zadań, sprawdź, są tworzone oddzielne kolejek oraz tabela historii dla nowej wersji aplikacji.

Firma Microsoft zaleca wdrożenia nowej wersji aplikacji funkcji na nowy [miejsce wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Miejsca wdrożenia umożliwiają uruchamianie wielu kopii z funkcji aplikacji po stronie by-side tylko jeden z nich jako aktywne *produkcji* miejsca. Gdy wszystko będzie gotowe do udostępnienia nowej logiki aranżacji w istniejącej infrastrukturze, może być tak proste, jak wymiany nowej wersji do miejsca produkcji.

> [!NOTE]
> Ta strategia działa najlepiej, gdy używasz wyzwalacze HTTP i elementu webhook dla funkcji programu orchestrator. Wyzwalaczy protokołu HTTP, takie jak kolejki i usługi Event Hubs definicję wyzwalacza powinien pochodzić z ustawienia aplikacji, która jest aktualizowana w ramach operacji wymiany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać wydajności i skalowania problemów](durable-functions-perf-and-scale.md)
