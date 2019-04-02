---
title: Wyświetlanie dzienników aktywności platformy Azure do monitorowania zasobów | Dokumentacja firmy Microsoft
description: Użyj dzienników aktywności do przeglądu użytkownika akcje i błędy. Zawiera Portal Azure — program PowerShell, wiersza polecenia platformy Azure i REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: 8348099d778a9ec65e907bb3d21ae995041b9fb6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802291"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Wyświetlanie dzienników aktywności, aby przeprowadzić inspekcję działań na zasoby

Za pomocą dzienników aktywności można określić:

* jakie operacje zostały wykonane na zasobów w ramach subskrypcji
* kto rozpoczął wykonać operację
* Czas wystąpienia operacji
* Stan operacji
* Wartości innych właściwości, które mogą ułatwić zbadanie operacji

Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) wykonywanych względem zasobów. Go nie obejmują operacji odczytu (GET). Aby uzyskać listę zasobów czynności, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Dzienniki inspekcji można użyć do znalezienia błędu podczas rozwiązywania problemów z lub do monitorowania, jak użytkownik w organizacji zasób został zmodyfikowany.

Dzienniki aktywności są przechowywane przez 90 dni. Można wyszukać dowolny zakres dat, tak długo, jak Data początkowa nie więcej niż 90 dni w przeszłość.

Można pobrać informacji z dzienników aktywności w portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST usługi Insights lub [Insights — Biblioteka .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="the-azure-portal"></a>Witryna Azure Portal

1. Aby wyświetlić dzienniki aktywności w portalu, wybierz **Monitor**.

    ![Wybierz monitor](./media/resource-group-audit/select-monitor.png)

1. Wybierz **dziennika aktywności**.

    ![Wybierz dziennik aktywności](./media/resource-group-audit/select-activity-log.png)

1. Zostanie wyświetlone podsumowanie ostatnich operacji. Domyślny zestaw filtry są stosowane do operacji.

    ![Wyświetl podsumowanie, ostatnie operacji](./media/resource-group-audit/audit-summary.png)

1. Aby szybko uruchomić wstępnie zdefiniowany zestaw filtrów, zaznacz **szybki wgląd w szczegóły** i wybierz jedną z opcji.

    ![Wybierz zapytanie](./media/resource-group-audit/quick-insights.png)

1. Aby skoncentrować się na określonych operacji, Zmień filtry lub deklarowania nowych. Na przykład na poniższej ilustracji przedstawiono nową wartość dla **Timespan** i **typ zasobu** jest ustawiona na kontach magazynu. 

    ![Ustaw opcje filtru](./media/resource-group-audit/set-filter.png)

1. Jeśli potrzebujesz uruchomić zapytanie później, wybierz **filtry bieżący numer Pin**.

    ![Filtry numeru PIN](./media/resource-group-audit/pin-filters.png)

1. Nazwij filtr.

    ![Filtry nazw](./media/resource-group-audit/name-filters.png)

1. Filtr jest dostępny na pulpicie nawigacyjnym.

    ![Pokaż filtru na pulpicie nawigacyjnym](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Uruchom, aby pobrać Wpisy dziennika **Get AzLog** polecenia. Możesz podać dodatkowe parametry, aby filtrować listę wpisów. Jeśli nie określisz godzinę rozpoczęcia i zakończenia są zwracane wpisy z ostatnich siedmiu dni.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    Poniższy przykład pokazuje, jak korzystać z dziennika aktywności do badań operacje wykonywane w czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    Alternatywnie można użyć funkcji daty, aby określić zakres dat, takich jak ostatnich 14 dni.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* Możesz wyszukać akcje wykonywane przez określonego użytkownika, nawet w przypadku grupy zasobów, który już nie istnieje.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* Można filtrować operacje zakończone niepowodzeniem.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* Można skoncentrować się na jeden błąd, analizując komunikat o stanie dla tego wpisu.

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* Możesz wybrać określone wartości, aby ograniczyć ilość danych, która jest zwracana.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* W zależności od godziny rozpoczęcia, które określisz poprzednie polecenia może zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki do czego szukasz, zapewniając kryteria wyszukiwania. Na przykład można filtrować według typu działania.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

* Uruchom, aby pobrać Wpisy dziennika [az monitor dziennika aktywności listy](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) polecenia Przesunięcie do wskazania przedział czasu.

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  Poniższy przykład pokazuje, jak korzystać z dziennika aktywności do badań operacje wykonywane w czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* Możesz wyszukać akcje wykonywane przez określonego użytkownika, nawet w przypadku grupy zasobów, który już nie istnieje.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* Można filtrować operacje zakończone niepowodzeniem.

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* Można skoncentrować się na jeden błąd, analizując komunikat o stanie dla tego wpisu.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* Możesz wybrać określone wartości, aby ograniczyć ilość danych, która jest zwracana.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* W zależności od godziny rozpoczęcia, które określisz poprzednie polecenia może zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki do czego szukasz, zapewniając kryteria wyszukiwania. Na przykład można filtrować według typu działania.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennika aktywności są częścią [interfejsu API REST usługi Insights](/rest/api/monitor/). Aby pobrać zdarzenia dziennika aktywności, zobacz [listy zdarzeń zarządzania w ramach subskrypcji](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Kolejne kroki

* Dzienniki aktywności platformy Azure może służyć za pomocą usługi Power BI do uzyskania bardziej wnikliwych analiz dotyczących działań w ramach subskrypcji. Zobacz [wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Aby dowiedzieć się więcej na temat ustawiania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).
* Aby dowiedzieć się więcej na temat poleceń w celu wyświetlania operacji wdrażania, zobacz [wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md).
* Aby dowiedzieć się, jak zapobiegać usunięcia zasobu dla wszystkich użytkowników, zobacz [blokowanie zasobów w usłudze Azure Resource Manager](resource-group-lock-resources.md).
* Aby wyświetlić listę operacji, które są dostępne dla każdego dostawcy usługi Microsoft Azure Resource Manager, zobacz [operacji dostawcy zasobów usługi Resource Manager platformy Azure](../role-based-access-control/resource-provider-operations.md)
