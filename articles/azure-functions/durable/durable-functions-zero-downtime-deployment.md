---
title: Wdrożenie bez przestojów dla Durable Functions
description: Dowiedz się, jak włączyć aranżację Durable Functions w przypadku wdrożeń bez przestojów.
services: functions
author: tsushi
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: b47604f2c8703ba587e98d68dc30552e5944f562
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614500"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Wdrożenie bez przestojów dla Durable Functions

[Model niezawodnego wykonywania](durable-functions-checkpointing-and-replay.md) Durable Functions wymaga, aby aranżacje były deterministyczne, co tworzy dodatkowe wyzwanie, które należy wziąć pod uwagę podczas wdrażania aktualizacji. Gdy wdrożenie zawiera zmiany sygnatury funkcji działania lub logiki programu Orchestrator, wystąpienia aranżacji w locie kończą się niepowodzeniem. Ta sytuacja dotyczy szczególnie problemów z wystąpieniami długotrwałych aranżacji, które mogą reprezentować godziny lub dni pracy.

Aby zapobiec wystąpieniu tych błędów, należy opóźnić wdrożenie do momentu zakończenia wszystkich uruchomionych wystąpień aranżacji lub upewnić się, że wszystkie uruchomione wystąpienia aranżacji korzystają z istniejących wersji funkcji. Aby uzyskać więcej informacji na temat przechowywania wersji, zobacz [przechowywanie wersji w Durable Functions](durable-functions-versioning.md).

> [!NOTE]
> Ten artykuł zawiera wskazówki dotyczące aplikacji usługi Functions ukierunkowanych na Durable Functions 1. x. Nie została jeszcze zaktualizowana w celu uwzględnienia zmian wprowadzonych w Durable Functions 2. x. Aby uzyskać więcej informacji o różnicach między wersjami rozszerzeń, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

Poniższy wykres zawiera porównanie trzech głównych strategii w celu osiągnięcia nieprzerwanego wdrożenia dla Durable Functions: 

| Strategia |  Kiedy stosować | Formaty | Wady |
| -------- | ------------ | ---- | ---- |
| **[Przechowywanie wersji](#versioning)** |  Aplikacje, które nie są często spotykanymi [zmianami.](durable-functions-versioning.md) | Łatwość zaimplementowania. |  Zwiększony rozmiar aplikacji funkcji w pamięci i liczbie funkcji.<br/>Duplikowanie kodu. |
| **[Sprawdzanie stanu z miejscem](#status-check-with-slot)** | System, który nie ma długotrwałych aranżacji trwałych więcej niż 24 godziny lub często nakładających się aranżacji. | Prosta baza kodu.<br/>Nie wymaga dodatkowego zarządzania aplikacjami funkcji. | Wymaga dodatkowego konta magazynu lub zarządzania centrum zadań.<br/>Wymaga okresu czasu, gdy nie są uruchomione żadne aranżacje. |
| **[Routing aplikacji](#application-routing)** | System, który nie ma okresów czasu, gdy aranżacje nie działają, takich jak te z aranżacjami trwającymi ponad 24 godziny lub z często nakładającymi się aranżacjami. | Obsługuje nowe wersje systemów z ciągle uruchomionymi aranżacjami, które mają istotne zmiany. | Wymaga inteligentnego routera aplikacji.<br/>Może przekroczyć maksymalną liczbę aplikacji funkcji dozwolonych w ramach subskrypcji (domyślnie 100). |

## <a name="versioning"></a>Obsługa wersji

Zdefiniuj nowe wersje funkcji i pozostaw stare wersje w aplikacji funkcji. Jak widać na diagramie, wersja funkcji stanie się częścią nazwy. Ze względu na to, że poprzednie wersje funkcji są zachowywane, wystąpienia aranżacji w locie mogą nadal odwoływać się do nich. Tymczasem żądania nowych wystąpień aranżacji wywołują najnowszą wersję, którą funkcja klienta aranżacji może odwoływać się z poziomu aplikacji.

![Strategia przechowywania wersji](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

W tej strategii każda funkcja musi zostać skopiowana wraz z jej odwołaniami do innych zaktualizowanych funkcji. Można to ułatwić, pisząc skrypt. Oto [przykładowy projekt](https://github.com/TsuyoshiUshio/DurableVersioning) z skryptem migracji.

>[!NOTE]
>Ta strategia używa miejsc wdrożenia, aby uniknąć przestoju podczas wdrażania. Aby uzyskać szczegółowe informacje na temat tworzenia i używania nowych miejsc wdrożenia, zapoznaj się z tematem [Azure Functions miejsc wdrożenia](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Sprawdzanie stanu z miejscem

Gdy bieżąca wersja aplikacji funkcji jest uruchomiona w miejscu produkcyjnym, wdróż nową wersję aplikacji funkcji w miejscu przejściowym. Przed zamianą miejsc produkcyjnych i przejściowych Sprawdź, czy są uruchomione wystąpienia aranżacji. Po zakończeniu wszystkich wystąpień aranżacji można wykonać zamianę. Ta strategia działa w przypadku przewidywalnych okresów, gdy żadne wystąpienia aranżacji nie są w locie. Jest to najlepsze rozwiązanie, gdy aranżacje nie są długotrwałe i kiedy wykonywanie aranżacji nie jest często nakładane.

### <a name="function-app-configuration"></a>Konfiguracja aplikacji funkcji

Aby skonfigurować ten scenariusz, można użyć następującej procedury:

1. [Dodawanie miejsc wdrożenia](../functions-deployment-slots.md#add-a-slot) do aplikacji funkcji na potrzeby przemieszczania i produkcji.

1. Dla każdego gniazda należy ustawić [ustawienie aplikacji AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) na parametry połączenia dla konta magazynu udostępnionego. Będzie on używany przez środowisko uruchomieniowe Azure Functions. To konto będzie używane przez środowisko uruchomieniowe Azure Functions i zarządza kluczami funkcji.

1. Dla każdego gniazda Utwórz nowe ustawienie aplikacji (np. DurableManagementStorage) i ustaw jej wartość na parametry połączenia różnych kont magazynu. Te konta magazynu będą używane przez rozszerzenie Durable Functions do [niezawodnego wykonywania](durable-functions-checkpointing-and-replay.md). Użyj oddzielnego konta magazynu dla każdego miejsca. Nie oznaczaj tego ustawienia jako ustawienia miejsca wdrożenia.

1. W [sekcji durableTask pliku host. JSON](durable-functions-bindings.md#hostjson-settings)aplikacji funkcji Określ azureStorageConnectionStringName jako nazwę ustawienia aplikacji utworzonego w kroku 3.

Na poniższym diagramie pokazano, jak opisano konfigurację miejsc wdrożenia i kont magazynu. W tym potencjalnym scenariuszu przed wdrożeniem wersja 2 aplikacji funkcji jest uruchomiona w miejscu produkcyjnym, natomiast wersja 1 pozostaje w miejscu przejściowym.

![Miejsce wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>Przykłady pliku host. JSON

Poniższe fragmenty kodu JSON to przykłady ustawienia parametrów połączenia w pliku host. JSON.

#### <a name="functions-20"></a>Funkcje 2,0

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

### <a name="cicd-pipeline-configuration"></a>Konfiguracja potoku ciągłej integracji/ciągłego wdrażania

Skonfiguruj potok ciągłej integracji/ciągłego wdrażania tylko wtedy, gdy aplikacja funkcji nie ma oczekujących lub uruchomionych wystąpień aranżacji. W przypadku korzystania z Azure Pipelines można utworzyć funkcję, która sprawdza, czy są spełnione te warunki, jak w poniższym przykładzie:

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

Następnie skonfiguruj bramę przemieszczania, aby czekać, aż żadna organizacja nie zostanie uruchomiona. Aby uzyskać więcej informacji, zobacz [wydawanie kontroli wdrożenia przy użyciu bram](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Brama wdrażania](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines sprawdza aplikację funkcji pod kątem uruchamiania wystąpień aranżacji przed rozpoczęciem wdrażania.

![Brama wdrażania (uruchomiona)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Teraz Nowa wersja aplikacji funkcji powinna zostać wdrożona w miejscu przejściowym.

![Miejsce wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Na koniec zastępowanie miejsc. 

Ustawienia aplikacji, które nie są oznaczone jako ustawienia miejsca wdrożenia, są również zamieniane, więc aplikacja w wersji 2 zachowuje swoje odwołanie do konta magazynu A. Ponieważ stan aranżacji jest śledzony na koncie magazynu, wszystkie aranżacje działające w aplikacji w wersji 2 będą nadal działać w nowym gnieździe bez przeszkód.

![Miejsce wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Aby użyć tego samego konta magazynu dla obu gniazd, można zmienić nazwy centrów zadań. W takim przypadku należy zarządzać stanem swoich miejsc i ustawień HubName aplikacji. Aby dowiedzieć się więcej, zobacz [centra zadań w Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Routing aplikacji

Ta strategia jest najbardziej skomplikowana. Można go jednak używać dla aplikacji funkcji, które nie mają czasu między uruchomionymi aranżacjami.

W przypadku tej strategii należy utworzyć *router aplikacji* przed Durable Functions. Ten router można zaimplementować przy użyciu Durable Functions i ma następujące obowiązki:

* Wdrażanie aplikacji funkcji.
* Zarządzanie wersją Durable Functions. 
* Kierowanie żądań aranżacji do aplikacji funkcji.

Po pierwszym odebraniu żądania aranżacji router wykonuje zadania:

1. Tworzy nową aplikację funkcji na platformie Azure.
2. Wdraża kod aplikacji funkcji w nowej aplikacji funkcji na platformie Azure.
3. Przekazuje żądanie aranżacji do nowej aplikacji.

Router zarządza stanem wdrożonej wersji kodu aplikacji na platformie Azure.

![Routing aplikacji (pierwszy raz)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Router kieruje żądania wdrożenia i aranżacji do odpowiedniej aplikacji funkcji na podstawie `version` wysyłanej do żądania, ignorując wersję poprawki.

Podczas wdrażania nowej wersji aplikacji *bez* zmiany, można zwiększyć wersję poprawki. Router jest wdrażany w istniejącej aplikacji funkcji i wysyła żądania dla starych i nowych wersji kodu są kierowane do tej samej aplikacji funkcji.

![Routing aplikacji (nieprzerwana zmiana)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Podczas wdrażania nowej wersji aplikacji z istotną zmianą można zwiększyć wersję główną lub pomocniczą. Następnie router aplikacji tworzy nową aplikację funkcji na platformie Azure, wdraża ją i przekierowuje do niej żądania nowej wersji aplikacji. Na poniższym diagramie uruchomiono aranżacje w wersji 1.0.1 aplikacji, ale żądania dotyczące wersji 1.1.0 są kierowane do nowej aplikacji funkcji.

![Routing aplikacji (zmiana istotna)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Router monitoruje stan aranżacji w wersji 1.0.1 i usuwa aplikacje po zakończeniu wszystkich aranżacji.  

### <a name="tracking-store-settings"></a>Śledzenie ustawień magazynu

Każda aplikacja funkcji powinna używać oddzielnych kolejek planowania, prawdopodobnie w oddzielnym koncie magazynu. Jeśli jednak chcesz wysyłać zapytania o wszystkie wystąpienia aranżacji we wszystkich wersjach aplikacji, możesz udostępniać tabele wystąpień i historii w aplikacjach funkcji. Tabele można udostępniać, konfigurując `trackingStoreConnectionStringName` i `trackingStoreNamePrefix` w pliku [ustawień hosta. JSON](durable-functions-bindings.md#host-json) , tak aby wszystkie te same wartości były używane.

Aby uzyskać więcej informacji, [Zarządzaj wystąpieniami w Durable Functions na platformie Azure](durable-functions-instance-management.md).

![Śledzenie ustawień magazynu](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Durable Functions wersji](durable-functions-versioning.md)

