---
title: Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
description: Dowiedz się, jak tworzyć grupy i zarządzać nimi akcji w witrynie Azure portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705256"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal
Grupy akcji to zbiór preferencje powiadamiania zdefiniowane przez właściciela subskrypcji platformy Azure. Alerty monitorowania i kondycji usług platformy Azure umożliwia powiadomienie użytkowników, czy alert został wywołany grup akcji. Różne alerty może używać tej samej grupy akcji lub grupy inną akcję w zależności od wymagań użytkownika. Można skonfigurować maksymalnie 2000 grup akcji w ramach subskrypcji.

Możesz skonfigurować akcję, aby powiadomić osoby, adres e-mail lub wiadomości SMS, otrzymują potwierdzenie wskazujący, że zostały one dodane do grupy akcji.

W tym artykule przedstawiono sposób tworzenia grup i zarządzanie nimi akcji w witrynie Azure portal.

Każda akcja składa się z następującymi właściwościami:

* **Nazwa**: Unikatowy identyfikator w obrębie grupy akcji.  
* **Typ akcji**: Akcja została wykonana. Przykłady obejmują wysyłanie pocztą e-mail wywołanie, wiadomość SMS, głos; lub różnego rodzaju akcje automatyczne wyzwalanie. Zobacz typy w dalszej części tego artykułu.
* **Szczegóły**: Odpowiednimi szczegółami, które różnią się zależnie od *typ akcji*.

Aby uzyskać informacje na temat konfigurowania grup akcji przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów usługi Resource Manager grupy akcji](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Tworzenie grupy akcji przy użyciu witryny Azure portal

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Monitor**. **Monitor** okienko konsoliduje wszystkie ustawienia monitorowania i danych w jednym widoku.

    ![Usługa "Monitor"](./media/action-groups/home-monitor.png)
    
1. Wybierz **alerty** polecenie **zarządzać akcjami**.

    ![Zarządzanie przycisk Akcje](./media/action-groups/manage-action-groups.png)
    
1. Wybierz **Dodaj grupę akcji**, a następnie wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/action-groups/add-action-group.png)
    
1. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

      ![Okno dialogowe Dodawanie grupy akcji"](./media/action-groups/action-group-define.png)

1. **Subskrypcji** polu autofills przy użyciu Twojej bieżącej subskrypcji. Ta subskrypcja jest ten, w którym jest zapisany do grupy akcji.

1. Wybierz **grupy zasobów** w są zapisywane do grupy akcji.

1. Zdefiniuj listę akcji. Podaj następujące czynności dla każdej akcji:

    1. **Nazwa**: Wprowadź unikatowy identyfikator dla tej akcji.

    1. **Typ akcji**: Wybierz adres E-mail/SMS/wypychania/rejestr, aplikację logiki, element Webhook, ITSM lub elementu Runbook usługi Automation.

    1. **Szczegóły**: Na podstawie akcji typu, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikacja platformy Azure, połączenia narzędzia ITSM lub elementu runbook usługi Automation. Dla akcji ITSM, należy również określić **elementu roboczego** i wymaga narzędziem ITSM, inne pola.
    
    1. **Wspólny schemat alertu**: Istnieje możliwość włączenia [wspólny schemat alertu](https://aka.ms/commonAlertSchemaDocs), zapewniającą zaletą pojedynczej rozszerzalne i ujednoliconego ładunku alertu przez ten alert usługi w usłudze Azure Monitor.

1. Wybierz **OK** można utworzyć grupy akcji.

## <a name="manage-your-action-groups"></a>Zarządzanie grupami działań

Po utworzeniu grupy akcji, jest ona widoczna na **grup akcji** części **Monitor** okienka. Wybierz grupę akcji, które mają być zarządzane do:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuń grupę akcji.

## <a name="action-specific-information"></a>Informacje o określonej akcji

> [!NOTE]
> Zobacz [limity usług subskrypcji do monitorowania](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) dla ograniczeń numerycznych na każdym z poniższych elementów.  

### <a name="azure-app-push-notifications"></a>Powiadomienia Push aplikacji platformy Azure
Ograniczona liczba akcji aplikacji platformy Azure może mieć w grupy akcji.

### <a name="email"></a>Email
Wiadomości e-mail będą wysyłane z następujących adresów e-mail. Upewnij się, że filtrowanie wiadomości e-mail jest prawidłowo skonfigurowany
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Mogą mieć ograniczoną liczbę akcji poczty e-mail do grupy akcji. Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykułu.

### <a name="itsm"></a>ITSM
Akcja ITSM wymaga połączenia narzędzia ITSM. Dowiedz się, jak utworzyć [połączenia narzędzia ITSM](../../azure-monitor/platform/itsmc-overview.md).

Mogą mieć ograniczoną liczbę akcje ITSM w grupy akcji. 

### <a name="logic-app"></a>Aplikacja logiki
Może być ograniczona liczba akcji aplikacji logiki do grupy akcji.

### <a name="function"></a>Funkcja
Klawiszy funkcyjnych dla aplikacji funkcji, skonfigurowany jako akcje są odczytywane przy użyciu interfejsu API funkcji wymagająca aplikacji funkcji w wersji 2 do skonfigurowania aplikacji, ustawienie "AzureWebJobsSecretStorageType" na "files". Aby uzyskać więcej informacji, zobacz [zmieni się na zarządzanie kluczami w funkcje w wersji 2]( https://aka.ms/funcsecrets).

Mogą mieć ograniczoną liczbę funkcji akcji grupy akcji.

### <a name="automation-runbook"></a>Element Runbook usługi Automation
Zapoznaj się [limity usług subskrypcji platformy Azure](../../azure-subscription-service-limits.md) limity ładunków elementu Runbook.

Grupy akcji, mogą mieć ograniczoną liczbę działań elementu Runbook. 

### <a name="sms"></a>SMS
Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) i [SMS alert zachowanie](../../azure-monitor/platform/alerts-sms-behavior.md) dodatkowe ważne informacje.

Mogą mieć ograniczoną liczbę akcji programu SMS w grupy akcji.  

### <a name="voice"></a>Połączenia głosowe
Zobacz [ograniczania informacje o szybkości](./../../azure-monitor/platform/alerts-rate-limiting.md) artykułu.

Mogą mieć ograniczoną liczbę akcji głosu w grupy akcji.

### <a name="webhook"></a>Webhook
Elementy Webhook są zwalniane, używając następujących reguł. Wywołanie elementu webhook zostanie ponowiony więcej niż 2 godziny po następujące kody stanu HTTP są zwracane: 408, 429, 503, 504 lub punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Drugi ponawiania odbywa się po 100 sekund. Po awarii dwóch węzłów żadna grupa akcji wywoła punktu końcowego w ciągu 30 minut. 

Zakresy adresów IP źródła
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Aby otrzymywać aktualizacje o zmianach na te adresy IP, zalecane Konfigurowanie alertu usługa kondycji monitoruje informacyjny powiadomień dotyczących usługi grupy akcji.

Mogą mieć ograniczoną liczbę akcji elementów Webhook w grupy akcji.

#### <a name="secure-webhook"></a>Zabezpieczenia elementu Webhook
**Funkcja bezpiecznego elementu Webhook jest obecnie w wersji zapoznawczej.**

Ta akcja elementu Webhook grup akcji umożliwia korzystanie z zalet usługi Azure Active Directory, aby zabezpieczyć połączenia między grupy akcji i chronionego internetowego interfejsu API (punkt końcowy elementu webhook). Poniżej opisano ogólny przepływ pracy korzystając z zalet tej funkcji. Aby uzyskać omówienie aplikacje usługi Azure AD i nazwy główne usług, zobacz [Przegląd platformy (w wersji 2.0) programu Microsoft identity](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Utwórz aplikację usługi Azure AD chronionego internetowego interfejsu API. Zobacz https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Skonfiguruj chronione interfejsu API można wywoływać za pomocą aplikacji demona.
    
1. Włącz grup akcji w celu korzystania z aplikacji usługi AD systemu Azure.

    > [!NOTE]
    > Musisz być członkiem [roli administratora usługi Azure AD aplikacji](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) do uruchomienia tego skryptu.
    
    - Zmodyfikuj wywołanie Connect-AzureAD skrypt programu PowerShell Użyj identyfikatora swojej dzierżawy usługi Azure AD.
    - Zmodyfikuj skrypt programu PowerShell zmiennej $myAzureADApplicationObjectId używanej identyfikator obiektu aplikacji usługi AD systemu Azure
    - Uruchom zmodyfikowany skrypt.
    
1. Konfigurowanie akcji elementu Webhook grupy akcji.
    - Skopiuj $myApp.ObjectId wartości ze skryptu, a następnie wprowadź go w polu Identyfikator obiektu aplikacji w definicji działania elementu Webhook.
    
    ![Zabezpieczanie Akcja elementu Webhook](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Zabezpieczanie skrypt programu PowerShell elementu Webhook

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


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [SMS alert zachowanie](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Uzyskaj [zrozumieć schemat elementów webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [łącznik ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Dowiedz się więcej o [szybkości](../../azure-monitor/platform/alerts-rate-limiting.md) alerty.
* Pobierz [Przegląd alertów dziennika aktywności](../../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [Konfigurowanie alertów po każdym opublikowaniu powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
