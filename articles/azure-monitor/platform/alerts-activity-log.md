---
title: Tworzenie, wyświetlanie i zarządzanie aktywności alerty dzienników w usłudze Azure Monitor
description: Jak tworzenie alertów dziennika aktywności przy użyciu witryny Azure portal, szablonów usługi Azure Resource Manager i programu Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 8183c7070b5d42e1c7a96fc0d64974658b2ec7d0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448923"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie przy użyciu usługi Azure Monitor alertów dziennika aktywności  

## <a name="overview"></a>Omówienie
Alerty dzienników aktywności są alerty, które aktywować po wystąpieniu nowego zdarzenia dziennika aktywności, który spełnia warunki określone w alercie.

Te alerty są dla zasobów platformy Azure, można utworzyć przy użyciu szablonu usługi Azure Resource Manager. One również mogą być tworzone, zaktualizował lub usunął w witrynie Azure portal. Zazwyczaj można Tworzenie alertów dziennika aktywności, aby otrzymywać powiadomienia o zmianach określonych zasobów w subskrypcji platformy Azure, często są ograniczone do określonych grupach zasobów lub zasobu. Na przykład możesz chcieć otrzymasz powiadomienie, gdy dowolne maszyny wirtualnej (Przykładowe grupy zasobów) **myProductionResourceGroup** zostanie usunięty lub możesz chcieć uzyskać powiadomienia, gdy wszystkie nowe role są przypisane do użytkownika w ramach subskrypcji.

> [!IMPORTANT]
> Nie można utworzyć alerty dotyczące kondycji usługi powiadomień za pośrednictwem interfejsu tworzenia alertu dziennika aktywności. Aby dowiedzieć się więcej o tworzeniu i za pomocą powiadomień dotyczących kondycji usługi, zobacz [odbieranie alertów dziennika aktywności dla powiadomień dotyczących kondycji usług](alerts-activity-log-service-notifications.md).

Podczas tworzenia reguł alertów, zapewnić następujące okoliczności:

- Subskrypcja w zakresie nie jest różni się od subskrypcji, której tworzona jest alert.
- Kryteria muszą być poziom/status/obiekt wywołujący / grupa zasobów / identyfikator zasobu / Typ zasobu / kategorii zdarzeń, na którym skonfigurowano alert.
- Istnieje warunek "anyOf" lub warunki zagnieżdżone w konfiguracji alertu JSON (zasadniczo tylko jeden nieobsługiwanymi jest dozwolona w nie dalszych nieobsługiwanymi/anyOf).
- Jeśli kategoria jest "administracyjne". Należy określić co najmniej jednego z poprzednich kryteriów w alertu. Nie można utworzyć alert, który aktywuje za każdym razem, gdy zdarzenie zostanie utworzone w dziennikach aktywności.


## <a name="azure-portal"></a>Azure Portal

Za pomocą witryny Azure portal, użytkownik może utworzyć i modyfikowanie reguł alertów dzienników aktywności. I środowisko jest zintegrowana z dziennika aktywności platformy Azure — w celu zapewnienia bezproblemowego tworzenia alertu dla określonych zdarzeń zainteresowania.

### <a name="create-with-azure-portal"></a>Tworzenie za pomocą witryny Azure portal

Użyj poniższej procedury:

1. W witrynie Azure portal wybierz **Monitor** > **alertów**
2. Kliknij przycisk **Nowa reguła alertu** w górnej części **alerty** okna.

     ![Nowa reguła alertu](media/alerts-activity-log/AlertsPreviewOption.png)

     **Utwórz regułę** zostanie wyświetlone okno.

      ![nowe opcje reguły alertu](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **W ramach warunku zdefiniować alertu** Podaj następujące informacje, a następnie kliknij przycisk **gotowe**.

   - **Element docelowy alertu:** Aby wyświetlić i wybierz element docelowy dla nowego alertu, należy użyć **Filtruj według subskrypcji** / **Filtruj według typu zasobu** i wybierz zasób lub grupa zasobów z wyświetlonej listy.

     > [!NOTE]
     > 
     > można wybrać zasób, grupy zasobów lub subskrypcji usługi całej sygnału dziennika aktywności.

     **Docelowy przykładowy widok alertów**
     ![wybierz docelowy](media/alerts-activity-log/select-target.png)

   - W obszarze **kryteriów docelowych**, kliknij przycisk **Dodaj kryteria** i są wyświetlane wszystkie dostępne sygnały dla elementu docelowego, łącznie ze składnikami z różnych kategorii **dziennika aktywności**; Nazwa kategorii jest dołączany w **usługi Monitor** nazwy.

   - Wybierz sygnał na liście wyświetlonej różnych operacji możliwe dla typu **dziennika aktywności**.

     Można wybrać na osi czasu historia dziennika i odpowiednie logikę alertów ten sygnał docelowy:

     **Dodawanie ekranu z kryteriów**

     ![Dodaj kryteria](media/alerts-activity-log/add-criteria.png)

     **Czas historii**: Zdarzenia dostępne dla wybranej operacji jest można wykreślić przez ostatnie 6/12/24 godziny (lub) w ciągu ostatniego tygodnia.

     **Zgłoś alert logic**:

     - **Poziom zdarzenia**— poziom ważności zdarzenia. _Pełne_, _informacyjny_, _ostrzeżenie_, _błąd_, lub _krytyczne_.
     - **Stan**: Stan zdarzenia. _Pracę_, _nie powiodło się_, lub _zakończyło się pomyślnie_.
     - **Zdarzenie zainicjowane przez**: Znany także jako obiekt wywołujący; Adres e-mail lub usługi Azure Active Directory identyfikator użytkownika, który wykonał operację.

       Przykładowy wykres sygnał z alert logic stosowane:

       ![ wybrane kryteria](media/alerts-activity-log/criteria-selected.png)

4. W obszarze **Zdefiniuj szczegóły reguły alertów**, należy podać następujące dane:

    - **Nazwa reguły alertu** — Nazwa nowej reguły alertu
    - **Opis** — opis nowej reguły alertu
    - **Zapisz alert w grupie zasobów** — wybierz grupę zasobów, w którym chcesz zapisać tej nowej reguły.

5. W obszarze **grupy akcji**, z menu rozwijanego, należy określić grupę akcji, które chcesz przypisać do tej nowej reguły alertu. Alternatywnie [Utwórz nową grupę akcji](../../azure-monitor/platform/action-groups.md) i przypisać do nowej reguły. Aby utworzyć nową grupę, kliknij przycisk **+ Nowa grupa**.

6. Aby włączyć reguły, po jego utworzeniu, kliknij przycisk **tak** dla **Włącz regułę po utworzeniu** opcji.
7. Kliknij przycisk **Utwórz regułę alertu**.

    Nowa reguła alertu dziennika aktywności zostanie utworzony i wyświetlony komunikat potwierdzenia wyświetlany u góry po prawej krawędzi okna.

    Możesz włączyć, wyłączyć, edytować lub usunąć regułę. Dowiedz się więcej o zarządzaniu reguł dzienników aktywności.


Alternatywnie prosty sposób analogiczny opis warunki, na których można tworzyć reguły alertu w dzienniku aktywności jest aby eksplorować lub filtrowanie zdarzeń za pomocą [dziennika aktywności w witrynie Azure portal](activity-log-view.md#azure-portal). W usłudze Azure Monitor — dziennik aktywności jeden można filtrować lub znaleźć wymaganych zdarzeń i następnie utworzyć alert przy użyciu **Dodaj alert dziennika aktywności** przycisk; następnie wykonaj kroki 4 i nowszych wersjach opisany powyżej samouczka.
    
 ![ Dodaj alert dziennika aktywności](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Wyświetlanie i zarządzanie nimi w witrynie Azure portal

1. W witrynie Azure portal kliknij **Monitor** > **alerty** i kliknij przycisk **zarządzanie regułami** w lewym górnym rogu okna.

    ![ Zarządzaj regułami alertów](media/alerts-activity-log/manage-alert-rules.png)

    Zostanie wyświetlona lista dostępnych zasad.

2. Wyszukaj regułę dziennika aktywności w celu zmodyfikowania.

    ![ Wyszukaj reguł alertów dzienników aktywności](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Możesz użyć filtrów dostępnych - _subskrypcji_, _grupy zasobów_, _zasobów_, _typu sygnału_, lub _stanu_  można znaleźć reguły działania, które chcesz edytować.

   > [!NOTE]
   > 
   > Możesz edytować tylko **opis** , **docelowe kryteria** i **grup akcji**.

3. Wybierz regułę, a następnie kliknij dwukrotnie, aby edytować opcje reguły. Wprowadź wymagane zmiany, a następnie kliknij przycisk **Zapisz**.

   ![ Zarządzaj regułami alertów](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Można wyłączyć, włączyć lub usunąć regułę. Wybierz odpowiednią opcję w górnej części okna, po wybraniu reguły, zgodnie z opisem w kroku 2.


## <a name="azure-resource-template"></a>Szablonu zasobów platformy Azure
Aby utworzyć alertu dziennika aktywności przy użyciu szablonu usługi Resource Manager, należy utworzyć zasób tego typu `microsoft.insights/activityLogAlerts`. Następnie należy wypełnić wszystkie powiązane właściwości. Oto szablon, który tworzy alertu dziennika aktywności.

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
Przykładowy kod json powyżej, można zapisać jako (np.) sampleActivityLogAlert.json na potrzeby ten przewodnik i można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Może upłynąć do 5 minut nowych reguła alertu dziennika aktywności stanie się aktywna

## <a name="rest-api"></a>Interfejs API REST 
[Usługa Azure Monitor — dziennik aktywności interfejs API alertów](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) jest w pełni zgodna z usługą Azure Resource Manager REST API i interfejs API REST. Dlatego można używać za pośrednictwem programu Powershell przy użyciu polecenia cmdlet usługi Resource Manager, a także wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>Wdrażanie szablonu usługi Resource Manager przy użyciu programu PowerShell
Przy użyciu programu PowerShell do wdrożenia przykład szablonu usługi Resource pokazano w poprzedniej [sekcji zasobów szablonu] (#--szablonu usługi resource manager, użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

gdy sampleActivityLogAlert.parameters.json zawiera wartości podanych dla parametrów wymaganych do utworzenia reguły alertu.

### <a name="use-activity-log-powershell-cmdlets"></a>Korzystanie z dziennika aktywności poleceń cmdlet programu PowerShell

Alerty dziennika aktywności są wyposażone w dedykowane dostępnych poleceń cmdlet programu PowerShell:

- [Zestaw AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert) : Tworzy nową lub aktualizowanie istniejącego alertu dziennika aktywności.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert) : Pobiera jeden lub więcej działań zasoby alertów dzienników.
- [Włącz AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert) : Umożliwia istniejącego alertu dziennika aktywności i ustawia jego tagów.
- [Wyłącz AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert) : Wyłącza istniejącego alertu dziennika aktywności i ustawia jego tagów.
- [Usuń AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert) : Usuwa alertu dziennika aktywności.

## <a name="cli"></a>Interfejs wiersza polecenia

Polecenia wiersza polecenia platformy Azure w ramach zestawu w wersji dedykowanej [alert dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) są dostępne do zarządzania reguł alertów dzienników aktywności.

Aby utworzyć nową regułę alertu dziennika aktywności, należy użyć w następującej kolejności:

1. [Utwórz alert dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Tworzenie nowego zasobu reguły alertu dziennika aktywności
1. [zakres alertu dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Dodaj zakres dla reguł alertów dzienników utworzonego działania
1. [AZ monitor dziennika aktywności alertu — grupy akcji](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Dodaj grupę akcji do reguł alertów dzienników aktywności

Do pobrania jednego działania reguły alertu zasób dziennika, polecenia wiersza polecenia platformy Azure [Pokaż alertu dziennika aktywności monitora az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) mogą być używane. W przypadku wyświetlania wszystkich zasobów reguły alertu dziennika aktywności w grupie zasobów, użyj [Lista alertów dziennika aktywności az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Zasoby reguły alertu dziennika aktywności można usunąć za pomocą polecenia interfejsu wiersza polecenia Azure [Usuwanie alertu dziennika aktywności az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Kolejne kroki

- [Schemat elementów Webhook o Dzienniki aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Przegląd dzienników aktywności](../../azure-monitor/platform/activity-log-alerts.md) 
- Dowiedz się więcej o [grup akcji](../../azure-monitor/platform/action-groups.md).  
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../../azure-monitor/platform/service-notifications.md).
