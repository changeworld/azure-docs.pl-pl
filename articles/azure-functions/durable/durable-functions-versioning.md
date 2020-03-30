---
title: Przechowywanie wersji w trwałych funkcjach — Azure
description: Dowiedz się, jak zaimplementować przechowywanie wersji w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232757"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Przechowywanie wersji w funkcjach trwałych (usługi Azure)

Jest nieuniknione, że funkcje zostaną dodane, usunięte i zmienione w okresie istnienia aplikacji. [Funkcje trwałe](durable-functions-overview.md) umożliwia łączenie funkcji razem w sposób, który nie był wcześniej możliwe, a to tworzenie łańcucha wpływa na sposób obsługi wersji.

## <a name="how-to-handle-breaking-changes"></a>Jak radzić sobie ze zmianami przerywania

Istnieje kilka przykładów przełomowych zmian, o których należy pamiętać. W tym artykule omówiono te najczęściej. Głównym tematem za wszystkie z nich jest, że zarówno nowe, jak i istniejące aranżacji funkcji mają wpływ zmiany w kodzie funkcji.

### <a name="changing-activity-or-entity-function-signatures"></a>Zmienianie podpisów funkcji działania lub encji

Zmiana podpisu odnosi się do zmiany nazwy, danych wejściowych lub wyjściowych funkcji. Jeśli tego rodzaju zmiany są dokonywane do działania lub funkcji jednostki, może przerwać dowolną funkcję koordynatora, który zależy od niego. Jeśli zaktualizujesz funkcję koordynatora, aby uwzględnić tę zmianę, można przerwać istniejące wystąpienia w locie.

Na przykład załóżmy, że mamy następującą funkcję koordynatora.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ta uproszczona funkcja przyjmuje wyniki **Foo** i przekazuje go do **baru.** Załóżmy, że musimy zmienić wartość zwracaną `int` **Foo** z `bool` do obsługi szerszej gamy wartości wyników. Wynik wygląda następująco:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Poprzednie przykłady Języka C# docelowe funkcje trwałe 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

Ta zmiana działa poprawnie dla wszystkich nowych wystąpień funkcji koordynatora, ale przerywa wszelkie wystąpienia w locie. Rozważmy na przykład przypadek, w którym wystąpienie aranżacji wywołuje funkcję o nazwie `Foo`, zwraca wartość logiczną, a następnie punkty kontrolne. Jeśli zmiana podpisu zostanie wdrożona w tym momencie, wystąpienie z punktu kontrolnego zakończy się niepowodzeniem natychmiast po wznowieniu i powtórzeniu połączenia z programem `context.CallActivityAsync<int>("Foo")`. Ten błąd występuje, ponieważ wynik `bool` w tabeli historii jest, ale `int`nowy kod próbuje deserialize go do .

W tym przykładzie jest tylko jednym z wielu różnych sposobów, że zmiana podpisu może podzielić istniejące wystąpienia. Ogólnie rzecz biorąc, jeśli koordynator musi zmienić sposób wywołuje funkcję, a następnie zmiana może być problematyczne.

### <a name="changing-orchestrator-logic"></a>Zmiana logiki orkiestratora

Inne problemy z przechowywaniem wersji pochodzą ze zmiany kodu funkcji koordynatora w sposób, który myli logikę powtarzania dla wystąpień w locie.

Należy wziąć pod uwagę następującą funkcję koordynatora:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Teraz załóżmy, że chcesz dokonać pozornie niewinnej zmiany, aby dodać kolejne wywołanie funkcji.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Poprzednie przykłady Języka C# docelowe funkcje trwałe 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

Ta zmiana dodaje nowe wywołanie funkcji do **SendNotification** między **Foo** i **Bar**. Nie ma żadnych zmian podpisu. Problem pojawia się, gdy istniejące wystąpienie zostanie wznowione z wywołania do **paska**. Podczas odtwarzania, jeśli oryginalne **wywołanie Foo** wrócił `true`, a następnie powtórka orkiestry wywoła **SendNotification**, który nie jest w historii wykonywania. W rezultacie trwałe framework zadań kończy `NonDeterministicOrchestrationException` się niepowodzeniem z, ponieważ napotkał wywołanie **SendNotification,** gdy oczekuje się, aby zobaczyć wywołanie **bar**. Ten sam typ problemu może wystąpić podczas dodawania wszelkich wywołań do "trwałych" interfejsów API, w tym `CreateTimer`, `WaitForExternalEvent`itp.

## <a name="mitigation-strategies"></a>Strategie łagodzenia zmiany klimatu

Oto niektóre ze strategii radzenia sobie z problemami związanymi z przechowywaniem wersji:

* Nic nie rób
* Zatrzymywać wszystkie wystąpienia podczas lotu
* Wdrożenia obok siebie

### <a name="do-nothing"></a>Nic nie rób

Najprostszym sposobem obsługi zmiany podziału jest niech instancji aranżacji w locie zakończyć się niepowodzeniem. Nowe wystąpienia pomyślnie uruchomić zmieniony kod.

To, czy ten rodzaj awarii jest problemem, zależy od znaczenia wystąpień podczas lotu. Jeśli jesteś w aktywnym rozwoju i nie dbają o instancje w locie, może to być wystarczająco dobre. Jednak należy radzić sobie z wyjątkami i błędami w potoku diagnostyki. Jeśli chcesz uniknąć tych rzeczy, należy wziąć pod uwagę inne opcje przechowywania wersji.

### <a name="stop-all-in-flight-instances"></a>Zatrzymywać wszystkie wystąpienia podczas lotu

Inną opcją jest zatrzymanie wszystkich wystąpień podczas lotu. Zatrzymanie wszystkich wystąpień można wykonać, czyszcząc zawartość **kolejek kontroli** wewnętrznej i **kolejek roboczych.** Wystąpienia będą zawsze zablokowane tam, gdzie są, ale nie zaśmiecają dzienników komunikatami o błędach. Takie podejście jest idealne w szybkim rozwoju prototypów.

> [!WARNING]
> Szczegóły tych kolejek mogą się zmieniać wraz z czasem, więc nie polegaj na tej technice dla obciążeń produkcyjnych.

### <a name="side-by-side-deployments"></a>Wdrożenia obok siebie

Najbardziej niezawodnym sposobem zapewnienia bezpiecznego wdrażania zmian przerywania jest wdrożenie ich obok starszych wersji. Można to zrobić za pomocą dowolnej z następujących technik:

* Wdrażanie wszystkich aktualizacji jako całkowicie nowe funkcje, pozostawiając istniejące funkcje w stanie rzeczywistym. Może to być trudne, ponieważ wywołania nowych wersji funkcji muszą być aktualizowane, jak również zgodnie z tymi samymi wytycznymi.
* Wdrażanie wszystkich aktualizacji jako nowej aplikacji funkcji z innym kontem magazynu.
* Wdrażanie nowej kopii aplikacji funkcji z tym samym `taskHub` kontem magazynu, ale ze zaktualizowaną nazwą. Wdrożenia side-by-side jest zalecaną techniką.

### <a name="how-to-change-task-hub-name"></a>Jak zmienić nazwę centrum zadań

Centrum zadań można skonfigurować w pliku *host.json* w następujący sposób:

#### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funkcje 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Domyślną wartością funkcji trwałych `DurableFunctionsHub`v1.x jest . Począwszy od funkcji trwałych w wersji 2.0, domyślna nazwa centrum `TestHubName` zadań jest taka sama jak nazwa aplikacji funkcji na platformie Azure lub jeśli działa poza platformą Azure.

Wszystkie jednostki usługi Azure Storage `hubName` są nazwane na podstawie wartości konfiguracji. Nadając centrum zadań nową nazwę, należy upewnić się, że oddzielne kolejki i tabela historii są tworzone dla nowej wersji aplikacji. Aplikacja funkcji, jednak zatrzyma przetwarzania zdarzeń dla aranżacji lub jednostek utworzonych pod poprzednią nazwą centrum zadań.

Zaleca się wdrożenie nowej wersji aplikacji funkcji do nowego [gniazda wdrażania](../functions-deployment-slots.md). Miejsca wdrożenia umożliwiają uruchamianie wielu kopii aplikacji funkcji obok siebie, a tylko jeden z nich jako aktywne gniazdo *produkcyjne.* Gdy wszystko będzie gotowe do udostępnienia nowej logiki aranżacji do istniejącej infrastruktury, może to być tak proste, jak zamiana nowej wersji do gniazda produkcyjnego.

> [!NOTE]
> Ta strategia działa najlepiej, gdy używasz wyzwalaczy HTTP i webhook dla funkcji koordynatora. W przypadku wyzwalaczy innych niż HTTP, takich jak kolejki lub centra zdarzeń, definicja wyzwalacza powinna [pochodzić z ustawienia aplikacji,](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) które jest aktualizowane w ramach operacji wymiany.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak radzić sobie z problemami z wydajnością i skalą](durable-functions-perf-and-scale.md)
