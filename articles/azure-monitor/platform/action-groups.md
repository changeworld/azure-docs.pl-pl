---
title: Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal
description: Dowiedz się, jak tworzyć grupy akcji i zarządzać nimi w witrynie Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 9bc191bb27ebb0bac631ef5cfa8ddc34bbd8214e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520888"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal
Grupa akcji to zbiór preferencji powiadomień zdefiniowanych przez właściciela subskrypcji platformy Azure. Alerty usługi Azure Monitor i service health używają grup akcji do powiadamiania użytkowników o wyzwoleniu alertu. Różne alerty mogą używać tej samej grupy akcji lub różnych grup akcji w zależności od wymagań użytkownika. W ramach subskrypcji można skonfigurować maksymalnie 2000 grup akcji.

Skonfiguruj akcję, aby powiadomić osobę e-mailem lub SMS-em, otrzymuje on potwierdzenie wskazujące, że została dodana do grupy akcji.

W tym artykule pokazano, jak tworzyć grupy akcji i zarządzać nimi w witrynie Azure Portal.

Każda akcja składa się z następujących właściwości:

* **Nazwa**: Unikatowy identyfikator w grupie akcji.  
* **Typ akcji**: Wykonana akcja. Przykłady obejmują wysyłanie połączeń głosowych, SMS, e-mail; lub wyzwalanie różnego rodzaju zautomatyzowanych działań. Zobacz typy w dalszej części tego artykułu.
* **Szczegóły**: Odpowiednie szczegóły, które różnią się w zależności *od typu akcji*.

Aby uzyskać informacje na temat konfigurowania grup akcji za pomocą szablonów usługi Azure Resource Manager, zobacz [Szablony Menedżera zasobów grupy akcji](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu portalu Azure

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Monitor**. Okienko **Monitor** konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Zarządzaj akcjami**.

    ![Przycisk Zarządzaj akcjami](./media/action-groups/manage-action-groups.png)
    
1. Wybierz **pozycję Dodaj grupę akcji**i wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.png)
    
1. Wprowadź nazwę w polu **Nazwa grupy akcji** i wprowadź jej nazwę w polu Nazwa **krótka.** Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

      ![Okno dialogowe Dodawanie grupy akcji"](./media/action-groups/action-group-define.png)

1. Pole **Subskrypcja** jest automatycznie wypełniane przy bieżącej subskrypcji. Ta subskrypcja jest tą, w której grupa akcji jest zapisywana.

1. Wybierz **grupę Zasobów,** w której jest zapisywana grupa akcji.

1. Zdefiniuj listę akcji. Podaj następujące informacje dla każdej akcji:

    1. **Nazwa**: Wprowadź unikatowy identyfikator dla tej akcji.

    1. **Typ akcji:** Wybierz element runbook automatyzacji, funkcję platformy Azure, rolę usługi Azure Resource Manager poczty e-mail, e-mail/SMS/Push/Voice, ITSM, aplikację logiki, bezpieczny element webhook, element webhook.

    1. **Szczegóły:** Na podstawie typu akcji wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikację platformy Azure, połączenie ITSM lub element runbook automatyzacji. W przypadku akcji ITSM należy dodatkowo określić **element pracy** i inne pola wymagane przez narzędzie ITSM.
    
    1. **Wspólny schemat alertów:** Można włączyć [wspólny schemat alertów](https://aka.ms/commonAlertSchemaDocs), który zapewnia przewagę posiadania pojedynczego rozszerzalnego i ujednoliconego ładunku alertów we wszystkich usługach alertów w usłudze Azure Monitor.

1. Wybierz **przycisk OK,** aby utworzyć grupę akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami akcji

Po utworzeniu grupy akcji możesz wyświetlić **grupy akcji,** wybierając **pozycję Zarządzaj akcjami** na stronie docelowej **Alerty** w okienku **Monitor.** Wybierz grupę akcji, którą chcesz zarządzać:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje specyficzne dla działania

> [!NOTE]
> Zobacz [Limity usług subskrypcji do monitorowania](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) limitów liczbowych dla każdego z poniższych elementów.  

### <a name="automation-runbook"></a>System eks-owy automatyzacji
Aby uzyskać limity dotyczące ładunków ładowności ładowności systemu Runbook, zapoznaj się z [limitami usług subskrypcji platformy Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

W grupie akcji może być ograniczona liczba akcji uruchomieniu. . 

### <a name="azure-app-push-notifications"></a>Powiadomienia wypychania aplikacji platformy Azure
W grupie akcji może być ograniczona liczba akcji aplikacji platformy Azure.

### <a name="email"></a>Adres e-mail
Wiadomości e-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie poczty e-mail jest odpowiednio skonfigurowane
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

W grupie akcji może być ograniczona liczba akcji e-mail. Zobacz artykuł [o ograniczeniu szybkości.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Rola usługi Azure Resource Manager pocztą e-mail
Wyślij wiadomość e-mail do członków roli subskrypcji. Wiadomości e-mail będą wysyłane tylko do **użytkowników usługi Azure AD** członków roli. Wiadomości e-mail nie będą wysyłane do grup usługi Azure AD ani podmiotów zabezpieczeń usługi.

W grupie akcji może być ograniczona liczba akcji e-mail. Zobacz artykuł [o ograniczeniu szybkości.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Funkcja
Wywołuje istniejący punkt końcowy wyzwalacza HTTP w [usłudze Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

W grupie akcji może być ograniczona liczba akcji funkcji.

### <a name="itsm"></a>ITSM
ITSM Action wymaga połączenia ITSM. Dowiedz się, jak utworzyć [połączenie ITSM](../../azure-monitor/platform/itsmc-overview.md).

W grupie działania może znajdować się ograniczona liczba akcji ITSM. 

### <a name="logic-app"></a>Aplikacja logiki
W grupie akcji może być ograniczona liczba akcji aplikacji logiki.

### <a name="secure-webhook"></a>Bezpieczny element Webhook
Akcja Webhook grup akcji umożliwia korzystanie z usługi Azure Active Directory w celu zabezpieczenia połączenia między grupą akcji a chronionym interfejsem API sieci Web (punktem końcowym elementu webhook). Ogólny przepływ pracy w celu skorzystania z tej funkcji opisano poniżej. Aby zapoznać się z omówieniem aplikacji usługi Azure AD i podmiotów świadczących usługi, zobacz [omówienie platformy tożsamości firmy Microsoft (w wersji 2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Utwórz aplikację usługi Azure AD dla chronionego interfejsu API sieci Web. Zobacz: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Skonfiguruj chroniony interfejs API, który ma być wywoływany przez aplikację demona.
    
1. Włącz grupy akcji do korzystania z aplikacji usługi Azure AD.

    > [!NOTE]
    > Aby wykonać ten skrypt, musisz być członkiem [roli administratora aplikacji usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)
    
    - Zmodyfikuj wywołanie connect-AzureAD skryptu programu PowerShell, aby użyć identyfikatora dzierżawy usługi Azure AD.
    - Modyfikowanie zmiennej $myAzureADApplicationObjectId skryptu programu PowerShell w celu użycia identyfikatora obiektu aplikacji usługi Azure AD
    - Uruchom zmodyfikowany skrypt.
    
1. Skonfiguruj akcję Bezpiecznego elementu Webhook grupy akcji.
    - Skopiuj wartość $myApp.ObjectId ze skryptu i wprowadź ją w polu Identyfikator obiektu aplikacji w definicji akcji Webhook.
    
    ![Akcja Bezpiecznego elementu Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Bezpieczny skrypt programu PowerShell bezpiecznego elementu Webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Aby uzyskać dodatkowe ważne informacje, zobacz [informacje o ograniczaniu szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) i zachowanie [alertów SMS.](../../azure-monitor/platform/alerts-sms-behavior.md)

W grupie akcji może być ograniczona liczba akcji SMS.  

### <a name="voice"></a>Połączenia głosowe
Zobacz artykuł [o ograniczeniu szybkości.](./../../azure-monitor/platform/alerts-rate-limiting.md)

W grupie akcji może być ograniczona liczba akcji głosowych.

### <a name="webhook"></a>Webhook
Elementy webhook są ponowione przy użyciu następujących reguł. Wywołanie elementu webhook jest ponowione maksymalnie 2 razy, gdy zwracane są następujące kody stanu HTTP: 408, 429, 503, 504 lub punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Druga ponowna próba nastąpi po 100 sekundach. Po dwóch błędach żadna grupa akcji nie wywoła punktu końcowego przez 30 minut. 

Zakresy źródłowych adresów IP
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Aby otrzymywać aktualizacje dotyczące zmian w tych adresach IP, zaleca się skonfigurowanie alertu kondycji usługi, który monitoruje powiadomienia informacyjne o usłudze grupy akcji.

W grupie akcji może być ograniczona liczba akcji elementu Webhook.



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zachowaniu alertów SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Uzyskaj [zrozumienie schematu programu webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [złączu ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Dowiedz się więcej o [ograniczaniu szybkości](../../azure-monitor/platform/alerts-rate-limiting.md) alertów.
* Zapoznaj się [z omówieniem alertów dziennika aktywności](../../azure-monitor/platform/alerts-overview.md)i dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [konfigurować alerty przy każdym opublikowaniu powiadomienia o kondycji usługi.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)
