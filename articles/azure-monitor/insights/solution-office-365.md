---
title: Rozwiązanie do zarządzania usługi Office 365 na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfiguracji i użytkowania rozwiązanie usługi Office 365 na platformie Azure.  Zawiera szczegółowy opis rekordy usługi Office 365 w usłudze Azure Monitor.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: bwren
ms.openlocfilehash: 4c7e1225a8da1e20bc90986d1530b781f7f2c11a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357573"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Rozwiązanie do zarządzania usługi Office 365 na platformie Azure (wersja zapoznawcza)

![Logo usługi Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> Zalecaną metodą, aby zainstalować i skonfigurować rozwiązanie usługi Office 365 jest zapewnienie [łącznik usługi Office 365](../../sentinel/connect-office-365.md) w [Azure przez wartownika](../../sentinel/overview.md) zamiast przy użyciu kroków w tym artykule. Jest to zaktualizowaną wersję rozwiązania usługi Office 365 ze środowiskiem ulepszone konfiguracji. Aby połączyć dzienniki usługi Azure AD, należy użyć [łącznika usługi Azure AD z usługą Azure Sentinel](../../sentinel/connect-azure-active-directory.md), który dostarcza dokładniejsze dane dziennika, niż dzienniki zarządzania usługi Office 365. 
>
> Gdy możesz [dołączanie przez wartownika Azure](../../sentinel/quickstart-onboard.md), określ obszar roboczy usługi Log Analytics, odpowiedniego rozwiązania usługi Office 365 instalowane w. Po włączeniu łącznik rozwiązania będą dostępne w obszarze roboczym i używać dokładnie tak samo jako żadnych innych rozwiązań do monitorowania, zainstalowane.
>
> Użytkownicy chmury platformy Azure dla instytucji rządowych, należy zainstalować usługi Office 365, korzystając z procedury w tym artykule, ponieważ przez wartownika Azure nie jest jeszcze dostępna w chmurze dla instytucji rządowych.

Rozwiązanie do zarządzania usługi Office 365 umożliwia monitorowanie środowiska usługi Office 365 w usłudze Azure Monitor.

- Monitorowanie aktywności użytkowników na konta usługi Office 365, aby analizować wzorce użycia, a także trendów zachowania. Na przykład można wyodrębnić scenariusze użycia określonych, takich jak pliki, które są udostępniane poza swoją organizację lub Najpopularniejsze witryny programu SharePoint.
- Monitorowanie działania wykonywane przez administratora do śledzenia zmian konfiguracji lub operacji na wysokim poziomie uprawnień.
- Wykrywanie i badanie użytkownika niepożądane zachowanie, które można dostosować do potrzeb organizacji.
- Prezentacja inspekcje i zapewniaj zgodność. Na przykład można monitorować operacji na plikach dostęp do poufnych plików, które pomogą Ci z procesem inspekcje i zapewniaj zgodność.
- Rozwiązywać problemy operacyjne przy użyciu [rejestrowania zapytań](../log-query/log-query-overview.md) na podstawie danych aktywności usługi Office 365 w Twojej organizacji.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest spełnienie następujących przed to rozwiązanie jest zainstalowane i skonfigurowane.

- Organizacji subskrypcji usługi Office 365.
- Poświadczenia dla konta użytkownika, który jest administratorem globalnym.
- Aby odbierać dane inspekcji, musisz mieć [skonfigurować inspekcję](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) w ramach subskrypcji usługi Office 365.  Należy pamiętać, że [inspekcję skrzynki pocztowej](https://technet.microsoft.com/library/dn879651.aspx) skonfigurowano oddzielnie.  Można nadal zainstalować rozwiązanie i zbierać inne dane, jeśli nie skonfigurowano inspekcji.
 

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie można zainstalować wszystkie pakiety administracyjne w [podłączone grupy zarządzania](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Instalowanie i konfigurowanie

Rozpocznij od dodania [rozwiązanie usługi Office 365, aby Twoja subskrypcja](solutions.md#install-a-monitoring-solution). Po dodaniu, należy wykonać kroki konfiguracji w tej sekcji, aby umożliwić dostęp do subskrypcji usługi Office 365.

### <a name="required-information"></a>Wymagane informacje

Przed rozpoczęciem tej procedury, Zbierz następujące informacje.

W obszarze roboczym usługi Log Analytics:

- Nazwa obszaru roboczego: Obszar roboczy, gdzie będą zbierane dane usługi Office 365.
- Nazwa grupy zasobów: Grupa zasobów, która zawiera obszar roboczy.
- Identyfikator subskrypcji platformy Azure: Subskrypcja, która zawiera obszar roboczy.

Z subskrypcji usługi Office 365:

- Nazwa użytkownika: Adres e-mail konta administracyjnego.
- Identyfikator dzierżawy: Unikatowy identyfikator dla subskrypcji usługi Office 365.
- Identyfikator klienta: 16-znakowy ciągu, który reprezentuje klienta usługi Office 365.
- Klucz tajny klienta: Zaszyfrowanego ciągu jest niezbędne do uwierzytelniania.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Tworzenie aplikacji usługi Office 365 w usłudze Azure Active Directory

Pierwszym krokiem jest do tworzenia aplikacji w usłudze Azure Active Directory, który rozwiązania do zarządzania będzie używany do dostępu do rozwiązania do usługi Office 365.

1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com/).
1. Wybierz **usługi Azure Active Directory** i następnie **rejestracje aplikacji**.
1. Kliknij pozycję **Rejestrowanie nowej aplikacji**.

    ![Dodawanie rejestracji aplikacji](media/solution-office-365/add-app-registration.png)
1. Wprowadź aplikację **nazwa** i **adres URL logowania**.  Nazwa powinna być opisowe.  Użyj `http://localhost` dla adresu URL i Zachowaj _aplikacji sieci Web / interfejs API_ dla **typ aplikacji**
    
    ![Tworzenie aplikacji](media/solution-office-365/create-application.png)
1. Kliknij przycisk **Utwórz** i zweryfikować informacje o aplikacji.

    ![Zarejestrowana aplikacja](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurowanie aplikacji dla usługi Office 365

1. Kliknij przycisk **ustawienia** otworzyć **ustawienia** menu.
1. Wybierz **właściwości**. Zmiana **wielodostępnych** do _tak_.

    ![Ustawienia wielodostępnej](media/solution-office-365/settings-multitenant.png)

1. Wybierz **wymagane uprawnienia** w **ustawienia** menu, a następnie kliknij przycisk **Dodaj**.
1. Kliknij przycisk **wybierz interfejs API** i następnie **interfejsów API zarządzania usługi Office 365**. Kliknij przycisk **interfejsów API zarządzania usługi Office 365**. Kliknij pozycję **Wybierz**.

    ![Wybieranie interfejsu API](media/solution-office-365/select-api.png)

1. W obszarze **wybierz uprawnienia** wybierz następujące opcje dla obu **uprawnienia aplikacji** i **delegowane uprawnienia**:
   - Przeczytaj informacje o kondycji usług w Twojej organizacji
   - Odczytaj dane aktywności dla Twojej organizacji
   - Odczytuj raporty aktywności dotyczące organizacji

     ![Wybieranie interfejsu API](media/solution-office-365/select-permissions.png)

1. Kliknij przycisk **wybierz** i następnie **gotowe**.
1. Kliknij przycisk **udzielić uprawnień** a następnie kliknij przycisk **tak** po wyświetleniu monitu o weryfikację.

    ![Udzielenie uprawnień](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Dodaj klucz aplikacji

1. Wybierz **klucze** w **ustawienia** menu.
1. Wpisz **opis** i **czas trwania** nowego klucza.
1. Kliknij przycisk **Zapisz** , a następnie skopiuj **wartość** generowany.

    ![Klucze](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Dodaj zgoda administratora

Aby włączyć konta administracyjnego po raz pierwszy, musisz podać zgody administratora dla aplikacji. Można to zrobić za pomocą skryptu programu PowerShell. 

1. Zapisz poniższy skrypt jako *office365_consent.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Uruchom skrypt za pomocą następującego polecenia. Użytkownik jest monitowany dwukrotnie o poświadczenia. Najpierw podaj poświadczenia dla obszaru roboczego usługi Log Analytics, a następnie dzierżawy poświadczenia administratora globalnego usługi Office 365.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Przykład:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Użytkownik zobaczy okno podobny do przedstawionego poniżej. Kliknij przycisk **zaakceptować**.
    
    ![zgoda administratora](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Subskrybowanie do obszaru roboczego usługi Log Analytics

Ostatnim krokiem jest do subskrybowania aplikacji do swojego obszaru roboczego usługi Log Analytics. Możesz to zrobić również za pomocą skryptu programu PowerShell.

1. Zapisz poniższy skrypt jako *office365_subscription.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Uruchom skrypt za pomocą następującego polecenia:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Przykład:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli aplikacja jest już subskrybowana przez ten obszar roboczy lub jeśli ta dzierżawa jest subskrybentem z innym obszarem roboczym, mogą pojawić się następujący błąd.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Może zostać wyświetlony następujący błąd, jeśli podano nieprawidłowe wartości parametrów.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Dezinstalacja

Można usunąć rozwiązania do zarządzania usługi Office 365 przy użyciu procesu w [usunąć to rozwiązanie do zarządzania](solutions.md#remove-a-monitoring-solution). Zbierane dane z usługi Office 365 do usługi Azure Monitor jednak nie powoduje wstrzymania. Postępuj zgodnie z poniższą procedurą, aby anulować subskrypcję usługi Office 365 i zatrzymać zbieranie danych.

1. Zapisz poniższy skrypt jako *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Uruchom skrypt za pomocą następującego polecenia:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Przykład:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

Rozwiązania usługi Office 365 nie pobierać dane z dowolnego z [agentów usługi Log Analytics](../platform/agent-data-sources.md).  Pobiera dane bezpośrednio z usługi Office 365.

### <a name="collection-frequency"></a>Częstotliwość zbierania

Może upłynąć kilka godzin początkowo zbierane dane. Po uruchomieniu zbieranie, usługi Office 365 wysyła [powiadomień elementu webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) szczegółowych danych do usługi Azure Monitor zawsze jest tworzony rekord. Ten rekord jest dostępna w usłudze Azure Monitor w ramach po kilku minutach od jego odebrania.

## <a name="using-the-solution"></a>Użycie rozwiązania

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Po dodaniu rozwiązania usługi Office 365 do obszaru roboczego usługi Log Analytics, **usługi Office 365** zostanie dodany Kafelek do pulpitu nawigacyjnego. Ten kafelek zawiera liczbę oraz graficzną reprezentację liczby komputerów w środowisku wraz z informacjami o ich zgodności aktualizacji.<br><br>
![Kafelek podsumowania usługi Office 365](media/solution-office-365/tile.png)  

Kliknij pozycję **usługi Office 365** Kafelek, aby otworzyć **usługi Office 365** pulpitu nawigacyjnego.

![Pulpit nawigacyjny usługi Office 365](media/solution-office-365/dashboard.png)  

Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera dziesięć najważniejszych alertów według liczby odpowiadającego kryteriom tej kolumny dla określonego zakresu i przedziału czasu. Możesz uruchomić przeszukiwanie dziennika, które zawiera całą listę, klikając przycisk Zobacz wszystko w dolnej części kolumny lub przez kliknięcie nagłówka kolumny.

| Kolumna | Opis |
|:--|:--|
| Operacje | Zawiera informacje dotyczące aktywnych użytkowników z wszystkich monitorowanych subskrypcji usługi Office 365. Można również wyświetlić liczbę działań, które zdarzają się wraz z upływem czasu.
| Exchange | Pokazuje podział działania serwera Exchange, takie jak uprawnienia Dodaj skrzynkę pocztową lub skrzynki pocztowej Ustaw. |
| Sharepoint | Pokazuje najważniejszych działań, że użytkownicy wykonać na dokumentów programu SharePoint. Podczas przechodzenia do szczegółów z tego kafelka na stronie wyszukiwania znajdują się szczegółowe informacje o tych działań, takich jak dokument docelowy i lokalizację tego działania. Na przykład w przypadku zdarzenia dostępu do pliku będzie mógł wyświetlić dokument, który jest dostępny, jego nazwa skojarzonego konta i adres IP. |
| Usługa Azure Active Directory | Obejmuje działania użytkowników najważniejsze, takich jak zresetować hasło użytkownika i prób logowania. Podczas przechodzenia do szczegółów można wyświetlić szczegóły tych działań, takich jak stan wyniku. Najczęściej jest to przydatne, jeśli chcesz monitorować podejrzanych działań w usłudze Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Rekordy dziennika w usłudze Azure Monitor

Wszystkie rekordy utworzone w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor przez rozwiązania usługi Office 365 mają **typu** z **OfficeActivity**.  **OfficeWorkload** właściwość określa, które usługi Office 365, rekord, który odwołuje się do — Exchange, usługi AzureActiveDirectory, SharePoint lub OneDrive.  **RecordType** właściwość określa typ operacji.  Właściwości różnią się dla każdego typu operacji i przedstawiono w poniższych tabelach.

### <a name="common-properties"></a>Wspólne właściwości

Następujące właściwości są wspólne dla wszystkich rekordów w usłudze Office 365.

| Właściwość | Opis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | Adres IP urządzenia, które było używane podczas zarejestrowania działania. Adres IP jest wyświetlany w formacie adresu IPv4 lub IPv6. |
| OfficeWorkload | Usługa Office 365, która rekord, który odwołuje się do.<br><br>AzureActiveDirectory<br>Exchange<br>Sharepoint|
| Operacja | Nazwa działania użytkownika lub administratora.  |
| Identyfikatora organizacji | Identyfikator GUID dzierżawy usługi Office 365 w Twojej organizacji. Tę wartość, zawsze będzie taka sama dla Twojej organizacji, niezależnie od usługi Office 365, w której występuje. |
| RecordType | Typ operacji wykonywanej. |
| ResultStatus | Wskazuje, czy akcja (określona we właściwości Operation) zakończyła się powodzeniem. Możliwe wartości to Succeeded, częściowe powodzenie lub niepowodzenie. Wartość dla działania administracyjnego programu Exchange, ma wartość PRAWDA lub FAŁSZ. |
| UserId | Nazwa UPN (główna nazwa użytkownika) użytkownika, który wykonał operację powodującą zarejestrowanie rekordu na przykład my_name@my_domain_name. Należy pamiętać, że rekordy działań wykonywanych przez konta systemowe (takie jak SHAREPOINT\system lub Zarządzanie NT\System) dostępne są również. | 
| UserKey | Alternatywnego Identyfikatora użytkownika zidentyfikowanego we właściwości identyfikatora użytkownika.  Na przykład właściwość ta jest wypełniana passport Unikatowy identyfikator (PUID) zdarzenia, wykonywane przez użytkowników w programie SharePoint, usłudze OneDrive dla firm i programem Exchange. Ta właściwość może również określić tę samą wartość jako właściwość identyfikatora użytkownika dla zdarzenia zachodzące w innych usługach i zdarzeniach wykonywanych przez konta systemowe|
| UserType | Typ użytkownika, który wykonał operację.<br><br>Jednostka administracyjna<br>Aplikacja<br>DcAdmin<br>Regularne<br>Zarezerwowany<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Podstawowa usługi Azure Active Directory

Następujące właściwości są wspólne dla wszystkich rekordów w usłudze Azure Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ zdarzenia usługi Azure AD. |
| ExtendedProperties | Właściwości rozszerzonych zdarzeń usługi Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logowania konto usługi Active Directory platformy Azure

Te rekordy są tworzone, gdy próbuje zalogować użytkownika usługi Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Aplikacja | Aplikacja, która wyzwala zdarzenia logowania, konta, takie jak pakietu Office 15. |
| Klient | Szczegółowe informacje o kliencie urządzenia, system operacyjny urządzenia i przeglądarki urządzenia, które było używane dla zdarzenia logowania konta. |
| LoginStatus | Ta właściwość jest bezpośrednio z OrgIdLogon.LoginStatus. Mapowania różnych interesujących błędów logowania może zostać wykonana przez alerty algorytmów. |
| UserDomain | Tożsamość informacji o dzierżawie (TII). | 


### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Te rekordy są tworzone po dokonaniu zmiany lub dodatki do obiektów w usłudze Azure Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Użytkownik wykonanej akcji (identyfikowanych na podstawie właściwości Operation). |
| aktora | Użytkownik lub nazwa główna usługi, który wykonał akcję. |
| ActorContextId | Identyfikator GUID aktora należy do organizacji. |
| ActorIpAddress | Adres IP aktora w formacie adresu IPV4 lub IPV6. |
| InterSystemsId | Identyfikator GUID śledzić akcje składnikami w ramach usługi Office 365. |
| IntraSystemId |   Identyfikator GUID, który jest generowany przez usługę Azure Active Directory do śledzenia działania. |
| SupportTicketId | Klient obsługuje identyfikator biletu dla akcji w sytuacjach, "act w imieniu z". |
| TargetContextId | Identyfikator GUID docelowej użytkownik należy do organizacji. |


### <a name="data-center-security"></a>Zabezpieczeń Centrum danych

Te rekordy są tworzone na podstawie danych inspekcji zabezpieczeń w centrum danych.  

| Właściwość | Opis |
|:--- |:--- |
| EffectiveOrganization | Nazwa dzierżawy, która była celem podniesienia uprawnień/polecenia cmdlet. |
| ElevationApprovedTime | Znacznik czasu podczas podniesienia uprawnień zostało zatwierdzone. |
| ElevationApprover | Nazwa menedżera firmy Microsoft. |
| ElevationDuration | Czas trwania, dla którego była aktywna podniesienia uprawnień. |
| ElevationRequestId |  Unikatowy identyfikator dla żądania podniesienia uprawnień. |
| ElevationRole | Zażądano roli podniesienia uprawnień. |
| ElevationTime | Godzina rozpoczęcia podniesienia uprawnień. |
| Godzina_rozpoczęcia | Czas rozpoczęcia wykonywania poleceń cmdlet. |


### <a name="exchange-admin"></a>Administrator programu Exchange

Te rekordy są tworzone, gdy zmiany zostaną wprowadzone do konfiguracji programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Określa, czy polecenie cmdlet zostało uruchomione przez użytkownika w organizacji przez personel centrum danych firmy Microsoft lub konta usługi z centrum danych, lub administrator delegowany. Wartość False wskazuje, że polecenie cmdlet zostało uruchomione przez kogoś w organizacji. Wartość True wskazuje, że polecenie cmdlet zostało uruchomione przez personel centrum danych, konta usługi Centrum danych lub administratora delegowanego. |
| ModifiedObjectResolvedName |  Jest to nazwa przyjazny dla użytkownika, obiektu, który został zmodyfikowany przez polecenie cmdlet. To jest rejestrowany tylko wtedy, gdy polecenie cmdlet modyfikuje obiektu. |
| Nazwa_organizacji | Nazwa dzierżawy. |
| OriginatingServer | Nazwa serwera, z którego wykonano polecenia cmdlet. |
| Parametry | Nazwa i wartość dla wszystkich parametrów, które były używane z poleceniem cmdlet, który jest identyfikowany we właściwości operacji. |


### <a name="exchange-mailbox"></a>Skrzynka pocztowa programu Exchange

Te rekordy są tworzone, gdy zmiany i dodatki, które zostaną wprowadzone do skrzynek pocztowych programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informacje o kliencie poczty e-mail, który został użyty do wykonania tej operacji, takich jak wersja przeglądarki, wersja programu Outlook i informacje o urządzeniach przenośnych. |
| Client_IPAddress | Adres IP urządzenia, które było używane podczas operacji zostało zarejestrowane. Adres IP jest wyświetlany w formacie adresu IPv4 lub IPv6. |
| ClientMachineName | Nazwa komputera, który jest hostem klient programu Outlook. |
| ClientProcessName | Klient poczty e-mail, który został użyty do uzyskania dostępu do skrzynki pocztowej. |
| ClientVersion | Wersja klienta poczty e-mail. |
| InternalLogonType | Zarezerwowane do użytku wewnętrznego. |
| Logon_Type | Wskazuje typ użytkownika, który dostęp do skrzynki pocztowej i wykonać operację, która została zarejestrowana. |
| LogonUserDisplayName |    Przyjazny dla użytkownika nazwa użytkownika, który wykonał operację. |
| LogonUserSid | Identyfikator SID użytkownika, który wykonał operację. |
| MailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, która została otwarta. |
| MailboxOwnerMasterAccountSid | Skrzynka pocztowa właściciel konta głównego identyfikatora SID konta. |
| MailboxOwnerSid | Identyfikator SID właściciela skrzynki pocztowej. |
| MailboxOwnerUPN | Adres e-mail osoby, która jest właścicielem skrzynki pocztowej, która została otwarta. |


### <a name="exchange-mailbox-audit"></a>Inspekcji skrzynki pocztowej programu Exchange

Te rekordy są tworzone, gdy zostanie utworzony wpis inspekcji skrzynki pocztowej.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Element | Reprezentuje element, na którym wykonano operację | 
| SendAsUserMailboxGuid | Skrzynka pocztowa, do którego uzyskano dostęp do wysyłania wiadomości e-mail jako identyfikator GUID programu Exchange. |
| SendAsUserSmtp | Adres SMTP użytkownika, który jest Spersonifikowany. |
| SendonBehalfOfUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, która została otwarta na wysyłanie wiadomości e-mail w imieniu osób. |
| SendOnBehalfOfUserSmtp | Adres SMTP użytkownika, w imieniu którego zostanie wysłana wiadomość e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupa inspekcji skrzynki pocztowej programu Exchange

Te rekordy są tworzone, gdy zmiany i dodatki, które zostaną wprowadzone do grupy programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informacje o każdym elemencie w grupie. |
| CrossMailboxOperations | Wskazuje, jeśli operacja zaangażowany więcej niż jedną skrzynkę pocztową. |
| DestMailboxId | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa skrzynki pocztowej docelowy identyfikator GUID. |
| DestMailboxOwnerMasterAccountSid | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa identyfikator SID dla konta głównego identyfikator SID właściciela docelowej skrzynki pocztowej. |
| DestMailboxOwnerSid | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa identyfikator SID docelowej skrzynki pocztowej. |
| DestMailboxOwnerUPN | Ustaw tylko wtedy, gdy parametr CrossMailboxOperations ma wartość True. Określa nazwę UPN właściciela do docelowej skrzynki pocztowej. |
| DestFolder | Folder docelowy, operacje, takie jak przeniesienie. |
| Folder | Folder, w której znajduje się grupa elementów. |
| Foldery |     Informacji o folderach źródła uwzględnionego w operacji; na przykład, jeśli foldery są zaznaczone, a następnie usuwane. |


### <a name="sharepoint-base"></a>SharePoint Base

Te właściwości są wspólne dla wszystkich rekordów z programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| EventSource | Określa, że wystąpiło zdarzenie w programie SharePoint. Możliwe wartości to SharePoint lub ObjectModel. |
| ItemType | Typ obiektu, który został otwierane ani modyfikowane. Znajdują się w tabeli ItemType, aby uzyskać szczegółowe informacje na temat typów obiektów. |
| MachineDomainInfo | Informacje o operacjach synchronizacji urządzenia. Te informacje są zgłaszane tylko wtedy, gdy jest obecny w żądaniu. |
| MachineId |   Informacje o operacjach synchronizacji urządzenia. Te informacje są zgłaszane tylko wtedy, gdy jest obecny w żądaniu. |
| Site_ | Identyfikator GUID lokacji, w którym znajduje się plik lub folder, dostępne przez użytkownika. |
| Source_Name | Jednostka, która wyzwoliła inspekcji operacji. Możliwe wartości to SharePoint lub ObjectModel. |
| UserAgent | Informacje dotyczące klienta lub przeglądarki przez użytkownika. Te informacje są dostarczane przez klienta lub przeglądarki. |


### <a name="sharepoint-schema"></a>Schemat programu SharePoint

Te rekordy są tworzone podczas zmiany konfiguracji zostały wprowadzone w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| CustomEvent | Opcjonalne parametry dla niestandardowych zdarzeń. |
| Event_Data |  Opcjonalne ładunek dla niestandardowych zdarzeń. |
| ModifiedProperties | Właściwość jest dołączona do zdarzenia administracyjne, takie jak dodawanie użytkownika jako członka lokacji lub grupy Administrator kolekcji witryny. Właściwość zawiera nazwę właściwości, który został zmodyfikowany (na przykład grupa administratorów witryny), nowa wartość właściwości zmodyfikowany (taki użytkownik, który został dodany jako administrator witryny), a wartość poprzedniego zmodyfikowanego obiektu. |


### <a name="sharepoint-file-operations"></a>Operacje na plikach programu SharePoint

Te rekordy są tworzone w odpowiedzi na operacje na plikach w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Rozszerzenie pliku, który jest kopiowane lub przeniesiony. Ta właściwość jest wyświetlany tylko w przypadku FileCopied i FileMoved zdarzeń. |
| DestinationFileName | Nazwa pliku który jest kopiowane lub przeniesiony. Ta właściwość jest wyświetlany tylko w przypadku FileCopied i FileMoved zdarzeń. |
| DestinationRelativeUrl | Adres URL folderu docelowego, gdzie plik jest kopiowane lub przeniesiony. Kombinacja wartości parametrów SiteURL DestinationRelativeURL i parametr DestinationFileName jest taka sama jak wartość właściwości identyfikator obiektu jest pełna nazwa ścieżki dla pliku, który został skopiowany. Ta właściwość jest wyświetlany tylko w przypadku FileCopied i FileMoved zdarzeń. |
| SharingType | Typ uprawnienia, które zostały przypisane do użytkownika, który został udostępniony zasób do udostępniania. Ten użytkownik jest identyfikowany przez parametr UserSharedWith. |
| Site_Url | Adres URL witryny, w którym znajduje się plik lub folder, dostępne przez użytkownika. |
| SourceFileExtension | Rozszerzenie pliku, która została otwarta przez użytkownika. Ta właściwość jest pusta, jeśli obiekt, który uzyskano jest folderem. |
| SourceFileName |  Nazwa pliku lub folderu dostępne przez użytkownika. |
| SourceRelativeUrl | Adres URL folderu, który zawiera plik dostępne przez użytkownika. Kombinacja wartości parametrów SiteURL SourceRelativeURL i SourceFileName jest taka sama jak wartość właściwości identyfikator obiektu jest pełna nazwa ścieżki dla pliku dostępne przez użytkownika. |
| UserSharedWith |  Użytkownik, który został udostępniony zasób. |




## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania w dzienniku dotyczące rekordów aktualizacji zbieranych przez to rozwiązanie.

| Zapytanie | Opis |
| --- | --- |
|Liczba wszystkich operacji w ramach subskrypcji usługi Office 365 |OfficeActivity &#124; Podsumuj count() by operacji |
|Użycie witryny programu SharePoint|OfficeActivity &#124; gdzie OfficeWorkload = ~ "sharepoint" &#124; Podsumuj count() by SiteUrl \| Sortuj według Count asc|
|Operacje dostępu do plików według typu użytkownika|Wyszukaj w OfficeWorkload (OfficeActivity) = ~ "usługi azureactivedirectory" i "Test"|
|Wyszukiwanie przy użyciu określonego słowa kluczowego|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Monitorowanie działań zewnętrznych, na serwerze Exchange|OfficeActivity &#124; gdzie OfficeWorkload = ~ "programu exchange" i ExternalAccess == true|



## <a name="next-steps"></a>Kolejne kroki

* Użyj [rejestrowania zapytań w usłudze Azure Monitor](../log-query/log-query-overview.md) do wyświetlania szczegółowych danych aktualizacji.
* [Tworzenie własnych pulpitów nawigacyjnych](../learn/tutorial-logs-dashboards.md) Aby wyświetlić ulubione zapytania wyszukiwania usługi Office 365.
* [Tworzenie alertów](../platform/alerts-overview.md) do aktywnego powiadomienia o ważnych działań usługi Office 365.  