---
title: Omówienie wersji funkcji trwałych — usługi Azure Functions
description: Dowiedz się więcej o wersjach funkcji trwałych.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152894"
---
# <a name="durable-functions-versions-overview"></a>Omówienie wersji funkcji trwałych

*Funkcje trwałe* to rozszerzenie [usługi Azure Functions](../functions-overview.md) i Azure [WebJobs,](../../app-service/web-sites-create-web-jobs.md) które umożliwia pisanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem. Jeśli nie znasz jeszcze funkcji trwałych, zapoznaj się z [dokumentacją przeglądu](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nowe funkcje w 2.x

W tej sekcji opisano funkcje funkcji trwałych, które są dodawane w wersji 2.x.

### <a name="durable-entities"></a>Trwałe elementy

W funkcji trwałe 2.x wprowadziliśmy nową koncepcję [funkcji jednostki.](durable-functions-entities.md)

Funkcje encji definiują operacje odczytywania i aktualizowania małych fragmentów stanu, nazywanych *trwałymi elementami*. Podobnie jak funkcje orkiestratora, funkcje encji są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczu jednostki*. W przeciwieństwie do funkcji aranżatora funkcje jednostki nie mają żadnych ograniczeń określonego kodu. Funkcje jednostki również zarządzać stan jawnie, a nie niejawnie reprezentujących stan za pośrednictwem przepływu sterowania.

Aby dowiedzieć się więcej, zobacz artykuł [trwałe jednostki.](durable-functions-entities.md)

### <a name="durable-http"></a>Trwały http

W funkcji trwałe 2.x wprowadziliśmy nową trwałą funkcję [HTTP,](durable-functions-http-features.md#consuming-http-apis) która pozwala na:

* Wywoływanie interfejsów API HTTP bezpośrednio z funkcji aranżacji (z pewnymi udokumentowanymi ograniczeniami).
* Zaimplementuj automatyczne sondowanie stanu HTTP 202 po stronie klienta.
* Wbudowana obsługa [tożsamości zarządzanych platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Aby dowiedzieć się więcej, zobacz artykuł o [funkcjach HTTP.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Migrowanie z 1,5 do 2,5 x

W tej sekcji opisano sposób migracji istniejącej wersji 1.x Funkcje trwałe do wersji 2.x, aby skorzystać z nowych funkcji.

### <a name="upgrade-the-extension"></a>Uaktualnienie rozszerzenia

Zainstaluj wersję 2.x [rozszerzenia powiązań funkcji trwałych](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) w projekcie. Aby uzyskać więcej [informacji, zobacz Rejestrowanie rozszerzeń powiązania usługi Azure Functions.](../functions-bindings-register.md)

### <a name="update-your-code"></a>Aktualizowanie kodu

Funkcje trwałe 2.x wprowadza kilka przełomowych zmian. Funkcje trwałe 1.x aplikacje nie są zgodne z funkcjami trwałymi 2.x bez zmian kodu. W tej sekcji wymieniono niektóre zmiany, które należy wprowadzić podczas uaktualniania funkcji w wersji 1.x do wersji 2.x.

#### <a name="hostjson-schema"></a>Schemat hosta.json

Funkcje trwałe 2.x używa nowego schematu host.json. Główne zmiany od 1.x obejmują:

* `"storageProvider"`(i `"azureStorage"` podsekcja) dla konfiguracji specyficznej dla magazynu.
* `"tracing"`do śledzenia i rejestrowania konfiguracji.
* `"notifications"`(i `"eventGrid"` podsekcja) dla konfiguracji powiadomień siatki zdarzeń.

Szczegółowe informacje można znaleźć w [dokumentacji dokumentacji funkcji trwałych host.json.](durable-functions-bindings.md#durable-functions-2-0-host-json)

#### <a name="default-taskhub-name-changes"></a>Domyślne zmiany nazw w uszczowalnie

W wersji 1.x, jeśli nazwa centrum zadań nie została określona w pliku host.json, została ona domyślnie domyślnie prowadzona jako "DurableFunctionsHub". W wersji 2.x domyślna nazwa centrum zadań jest teraz pochodną nazwy aplikacji funkcji. Z tego powodu, jeśli nie określono nazwy centrum zadań podczas uaktualniania do 2.x, kod będzie działać z nowym centrum zadań, a wszystkie aranżacji w locie nie będzie już ich przetwarzania aplikacji. Aby obejść ten problem, można jawnie ustawić nazwę centrum zadań na domyślną wartość v1.x "DurableFunctionsHub", lub możesz wykonać nasze [wskazówki dotyczące wdrażania zer-przestojem,](durable-functions-zero-downtime-deployment.md) aby uzyskać szczegółowe informacje na temat obsługi zmian w przerwaniu lotu.

#### <a name="public-interface-changes-net-only"></a>Zmiany w interfejsie publicznym (tylko na platformie NET)

W wersji 1.x różnych obiektów _kontekstu_ obsługiwanych przez funkcje trwałe mają abstrakcyjne klasy podstawowe przeznaczone do użycia w testowaniu jednostkowym. W ramach funkcji trwałych 2.x te abstrakcyjne klasy podstawowe są zastępowane interfejsami.

Poniższa tabela przedstawia główne zmiany:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` lub `IDurableClient` |
| `DurableOrchestrationContext` lub `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` lub `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

W przypadku, gdy abstrakcyjna klasa podstawowa zawierała metody wirtualne, `DurableContextExtensions`te metody wirtualne zostały zastąpione metodami rozszerzenia zdefiniowanymi w .

#### <a name="functionjson-changes-javascript-and-c-script"></a>zmiany w pliku function.json (JavaScript i C#Script)

W funkcji trwałych 1.x powiązanie klienta `type` `orchestrationClient`aranżacji używa . Zamiast tego używa wersji `durableClient` 2.x.
