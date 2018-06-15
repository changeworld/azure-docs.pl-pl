---
title: Wyświetl dzienniki aktywności platformy Azure do monitorowania zasobów | Dokumentacja firmy Microsoft
description: Użyj dzienników działania do przeglądu użytkownika akcje i błędy. Pokazuje portalu Azure w programie PowerShell, interfejsu wiersza polecenia platformy Azure i REST.
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
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: 2dcf93a635a8eb0a01ec266d2478b6e5a336ec00
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358697"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Wyświetl dzienniki aktywności inspekcji akcje zasobów

Za pomocą działania dzienniki można określić:

* jakie operacje zostały pobrane do zasobów w Twojej subskrypcji
* kto zainicjował operację (chociaż operacje inicjowane przez usługi wewnętrznej bazy danych nie mają użytkownika jako obiekt wywołujący)
* Podczas operacji wystąpił
* Stan operacji
* Wartości innych właściwości, które mogą ułatwić badania operacji

Dziennik zawiera wszystkie operacje zapisu (PUT, POST, DELETE) w Twoich zasobów. Nie ma operacji odczytu (GET). Lista zasobów akcji, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Dzienniki inspekcji można użyć do znalezienia błędu podczas rozwiązywania problemów z lub do monitorowania, jak użytkownik w organizacji zmienić zasobu.

Dzienniki aktywności są przechowywane przez 90 dni. Mogą wykonywać kwerendę o dowolnym zakres dat, tak długo, jak Data początkowa nie jest więcej niż 90 dni w przeszłości.



Mogą pobierać informacje z Dzienniki aktywności za pośrednictwem portalu programu PowerShell, interfejsu wiersza polecenia Azure, interfejsu API REST szczegółowych informacji, lub [biblioteki .NET Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portal

1. Aby wyświetlić dzienniki aktywności za pośrednictwem portalu, wybierz **Monitor**.
   
    ![Wybierz Dzienniki aktywności](./media/resource-group-audit/select-monitor.png)

   Lub, aby automatycznie odfiltrować dziennik aktywności dla określonego zasobu lub grupy zasobów, wybierz **dziennik aktywności**. Zwróć uwagę, że dziennik aktywności automatycznie są filtrowane według wybranych zasobów.
   
    ![Filtruj według zasobu](./media/resource-group-audit/filtered-by-resource.png)
2. W **dziennik aktywności**, wyświetlane jest podsumowanie ostatnich operacji.
   
    ![Pokaż akcje](./media/resource-group-audit/audit-summary.png)
3. Aby ograniczyć liczbę operacji wyświetlane, wybierz inne warunki. Na przykład poniższy obraz przedstawia **Timespan** i **zdarzenie inicjowane przez** pola zmienione aby wyświetlić akcje wykonywane przez określonego użytkownika lub aplikacji w ciągu ostatnich miesiącu. Wybierz **Zastosuj** , aby wyświetlić wyniki zapytania.
   
    ![Ustaw opcje filtru](./media/resource-group-audit/set-filter.png)

4. Jeśli potrzebujesz ponownie później, uruchom zapytanie, wybierz **zapisać** i nadaj nazwę zapytania.
   
    ![Zapisz zapytanie](./media/resource-group-audit/save-query.png)
5. Aby szybko uruchomić zapytanie, można wybrać jedną z wbudowanych zapytania, takie jak wdrożenia nie powiodło się.

    ![Wybierz zapytanie](./media/resource-group-audit/select-quick-query.png)

   Wybrane zapytanie automatycznie ustawia wartości filtru wymagana.

    ![Wyświetl błędy wdrażania](./media/resource-group-audit/view-failed-deployment.png)   

6. Wybierz jedną z operacji, aby wyświetlić podsumowanie zdarzenia.

    ![operacji dotyczącej widoku](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell

1. Aby pobrać wpisów dziennika, należy uruchomić **Get-AzureRmLog** polecenia. Możesz podać dodatkowe parametry do filtrowania pozycji listy. Jeśli nie określisz godzina rozpoczęcia i zakończenia, wpisy w ciągu ostatniej godziny są zwracane. Na przykład, aby pobrać operacji dla grupy zasobów w ciągu ostatniej godziny uruchomienia:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    Poniższy przykład przedstawia użycie dziennika aktywności do operacji research podjąć w określonym czasie. Określono daty rozpoczęcia i zakończenia w formacie daty.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Alternatywnie możesz użyć funkcji Data, aby określić zakres dat, takie jak ostatnie 14 dni.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. W zależności od godziny rozpoczęcia, które określisz poprzednie polecenia może zwrócić długą listę operacji dla grupy zasobów. Można filtrować wyniki są odpowiednie podając kryteria wyszukiwania. Na przykład jeśli chcesz się dowiedzieć, jak aplikacji sieci web została zatrzymana, można uruchomić następujące polecenie:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Który w tym przykładzie wskazuje, że Akcja zatrzymania została wykonana przez użytkownika someone@contoso.com. 

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

3. Można wyszukiwać akcje wykonywane przez określonego użytkownika, nawet w przypadku grupy zasobów, która już nie istnieje.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Można filtrować zakończone niepowodzeniem operacje.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Można skupić się na jeden błąd analizując komunikatu o stanie dla tego wpisu.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Polecenie to zwraca:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby pobrać wpisów dziennika, należy uruchomić [listy dziennik aktywności monitora az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) polecenia.

  ```azurecli
  az monitor activity-log list --resource-group <group name>
  ```


## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennika aktywności są częścią [interfejsu API REST usługi Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Aby pobrać zdarzenia dziennika aktywności, zobacz [listy zdarzeń zarządzania w ramach subskrypcji](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Kolejne kroki

* Dzienniki aktywności platformy Azure może służyć przy użyciu usługi Power BI, aby uzyskać bardziej szczegółowe analizy o akcjach w ramach subskrypcji. Zobacz [widoku i analizować Dzienniki aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Aby dowiedzieć się więcej na temat ustawiania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).
* Informacje na temat polecenia do wyświetlania operacji wdrażania, zobacz [wyświetlić operacje wdrażania](resource-manager-deployment-operations.md).
* Aby dowiedzieć się uniknąć usunięcia zasobu dla wszystkich użytkowników, zobacz [blokowania zasobów z usługi Azure Resource Manager](resource-group-lock-resources.md).
* Aby wyświetlić listę działań, które są dostępne dla każdego dostawcy Microsoft Azure Resource Manager, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md)

