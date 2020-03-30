---
title: Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności w usłudze Azure Monitor
description: Tworzenie alertów dziennika aktywności przy użyciu witryny Azure Portal, szablonu usługi Azure Resource Manager i programu Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132405"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności przy użyciu usługi Azure Monitor  

## <a name="overview"></a>Omówienie

Alerty dziennika aktywności są alerty, które są aktywowane, gdy wystąpi zdarzenie nowego dziennika aktywności, które spełnia warunki określone w alecie.

Te alerty są dla zasobów platformy Azure i można utworzyć przy użyciu szablonu Usługi Azure Resource Manager. Można je również tworzyć, aktualizować lub usuwać w witrynie Azure portal. Zazwyczaj można utworzyć alerty dziennika aktywności, aby otrzymywać powiadomienia, gdy wystąpią określone zmiany w zasobach w subskrypcji platformy Azure. Alerty są często ograniczone do określonych grup zasobów lub zasobów. Na przykład można otrzymywać powiadomienia o usunięciu dowolnej maszyny wirtualnej w przykładowej grupie zasobów **myProductionResourceGroup.** Lub można uzyskać powiadomienie, jeśli nowe role są przypisane do użytkownika w ramach subskrypcji.

> [!IMPORTANT]
> Alertów o powiadomieniu o kondycji usługi nie można utworzyć za pośrednictwem interfejsu do tworzenia alertów dziennika aktywności. Aby dowiedzieć się więcej o tworzeniu i używaniu powiadomień o kondycji usługi, zobacz [Odbieranie alertów dziennika aktywności w powiadomieniach o kondycji usługi](alerts-activity-log-service-notifications.md).

Podczas tworzenia reguł alertów należy upewnić się, że:

- Subskrypcja w zakresie nie różni się od subskrypcji, w której tworzony jest alert.
- Kryteria muszą być poziom, stan, wywołujący, grupa zasobów, identyfikator zasobu lub kategorii zdarzeń typu zasobu, na którym jest skonfigurowany alert.
- W konfiguracji alertu JSON nie ma żadnego warunku "anyOf" ani warunków zagnieżdżonych. Zasadniczo tylko jeden warunek "allOf" jest dozwolony bez dalszych warunków "allOf" lub "anyOf".
- Gdy kategoria jest "administracyjna", należy określić co najmniej jedno z powyższych kryteriów w alertie. Nie można utworzyć alertu, który aktywuje się za każdym razem, gdy zdarzenie jest tworzone w dziennikach aktywności.

## <a name="azure-portal"></a>Portal Azure

Za pomocą witryny Azure Portal można tworzyć i modyfikować reguły alertów dziennika aktywności. Środowisko jest zintegrowane z dziennikiem aktywności platformy Azure, aby zapewnić bezproblemowe tworzenie alertów dla określonych zdarzeń zainteresowania.

### <a name="create-with-the-azure-portal"></a>Tworzenie za pomocą portalu Azure

Użyj poniższej procedury.

1. W witrynie Azure portal wybierz pozycję > **Monitoruj alerty**. **Monitor**
2. Wybierz **pozycję Nowa reguła alertu** w lewym górnym rogu okna **Alerty.**

     ![Nowa reguła alertu](media/alerts-activity-log/AlertsPreviewOption.png)

     Zostanie **wyświetlene** okno Utwórz regułę.

      ![Nowe opcje reguł alertów](media/alerts-activity-log/create-new-alert-rule-options.png)

3. W obszarze **Definiuj warunek alertu**podaj następujące informacje i wybierz opcję **Gotowe:**

   - **Cel alertu:** Aby wyświetlić i wybrać obiekt docelowy dla nowego alertu, użyj **filtruj według subskrypcji** / **Filtr według typu zasobu**. Wybierz zasób lub grupę zasobów z wyświetlona lista.

     > [!NOTE]
     > 
     > Można wybrać tylko [usługę Azure Resource Manager](../../azure-resource-manager/management/overview.md) śledzone zasobów, grupy zasobów lub całej subskrypcji dla sygnału dziennika aktywności. 

     **Widok próbki obiektu docelowego alertu**

     ![Wybieranie obiektu docelowego](media/alerts-activity-log/select-target.png)

   - W obszarze **Kryteria docelowe**wybierz pozycję **Dodaj kryteria**. Wyświetlane są wszystkie dostępne sygnały dla celu, w tym sygnały z różnych kategorii **dziennika aktywności.** Nazwa kategorii jest dołączana do nazwy **usługi monitora.**

   - Wybierz sygnał z listy wyświetlanych różnych operacji, które są możliwe dla typu **Dziennik aktywności**.

     Można wybrać oś czasu historii dziennika i odpowiednią logikę alertu dla tego sygnału docelowego:

     **Ekran Dodaj kryteria**

     ![Dodaj kryteria](media/alerts-activity-log/add-criteria.png)

     - **Czas historii:** Zdarzenia dostępne dla wybranej operacji mogą być drukowane w ciągu ostatnich 6, 12 lub 24 godzin lub w ciągu ostatniego tygodnia.

     - **Logika alertu:**

       - **Poziom zdarzenia**: Poziom ważności zdarzenia: _Pełne_, _Informacyjne_, _Ostrzeżenie_, _Błąd_lub _Krytyczny_.
       - **Stan**: Stan zdarzenia: _Uruchomiono,_ _nie powiodło się_lub _powiodło się_.
       - **Zdarzenie zainicjowane przez**: Znany również jako wywołujący. Adres e-mail lub identyfikator usługi Azure Active Directory użytkownika, który wykonał operację.

       Ten przykładowy wykres sygnału ma zastosowaną logikę alertu:

       ![Wybrane kryteria](media/alerts-activity-log/criteria-selected.png)

4. W obszarze **Definiowanie szczegółów alertu**podaj następujące szczegóły:

    - **Nazwa reguły alertu**: Nazwa nowej reguły alertu.
    - **Opis**: Opis nowej reguły alertu.
    - **Zapisz alert w grupie zasobów:** Wybierz grupę zasobów, w której chcesz zapisać tę nową regułę.

5. W obszarze **Grupa akcji**z menu rozwijanego określ grupę akcji, którą chcesz przypisać do tej nowej reguły alertu. Możesz też [utworzyć nową grupę akcji](../../azure-monitor/platform/action-groups.md) i przypisać ją do nowej reguły. Aby utworzyć nową grupę, wybierz **pozycję + Nowa grupa**.

6. Aby włączyć reguły po ich utworzeniu, wybierz pozycję **Tak** dla opcji **Włącz regułę przy tworzeniu.**
7. Wybierz pozycję **Utwórz regułę alertu**.

    Zostanie utworzona nowa reguła alertu dla dziennika aktywności, a w prawym górnym rogu okna pojawi się komunikat potwierdzający.

    Regułę można włączyć, wyłączyć, edytować lub usunąć. Dowiedz się więcej o zarządzaniu regułami dziennika aktywności.


Prostą analogią do zrozumienia warunków, na których można tworzyć reguły alertów w dzienniku działań, jest eksplorowanie lub filtrowanie zdarzeń za pośrednictwem [dziennika aktywności w witrynie Azure portal.](activity-log-view.md#azure-portal) Na ekranie **dziennika usługi Azure Monitor —** można filtrować lub znaleźć niezbędne zdarzenie, a następnie utworzyć alert przy użyciu przycisku Dodaj alert dziennika **aktywności.** Następnie wykonaj kroki od 4 do 7, jak pokazano wcześniej.
    
 ![Dodawanie alertu z dziennika aktywności](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Wyświetlanie portalu Azure i zarządzanie nimi

1. W witrynie Azure portal wybierz pozycję > **Monitoruj alerty**. **Monitor** Wybierz **pozycję Zarządzaj regułami alertów** w lewym górnym rogu okna.

    ![Zarządzaj regułami alertów](media/alerts-activity-log/manage-alert-rules.png)

    Pojawi się lista dostępnych reguł.

2. Wyszukaj regułę dziennika aktywności do zmodyfikowania.

    ![Reguły alertów dziennika aktywności wyszukiwania](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Można użyć dostępnych filtrów, _Subskrypcja,_ _Grupa zasobów,_ _Zasób,_ _Typ sygnału_lub _Stan_, aby znaleźć regułę działania, którą chcesz edytować.

   > [!NOTE]
   > 
   > Można edytować tylko **opis,** **kryteria docelowe**i **grupy akcji**.

3. Wybierz regułę i kliknij dwukrotnie, aby edytować opcje reguły. Wykonuj wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

   ![Zarządzaj regułami alertów](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Regułę można włączyć, wyłączyć lub usunąć. Wybierz odpowiednią opcję w górnej części okna po wybraniu reguły zgodnie z opisem w kroku 2.


## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Aby utworzyć regułę alertu dziennika aktywności przy użyciu szablonu usługi `microsoft.insights/activityLogAlerts`Azure Resource Manager, należy utworzyć zasób tego typu . Następnie należy wypełnić wszystkie powiązane właściwości. Oto szablon, który tworzy regułę alertu dziennika aktywności:

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
Poprzedni przykładowy JSON można zapisać jako, na przykład, sampleActivityLogAlert.json na potrzeby tego przejścia i można go wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../../azure-resource-manager/templates/deploy-portal.md).

Następujące pola są opcje, które można użyć w szablonie Usługi Azure Resource Manager dla pól warunków: Należy zauważyć, że "Kondycja zasobów", "Doradca" i "Kondycja usługi" mają dodatkowe właściwości pola dla ich pól specjalnych. 
1. resourceId: Identyfikator zasobu dotkniętego zasobu w zdarzeniu dziennika aktywności, na które powinien zostać wygenerowany alert.
2. kategoria: Kategoria w zdarzeniu dziennika aktywności. Na przykład: Adminis, ServiceHealth, ResourceHealth, Autoscale, Security, Recommendation, Policy.
3. wywołujący: adres e-mail lub identyfikator usługi Azure Active Directory użytkownika, który wykonał działanie zdarzenia dziennika aktywności.
4. poziom: Poziom działania w zdarzeniu dziennika aktywności, na które powinien zostać wygenerowany alert. Na przykład: Krytyczny, Błąd, Ostrzeżenie, Informacyjny, Pełny.
5. operationName: Nazwa operacji w zdarzeniu dziennika aktywności. Na przykład: Microsoft.Resources/deployments/write
6. resourceGroup: Nazwa grupy zasobów dla zasobu, do przypadku dotkniętego zdarzenia dziennika aktywności.
7. resourceProvider: [Dostawcy zasobów platformy Azure i typy wyjaśnienia](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Aby uzyskać listę map dostawców zasobów do usług platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: Ciąg opisujący stan operacji w zdarzeniu działania. Na przykład: Uruchomiono, w toku, powiodło się, nie powiodło się, aktywnie, rozwiązano
9. subStatus: Zazwyczaj kod stanu HTTP odpowiedniego wywołania REST, ale może również zawierać inne ciągi opisujące podstatus.   Na przykład: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), zaakceptowany (kod stanu HTTP: 202), brak zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nieodebrania (kod stanu HTTP: 404), konflikt (kod stanu HTTP: 409), serwer wewnętrzny Błąd (kod stanu HTTP: 500), usługa niedostępna (kod stanu HTTP: 503), limit czasu bramy (kod stanu HTTP: 504).
10. resourceType: typ zasobu, którego dotyczy zdarzenie. Na przykład: Zasoby firmy Microsoft/wdrożenia

Przykład:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Więcej informacji na temat pól dziennika aktywności można znaleźć [tutaj](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Może upłynąć do 5 minut, aby nowa reguła alertu dziennika aktywności stała się aktywna.

## <a name="rest-api"></a>Interfejs API REST 
Interfejs [API alertów dziennika aktywności monitora azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) jest interfejsem API REST. Jest w pełni zgodny z interfejsem API REST usługi Azure Resource Manager. Może być używany za pośrednictwem programu PowerShell przy użyciu polecenia cmdlet usługi Resource Manager lub interfejsu wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Wdrażanie szablonu Menedżera zasobów za pomocą programu PowerShell
Aby wdrożyć przykładowy szablon usługi Resource Manager wyświetlany w poprzedniej sekcji [szablonów usługi Azure Resource Manager,](#azure-resource-manager-template) użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

gdzie sampleActivityLogAlert.parameters.json zawiera wartości podane dla parametrów potrzebnych do tworzenia reguł alertów.

### <a name="use-activity-log-powershell-cmdlets"></a>Używanie poleceń cmdlet dziennika aktywności programu PowerShell

Alerty dziennika aktywności mają dedykowane polecenia cmdlet programu PowerShell dostępne:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Tworzy nowy alert dziennika aktywności lub aktualizuje istniejący alert dziennika aktywności.
- [Get-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert)Pobiera jeden lub więcej zasobów alertu dziennika aktywności.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Włącza alert istniejącego dziennika aktywności i ustawia jego tagi.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Wyłącza istniejący alert dziennika aktywności i ustawia jego tagi.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Usuwa alert dziennika aktywności.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dedykowane polecenia interfejsu wiersza polecenia platformy Azure w ramach [ustawionego alertu dziennika aktywności monitora AZ](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) są dostępne do zarządzania regułami alertów dziennika aktywności.

Aby utworzyć nową regułę alertu dziennika aktywności, użyj następujących poleceń w tej kolejności:

1. [AZ monitor alertu dziennika aktywności — tworzenie:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Utwórz nowy zasób reguły alertu dziennika aktywności.
1. [AZ monitoruj zakres alertu dziennika aktywności:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)Dodaj zakres dla utworzonej reguły alertu dziennika aktywności.
1. [AZ monitoruj grupę akcji alertów dziennika aktywności:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)Dodaj grupę akcji do reguły alertu dziennika aktywności.

Aby pobrać jeden zasób reguły alertu dziennika aktywności, użyj polecenia [az monitor az alert alertu dziennika aktywności monitora](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)platformy Azure. Aby wyświetlić wszystkie zasoby reguł alertów dziennika aktywności w grupie zasobów, użyj [listy alertów az monitor monitoruj aktywność.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)
Zasoby reguł alertów dziennika aktywności można usunąć za pomocą polecenia Azure CLI [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [schemacie elementu webhook dla dzienników aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Przeczytaj [przegląd dzienników aktywności](../../azure-monitor/platform/activity-log-alerts.md).
- Dowiedz się więcej o [grupach akcji](../../azure-monitor/platform/action-groups.md).  
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../../azure-monitor/platform/service-notifications.md).
