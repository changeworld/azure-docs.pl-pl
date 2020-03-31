---
title: Trwałe aranżacji — funkcje platformy Azure
description: Wprowadzenie do funkcji aranżacji dla usług Azure funkcje trwałe.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241360"
---
# <a name="durable-orchestrations"></a>Trwałe aranżacji

Funkcje trwałe jest rozszerzeniem [funkcji platformy Azure](../functions-overview.md). Można użyć *funkcji orchestrator* do organizowania wykonywania innych trwałych funkcji w aplikacji funkcji. Funkcje koordynatora mają następujące cechy:

* Funkcje koordynatora definiują przepływy pracy funkcji przy użyciu kodu proceduralnego. Nie są potrzebne schematy deklaratywne ani projektanci.
* Funkcje orchestrator można wywołać inne trwałe funkcje synchronicznie i asynchronicznie. Dane wyjściowe z wywoływanych funkcji można niezawodnie zapisywać w zmiennych lokalnych.
* Funkcje orkiestratora są trwałe i niezawodne. Postęp wykonywania jest automatycznie punkt kontrolny, gdy funkcja "czeka" lub "daje". Możliwość odtwarzania procesu lub ponownego uruchamiania maszyny wirtualnej bez utraty stanu lokalnego.
* Funkcje orkiestratora mogą być długotrwałe. Całkowita żywotność *wystąpienia aranżacji* może wynosić sekundy, dni, miesiące lub niekończące się.

W tym artykule przedstawiono omówienie funkcji koordynatora i ich pomoc w rozwiązywaniu różnych problemów związanych z tworzeniem aplikacji. Jeśli nie są jeszcze zaznajomieni z typami funkcji dostępnych w aplikacji funkcje trwałe, przeczytaj najpierw [typy funkcji trwałe](durable-functions-types-features-overview.md) artykułu.

## <a name="orchestration-identity"></a>Tożsamość aranżacji

Każde *wystąpienie* aranżacji ma identyfikator wystąpienia (znany również jako *identyfikator wystąpienia).* Domyślnie każdy identyfikator wystąpienia jest automatyczniegenerowanym identyfikatorem GUID. Jednak identyfikatory wystąpień mogą być również dowolną wartością ciągu generowaną przez użytkownika. Każdy identyfikator wystąpienia aranżacji musi być unikatowy w [centrum zadań](durable-functions-task-hubs.md).

Poniżej przedstawiono niektóre reguły dotyczące identyfikatorów wystąpień:

* Identyfikatory wystąpień muszą zawierać od 1 do 256 znaków.
* Identyfikatory wystąpień nie `@`mogą zaczynać się od .
* Identyfikatory wystąpień nie `/` `\`mogą `#`zawierać `?` znaków ani znaków.
* Identyfikatory wystąpień nie mogą zawierać znaków sterujących.

> [!NOTE]
> Ogólnie zaleca się używanie automatyczniegenerowanych identyfikatorów wystąpień, gdy tylko jest to możliwe. Identyfikatory wystąpień generowane przez użytkownika są przeznaczone dla scenariuszy, w których istnieje mapowanie jeden do jednego między wystąpieniem aranżacji a jakąś jednostką specyficzną dla aplikacji zewnętrznej, taką jak zamówienie zakupu lub dokument.

Identyfikator wystąpienia aranżacji jest wymaganym parametrem dla większości [operacji zarządzania wystąpieniami.](durable-functions-instance-management.md) Są one również ważne dla diagnostyki, takich jak [przeszukiwanie danych śledzenia aranżacji](durable-functions-diagnostics.md#application-insights) w usłudze Application Insights do rozwiązywania problemów lub do celów analitycznych. Z tego powodu zaleca się zapisanie wygenerowanych identyfikatorów wystąpień w jakiejś lokalizacji zewnętrznej (na przykład w bazie danych lub w dziennikach aplikacji), gdzie można je łatwo odwołać później.

## <a name="reliability"></a>Niezawodność

Funkcje orchestrator niezawodnie utrzymać ich stan wykonywania przy użyciu wzorca projektowania [pozyskiwania zdarzeń.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) Zamiast bezpośrednio przechowywania bieżącego stanu aranżacji, trwałe Task Framework używa magazynu tylko do dołączania do nagrywania pełnej serii akcji aranżacji funkcji. Magazyn tylko do dołączania ma wiele korzyści w porównaniu do "dumpingu" w pełnym czasie wykonywania. Korzyści obejmują zwiększoną wydajność, skalowalność i szybkość reakcji. Można również uzyskać spójność ostateczną dla danych transakcyjnych i pełne ścieżki inspekcji i historii. Ścieżki inspekcji wspierają niezawodne działania kompensacyjne.

Funkcje trwałe używa źródła zdarzeń w sposób przejrzysty. W tle `await` (C#) lub `yield` (JavaScript) operator w funkcji orchestrator daje kontrolę wątku orchestrator z powrotem do dyspozytora trwałe task framework. Dyspozytor następnie zatwierdza wszelkie nowe akcje, które zaplanowano funkcję koordynatora (takie jak wywołanie jednej lub więcej funkcji podrzędnych lub planowanie trwałego czasomierza) do magazynu. Akcja przezroczystego zatwierdzania dołącza do historii wykonywania wystąpienia aranżacji. Historia jest przechowywana w tabeli magazynu. Akcja zatwierdzania następnie dodaje wiadomości do kolejki, aby zaplanować pracę rzeczywistą. W tym momencie funkcja koordynatora może zostać zwolniona z pamięci.

Gdy funkcja aranżacji ma więcej pracy do wykonania (na przykład komunikat odpowiedzi jest odbierany lub czasomierz trwałe wygasa), program koordynator wznawia i ponownie wykonuje całą funkcję od początku, aby odbudować stan lokalny. Podczas powtarzania, jeśli kod próbuje wywołać funkcję (lub wykonać dowolną inną pracę asynchroniczną), struktura trwałego zadania konsultuje się z historią wykonywania bieżącej aranżacji. Jeśli stwierdzi, że [funkcja działania](durable-functions-types-features-overview.md#activity-functions) została już wykonana i przyniosły wynik, odtwarza wynik tej funkcji i kod koordynatora nadal działać. Powtarzanie jest kontynuowane, dopóki kod funkcji nie zostanie ukończony lub dopóki nie zaplanował nowej pracy asynchronizowej.

> [!NOTE]
> Aby wzorzec odtwarzania działał poprawnie i niezawodnie, kod funkcji koordynatora musi być *deterministyczny.* Aby uzyskać więcej informacji na temat ograniczeń kodu dla funkcji koordynatora, zobacz temat [ograniczeń kodu funkcji koordynatora.](durable-functions-code-constraints.md)

> [!NOTE]
> Jeśli funkcja koordynatora emituje komunikaty dziennika, zachowanie powtarzania może spowodować, że zostaną wyemitowane zduplikowane komunikaty dziennika. Zobacz [Logging temat,](durable-functions-diagnostics.md#logging) aby dowiedzieć się więcej o tym, dlaczego to zachowanie występuje i jak obejść.

## <a name="orchestration-history"></a>Historia aranżacji

Zachowanie pozyskiwania zdarzeń trwałej struktury zadań jest ściśle powiązane z kodem funkcji koordynatora, który piszesz. Załóżmy, że masz funkcję koordynatora łańcucha działania, taką jak następująca funkcja koordynatora:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

---

W `await` każdej instrukcji (C#) lub `yield` (JavaScript) struktura trwałe task framework punktów kontrolnych stan wykonywania funkcji do niektórych trwałe zaplecze magazynu (zazwyczaj azure magazynu tabel). Ten stan jest to, co jest określane jako *historia aranżacji*.

### <a name="history-table"></a>Tabela historii

Ogólnie rzecz biorąc, trwałe ramy zadań wykonuje następujące czynności w każdym punkcie kontrolnym:

1. Zapisuje historię wykonywania w tabelach usługi Azure Storage.
2. Wysyła w kolejce wiadomości dla funkcji, które program orchestrator chce wywołać.
3. Wpisuje w kolejce wiadomości &mdash; dla samego koordynatora, na przykład komunikaty czasomierza trwałe.

Po zakończeniu punktu kontrolnego funkcja koordynatora może być usuwana z pamięci, dopóki nie będzie więcej pracy do wykonania.

> [!NOTE]
> Usługa Azure Storage nie zapewnia żadnych gwarancji transakcyjnych między zapisywaniem danych w magazynie tabel i kolejkach. Aby obsłużyć błędy, dostawca magazynu funkcji trwałych używa wzorców *spójności ostatecznej.* Te wzorce zapewniają, że żadne dane nie zostaną utracone, jeśli wystąpi awaria lub utrata łączności w środku punktu kontrolnego.

Po zakończeniu historia funkcji pokazanej wcześniej wygląda mniej więcej tak jak w poniższej tabeli w usłudze Azure Table Storage (w skrócie do celów ilustracyjnych):

| PartitionKey (identyfikator wystąpienia)                     | Typ zdarzenia             | Znacznik czasu               | Dane wejściowe | Nazwa             | Wynik                                                    | Stan |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | Rozpoczęcie wykonywania      | 2017-05-05T18:45:28.852Z | wartość null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | Zadanieplanowane         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorKończone | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | ZadanieZakończone         | 2017-05-05T18:45:34.201Z |       |                  | """Cześć Tokio!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | Zadanieplanowane         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorKończone | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | ZadanieZakończone         | 2017-05-05T18:45:34.763Z |       |                  | """Cześć Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Zadanieplanowane         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorKończone | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | ZadanieZakończone         | 2017-05-05T18:45:34.919Z |       |                  | """Cześć Londyn!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorKończone | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | WykonanieNiepełnione    | 2017-05-05T18:45:35.044Z |       |                  | "["Hello Tokyo!"","Hello Seattle!","Hello London!""]" | Zakończone           |

Kilka uwag na temat wartości kolumn:

* **PartitionKey**: Zawiera identyfikator wystąpienia aranżacji.
* **EventType**: Reprezentuje typ zdarzenia. Może to być jeden z następujących typów:
  * **OrchestrationStarted:** Funkcja orkiestratora wznowione z await lub jest uruchomiony po raz pierwszy. Kolumna `Timestamp` służy do wypełniania wartości deterministycznej dla `CurrentUtcDateTime` interfejsów API (.NET) i `currentUtcDateTime` (JavaScript).
  * **ExecutionStarted**: Funkcja koordynatora rozpoczęła wykonywanie po raz pierwszy. To zdarzenie zawiera również dane `Input` wejściowe funkcji w kolumnie.
  * **TaskScheduled**: Zaplanowano funkcję działania. Nazwa funkcji działania jest przechwytywany w kolumnie. `Name`
  * **ZadanieZakończone**: Funkcja działania ukończona. Wynik funkcji znajduje się `Result` w kolumnie.
  * **TimerCreated:** Utworzono trwały zegar. Kolumna `FireAt` zawiera zaplanowany czas UTC, w którym czasomierz wygasa.
  * **TimerFired**: Trwały zegar opalany.
  * **EventRaised**: Zdarzenie zewnętrzne zostało wysłane do wystąpienia aranżacji. Kolumna `Name` przechwytuje nazwę zdarzenia i `Input` kolumna przechwytuje ładunek zdarzenia.
  * **OrchestratorCompleted**: Oczekiwano funkcji orkiestra.
  * **ContinueAsNew**: Funkcja koordynatora została ukończona i ponownie uruchomiona z nowym stanem. Kolumna `Result` zawiera wartość, która jest używana jako dane wejściowe w ponownym wystąpieniu.
  * **ExecutionCompleted**: Funkcja koordynatora została ukończona (lub nie powiodła się). Dane wyjściowe funkcji lub szczegóły błędu są `Result` przechowywane w kolumnie.
* **Sygnatura czasowa:** Sygnatura czasowa UTC zdarzenia historii.
* **Nazwa**: Nazwa funkcji, która została wywołana.
* **Wejście:** Wejście funkcji w formacie JSON.
* **Wynik:** Wyjście funkcji; oznacza to, że jego wartość zwrotu.

> [!WARNING]
> Chociaż jest to przydatne jako narzędzie do debugowania, nie należy przyjmować żadnych zależności od tej tabeli. Może się zmienić w miarę rozwoju rozszerzenia Funkcje trwałe.

Za każdym razem, gdy `await` funkcja jest `yield` wznawiana z (C#) lub (JavaScript), trwała struktura zadań ponawia funkcję koordynatora od podstaw. Przy każdym ponownym uruchomieniu sprawdza historię wykonywania, aby ustalić, czy bieżąca operacja asynchronii miała miejsce.  Jeśli operacja miała miejsce, struktura odtwarza dane wyjściowe tej operacji `await` natychmiast i przechodzi `yield` do następnego (C#) lub (JavaScript). Ten proces trwa do momentu odtwo nie odtwołych całej historii. Po odtwoniu bieżącej historii zmienne lokalne zostaną przywrócone do ich poprzednich wartości.

## <a name="features-and-patterns"></a>Cechy i wzory

W następnych sekcjach opisano funkcje i wzorce funkcji koordynatora.

### <a name="sub-orchestrations"></a>Orkiestracje podrzędne

Funkcje orchestrator można wywołać funkcje działania, ale także inne funkcje orkiestratora. Na przykład można utworzyć większą aranżację z biblioteki funkcji koordynatora. Lub można uruchomić wiele wystąpień funkcji orkiestratora równolegle.

Aby uzyskać więcej informacji i przykłady, zobacz [sub-aranżacji](durable-functions-sub-orchestrations.md) artykułu.

### <a name="durable-timers"></a>Trwałe zegary

Aranżacji można zaplanować *trwałe czasomierze* do zaimplementowania opóźnień lub skonfigurować obsługę limitu czasu na akcje asynchronicznej. Użyj trwałe timery w funkcjach `Thread.Sleep` `Task.Delay` orkiestratora zamiast `setTimeout()` `setInterval()` i (C#) lub (JavaScript).

Aby uzyskać więcej informacji i przykłady, zobacz [trwałe czasomierze](durable-functions-timers.md) artykułu.

### <a name="external-events"></a>Zdarzenia zewnętrzne

Funkcje koordynatora mogą czekać na zdarzenia zewnętrzne, aby zaktualizować wystąpienie aranżacji. Ta funkcja funkcji trwałe często jest przydatna do obsługi interakcji z człowiekiem lub innych wywołań zwrotnych zewnętrznych.

Aby uzyskać więcej informacji i przykłady, zobacz zdarzenia [zewnętrzne](durable-functions-external-events.md) artykułu.

### <a name="error-handling"></a>Obsługa błędów

Funkcje orchestrator można użyć funkcji obsługi błędów języka programowania. Istniejące wzorce, takie jak `try` / `catch` są obsługiwane w kodzie aranżacji.

Funkcje orchestrator można również dodać zasady ponawiania do działania lub funkcji podaranatora, które wywołują. Jeśli działanie lub funkcja podaranżatora nie powiedzie się z wyjątkiem, określone zasady ponawiania próby można automatycznie opóźnić i ponowić próbę wykonania do określonej liczby razy.

> [!NOTE]
> Jeśli istnieje nieobsługiowany wyjątek w funkcji aranżatora, wystąpienie aranżacji zostanie ukończone `Failed` w stanie. Nie można ponowić wystąpienia aranżacji po jego zakończeniu.

Aby uzyskać więcej informacji i przykłady, zobacz [błąd obsługi](durable-functions-error-handling.md) artykułu.

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Sekcje krytyczne (funkcje trwałe 2.x, obecnie tylko .NET)

Wystąpienia aranżacji są jednowątkowe, więc nie jest konieczne martwić się o warunki wyścigu *w ramach* aranżacji. Jednak warunki wyścigu są możliwe, gdy aranżacji interakcji z systemami zewnętrznymi. Aby ograniczyć warunki wyścigu podczas interakcji z systemami zewnętrznymi, `LockAsync` funkcje aranżatora można zdefiniować *sekcje krytyczne* przy użyciu metody w .NET.

Poniższy przykładowy kod przedstawia funkcję koordynatora, która definiuje sekcję krytyczną. Wprowadza sekcję krytyczną `LockAsync` przy użyciu metody. Ta metoda wymaga przekazywania jednego lub więcej odwołań do [trwałego elementu](durable-functions-entities.md), który trwale zarządza stan blokady. Tylko jedno wystąpienie tej aranżacji można wykonać kod w sekcji krytycznej w czasie.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

Uzyskuje `LockAsync` trwałe blokady i `IDisposable` zwraca, który kończy sekcji krytycznej po usunięciu. Ten `IDisposable` wynik może służyć `using` razem z blokiem, aby uzyskać reprezentację składni sekcji krytycznej. Gdy funkcja koordynatora wchodzi do sekcji krytycznej, tylko jedno wystąpienie można wykonać tego bloku kodu. Wszystkie inne wystąpienia, które próbują wprowadzić sekcję krytyczną, zostaną zablokowane, dopóki poprzednie wystąpienie nie zakończy sekcji krytycznej.

Funkcja sekcji krytycznej jest również przydatna do koordynowania zmian trwałych elementów. Aby uzyskać więcej informacji na temat sekcji krytycznych, zobacz [trwałe jednostki "Koordynacja jednostki"](durable-functions-entities.md#entity-coordination) temat.

> [!NOTE]
> Sekcje krytyczne są dostępne w sekcji Funkcje trwałe 2.0 i powyżej. Obecnie tylko aranżacji platformy .NET implementuje tę funkcję.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Wywoływanie punktów końcowych HTTP (funkcje trwałe 2.x)

Funkcje programu Orchestrator nie mogą wykonywać funkcji We/Wy, zgodnie z opisem w [ograniczeniach kodu funkcji koordynatora.](durable-functions-code-constraints.md) Typowym obejściem tego ograniczenia jest zawijanie dowolnego kodu, który musi wykonać we/wy w funkcji działania. Aranżacje, które współdziałają z systemami zewnętrznymi często używają funkcji działania do wykonywania wywołań HTTP i zwracania wyniku do aranżacji.

# <a name="c"></a>[C #](#tab/csharp)

Aby uprościć ten wspólny wzorzec, funkcje programu orchestrator można użyć `CallHttpAsync` metody do wywołania interfejsów API HTTP bezpośrednio.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Oprócz obsługi podstawowych wzorców żądań/odpowiedzi, metoda obsługuje automatyczną obsługę typowych wzorców sondowania ASYNW ASYnc HTTP 202, a także obsługuje uwierzytelnianie za pomocą usług zewnętrznych przy użyciu [tożsamości zarządzanych.](../../active-directory/managed-identities-azure-resources/overview.md)

Aby uzyskać więcej informacji i szczegółowe przykłady, zobacz artykuł [o funkcjach HTTP.](durable-functions-http-features.md)

> [!NOTE]
> Wywoływanie punktów końcowych HTTP bezpośrednio z funkcji programu orchestrator jest dostępne w funkcji trwałych 2.0 i powyżej.

### <a name="passing-multiple-parameters"></a>Przekazywanie wielu parametrów

Nie jest możliwe przekazywanie wielu parametrów do funkcji działania bezpośrednio. Zalecenie jest przekazać w tablicy obiektów lub obiektów złożonych.

# <a name="c"></a>[C #](#tab/csharp)

W .NET można również użyć [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) obiektów. W poniższym przykładzie za pomocą nowych funkcji [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) dodane z [C# 7:](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples)

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="activity"></a>Działanie

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ograniczenia kodu orkiestratora](durable-functions-code-constraints.md)
