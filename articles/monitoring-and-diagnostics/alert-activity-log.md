---
title: Tworzenie widoku i zarządzanie nimi alertów dziennika aktywności w usłudze Azure Monitor
description: Jak utworzyć alertów dzienników aktywności z witryny Azure Portal, szablonów zasobów i programu PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a95cdbb48371cf960211f55bf077cea9db783db5
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248333"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie przy użyciu usługi Azure Monitor alertów dziennika aktywności  

## <a name="overview"></a>Przegląd
Alerty dzienników aktywności są alerty, które aktywować po wystąpieniu nowego zdarzenia dziennika aktywności, który spełnia warunki określone w alercie.

Te alerty są dla zasobów platformy Azure, można utworzyć przy użyciu szablonu usługi Azure Resource Manager. One również mogą być tworzone, zaktualizował lub usunął w witrynie Azure portal. Zazwyczaj można Tworzenie alertów dziennika aktywności, aby otrzymywać powiadomienia o zmianach określonych zasobów w subskrypcji platformy Azure, często są ograniczone do określonych grupach zasobów lub zasobu. Na przykład możesz chcieć otrzymasz powiadomienie, gdy dowolne maszyny wirtualnej (Przykładowe grupy zasobów) **myProductionResourceGroup** zostanie usunięty lub możesz chcieć uzyskać powiadomienia, gdy wszystkie nowe role są przypisane do użytkownika w ramach subskrypcji.

> [!IMPORTANT]
> Nie można utworzyć alerty dotyczące kondycji usługi powiadomień za pośrednictwem interfejsu tworzenia alertu dziennika aktywności. Aby dowiedzieć się więcej o tworzeniu i za pomocą powiadomień dotyczących kondycji usługi, zobacz [odbieranie alertów dziennika aktywności dla powiadomień dotyczących kondycji usług](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="azure-portal"></a>Azure Portal

> [!NOTE]

>  Podczas tworzenia reguł alertów, zapewnić następujące okoliczności:

> - Subskrypcja w zakresie nie jest różni się od subskrypcji, której tworzona jest alert.
- Kryteria muszą być poziom/status/obiekt wywołujący / grupa zasobów / identyfikator zasobu / Typ zasobu / kategorii zdarzeń, na którym skonfigurowano alert.
- Istnieje warunek "anyOf" lub warunki zagnieżdżone w konfiguracji alertu JSON (zasadniczo tylko jeden nieobsługiwanymi jest dozwolona w nie dalszych nieobsługiwanymi/anyOf).
- Jeśli kategoria jest "administracyjne". Należy określić co najmniej jednego z poprzednich kryteriów w alertu. Nie można utworzyć alert, który aktywuje za każdym razem, gdy zdarzenie zostanie utworzone w dziennikach aktywności.

### <a name="create-with-azure-portal"></a>Tworzenie za pomocą witryny Azure portal

Postępuj zgodnie z następującą procedurą:

1. W witrynie Azure portal wybierz **Monitor** > **alertów**
2. Kliknij przycisk **Nowa reguła alertu** w górnej części **alerty** okna.

     ![Nowa reguła alertu](./media/monitor-alerts-unified/AlertsPreviewOption.png)

     **Utwórz regułę** zostanie wyświetlone okno.

      ![nowe opcje reguły alertu](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **W ramach warunku zdefiniować alertu** Podaj następujące informacje, a następnie kliknij przycisk **gotowe**.

    - **Element docelowy alertu:** do przeglądania i wybierz element docelowy dla nowego alertu, należy użyć **Filtruj według subskrypcji** / **Filtruj według typu zasobu** i wybierz zasób lub grupa zasobów, z Lista wyświetlana.

    > [!NOTE]

    > można wybrać zasób, grupy zasobów lub subskrypcji usługi całej sygnału dziennika aktywności.

    **Docelowy przykładowy widok alertów** ![wybierz docelowy](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - W obszarze **kryteriów docelowych**, kliknij przycisk **Dodaj kryteria** i są wyświetlane wszystkie dostępne sygnały dla elementu docelowego, łącznie ze składnikami z różnych kategorii **dziennika aktywności**; Nazwa kategorii jest dołączany w **usługi Monitor** nazwy.

    - Wybierz sygnał na liście wyświetlonej różnych operacji możliwe dla typu **dziennika aktywności**.

    Można wybrać na osi czasu historia dziennika i odpowiednie logikę alertów ten sygnał docelowy:

    **Dodawanie ekranu z kryteriów**

    ![Dodaj kryteria](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Czas historii**: zdarzenia dostępne dla wybranej operacji jest można wykreślić przez ostatnie 6/12/24 godziny (lub) w ciągu ostatniego tygodnia.

    **Zgłoś alert logic**:

     - **Poziom zdarzenia**— poziom ważności zdarzenia. _Pełne_, _informacyjny_, _ostrzeżenie_, _błąd_, lub _krytyczne_.
     - **Stan**: stan zdarzenia. _Pracę_, _nie powiodło się_, lub _zakończyło się pomyślnie_.
     - **Zdarzenie zainicjowane przez**: tzw. obiekt wywołujący; Adres e-mail lub usługi Azure Active Directory identyfikator użytkownika, który wykonał operację.

        Przykładowy wykres sygnał z alert logic stosowane:

        ![ wybrane kryteria](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. W obszarze **Zdefiniuj szczegóły reguły alertów**, należy podać następujące dane:

    - **Nazwa reguły alertu** — Nazwa nowej reguły alertu
    - **Opis** — opis nowej reguły alertu
    - **Zapisz alert w grupie zasobów** — wybierz grupę zasobów, w którym chcesz zapisać tej nowej reguły.

5. W obszarze **grupy akcji**, z menu rozwijanego, należy określić grupę akcji, które chcesz przypisać do tej nowej reguły alertu. Alternatywnie [Utwórz nową grupę akcji](monitoring-action-groups.md) i przypisać do nowej reguły. Aby utworzyć nową grupę, kliknij przycisk **+ Nowa grupa**.

6. Aby włączyć reguły, po jego utworzeniu, kliknij przycisk **tak** dla **Włącz regułę po utworzeniu** opcji.
7. Kliknij przycisk **Utwórz regułę alertu**.

    Nowa reguła alertu dziennika aktywności zostanie utworzony i wyświetlony komunikat potwierdzenia wyświetlany u góry po prawej krawędzi okna.

    Możesz włączyć, wyłączyć, edytować lub usunąć regułę. [Dowiedz się więcej](#view-and-manage-activity-log-alert-rules-in-azure-portal) o zarządzaniu reguł dzienników aktywności.


Alternatywnie prosty sposób analogiczny opis warunki, na których można tworzyć reguły alertu w dzienniku aktywności jest aby eksplorować lub filtrowanie zdarzeń za pomocą [dziennika aktywności w witrynie Azure portal](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). W usłudze Azure Monitor — dziennik aktywności jeden można filtrować lub znaleźć wymaganych zdarzeń i następnie utworzyć alert przy użyciu **Dodaj alert dziennika aktywności** przycisk; następnie wykonaj kroki 4 i nowszych wersjach opisany powyżej samouczka.
    
 ![ Dodaj alert dziennika aktywności](./media/monitoring-activity-log-alerts-new-experience/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Wyświetlanie i zarządzanie nimi w witrynie Azure portal

1. W witrynie Azure portal kliknij **Monitor** > **alerty** i kliknij przycisk **zarządzanie regułami** w lewym górnym rogu okna.

    ![ Zarządzaj regułami alertów](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Zostanie wyświetlona lista dostępnych zasad.

2. Wyszukaj regułę dziennika aktywności w celu zmodyfikowania.

    ![ Wyszukaj reguł alertów dzienników aktywności](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Możesz użyć filtrów dostępnych - _subskrypcji_, _grupy zasobów_, _zasobów_, _typu sygnału_, lub _stanu_  można znaleźć reguły działania, które chcesz edytować.

    > [!NOTE]

    > Możesz edytować tylko **opis** , **docelowe kryteria** i **grup akcji**.

3.  Wybierz regułę, a następnie kliknij dwukrotnie, aby edytować opcje reguły. Wprowadź wymagane zmiany, a następnie kliknij przycisk **Zapisz**.

    ![ Zarządzaj regułami alertów](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Można wyłączyć, włączyć lub usunąć regułę. Wybierz odpowiednią opcję w górnej części okna, po wybraniu reguły, zgodnie z opisem w kroku 2.


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
Przykładowy kod json powyżej, można zapisać jako (np.) sampleActivityLogAlert.json na potrzeby ten przewodnik i można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Może upłynąć do 5 minut nowych reguła alertu dziennika aktywności stanie się aktywna

## <a name="rest-api"></a>Interfejs API REST 
[Usługa Azure Monitor — interfejs API alertów dziennika aktywności](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) jest w pełni zgodna z usługą Azure Resource Manager REST API i interfejs API REST. Dlatego można używać za pośrednictwem programu Powershell przy użyciu polecenia cmdlet usługi Resource Manager, a także wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell
Przedstawione poniżej użycia za pomocą polecenia cmdlet programu PowerShell usługi Resource Manager platformy Azure, w przykładzie przedstawionym wcześniej szablon zasobu (sampleActivityLogAlert.json) w [sekcji zasobów szablonu](#manage-alert-rules-for-activity-log-using-azure-resource-template) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```
Którym sampleActivityLogAlert.parameters.json ma wartości podanych dla parametrów wymaganych do utworzenia reguły alertu.

## <a name="cli"></a>Interfejs wiersza polecenia
Przedstawione poniżej użycia za pomocą polecenia usługi Azure Resource Manager w interfejsie wiersza polecenia platformy Azure w przykładzie przedstawionym wcześniej szablon zasobu (sampleActivityLogAlert.json) w [sekcji zasobów szablonu](#manage-alert-rules-for-activity-log-using-azure-resource-template) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleActivityLogAlert.json --parameters @sampleActivityLogAlert.parameters.json
```
*SampleActivityLogAlert.parameters.json* plik zawiera wartości podanych dla parametrów wymaganych do utworzenia reguły alertu.


## <a name="next-steps"></a>Kolejne kroki

- [Schemat elementów Webhook o Dzienniki aktywności](monitoring-activity-log-alerts-webhook.md)
- [Przegląd dzienników aktywności](monitoring-activity-log-alerts.md) 
- Dowiedz się więcej o [grup akcji](monitoring-action-groups.md).  
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](monitoring-service-notifications.md).
