---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z badaniem zasobów platformy Azure za pomocą usługi Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480555"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Rozwiązywanie problemów z błędami przy użyciu grafu zasobów platformy Azure

Podczas wykonywania zapytania dotyczącego zasobów platformy Azure za pomocą usługi Azure Resource Graph mogą wystąpić błędy. W tym artykule opisano różne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Większość błędów jest wynikiem problemu podczas wykonywania zapytania przy użyciu grafu zasobów platformy Azure. Gdy zapytanie nie powiedzie się, zestaw SDK zawiera szczegółowe informacje o zapytaniu zakończonym niepowodzeniem. Te informacje wskazują, że problem może zostać naprawiony, a późniejsza kwerenda powiedzie się.

## <a name="general-errors"></a>Błędy ogólne

### <a name="toomanysubscription"></a>Scenariusz Zbyt wiele subskrypcji

#### <a name="issue"></a>Problem

Klienci mający dostęp do ponad 1000 subskrypcji, w tym subskrypcji między dzierżawcami i [usługi Azure Lighthouse](../../../lighthouse/overview.md), nie mogą pobierać danych ze wszystkich subskrypcji w ramach pojedynczego wywołania do grafu zasobów platformy Azure.

#### <a name="cause"></a>Przyczyna

Interfejs wiersza polecenia platformy Azure i program PowerShell przesyłają dalej tylko pierwsze subskrypcje 1000 do grafu zasobów platformy Azure. Interfejs API REST dla usługi Azure Resource Graph akceptuje maksymalną liczbę subskrypcji, na które należy wykonać zapytanie.

#### <a name="resolution"></a>Rozwiązanie

Żądania usługi Batch dotyczące zapytania z podzbiorem subskrypcji w ramach limitu subskrypcji 1000. Rozwiązanie korzysta z parametru **subskrypcji** w programie PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

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

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.