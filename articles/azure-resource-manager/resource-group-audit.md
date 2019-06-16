---
title: Wyświetlanie dzienników aktywności platformy Azure do monitorowania zasobów | Dokumentacja firmy Microsoft
description: Użyj dzienników aktywności do przeglądu użytkownika akcje i błędy. Pokazuje witryny Azure portal programu PowerShell, interfejsu wiersza polecenia platformy Azure i REST.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 7ff45be4eea5c6e8ab83093847164ede0e94579a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606575"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Wyświetlanie dzienników aktywności, aby monitorować działania dotyczące zasobów

Za pomocą dzienników aktywności można określić:

* jakie operacje zostały wykonane na zasobów w ramach subskrypcji
* kto rozpoczął wykonać operację
* Czas wystąpienia operacji
* Stan operacji
* Wartości innych właściwości, które mogą ułatwić zbadanie operacji

Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) dla zasobów. Go nie obejmują operacji odczytu (GET). Aby uzyskać listę zasobów czynności, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji.

Dzienniki aktywności są przechowywane przez 90 dni. Można wyszukać dowolny zakres dat, tak długo, jak Data początkowa nie więcej niż 90 dni w przeszłość.

Można pobrać informacji z dzienników aktywności w portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST usługi Insights lub [Insights — Biblioteka .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

1. Aby wyświetlić dzienniki aktywności w portalu, wybierz **Monitor**.

    ![Wybierz monitor](./media/resource-group-audit/select-monitor.png)

1. Wybierz **dziennika aktywności**.

    ![Wybierz dziennik aktywności](./media/resource-group-audit/select-activity-log.png)

1. Zostanie wyświetlone podsumowanie ostatnich operacji. Domyślny zestaw filtry są stosowane do operacji. Należy zauważyć, że zawiera informacje na temat podsumowania, kto rozpoczął akcję i kiedy to się stało.

    ![Wyświetl podsumowanie, ostatnie operacji](./media/resource-group-audit/audit-summary.png)

1. Aby szybko uruchomić wstępnie zdefiniowany zestaw filtrów, zaznacz **szybki wgląd w szczegóły**.

    ![Wybierz szybki wgląd w szczegóły](./media/resource-group-audit/select-quick-insights.png)

1. Wybierz jedną z opcji. Na przykład wybierz **nieudanych wdrożeń** zobaczyć błędy z wdrożenia.

    ![Wybierz opcję wdrożenia zakończone niepowodzeniem](./media/resource-group-audit/select-failed-deployments.png)

1. Zwróć uwagę, że filtry zostały zmienione skoncentrować się na błędy związane z wdrażaniem w ciągu ostatnich 24 godzin. Wyświetlane są tylko te operacje, które pasują do filtrów.

    ![Filtry widoku](./media/resource-group-audit/view-filters.png)

1. Aby skoncentrować się na określonych operacji, Zmień filtry lub deklarowania nowych. Na przykład na poniższej ilustracji przedstawiono nową wartość dla **Timespan** i **typ zasobu** jest ustawiona na kontach magazynu. 

    ![Ustaw opcje filtru](./media/resource-group-audit/set-filter.png)

1. Jeśli potrzebujesz uruchomić zapytanie później, wybierz **filtry bieżący numer Pin**.

    ![Filtry numeru PIN](./media/resource-group-audit/pin-filters.png)

1. Nazwij filtr.

    ![Filtry nazw](./media/resource-group-audit/name-filters.png)

1. Filtr jest dostępny na pulpicie nawigacyjnym.

    ![Pokaż filtru na pulpicie nawigacyjnym](./media/resource-group-audit/show-dashboard.png)

1. Z poziomu portalu możesz wyświetlić zmiany w zasobie. Przejdź do wartości domyślnych wyświetlać w monitorze i wybrać operację, z udziałem, zmieniając zasobu.

    ![Wybierz operację](./media/resource-group-audit/select-operation.png)

1. Wybierz **(wersja zapoznawcza) historię zmian** i wybierz jedną z dostępnych operacji.

    ![Wybierz pozycję Historia zmian](./media/resource-group-audit/select-change-history.png)

1. Zmiany w zasobie są wyświetlane.

    ![Pokaż zmiany](./media/resource-group-audit/show-changes.png)

Aby dowiedzieć się więcej na temat historii zmian, zobacz [pobieranie zmian zasobów](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uruchom, aby pobrać Wpisy dziennika **Get AzLog** polecenia. Możesz podać dodatkowe parametry, aby filtrować listę wpisów. Jeśli nie określisz godzinę rozpoczęcia i zakończenia są zwracane wpisy z ostatnich siedmiu dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Poniższy przykład pokazuje, jak korzystać z dziennika aktywności do badań operacje wykonywane w czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Alternatywnie można użyć funkcji daty, aby określić zakres dat, takich jak ostatnich 14 dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Możesz wyszukać akcje wykonywane przez konkretnego użytkownika.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Można filtrować operacje zakończone niepowodzeniem.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Można skoncentrować się na jeden błąd, analizując komunikat o stanie dla tego wpisu.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Możesz wybrać określone wartości, aby ograniczyć ilość danych, która jest zwracana.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

W zależności od godziny rozpoczęcia, które określisz poprzednie polecenia może zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki do czego szukasz, zapewniając kryteria wyszukiwania. Na przykład można filtrować według typu działania.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Wykres zasobów można użyć, aby wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [pobieranie zmian zasobów](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Uruchom, aby pobrać Wpisy dziennika [az monitor dziennika aktywności listy](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) polecenia Przesunięcie do wskazania przedział czasu.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Poniższy przykład pokazuje, jak korzystać z dziennika aktywności do badań operacje wykonywane w czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Możesz wyszukać akcje wykonywane przez określonego użytkownika, nawet w przypadku grupy zasobów, który już nie istnieje.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Można filtrować operacje zakończone niepowodzeniem.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Można skoncentrować się na jeden błąd, analizując komunikat o stanie dla tego wpisu.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Możesz wybrać określone wartości, aby ograniczyć ilość danych, która jest zwracana.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

W zależności od godziny rozpoczęcia, które określisz poprzednie polecenia może zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki do czego szukasz, zapewniając kryteria wyszukiwania. Na przykład można filtrować według typu działania.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Wykres zasobów można użyć, aby wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [pobieranie zmian zasobów](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennika aktywności są częścią [interfejsu API REST usługi Insights](/rest/api/monitor/). Aby pobrać zdarzenia dziennika aktywności, zobacz [listy zdarzeń zarządzania w ramach subskrypcji](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Kolejne kroki

* Dzienniki aktywności platformy Azure może służyć za pomocą usługi Power BI do uzyskania bardziej wnikliwych analiz dotyczących działań w ramach subskrypcji. Zobacz [wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Aby dowiedzieć się więcej na temat ustawiania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).
* Aby dowiedzieć się więcej na temat poleceń w celu wyświetlania operacji wdrażania, zobacz [wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md).
* Aby dowiedzieć się, jak zapobiegać usunięcia zasobu dla wszystkich użytkowników, zobacz [blokowanie zasobów w usłudze Azure Resource Manager](resource-group-lock-resources.md).
* Aby wyświetlić listę operacji, które są dostępne dla każdego dostawcy usługi Microsoft Azure Resource Manager, zobacz [operacji dostawcy zasobów usługi Resource Manager platformy Azure](../role-based-access-control/resource-provider-operations.md)
