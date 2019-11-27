---
title: Niezawodne aranżacje — Azure Functions
description: Wprowadzenie do funkcji aranżacji dla usługi Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 54e1eb0be18de8e5ed420e96629d6f23473272fe
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545705"
---
# <a name="durable-orchestrations"></a>Nietrwałe aranżacje

Durable Functions jest rozszerzeniem [Azure Functions](../functions-overview.md). Za pomocą funkcji programu *Orchestrator* można organizować wykonywanie innych trwałych funkcji w ramach aplikacji funkcji. Funkcje programu Orchestrator mają następującą charakterystykę:

* Funkcje programu Orchestrator definiują przepływy pracy funkcji przy użyciu kodu proceduralnego. Brak schematów deklaratywnych ani projektantów.
* Funkcje programu Orchestrator mogą wywoływać inne trwałe funkcje synchronicznie i asynchronicznie. Dane wyjściowe z wywołanych funkcji mogą być niezawodnie zapisywane w zmiennych lokalnych.
* Funkcje programu Orchestrator są trwałe i niezawodne. Postęp wykonywania jest automatycznie przetwarzany, gdy funkcja "await" lub "implikuje". Możliwość odtwarzania procesu lub ponownego uruchamiania maszyny wirtualnej bez utraty stanu lokalnego.
* Funkcje programu Orchestrator mogą być długotrwałe. Łącznym cykl życia *wystąpienia aranżacji* może być sekunda, dni, miesiące lub nie kończące się.

Ten artykuł zawiera omówienie funkcji programu Orchestrator oraz sposób, w jaki mogą one pomóc w rozwiązywaniu różnych wyzwań związanych z programowaniem aplikacji. Jeśli nie znasz już typów funkcji dostępnych w aplikacji Durable Functions, najpierw Przeczytaj artykuł o [typach trwałych funkcji](durable-functions-types-features-overview.md) .

## <a name="orchestration-identity"></a>Tożsamość aranżacji

Każde *wystąpienie* aranżacji ma identyfikator wystąpienia (znany również jako *Identyfikator wystąpienia*). Domyślnie każdy identyfikator wystąpienia jest automatycznie generowanym identyfikatorem GUID. Identyfikatory wystąpień mogą jednak być dowolną wartością ciągu wygenerowaną przez użytkownika. Każdy identyfikator wystąpienia aranżacji musi być unikatowy w ramach [centrum zadań](durable-functions-task-hubs.md).

Poniżej przedstawiono niektóre reguły dotyczące identyfikatorów wystąpień:

* Identyfikatory wystąpień muszą zawierać od 1 do 256 znaków.
* Identyfikatory wystąpień nie mogą rozpoczynać się od `@`.
* Identyfikatory wystąpień nie mogą zawierać `/`, `\`, `#`ani `?` znaków.
* Identyfikatory wystąpień nie mogą zawierać znaków kontrolnych.

> [!NOTE]
> Zwykle zaleca się używanie automatycznie generowanych identyfikatorów wystąpień, jeśli jest to możliwe. Identyfikatory wystąpień generowane przez użytkownika są przeznaczone dla scenariuszy, w których istnieje mapowanie jeden do jednego między wystąpieniem aranżacji a niezależną jednostką specyficzną dla aplikacji, taką jak zamówienie zakupu lub dokument.

Identyfikator wystąpienia aranżacji jest wymaganym parametrem dla większości [operacji zarządzania wystąpieniami](durable-functions-instance-management.md). Są one również ważne dla celów diagnostycznych, takich jak [wyszukiwanie danych śledzenia aranżacji](durable-functions-diagnostics.md#application-insights) w Application Insights na potrzeby rozwiązywania problemów lub analiz. Z tego powodu zaleca się zapisanie wygenerowanych identyfikatorów wystąpień do niektórych lokalizacji zewnętrznych (na przykład bazy danych lub dzienników aplikacji), gdzie można z łatwością przywoływać się później.

## <a name="reliability"></a>Niezawodność

Program Orchestrator działa niezawodnie utrzymują swój stan wykonywania przy użyciu wzorca projektowego [pozyskiwania zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Zamiast bezpośredniego przechowywania bieżącego stanu aranżacji, trwała platforma zadań korzysta ze sklepu tylko do dołączania, aby zarejestrować pełną serię akcji podejmowanych przez aranżację funkcji. Magazyn tylko do dołączania ma wiele korzyści w porównaniu do "zatopienia" całego stanu środowiska uruchomieniowego. Korzyści obejmują zwiększoną wydajność, skalowalność i elastyczność. Istnieje również możliwość zapewnienia spójności ostatecznej danych transakcyjnych oraz pełnych i historycznych historii inspekcji. Dzienniki inspekcji obsługują niezawodne akcje kompensowania.

Durable Functions używa źródeł zdarzeń w sposób przezroczysty. W tle operator `await` (C#) lub `yield` (JavaScript) w funkcji Orchestrator zapewnia kontrolę nad wątkiem programu Orchestrator z powrotem do dyspozytora trwałych struktur zadań. Następnie Dyspozytor zatwierdza wszelkie nowe akcje zaplanowane przez funkcję programu Orchestrator (takie jak wywołanie co najmniej jednej funkcji podrzędnej lub planowanie trwałego czasomierza) do magazynu. Nieprzezroczysta akcja zatwierdzania dołącza do historii wykonywania wystąpienia aranżacji. Historia jest przechowywana w tabeli magazynu. Akcja Zatwierdź powoduje dodanie komunikatów do kolejki w celu zaplanowania rzeczywistej pracy. W tym momencie funkcja programu Orchestrator może zostać zwolniona z pamięci.

Gdy funkcja aranżacji ma więcej pracy do wykonania (na przykład komunikat odpowiedzi jest odbierany lub trwały czasomierz wygasa), program Orchestrator wznawia działanie i ponownie wykonuje całą funkcję od początku, aby skompilować stan lokalny. W trakcie powtarzania, jeśli kod próbuje wywołać funkcję (lub wykonać inną zadani asynchroniczne), w środowisku trwałym można sprawdzić historię wykonywania bieżącej aranżacji. Jeśli okaże się, że [Funkcja działania](durable-functions-types-features-overview.md#activity-functions) została już wykonana i wygeneruje wynik, odtwarza wynik tej funkcji, a kod programu Orchestrator nadal działa. Odtwarzanie powtarza się do momentu zakończenia kodu funkcji lub do momentu zaplanowania nowej pracy asynchronicznej.

> [!NOTE]
> Aby wzorzec powtarzania działał poprawnie i niezawodnie, kod funkcji programu Orchestrator musi być *deterministyczny*. Aby uzyskać więcej informacji na temat ograniczeń dotyczących kodu dla funkcji programu Orchestrator, zobacz temat [ograniczenia kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) .

> [!NOTE]
> Jeśli funkcja programu Orchestrator emituje komunikaty dziennika, zachowanie odtwarzania może spowodować wyemitowanie zduplikowanych komunikatów dziennika. Zapoznaj się z tematem [Rejestrowanie](durable-functions-diagnostics.md#logging) , aby dowiedzieć się więcej o tym, dlaczego takie zachowanie występuje i jak obejść to działanie.

## <a name="orchestration-history"></a>Historia aranżacji

Zachowanie związane ze źródłem zdarzenia trwałej struktury zadań jest ściśle powiązane z kodem funkcji programu Orchestrator, który napiszesz. Załóżmy, że masz funkcję programu Orchestrator służącą do łańcucha aktywności, taką jak C# następująca funkcja programu Orchestrator:

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

W przypadku kodowania w języku JavaScript funkcja programu Orchestrator łańcucha aktywności może wyglądać podobnie do następującego przykładowego kodu:

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

W każdej instrukcji `await`C#() lub `yield` (JavaScript), trwała struktura zadań określa stan wykonywania funkcji w niektórych trwałych zapleczach magazynu (zwykle w usłudze Azure Table Storage). Ten stan jest określany mianem *historii aranżacji*.

### <a name="history-table"></a>Tabela historii

Ogólnie rzecz biorąc, usługa trwałej struktury zadań w każdym punkcie kontrolnym wykonuje następujące czynności:

1. Zapisuje historię wykonywania w tabelach usługi Azure Storage.
2. Komunikaty enqueues dla funkcji wywoływanych przez koordynatora.
3. Enqueues komunikaty dotyczące samego koordynatora &mdash; na przykład trwałe komunikaty czasomierza.

Po zakończeniu punktu kontrolnego funkcja programu Orchestrator jest bezpłatna do usunięcia z pamięci, dopóki nie będzie więcej pracy.

> [!NOTE]
> Usługa Azure Storage nie zapewnia żadnych transakcyjnych gwarancji między zapisywaniem danych w usłudze Table Storage i kolejkami. Aby obsłużyć błędy, dostawca magazynu Durable Functions będzie używać wzorców *spójności ostatecznej* . Te wzorce zapewniają, że żadne dane nie zostaną utracone w przypadku awarii lub utraty łączności w środku punktu kontrolnego.

Po zakończeniu historia funkcji pokazanej wcześniej wygląda podobnie do poniższej tabeli na platformie Azure Table Storage (skrócony dla celów ilustracyjnych):

| PartitionKey (identyfikator wystąpienia)                     | Klasę             | Znacznik czasu               | Dane wejściowe | Nazwa             | Wynik                                                    | Stan |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" Hello Tokio! ""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" Hello Seattle! ""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" Hello Londyn! ""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[" Hello Tokio! "", "Hello Seattle!" "," Hello Londyn! ""] " | Zakończone           |

Kilka informacji o wartościach kolumn:

* **PartitionKey**: zawiera identyfikator wystąpienia aranżacji.
* **EventType**: reprezentuje typ zdarzenia. Może być jednym z następujących typów:
  * **OrchestrationStarted**: funkcja programu Orchestrator została wznowiona z oczekiwania lub jest uruchomiona po raz pierwszy. Kolumna `Timestamp` służy do wypełniania wartości deterministycznych dla interfejsów API `CurrentUtcDateTime` (.NET) i `currentUtcDateTime` (JavaScript).
  * **ExecutionStarted**: funkcja programu Orchestrator rozpoczęła wykonywanie po raz pierwszy. To zdarzenie zawiera również dane wejściowe funkcji w kolumnie `Input`.
  * **TaskScheduled**: zaplanowano funkcję działania. Nazwa funkcji działania jest przechwytywana w kolumnie `Name`.
  * **TaskCompleted**: funkcja działania została ukończona. Wynik funkcji znajduje się w kolumnie `Result`.
  * **TimerCreated**: utworzono trwały czasomierz. Kolumna `FireAt` zawiera zaplanowany czas UTC, po upływie którego czasomierz wygasa.
  * **TimerFired**: wywołano trwały czasomierz.
  * **EventRaised**: zewnętrzne zdarzenie zostało wysłane do wystąpienia aranżacji. Kolumna `Name` przechwytuje nazwę zdarzenia, a kolumna `Input` przechwytuje ładunek zdarzenia.
  * **OrchestratorCompleted**: funkcja programu Orchestrator oczekuje.
  * **ContinueAsNew**: funkcja programu Orchestrator została zakończona i uruchomiona ponownie z nowym stanem. Kolumna `Result` zawiera wartość, która jest używana jako dane wejściowe w ponownie uruchomionym wystąpieniu.
  * **ExecutionCompleted**: funkcja programu Orchestrator działała do ukończenia (lub nie powiodła się). Dane wyjściowe funkcji lub szczegóły błędu są przechowywane w kolumnie `Result`.
* **Timestamp**: sygnatura czasowa UTC zdarzenia historii.
* **Name**: nazwa funkcji, która została wywołana.
* **Wejście**: dane wejściowe w formacie JSON.
* **Wynik**: dane wyjściowe funkcji; oznacza to, że wartość zwracana.

> [!WARNING]
> Chociaż jest to narzędzie do debugowania, nie należy podejmować żadnych zależności od tej tabeli. Może ulec zmianie w miarę rozwoju rozszerzenia Durable Functions.

Za każdym razem, gdy funkcja wznawia działanie z `await`C#() lub `yield` (JavaScript), trwała struktura zadań ponownie uruchamia funkcję programu Orchestrator od podstaw. Na każdym ponownym uruchomieniu zapoznaj się z historią wykonywania w celu ustalenia, czy bieżąca operacja asynchroniczna została wykonana.  Jeśli operacja miała miejsce, struktura odtwarza dane wyjściowe tej operacji natychmiast i przechodzi do następnego `await` (C#) lub `yield` (JavaScript). Ten proces jest kontynuowany do momentu odtworzenia całej historii. Po odinstalowaniu bieżącej historii zmienne lokalne zostaną przywrócone do poprzednich wartości.

## <a name="features-and-patterns"></a>Funkcje i wzorce

W następnych sekcjach opisano funkcje i wzorce funkcji programu Orchestrator.

### <a name="sub-orchestrations"></a>Orkiestracje podrzędne

Funkcje programu Orchestrator umożliwiają wywoływanie funkcji działania, ale również innych funkcji programu Orchestrator. Można na przykład utworzyć większą organizację poza biblioteką funkcji programu Orchestrator. Lub można uruchomić wiele wystąpień funkcji programu Orchestrator równolegle.

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz artykuł dotyczący [aranżacji podrzędnych](durable-functions-sub-orchestrations.md) .

### <a name="durable-timers"></a>Trwałe czasomierze

Aranżacje mogą zaplanować *trwałe czasomierze* w celu zaimplementowania opóźnień lub skonfigurować obsługę limitu czasu dla akcji asynchronicznych. Używaj trwałych czasomierzy w funkcjach programu Orchestrator zamiast `Thread.Sleep`C#i `Task.Delay` () lub `setTimeout()` i `setInterval()` (JavaScript).

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz artykuł dotyczący [trwałych czasomierzy](durable-functions-timers.md) .

### <a name="external-events"></a>Zdarzenia zewnętrzne

Funkcje programu Orchestrator mogą czekać, aż zdarzenia zewnętrzne zaktualizują wystąpienie aranżacji. Ta funkcja Durable Functions często jest przydatna do obsługi interakcji człowieka lub innych zewnętrznych wywołań zwrotnych.

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz artykuł dotyczący [zdarzeń zewnętrznych](durable-functions-external-events.md) .

### <a name="error-handling"></a>Obsługa błędów

Funkcje programu Orchestrator mogą korzystać z funkcji obsługi błędów w języku programowania. W kodzie aranżacji są obsługiwane istniejące wzorce, takie jak `try`/`catch`.

Funkcje programu Orchestrator mogą także dodawać zasady ponawiania dla działania lub funkcji programu Orchestrator, które wywołuje. Jeśli działanie lub funkcja podrzędna nie powiedzie się z wyjątkiem, określone zasady ponawiania mogą być automatycznie opóźniane i ponawiać próbę wykonania przez maksymalnie określoną liczbę razy.

> [!NOTE]
> W przypadku nieobsługiwanego wyjątku w funkcji programu Orchestrator wystąpienie aranżacji zakończy się w stanie `Failed`. Nie można ponowić próby wystąpienia aranżacji, gdy zakończyło się niepowodzeniem.

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz artykuł [dotyczący obsługi błędów](durable-functions-error-handling.md) .

### <a name="critical-sections-durable-functions-2x"></a>Sekcje krytyczne (Durable Functions 2. x)

Wystąpienia aranżacji są jednowątkowe, dlatego nie trzeba martwić się o sytuacje wyścigu *w ramach* aranżacji. Jednakże sytuacje wyścigu są możliwe, gdy aranżacje współpracują z systemami zewnętrznymi. Aby wyeliminować sytuacje wyścigu podczas współpracy z systemami zewnętrznymi, funkcje programu Orchestrator mogą definiować *sekcje krytyczne* przy użyciu metody `LockAsync` w programie .NET.

Poniższy przykładowy kod pokazuje funkcję programu Orchestrator, która definiuje sekcję krytyczną. Wprowadza sekcję krytyczną przy użyciu metody `LockAsync`. Ta metoda wymaga przekazania co najmniej jednego odwołania do [jednostki trwałej](durable-functions-entities.md), która trwale zarządza stanem blokady. Tylko jedno wystąpienie tej aranżacji może wykonać kod w sekcji krytycznej w danym momencie.

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

`LockAsync` uzyskuje trwałe blokady i zwraca `IDisposable` kończący sekcję krytyczną po usunięciu. Ten `IDisposable` wynik może być używany razem z blokiem `using`, aby uzyskać składniową reprezentację sekcji krytycznej. Gdy funkcja programu Orchestrator wprowadza sekcję krytyczną, tylko jedno wystąpienie może wykonać ten blok kodu. Wszystkie inne wystąpienia, które próbują wprowadzić sekcję krytyczną, zostaną zablokowane do momentu opuszczenia sekcji krytycznej w poprzednim wystąpieniu.

Funkcja sekcji krytycznej jest również przydatna do koordynowania zmian w trwałych jednostkach. Aby uzyskać więcej informacji na temat sekcji krytycznych, zobacz temat ["koordynacja jednostek trwałych](durable-functions-entities.md#entity-coordination) ".

> [!NOTE]
> Sekcje krytyczne są dostępne w Durable Functions 2,0 i nowszych. Obecnie tylko aranżacje platformy .NET implementują tę funkcję.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Wywoływanie punktów końcowych HTTP (Durable Functions 2. x)

Funkcje programu Orchestrator nie zezwalają na wykonywanie operacji we/wy, zgodnie z opisem w temacie [ograniczenia kodu funkcji](durable-functions-code-constraints.md)w programie Orchestrator. Typowym obejściem tego ograniczenia jest Zawijanie dowolnego kodu, który musi wykonać operacje we/wy w funkcji działania. Aranżacje, które współdziałają z systemami zewnętrznymi, często używają funkcji działania, aby nawiązywać wywołania HTTP i zwracać wynik do aranżacji.

Aby uprościć ten wspólny wzorzec, funkcje programu Orchestrator mogą używać metody `CallHttpAsync` w programie .NET do bezpośredniego wywoływania interfejsów API protokołu HTTP. Oprócz obsługi podstawowych wzorców żądania/odpowiedzi `CallHttpAsync` obsługuje automatyczną obsługę typowych wzorców sondowania asynchronicznych HTTP 202, a także obsługuje uwierzytelnianie z usługami zewnętrznymi przy użyciu [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md).

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

Aby uzyskać więcej informacji i zapoznać się z szczegółowymi przykładami, zobacz artykuł dotyczący [funkcji http](durable-functions-http-features.md) .

> [!NOTE]
> Wywoływanie punktów końcowych HTTP bezpośrednio z funkcji programu Orchestrator jest dostępne w Durable Functions 2,0 i nowszych.

### <a name="passing-multiple-parameters"></a>Przekazywanie wielu parametrów

Nie jest możliwe bezpośrednie przekazywanie wielu parametrów do funkcji działania. Zalecenie jest przekazywane do tablicy obiektów lub do korzystania z obiektów [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) w programie .NET.

Poniższy przykład korzysta z nowych funkcji [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) dodanych z [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ograniczenia kodu programu Orchestrator](durable-functions-code-constraints.md)
