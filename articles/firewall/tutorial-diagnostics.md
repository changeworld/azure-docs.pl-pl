---
title: Samouczek — monitorowanie dzienników usługi Azure Firewall
description: W tym samouczku dowiesz się, jak włączyć dzienniki usługi Azure Firewall i zarządzać nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991901"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Samouczek: monitorowanie dzienników usługi Azure Firewall

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

W przykładach podanych w artykułach na temat usługi Azure Firewall założono, że publiczna wersja zapoznawcza usługi Azure Firewall została już włączona. Aby uzyskać więcej informacji, zobacz [Włączanie publicznej wersji zapoznawczej usługi Azure Firewall](public-preview.md).

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do usługi [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Storage i Event Hubs oraz analizowane za pomocą usługi Log Analytics lub innych narzędzi, takich jak program Excel i usługa Power BI.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie rejestrowania za pośrednictwem witryny Azure Portal
> * Włączanie rejestrowania przy użyciu programu PowerShell
> * Wyświetlanie i analizowanie dziennika aktywności
> * Wyświetlanie i analizowanie dzienników reguł sieci i aplikacji

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

 Dla usługi Azure Firewall są dostępne następujące dzienniki diagnostyczne:

* **Dziennik reguł aplikacji**

   Dziennik reguł aplikacji jest zapisywany na koncie magazynu, przesyłany strumieniowo do usługi Event Hubs i/lub wysyłany do usługi Log Analytics tylko wtedy, gdy włączono go dla każdej zapory Azure Firewall. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł aplikacji, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Dziennik reguł sieci**

   Dziennik reguł sieci jest zapisywany na koncie magazynu, przesyłany strumieniowo do usługi Event Hubs i/lub wysyłany do usługi Log Analytics tylko wtedy, gdy włączono go dla każdej zapory Azure Firewall. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł sieci, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: konta magazynu najlepiej sprawdzają się w przypadku dzienników przechowywanych przez dłuższy czas i przeglądanych w razie potrzeby.
* **Event Hubs**: usługa Event Hubs to doskonałe rozwiązanie umożliwiające integrację z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM, Security Information and Event Management), aby otrzymywać alerty dotyczące zasobów.
* **Log Analytics**: usługa Log Analytics najlepiej nadaje się do ogólnego monitorowania aplikacji w czasie rzeczywistym lub przyglądania się trendom.

## <a name="activity-logs"></a>Dzienniki aktywności

   Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.

   Korzystając z [dzienników aktywności platformy Azure](../azure-resource-manager/resource-group-audit.md) (znanych wcześniej jako dzienniki operacyjne i dzienniki inspekcji), możesz wyświetlać wszystkie operacje przesyłane do Twojej subskrypcji platformy Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Włączanie rejestrowania diagnostycznego za pośrednictwem witryny Azure Portal

Od wykonania tej procedury w celu włączenia rejestrowania diagnostycznego może upłynąć kilka minut, zanim dane pojawią się w dziennikach. Jeśli na początku nie widzisz żadnych danych, sprawdź ponownie za kilka minut.

1. W witrynie Azure Portal otwórz grupę zasobów zapory, a następnie kliknij zaporę.
2. W obszarze **Monitorowanie** kliknij pozycję **Dzienniki diagnostyczne**.

   W przypadku usługi Azure Firewall są dostępne dwa dzienniki specyficzne dla usługi:

   * Dziennik reguł aplikacji
   * Dziennik reguł sieci

3. Aby rozpocząć zbieranie danych, kliknij pozycję **Włącz diagnostykę**.
4. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych. 
5. W tym przykładzie dzienniki są przechowywane w usłudze Log Analytics, więc wpisz nazwę **Firewall log analytics**.
6. Kliknij pozycję **Wyślij do usługi Log Analytics** w celu skonfigurowania obszaru roboczego. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.
7. W obszarze **Log Analytics** kliknij pozycję **Konfiguruj**.
8. Na stronie Obszary robocze pakietu OMS kliknij pozycję **Utwórz nowy obszar roboczy**.
9. Na stronie **Obszar roboczy usługi Log Analytics** wpisz nazwę **firewall-oms** dla nowego obszaru w polu **Obszar roboczy OMS**.
10. Wybierz swoją subskrypcję, użyj istniejącej grupy zasobów zapory (**Test-FW-RG**), a następnie wybierz lokalizację **Wschodnie stany USA** i warstwę cenową **Bezpłatna**.
11. Kliknij przycisk **OK**.
   ![Rozpoczynanie procesu konfiguracji][1]
12. W obszarze **Dziennik** kliknij pozycję **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule**, aby zbierać dzienniki dla reguł aplikacji i sieci.
   ![Zapisywanie ustawień diagnostycznych][2]
13. Kliknij pozycję **Zapisz**.

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników, należy włączyć rejestrowanie diagnostyczne.

Aby włączyć rejestrowanie diagnostyczne, wykonaj następujące kroki:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać: */subscriptions/\<identyfikatorSubskrypcji\>/resourceGroups/\<nazwa grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<nazwa konta magazynu\>*.

   Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal. Znajdują się one na stronie **Właściwości** zasobu.

2. Zanotuj identyfikator zasobu zapory, dla której rejestrowanie jest włączane. Ta wartość ma postać: */subscriptions/\<identyfikatorSubskrypcji\>/resourceGroups/\<nazwa grupy zasobów\>/providers/Microsoft.Network/azureFirewalls/\<nazwa zapory\>*.

   Te informacje możesz znaleźć w portalu.

3. Włącz rejestrowanie diagnostyczne przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Dzienniki diagnostyczne nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

## <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można wyświetlać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure**: pobierz informacje z dziennika aktywności przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub witryny Azure Portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w artykule [Activity operations with Resource Manager (Operacje działań przy użyciu usługi Resource Manager)](../azure-resource-manager/resource-group-audit.md).
* **Usługa Power BI**: jeśli nie masz jeszcze konta usługi [Power BI](https://powerbi.microsoft.com/pricing), możesz ją wypróbować bezpłatnie. Korzystając z [pakietu zawartości dzienników aktywności platformy Azure dla usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), możesz analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych, których możesz używać bez zmian lub po dostosowaniu.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Wyświetlanie i analizowanie dzienników reguł sieci i aplikacji

Usługa Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) zbiera pliki dzienników liczników i zdarzeń. Obejmuje ona wizualizacje oraz zaawansowane możliwości wyszukiwania na potrzeby analizowania dzienników.

Ponadto możesz połączyć się z kontem magazynu i pobrać wpisy dziennika JSON dotyczące dostępu i wydajności. Po pobraniu plików JSON możesz je przekonwertować do formatu CSV i wyświetlać w programie Excel, usłudze Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy skonfigurowano zaporę na potrzeby zbierania dzienników, możesz eksplorować usługę Log Analytics, aby wyświetlać dane.

> [!div class="nextstepaction"]
> [Rozwiązania do monitorowania sieci w usłudze Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
