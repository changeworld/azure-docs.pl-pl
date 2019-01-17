---
title: Wyświetlanie dzienników aktywności platformy Azure do monitorowania zasobów | Dokumentacja firmy Microsoft
description: Użyj dzienników aktywności do przeglądu użytkownika akcje i błędy. Zawiera Portal Azure — program PowerShell, wiersza polecenia platformy Azure i REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 636e4d5216f87440463fbaecd7f6c7a5a25c7502
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359395"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Wyświetlanie dzienników aktywności, aby przeprowadzić inspekcję działań na zasoby

Za pomocą dzienników aktywności można określić:

* jakie operacje zostały wykonane na zasobów w ramach subskrypcji
* kto zainicjował operację (chociaż operacji zainicjowanych przez usługę zaplecza nie zwracają użytkownika jako obiekt wywołujący)
* Czas wystąpienia operacji
* Stan operacji
* Wartości innych właściwości, które mogą ułatwić zbadanie operacji

Dziennik aktywności zawiera wszystkie operacje zapisu (PUT, POST, DELETE) wykonywanych względem zasobów. Nie obejmuje operacje odczytu (GET). Aby uzyskać listę zasobów czynności, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Dzienniki inspekcji można użyć do znalezienia błędu podczas rozwiązywania problemów z lub do monitorowania, jak użytkownik w organizacji zasób został zmodyfikowany.

Dzienniki aktywności są przechowywane przez 90 dni. Możesz wykonać zapytanie dotyczące dowolnego zakresu dat, jednak data początkowa nie może być wcześniejsza od daty bieżącej o więcej niż 90 dni.

Można pobrać informacji z dzienników aktywności w portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST usługi Insights lub [Insights — Biblioteka .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portal

1. Aby wyświetlić dzienniki aktywności w portalu, wybierz **Monitor**.

    ![Wybierz Dzienniki aktywności](./media/resource-group-audit/select-monitor.png)

   Lub, aby automatycznie filtrowanie dziennika aktywności dla określonego zasobu lub grupy zasobów, wybierz pozycję **dziennika aktywności**. Zwróć uwagę, czy dziennik aktywności jest automatycznie filtrowana według wybranego zasobu.

    ![Filtruj według zasobu](./media/resource-group-audit/filtered-by-resource.png)
2. W **dziennika aktywności**, zostanie wyświetlone podsumowanie ostatnich operacji.

    ![Pokaż akcje](./media/resource-group-audit/audit-summary.png)
3. Aby ograniczyć liczbę operacji wyświetlany, wybierz różnych warunków. Na przykład na poniższej ilustracji przedstawiono **Timespan** i **zdarzenie zainicjowane przez** pola zmienione, aby wyświetlić akcje wykonywane przez określonego użytkownika lub aplikacji dla ostatniego miesiąca. Wybierz **Zastosuj** do wyświetlania wyników zapytania.

    ![Ustaw opcje filtru](./media/resource-group-audit/set-filter.png)

4. Jeśli potrzebujesz uruchomić zapytanie później, wybierz **filtry bieżący numer Pin** i nadaj mu nazwę.

    ![Zapisz zapytanie](./media/resource-group-audit/save-query.png)
5. Szybko uruchomić zapytanie, możesz wybrać jedną z wbudowanych zapytań, takich jak wdrożenia zakończone niepowodzeniem.

    ![Wybierz zapytanie](./media/resource-group-audit/select-quick-query.png)

   Wybrane zapytanie automatycznie ustawia wartości wymaganego filtru.

    ![Wyświetl błędy związane z wdrażaniem](./media/resource-group-audit/view-failed-deployment.png)

6. Wybierz jedną z czynności, aby wyświetlić podsumowanie zdarzenia.

    ![Operacja przeglądania](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell

1. Uruchom, aby pobrać Wpisy dziennika **Get-AzureRmLog** polecenia. Możesz podać dodatkowe parametry, aby filtrować listę wpisów. Jeśli nie określisz godzinę rozpoczęcia i zakończenia, zwracane są wpisy w ciągu ostatniej godziny. Na przykład operacji dla grupy zasobów w ciągu ostatniej godziny Uruchom, aby pobrać:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```

    Poniższy przykład pokazuje, jak korzystać z dziennika aktywności do badań operacje wykonywane w czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Alternatywnie można użyć funkcji daty, aby określić zakres dat, takich jak ostatnich 14 dni.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. W zależności od godziny rozpoczęcia, które określisz poprzednie polecenia może zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki do czego szukasz, zapewniając kryteria wyszukiwania. Na przykład jeśli chcesz się dowiedzieć, jak aplikacja sieci web została zatrzymana, można uruchomić następujące polecenie:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Której w tym przykładzie pokazano, że Akcja zatrzymania zostało wykonane przez someone@contoso.com.

  ```powershell
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Możesz wyszukać akcje wykonywane przez określonego użytkownika, nawet w przypadku grupy zasobów, który już nie istnieje.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Można filtrować operacje zakończone niepowodzeniem.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Można skoncentrować się na jeden błąd, analizując komunikat o stanie dla tego wpisu.

  ```azurepowershell-interactive
  ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
  ```

    Zwraca:

        code           message
        ----           -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Uruchom, aby pobrać Wpisy dziennika [az monitor dziennika aktywności listy](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) polecenia.

  ```azurecli
  az monitor activity-log list --resource-group <group name>
  ```

## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennika aktywności są częścią [interfejsu API REST usługi Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Aby pobrać zdarzenia dziennika aktywności, zobacz [listy zdarzeń zarządzania w ramach subskrypcji](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Kolejne kroki

* Dzienniki aktywności platformy Azure może służyć za pomocą usługi Power BI do uzyskania bardziej wnikliwych analiz dotyczących działań w ramach subskrypcji. Zobacz [wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Aby dowiedzieć się więcej na temat ustawiania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).
* Aby dowiedzieć się więcej na temat poleceń w celu wyświetlania operacji wdrażania, zobacz [wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md).
* Aby dowiedzieć się, jak zapobiegać usunięcia zasobu dla wszystkich użytkowników, zobacz [blokowanie zasobów w usłudze Azure Resource Manager](resource-group-lock-resources.md).
* Aby wyświetlić listę operacji, które są dostępne dla każdego dostawcy usługi Microsoft Azure Resource Manager, zobacz [operacji dostawcy zasobów usługi Resource Manager platformy Azure](../role-based-access-control/resource-provider-operations.md)
