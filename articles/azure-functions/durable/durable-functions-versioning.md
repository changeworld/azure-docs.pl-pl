---
title: Przechowywanie wersji w funkcje trwałe - Azure
description: Dowiedz się, jak wdrożyć przechowywanie wersji w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 33ca6c36cd11d53a3c50a8374181c511fd2f8c3e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648153"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Przechowywanie wersji w funkcje trwałe (usługa Azure Functions)

Jest nieuniknione, że funkcje będzie można dodać, usunął i zmienił w okresie istnienia aplikacji. [Trwałe funkcje](durable-functions-overview.md) umożliwia tworzenie łańcuchów funkcji razem w sposób, który nie był wcześniej możliwe i ten łańcuch ma wpływ na sposób może obsługiwać przechowywanie wersji.

## <a name="how-to-handle-breaking-changes"></a>Jak obsługiwać zmiany powodujące niezgodność

Istnieje kilka przykładów przełomowe zmiany, których trzeba pamiętać. W tym artykule omówiono najbardziej typowe źródła. Główne motywu za wszystkie z nich jest zarówno aranżacji nowych i istniejących funkcji mają wpływ zmiany kodu funkcji.

### <a name="changing-activity-function-signatures"></a>Zmiana sygnatury funkcji działań

Zmień podpis odnosi się do zmiany nazwy, dane wejściowe lub danych wyjściowych przez funkcję. Tego rodzaju zmiany należy do działania funkcji może spowodować przerwanie funkcja orkiestratora, która zależy od niego. Jeśli zaktualizujesz funkcji programu orchestrator w celu uwzględnienia tej zmiany, może spowodować przerwanie istniejące locie wystąpienia.

Na przykład załóżmy, że mamy następującą funkcję.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta funkcja uproszczony pobiera wyniki **Foo** i przekazuje go do **pasek**. Załóżmy, że trzeba zmienić wartość zwracaną przez **Foo** z `bool` do `int` do obsługi szerszy zakres wartości dla wyników. Wynik wygląda następująco:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta zmiana działa prawidłowo dla wszystkich wystąpień nowych funkcji programu orchestrator, ale Dzieli aktywne wystąpienia. Na przykład, rozważmy przypadek, gdzie wystąpienie aranżacji wywołuje **Foo**, otrzymuje wartość logiczną, a następnie punktów kontrolnych. Jeśli zmiana podpisu jest wdrażana w tym momencie, wystąpienie utworzono punkt kontrolny nie powiedzie się natychmiast po wznowieniu działania i odtwarza wywołanie `context.CallActivityAsync<int>("Foo")`. Jest to spowodowane wynik w tabeli historii jest `bool` , ale nowy kod próbuje wykonać deserializacji go do `int`.

Jest to tylko jedna wiele różnych sposobów, Zmień podpis może uszkodzić istniejące wystąpienia. Ogólnie rzecz biorąc gdy trzeba zmienić sposób koordynatora wywołuje funkcję, a następnie ta zmiana jest przyczyną problemów.

### <a name="changing-orchestrator-logic"></a>Zmiana logiki programu orchestrator

Klasa problemy z wersjonowaniem pochodzą zmiana kodu funkcji programu orchestrator w sposób zapewniający confuses logikę powtarzania śledząc wystąpień.

Rozważmy następującą funkcję programu orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Teraz załóżmy, że chcesz próbę dokonania zmiany pozornie nieszkodliwie, aby dodać inne wywołanie funkcji.

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

Ta zmiana dodaje nowe wywołanie funkcji **SendNotification** między **Foo** i **pasek**. Nie wprowadzono żadnych zmian podpisu. Problem pojawia się po istniejącego wystąpienia wznowieniu działania z wywołania **pasek**. Podczas powtórzeń, jeśli oryginalne wywołanie **Foo** zwrócił `true`, a następnie powtarzanie orchestrator będzie wywoływać **SendNotification** która znajduje się w historię jej wykonywania. W rezultacie trwałe Framework zadań kończy się niepowodzeniem z `NonDeterministicOrchestrationException` ponieważ napotkał wywołanie **SendNotification** gdy oczekuje się wywołanie **pasek**.

## <a name="mitigation-strategies"></a>Strategii ograniczania ryzyka

Oto kilka strategii radzenia sobie z wersji wyzwania:

* Nic nie rób
* Zatrzymaj wszystkie wystąpienia w locie
* Side-by-side wdrożeń

### <a name="do-nothing"></a>Nic nie rób

Jest najprostszym sposobem obsługi istotnej zmiany umożliwiające śledząc aranżacji, wystąpień się nie powieść. Nowe wystąpienia pomyślnie uruchomić zmiany kodu.

Czy jest to problem, zależy od znaczenie śledząc wystąpień. Jeśli jesteś w aktywnie i nie dba o śledząc wystąpień, może to być wystarczające. Należy jednak wyjątki i błędy w potoku diagnostyki. Jeśli chcesz uniknąć tych elementów, należy wziąć pod uwagę innych opcji przechowywania wersji.

### <a name="stop-all-in-flight-instances"></a>Zatrzymaj wszystkie wystąpienia w locie

Innym rozwiązaniem jest zatrzymanie wszystkich wystąpień w locie. Można to zrobić przez wyczyszczenie zawartości, wewnętrznego **kolejki kontroli** i **kolejki elementów roboczych** kolejek. Wystąpienia będzie zawsze zablokowany, gdzie znajdowały, ale ich nie będzie zaśmiecać telemetrii za pomocą komunikatów o błędach. Jest to idealne rozwiązanie w prototypu szybkiego rozwoju.

> [!WARNING]
> Szczegółowe informacje o tych kolejkach mogą ulec zmianie wraz z upływem czasu, dlatego nie należy polegać na tej techniki w przypadku obciążeń produkcyjnych.

### <a name="side-by-side-deployments"></a>Side-by-side wdrożeń

Najbardziej Niepowodzenie weryfikacji sposób zapewnienia przełomowe zmiany wdrożonych bezpiecznie jest przez wdrażanie ich side-by-side przy użyciu usługi starszych wersji. Można to zrobić przy użyciu dowolnej z następujących technik:

* Wdrażanie wszystkich aktualizacji jako całkowicie nowe funkcje (nowe nazwy).
* Wszystkie aktualizacje należy wdrożyć jako nową aplikację funkcji przy użyciu innego konta magazynu.
* Wdrażanie nowej kopii aplikacji funkcji, ale zaktualizowaną `TaskHub` nazwy. Jest to zalecana technika.

### <a name="how-to-change-task-hub-name"></a>Jak zmienić nazwę Centrum zadania

Centrum zadania można skonfigurować w *host.json* pliku w następujący sposób:

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

Wszystkie jednostki usługi Azure Storage są nazywane na podstawie `HubName` wartości konfiguracji. Dając Centrum zadań nową nazwę, możesz upewnij się, oddzielne kolejek i tabel historii są tworzone dla nowej wersji aplikacji.

Firma Microsoft zaleca Wdrażanie nowej wersji aplikacji funkcji na nowe [miejsce wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Miejsca wdrożenia umożliwiają uruchamianie wielu kopii Twojej funkcji aplikacji side-by-side przy użyciu tylko jeden z nich jako aktywny *produkcji* miejsca. Gdy wszystko jest gotowe do udostępnienia nowej logiki aranżacji w istniejącej infrastrukturze, może być proste i polega na zamianę nowej wersji do miejsca produkcji.

> [!NOTE]
> Ta strategia sprawdza się najlepiej, korzystając z wyzwalaczami HTTP i elementy webhook dla funkcji programu orchestrator. Wyzwalaczy protokołu HTTP, takich jak kolejki i usługi Event Hubs do definicji wyzwalacza powinien [dziedziczyć ustawienia aplikacji](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , zostanie zaktualizowany w ramach operacji wymiany.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać wydajność i skalowanie problemów](durable-functions-perf-and-scale.md)
