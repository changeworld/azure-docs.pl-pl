---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z różnymi zestawami SDK podczas wykonywania zapytań o zasoby platformy Azure za pomocą programu Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303897"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Rozwiązywanie problemów z błędami przy użyciu programu Azure Resource Graph

Można napotkać błędy podczas wykonywania zapytań o zasoby platformy Azure za pomocą programu Azure Resource Graph. W tym artykule opisano różne błędy, które mogą wystąpić i jak je rozwiązać.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Większość błędów jest wynikiem problemu podczas uruchamiania kwerendy z programem Azure Resource Graph. Gdy kwerenda nie powiedzie się, SDK zawiera szczegółowe informacje na temat kwerendy nie powiodło się. Te informacje wskazują problem, dzięki czemu można go naprawić, a późniejsza kwerenda zakończy się pomyślnie.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenariusz: Zbyt wiele subskrypcji

#### <a name="issue"></a>Problem

Klienci z dostępem do ponad 1000 subskrypcji, w tym subskrypcji między dzierżawcami za pomocą [usługi Azure Lighthouse,](../../../lighthouse/overview.md)nie mogą pobierać danych we wszystkich subskrypcjach w ramach jednego wywołania usługi Azure Resource Graph.

#### <a name="cause"></a>Przyczyna

Narzędzia Azure CLI i PowerShell przekazuje tylko pierwsze 1000 subskrypcji usługi Azure Resource Graph. Interfejs API REST dla usługi Azure Resource Graph akceptuje maksymalną liczbę subskrypcji do wykonania kwerendy.

#### <a name="resolution"></a>Rozwiązanie

Żądania wsadowe dla kwerendy z podzbiorem subskrypcji, aby pozostać poniżej limitu subskrypcji 1000. Rozwiązanie używa **parametru Subskrypcja** w programie PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenariusz: Nieobsługicony nagłówek REST typu zawartości

#### <a name="issue"></a>Problem

Klienci, którzy pytają o interfejs API REST programu Azure Resource Graph, otrzymują zwróconą odpowiedź _500_ (wewnętrzny błąd serwera).

#### <a name="cause"></a>Przyczyna

Interfejs API REST programu Azure `Content-Type` Resource Graph obsługuje tylko **aplikację/json**. Niektóre narzędzia REST lub agenci domyślnie **mają tekst/zwykły**, który nie jest obsługiwane przez interfejs API REST.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy narzędzie lub agent używany do wykonywania zapytań o `Content-Type` usługę Azure Resource Graph ma skonfigurowany nagłówek interfejsu API REST dla **aplikacji/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenariusz: Brak uprawnień do odczytu wszystkich subskrypcji na liście

#### <a name="issue"></a>Problem

Klienci, którzy jawnie przekazać listę subskrypcji z kwerendą programu Azure Resource Graph uzyskać _odpowiedź 403_ (zabronione).

#### <a name="cause"></a>Przyczyna

Jeśli klient nie ma uprawnień do odczytu do wszystkich dostarczonych subskrypcji, żądanie jest odrzucane z powodu braku odpowiednich praw bezpieczeństwa.

#### <a name="resolution"></a>Rozwiązanie

Dołącz co najmniej jedną subskrypcję na liście subskrypcji, do których klient uruchamia kwerendę ma co najmniej dostęp do odczytu. Aby uzyskać więcej informacji, zobacz [Uprawnienia w grafie zasobów platformy Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.