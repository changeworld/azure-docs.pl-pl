---
title: Wdrożenie bez przestojów dla trwałych funkcji
description: Dowiedz się, jak włączyć aranżację funkcji trwałych dla wdrożeń o zerowym przestoju.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231253"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Wdrożenie bez przestojów dla trwałych funkcji

Model [niezawodnego wykonywania](durable-functions-checkpointing-and-replay.md) funkcji trwałych wymaga, aby aranżacji być deterministyczne, co tworzy dodatkowe wyzwanie do rozważenia podczas wdrażania aktualizacji. Gdy wdrożenie zawiera zmiany w podpisach funkcji działania lub logiki aranżacji, wystąpienia aranżacji w locie nie powiodą się. Ta sytuacja jest szczególnie problem dla wystąpień długotrwałych aranżacji, które mogą reprezentować godziny lub dni pracy.

Aby zapobiec wystąpieniu tych błędów, dostępne są dwie opcje: 
- Opóźnij wdrożenie, dopóki nie zostaną ukończone wszystkie uruchomione wystąpienia aranżacji.
- Upewnij się, że wszystkie uruchomione wystąpienia aranżacji używają istniejących wersji funkcji. 

> [!NOTE]
> Ten artykuł zawiera wskazówki dotyczące funkcji aplikacji, które są przeznaczone funkcje trwałe 1.x. Nie został zaktualizowany w celu uwzględnienia zmian wprowadzonych w funkcji trwałych 2.x. Aby uzyskać więcej informacji na temat różnic między wersjami rozszerzenia, zobacz [Wersje funkcji trwałych](durable-functions-versions.md).

Na poniższym wykresie porównano trzy główne strategie umożliwiające osiągnięcie wdrożenia zerem przestojów dla funkcji trwałych: 

| Strategia |  Kiedy stosować | Zalety | Wady |
| -------- | ------------ | ---- | ---- |
| [Przechowywanie wersji](#versioning) |  Aplikacje, które nie występują częste [zmiany podziału.](durable-functions-versioning.md) | Proste do wdrożenia. |  Zwiększony rozmiar aplikacji funkcji w pamięci i liczba funkcji.<br/>Powielanie kodu. |
| [Sprawdzanie stanu z gniazdem](#status-check-with-slot) | System, który nie ma długotrwałych aranżacji trwających dłużej niż 24 godziny lub często nakładających się aranżacji. | Prosta podstawa kodu.<br/>Nie wymaga dodatkowego zarządzania aplikacjami funkcji. | Wymaga dodatkowego konta magazynu lub zarządzania centrum zadań.<br/>Wymaga okresów, gdy nie są uruchomione aranżacji. |
| [Routing aplikacji](#application-routing) | System, który nie ma okresów czasu, gdy aranżacji nie są uruchomione, takich jak te okresy z aranżacji, które trwają dłużej niż 24 godziny lub często nakładających się aranżacji. | Obsługuje nowe wersje systemów z ciągle uruchomionymi aranżacjami, które mają przełomowe zmiany. | Wymaga inteligentnego routera aplikacji.<br/>Może maksymalnie liczba aplikacji funkcji dozwolonych przez subskrypcję. Wartość domyślna to 100. |

## <a name="versioning"></a>Przechowywanie wersji

Zdefiniuj nowe wersje swoich funkcji i pozostaw stare wersje w aplikacji funkcji. Jak widać na diagramie, wersja funkcji staje się częścią jej nazwy. Ponieważ poprzednie wersje funkcji są zachowywane, wystąpienia aranżacji w locie mogą nadal odwoływać się do nich. Tymczasem żądania nowych wystąpień aranżacji wywołać dla najnowszej wersji, do której funkcja klienta aranżacji można odwoływać się z ustawienia aplikacji.

![Strategia przechowywania wersji](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

W tej strategii każda funkcja musi być skopiowana, a jej odwołania do innych funkcji muszą zostać zaktualizowane. Możesz to ułatwić, pisząc skrypt. Oto [przykładowy projekt](https://github.com/TsuyoshiUshio/DurableVersioning) ze skryptem migracji.

>[!NOTE]
>Ta strategia używa gniazd wdrażania, aby uniknąć przestojów podczas wdrażania. Aby uzyskać bardziej szczegółowe informacje dotyczące tworzenia i używania nowych gniazd wdrażania, zobacz [Miejsca wdrażania usługi Azure Functions](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Sprawdzanie stanu z gniazdem

Gdy bieżąca wersja aplikacji funkcji jest uruchomiona w gnieździe produkcyjnym, wdrożyć nową wersję aplikacji funkcji do miejsca tymczasowego. Przed zamienić swoje miejsca produkcji i przemieszczania, sprawdź, czy istnieją jakieś uruchomione wystąpienia aranżacji. Po zakończeniu wszystkich wystąpień aranżacji można wykonać zamianę. Ta strategia działa, gdy masz przewidywalne okresy, gdy nie wystąpień aranżacji są w locie. Jest to najlepsze podejście, gdy aranżacje nie są długotrwałe i gdy wykonania aranżacji nie często nakładają się na siebie.

### <a name="function-app-configuration"></a>Konfiguracja aplikacji funkcji

Aby skonfigurować ten scenariusz, należy użyć poniższej procedury.

1. [Dodaj miejsca wdrożenia](../functions-deployment-slots.md#add-a-slot) do aplikacji funkcji do przemieszczania i produkcji.

1. Dla każdego gniazda ustaw [ustawienie aplikacji AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) na ciąg połączenia konta udostępnionego magazynu. Ten ciąg połączenia konta magazynu jest używany przez środowisko uruchomieniowe usługi Azure Functions. To konto jest używane przez środowisko uruchomieniowe usługi Azure Functions i zarządza kluczami funkcji.

1. Dla każdego gniazda utwórz nowe ustawienie `DurableManagementStorage`aplikacji, na przykład . Ustaw jego wartość na ciąg połączenia różnych kont magazynu. Te konta magazynu są używane przez rozszerzenie Funkcje trwałe do [niezawodnego wykonywania.](durable-functions-checkpointing-and-replay.md) Użyj osobnego konta magazynu dla każdego gniazda. Nie oznaczaj tego ustawienia jako ustawienia gniazda wdrożenia.

1. W [sekcji durableTask pliku host.json](durable-functions-bindings.md#hostjson-settings)aplikacji funkcji `azureStorageConnectionStringName` określ jako nazwę ustawienia aplikacji utworzonego w kroku 3.

Na poniższym diagramie przedstawiono opisaną konfigurację gniazd wdrażania i kont magazynu. W tym scenariuszu wstępnego wstępnego rozmieszczenia wersja 2 aplikacji funkcji jest uruchomiona w gnieździe produkcyjnym, podczas gdy wersja 1 pozostaje w miejscu przejściowym.

![Miejsca wdrożenia i konta magazynu](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>przykłady host.json

Następujące fragmenty JSON są przykładami ustawienia ciągu połączenia w pliku *host.json.*

#### <a name="functions-20"></a>Funkcje 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Konfiguracja potoku ciągłej integracji/dysku CD

Skonfiguruj potok ciągłej integracji/ciągłego wdrażania do wdrażania tylko wtedy, gdy aplikacja funkcji nie ma oczekujących lub uruchomionych wystąpień aranżacji. Korzystając z usługi Azure Pipelines, można utworzyć funkcję, która sprawdza pod kątem tych warunków, jak w poniższym przykładzie:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Następnie skonfiguruj bramę przemieszczania, aby czekać, aż nie zostaną uruchomione żadne aranżacji. Aby uzyskać więcej informacji, zobacz [Zwalnianie kontroli wdrażania przy użyciu bramek](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Brama wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Usługa Azure Pipelines sprawdza aplikację funkcji pod kątem uruchamiania wystąpień aranżacji przed rozpoczęciem wdrażania.

![Brama wdrażania (uruchomiona)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Teraz nowa wersja aplikacji funkcji powinny zostać wdrożone w miejscu przejściowym.

![Miejsce postojowe](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Na koniec zamienić szczeliny. 

Ustawienia aplikacji, które nie są oznaczone jako ustawienia gniazda wdrożenia są również zamienione, więc aplikacja w wersji 2 zachowuje odwołanie do konta magazynu A. Ponieważ stan aranżacji jest śledzony na koncie magazynu, wszystkie aranżacji uruchomionych w aplikacji w wersji 2 nadal działają w nowym gnieździe bez przerwy.

![Miejsce wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Aby użyć tego samego konta magazynu dla obu gniazd, można zmienić nazwy centrów zadań. W takim przypadku należy zarządzać stanem gniazd i ustawieniami hubname aplikacji. Aby dowiedzieć się więcej, zobacz [Centra zadań w aplikacji Funkcje trwałe](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Routing aplikacji

Strategia ta jest najbardziej złożona. Jednak może służyć do aplikacji funkcji, które nie mają czasu między uruchomionymi aranżacjami.

W przypadku tej strategii należy utworzyć *router aplikacji* przed funkcjami trwałymi. Ten router można zaimplementować z funkcjami trwałymi. Router jest odpowiedzialny za:

* Wdrażanie aplikacji funkcji.
* Zarządzanie wersją funkcji trwałych. 
* Rozsyłanie żądań aranżacji do aplikacji funkcyjnych.

Przy pierwszym odebraniu żądania aranżacji router wykonuje następujące zadania:

1. Tworzy nową aplikację funkcji na platformie Azure.
2. Wdraża kod aplikacji funkcji do nowej aplikacji funkcji na platformie Azure.
3. Przekazuje żądanie aranżacji do nowej aplikacji.

Router zarządza stanem, która wersja kodu aplikacji jest wdrażana do aplikacji funkcji na platformie Azure.

![Routing aplikacji (po raz pierwszy)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Router kieruje żądania wdrożenia i aranżacji do odpowiedniej aplikacji funkcji na podstawie wersji wysłanej z żądaniem. Ignoruje wersję poprawki.

Po wdrożeniu nowej wersji aplikacji bez zmiany podziału, można przyrost wersji poprawki. Router wdraża do istniejącej aplikacji funkcji i wysyła żądania dla starych i nowych wersji kodu, które są kierowane do tej samej aplikacji funkcji.

![Routing aplikacji (bez przerywania)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Podczas wdrażania nowej wersji aplikacji z przełomową zmianą można zwiększać wersję główną lub pomocniczą. Następnie router aplikacji tworzy nową aplikację funkcji na platformie Azure, wdraża do niej i kieruje żądania nowej wersji aplikacji do niego. Na poniższym diagramie uruchomione aranżacji w wersji 1.0.1 aplikacji nadal działa, ale żądania dla wersji 1.1.0 są kierowane do nowej aplikacji funkcji.

![Routing aplikacji (zmiana podziału)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Router monitoruje stan aranżacji w wersji 1.0.1 i usuwa aplikacje po zakończeniu wszystkich aranżacji. 

### <a name="tracking-store-settings"></a>Śledzenie ustawień sklepu

Każda aplikacja funkcji powinna używać oddzielnych kolejek planowania, ewentualnie na oddzielnych kontach magazynu. Jeśli chcesz zbadać wszystkie wystąpienia aranżacji we wszystkich wersjach aplikacji, możesz udostępniać tabele wystąpień i historii w aplikacjach funkcji. Tabele można udostępniać, `trackingStoreConnectionStringName` `trackingStoreNamePrefix` konfigurując ustawienia i ustawienia w pliku [ustawień host.json,](durable-functions-bindings.md#host-json) tak aby wszystkie używały tych samych wartości.

Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami w funkcjach trwałych na platformie Azure](durable-functions-instance-management.md).

![Śledzenie ustawień sklepu](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Trwałe funkcje przechowywania wersji](durable-functions-versioning.md)

