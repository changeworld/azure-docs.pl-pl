---
title: Tworzenie, wyświetlanie i zarządzanie alertów dziennika aktywności w usłudze Azure Monitor
description: Tworzenie alertów dziennika aktywności przy użyciu witryny Azure portal, szablonów usługi Azure Resource Manager i programu Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705292"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie alertów dziennika aktywności przy użyciu usługi Azure Monitor  

## <a name="overview"></a>Omówienie
Alerty dzienników aktywności są alerty, które aktywować po wystąpieniu nowego zdarzenia dziennika aktywności, który spełnia warunki określone w alercie.

Te alerty są dla zasobów platformy Azure i tworzyć przy użyciu szablonu usługi Azure Resource Manager. One również mogą być tworzone, zaktualizował lub usunął w witrynie Azure portal. Zazwyczaj można Tworzenie alertów dziennika aktywności, aby otrzymywać powiadomienia o zmianach określonych do zasobów w subskrypcji platformy Azure. Alerty często są ograniczone do określonego zasobu, grupy lub zasobów. Na przykład możesz chcieć otrzymasz powiadomienie, gdy dowolne maszyny wirtualnej w grupie zasobów przykładowej **myProductionResourceGroup** zostanie usunięty. Możesz też uzyskać powiadomienia, gdy wszystkie nowe role są przypisane do użytkownika w ramach subskrypcji.

> [!IMPORTANT]
> Nie można utworzyć alerty dotyczące powiadomienia o kondycji usługi za pośrednictwem interfejsu tworzenia alertu dziennika aktywności. Aby dowiedzieć się więcej na temat tworzenia i używania powiadomień dotyczących kondycji usługi, zobacz [odbieranie alertów dziennika aktywności dla powiadomień dotyczących kondycji usług](alerts-activity-log-service-notifications.md).

Podczas tworzenia reguł alertów zapewnić następujące okoliczności:

- Subskrypcji w zakresie nie różni się od subskrypcji, której tworzona jest alert.
- Kryteria muszą być poziom, stan, obiekt wywołujący, grupy zasobów, identyfikator zasobu lub kategorii zdarzeń typu zasobu na którym skonfigurowano alert.
- Nie ma stan "anyOf" lub warunki zagnieżdżone w konfiguracji alertu JSON. Zasadniczo tylko jeden warunek "nieobsługiwanymi" jest dozwolona bez dalszego "nieobsługiwanymi" lub "anyOf" warunki.
- Po "administracyjne" kategorii, należy określić co najmniej jednego z poprzednich kryteriów w alertu. Nie można utworzyć alert, który aktywuje za każdym razem, gdy zdarzenie zostanie utworzone w dziennikach aktywności.


## <a name="azure-portal"></a>Azure Portal

Azure portal umożliwia tworzenie i modyfikowanie reguł alertów dzienników aktywności. Środowisko jest zintegrowany z dziennika aktywności platformy Azure w celu zapewnienia bezproblemowego tworzenia alertu dla określonych zdarzeń zainteresowania.

### <a name="create-with-the-azure-portal"></a>Tworzenie za pomocą witryny Azure portal

Użyj poniższej procedury.

1. W witrynie Azure portal wybierz **Monitor** > **alerty**.
2. Wybierz **Nowa reguła alertu** w lewym górnym rogu **alerty** okna.

     ![Nowa reguła alertu](media/alerts-activity-log/AlertsPreviewOption.png)

     **Utwórz regułę** zostanie wyświetlone okno.

      ![nowe opcje reguły alertu](media/alerts-activity-log/create-new-alert-rule-options.png)

3. W obszarze **Zdefiniuj warunek alertu**, podaj następujące informacje i wybierz **gotowe**:

   - **Element docelowy alertu:** Aby wyświetlić i wybierz element docelowy dla nowego alertu, należy użyć **Filtruj według subskrypcji** / **Filtruj według typu zasobu**. Wybierz zasób lub grupa zasobów z wyświetlonej listy.

     > [!NOTE]
     > 
     > Można wybrać zasób, grupy zasobów lub subskrypcji usługi całej sygnału dziennika aktywności.

     **Element docelowy alertu przykładowy widok**

     ![Wybieranie obiektu docelowego](media/alerts-activity-log/select-target.png)

   - W obszarze **docelowe kryteria**, wybierz opcję **Dodaj kryteria**. Są wyświetlane wszystkie dostępne sygnały dla elementu docelowego, w tym te z różnych kategorii **dziennika aktywności**. Nazwa kategorii jest dołączany do **usługi Monitor** nazwy.

   - Wybierz sygnał na liście wyświetlonej różnych operacji możliwe dla typu **dziennika aktywności**.

     Można wybrać na osi czasu historia dziennika i odpowiednie logikę alertów ten sygnał docelowy:

     **Dodawanie ekranu z kryteriów**

     ![Dodaj kryteria](media/alerts-activity-log/add-criteria.png)

     - **Czas historii**: Zdarzenia dostępne dla wybranej operacji można wykreślić w ciągu ostatnich 6, 12 lub 24 godzin lub w ciągu ostatniego tygodnia.

     - **Zgłoś alert logic**:

       - **Poziom zdarzenia**: Poziom ważności zdarzenia: _Pełne_, _informacyjny_, _ostrzeżenie_, _błąd_, lub _krytyczne_.
       - **Stan**: Stan zdarzenia: _Pracę_, _nie powiodło się_, lub _zakończyło się pomyślnie_.
       - **Zdarzenie zainicjowane przez**: Znany także jako obiekt wywołujący. Adres e-mail lub usługi Azure Active Directory identyfikator użytkownika, który wykonał operację.

       Ten przykładowy wykres sygnału ma alert Logic Apps, stosowane:

       ![wybrane kryteria](media/alerts-activity-log/criteria-selected.png)

4. W obszarze **Zdefiniuj szczegóły alertu**, należy podać następujące dane:

    - **Nazwa reguły alertu**: Nazwa nowej reguły alertu.
    - **Opis**: Opis nowej reguły alertu.
    - **Zapisz alert w grupie zasobów**: Wybierz grupę zasobów, w którym chcesz zapisać tej nowej reguły.

5. W obszarze **grupy akcji**, z menu rozwijanego, należy określić grupę akcji, które chcesz przypisać do tej nowej reguły alertu. Ewentualnie [Utwórz nową grupę akcji](../../azure-monitor/platform/action-groups.md) i przypisać ją do nowej reguły. Aby utworzyć nową grupę, wybierz **+ Nowa grupa**.

6. Aby włączyć reguły po ich utworzeniu, zaznacz **tak** dla **Włącz regułę po utworzeniu** opcji.
7. Wybierz **Utwórz regułę alertu**.

    Utworzono nową regułę alertu dla dziennika aktywności, a w prawym górnym rogu okna pojawi się komunikat potwierdzający.

    Możesz włączyć, wyłączyć, edytować lub usunąć regułę. Dowiedz się więcej o tym, jak zarządzać regułami dziennika aktywności.


Prosty sposób analogiczny opis warunki, na których można tworzyć reguły alertu w dzienniku aktywności jest aby eksplorować lub filtrowanie zdarzeń za pomocą [dziennika aktywności w witrynie Azure portal](activity-log-view.md#azure-portal). W **usługi Azure Monitor — dziennik aktywności** ekranu, można filtrować lub znaleźć wymaganych zdarzeń i następnie utworzyć alert przy użyciu **Dodaj alert dziennika aktywności** przycisku. Następnie wykonaj kroki od 4 do 7, jak poprzednio wyświetlane.
    
 ![Dodaj alert dziennika aktywności](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Wyświetlanie i zarządzanie nimi w witrynie Azure portal

1. W witrynie Azure portal wybierz **Monitor** > **alerty**. Wybierz **Zarządzaj regułami alertów** w lewym górnym rogu okna.

    ![Zarządzaj regułami alertów](media/alerts-activity-log/manage-alert-rules.png)

    Zostanie wyświetlona lista dostępnych zasad.

2. Wyszukaj regułę dziennika aktywności w celu zmodyfikowania.

    ![Wyszukaj reguł alertów dzienników aktywności](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Można użyć dostępnych filtrów _subskrypcji_, _grupy zasobów_, _zasobów_, _sygnał typu_, lub _stanu_ , można znaleźć reguły działania, które chcesz edytować.

   > [!NOTE]
   > 
   > Możesz edytować tylko **opis**, **docelowe kryteria**, i **grup akcji**.

3. Wybierz regułę, a następnie kliknij dwukrotnie, aby edytować opcje reguły. Wprowadź wymagane zmiany, a następnie wybierz **Zapisz**.

   ![Zarządzaj regułami alertów](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Możesz włączyć, wyłączyć lub usunąć regułę. Po wybraniu reguły, zgodnie z opisem w kroku 2, wybierz odpowiednią opcję w górnej części okna.


## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Aby utworzyć alertu dziennika aktywności przy użyciu szablonu usługi Azure Resource Manager, należy utworzyć zasób tego typu `microsoft.insights/activityLogAlerts`. Następnie należy wypełnić wszystkie powiązane właściwości. Oto szablon, który tworzy alertu dziennika aktywności:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Poprzedni przykład JSON można zapisać jako, na przykład sampleActivityLogAlert.json na potrzeby tego przewodnika i którą można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Może upłynąć do 5 minut, zanim nowe reguła alertu dziennika aktywności stanie się aktywna.

## <a name="rest-api"></a>Interfejs API REST 
[Interfejsu API alerty dziennika aktywności usługi Azure monitora](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) to API typu REST. Jest w pełni zgodny z interfejsu REST API usługi Azure Resource Manager. Może służyć za pośrednictwem programu PowerShell przy użyciu polecenia cmdlet usługi Resource Manager lub wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Wdrażanie szablonu usługi Resource Manager przy użyciu programu PowerShell
Aby wdrożyć przykładowy szablon usługi Resource Manager, pokazano w poprzedniej przy użyciu programu PowerShell [szablonu usługi Azure Resource Manager](#azure-resource-manager-template) sekcji, użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

gdy sampleActivityLogAlert.parameters.json zawiera wartości podanych dla parametrów wymaganych do utworzenia reguły alertu.

### <a name="use-activity-log-powershell-cmdlets"></a>Korzystanie z dziennika aktywności poleceń cmdlet programu PowerShell

Alerty dziennika aktywności są wyposażone w dedykowane dostępnych poleceń cmdlet programu PowerShell:

- [Zestaw AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Tworzy nowy alert dziennika aktywności lub aktualizuje istniejącego alertu dziennika aktywności.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Pobiera jeden lub więcej działań zasoby alertów dzienników.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Umożliwia istniejącego alertu dziennika aktywności i ustawia jego tagów.
- [Wyłącz AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Wyłącza istniejącego alertu dziennika aktywności i ustawia jego tagów.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Usuwa alertu dziennika aktywności.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenia wiersza polecenia platformy Azure w ramach zestawu w wersji dedykowanej [alert dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) są dostępne do zarządzania reguł alertów dzienników aktywności.

Aby utworzyć nową regułę alertu dziennika aktywności, użyj następujących poleceń w następującej kolejności:

1. [Utwórz alert dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Utwórz nowy zasób reguły alertu dziennika aktywności.
1. [zakres alertu dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Dodaj zakres dla reguł alertów dzienników utworzonego działania.
1. [AZ monitor dziennika aktywności alertu — grupy akcji](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Dodaj grupę akcji do reguł alertów dzienników aktywności.

Aby pobrać jeden zasób reguły alertu dziennika aktywności, użyj polecenia interfejsu wiersza polecenia Azure [Pokaż alertu dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Aby wyświetlić wszystkie zasoby reguły alertu dziennika aktywności w grupie zasobów, użyj [Lista alertów dziennika aktywności az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Zasoby reguły alertu dziennika aktywności można usunąć za pomocą polecenia interfejsu wiersza polecenia Azure [Usuwanie alertu dziennika aktywności az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [schematu elementu webhook o Dzienniki aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Odczyt [Przegląd dzienników aktywności](../../azure-monitor/platform/activity-log-alerts.md).
- Dowiedz się więcej o [grup akcji](../../azure-monitor/platform/action-groups.md).  
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../../azure-monitor/platform/service-notifications.md).
