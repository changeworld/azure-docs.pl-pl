---
title: Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfiguracji i używania rozwiązania pakietu Office 365 na platformie Azure.  Zawiera szczegółowy opis rekordów pakietu Office 365 utworzonych w Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 032d52961b4867cad94d06802adb0a1f3eb00f5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553957"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Rozwiązanie do zarządzania pakietem Office 365 na platformie Azure (wersja zapoznawcza)

![Logo pakietu Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> Zalecaną metodą instalacji i konfiguracji rozwiązania pakietu Office 365 jest włączenie [łącznika pakietu office 365](../../sentinel/connect-office-365.md) na [platformie Azure](../../sentinel/overview.md) — zamiast korzystania z kroków opisanych w tym artykule. Jest to zaktualizowana wersja rozwiązania pakietu Office 365 z ulepszonym interfejsem konfiguracji. Aby nawiązać połączenie z dziennikami usługi Azure AD, możesz użyć [łącznika usługi Azure AD wskaźnikowego](../../sentinel/connect-azure-active-directory.md) Azure lub [skonfigurować ustawienia diagnostyczne usługi Azure AD](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), które udostępniają bogatsze dane dziennika niż dzienniki zarządzania pakietu Office 365. 
>
> Po dołączeniu [wskaźnikowego platformy Azure](../../sentinel/quickstart-onboard.md)wskaż obszar roboczy log Analytics, w którym ma zostać zainstalowane rozwiązanie pakietu Office 365. Po włączeniu łącznika rozwiązanie będzie dostępne w obszarze roboczym i używane dokładnie tak samo jak inne zainstalowane rozwiązania do monitorowania.
>
> Użytkownicy chmury platformy Azure dla instytucji rządowych muszą zainstalować pakiet Office 365, wykonując kroki opisane w tym artykule, ponieważ wskaźnik platformy Azure nie jest jeszcze dostępny w chmurze dla instytucji rządowych.

Rozwiązanie do zarządzania pakietem Office 365 umożliwia monitorowanie środowiska pakietu Office 365 w Azure Monitor.

- Monitoruj działania użytkowników na kontach pakietu Office 365, aby analizować wzorce użycia, a także identyfikować trendy behawioralne. Na przykład można wyodrębnić określone scenariusze użycia, takie jak pliki, które są udostępniane poza organizacją lub najpopularniejszymi witrynami programu SharePoint.
- Monitoruj działania administratora, aby śledzić zmiany konfiguracji lub operacje o wysokim poziomie uprawnień.
- Wykrywaj i badaj niepożądane zachowanie użytkowników, które można dostosować do potrzeb organizacji.
- Zademonstrowanie inspekcji i zgodności. Na przykład można monitorować operacje dostępu do plików dla poufnych plików, co może ułatwić proces inspekcji i zgodności.
- Rozwiązywanie problemów operacyjnych przy użyciu [zapytań dzienników](../log-query/log-query-overview.md) w oparciu o dane działania pakietu Office 365 w organizacji.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania są wymagane przed zainstalowaniem i skonfigurowaniem tego rozwiązania.

- Organizacja subskrypcji pakietu Office 365.
- Poświadczenia konta użytkownika, które jest administratorem globalnym.
- Aby otrzymywać dane inspekcji, należy [skonfigurować inspekcję](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) w subskrypcji pakietu Office 365.  Należy pamiętać, że [Inspekcja skrzynek pocztowych](https://technet.microsoft.com/library/dn879651.aspx) jest konfigurowana osobno.  Nadal można zainstalować rozwiązanie i zbierać inne dane, jeśli Inspekcja nie jest skonfigurowana.
 

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie instaluje żadnych pakietów administracyjnych w [podłączonych grupach zarządzania](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Instalowanie i konfigurowanie

Zacznij od dodania [rozwiązania pakietu Office 365 do subskrypcji](solutions.md#install-a-monitoring-solution). Po dodaniu należy wykonać kroki konfiguracji opisane w tej sekcji, aby uzyskać dostęp do subskrypcji pakietu Office 365.

### <a name="required-information"></a>Wymagane informacje

Przed rozpoczęciem tej procedury Zbierz poniższe informacje.

W obszarze roboczym Log Analytics:

- Nazwa obszaru roboczego: obszar roboczy, w którym będą zbierane dane pakietu Office 365.
- Nazwa grupy zasobów: Grupa zasobów, która zawiera obszar roboczy.
- Identyfikator subskrypcji platformy Azure: subskrypcja, która zawiera obszar roboczy.

Z subskrypcji pakietu Office 365:

- Nazwa użytkownika: adres E-mail konta administracyjnego.
- Identyfikator dzierżawy: unikatowy identyfikator subskrypcji pakietu Office 365.
- Identyfikator klienta: 16-znakowy ciąg, który reprezentuje klienta pakietu Office 365.
- Wpis tajny klienta: zaszyfrowany ciąg niezbędny do uwierzytelnienia.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Tworzenie aplikacji pakietu Office 365 w Azure Active Directory

Pierwszym krokiem jest utworzenie aplikacji w Azure Active Directory, która będzie używana przez rozwiązanie do zarządzania w celu uzyskania dostępu do rozwiązania pakietu Office 365.

1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com/).
1. Wybierz **Azure Active Directory** a następnie **rejestracje aplikacji**.
1. Kliknij pozycję **Nowa rejestracja**.

    ![Dodaj rejestrację aplikacji](media/solution-office-365/add-app-registration.png)
1. Wprowadź **nazwę**aplikacji. Wybierz **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny)** dla **obsługiwanych typów kont**.
    
    ![Tworzenie aplikacji](media/solution-office-365/create-application.png)
1. Kliknij pozycję **zarejestruj** i sprawdź poprawność informacji o aplikacji.

    ![Zarejestrowana aplikacja](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurowanie aplikacji dla pakietu Office 365

1. Wybierz pozycję **uwierzytelnianie** i sprawdź, czy w obszarze **obsługiwane typy kont**są wybrane **konta w dowolnym katalogu organizacyjnym (w katalogu usługi Azure AD — wielodostępny)** .

    ![Ustawienia wielodostępne](media/solution-office-365/settings-multitenant.png)

1. Wybierz pozycję **uprawnienia interfejsu API** , a następnie **Dodaj uprawnienie**.
1. Kliknij pozycję **interfejsy API zarządzania pakietu Office 365**. 

    ![Wybieranie interfejsu API](media/solution-office-365/select-api.png)

1. W obszarze **jakiego typu uprawnienia aplikacja jest wymagana?** wybierz następujące opcje **uprawnień aplikacji** i **delegowane uprawnienia**:
   - Odczytaj informacje o kondycji usługi dla Twojej organizacji
   - Odczytaj dane działania organizacji
   - Odczytaj raporty dotyczące działań dla Twojej organizacji

     ![Wybieranie interfejsu API](media/solution-office-365/select-permissions-01.png)![Wybieranie interfejsu API](media/solution-office-365/select-permissions-02.png)

1. Kliknij przycisk **Dodaj uprawnienia**.
1. Kliknij pozycję **Udziel zgody administratora** , a następnie kliknij przycisk **tak** po wyświetleniu monitu o weryfikację.


### <a name="add-a-secret-for-the-application"></a>Dodawanie wpisu tajnego dla aplikacji

1. Wybierz pozycję **certyfikaty & wpisy tajne** , a następnie **nowy klucz tajny klienta**.

    ![Klucze](media/solution-office-365/secret.png)
 
1. Wpisz **Opis** i **czas trwania** nowego klucza.
1. Kliknij przycisk **Dodaj** , a następnie skopiuj wygenerowaną **wartość** .

    ![Klucze](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Dodawanie zgody administratora

Aby włączyć konto administracyjne po raz pierwszy, musisz podać zgodę administracyjną dla aplikacji. Można to zrobić za pomocą skryptu programu PowerShell. 

1. Zapisz następujący skrypt jako *office365_consent. ps1*.

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

2. Uruchom skrypt za pomocą następującego polecenia. Dwa razy zostanie wyświetlony monit o podanie poświadczeń. Podaj najpierw poświadczenia dla obszaru roboczego Log Analytics a następnie poświadczenia administratora globalnego dla dzierżawy pakietu Office 365.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Przykład:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Zostanie wyświetlone okno podobne do poniższego. Kliknij przycisk **Akceptuj**.
    
    ![zgoda administratora](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Subskrybowanie obszaru roboczego Log Analytics

Ostatnim krokiem jest subskrybowanie aplikacji do obszaru roboczego Log Analytics. Można to zrobić również za pomocą skryptu programu PowerShell.

1. Zapisz następujący skrypt jako *office365_subscription. ps1*.

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

Jeśli aplikacja jest już subskrybowana w tym obszarze roboczym lub gdy ta dzierżawa jest subskrybowana w innym obszarze roboczym, może zostać wyświetlony następujący błąd.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Jeśli podano nieprawidłowe wartości parametrów, może zostać wyświetlony następujący błąd.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Dezinstalacja

Rozwiązanie do zarządzania pakietem Office 365 można usunąć, korzystając z procesu w temacie [usuwanie rozwiązania do zarządzania](solutions.md#remove-a-monitoring-solution). Nie spowoduje to zatrzymania zbierania danych z pakietu Office 365 do Azure Monitor. Wykonaj poniższą procedurę, aby anulować subskrypcję pakietu Office 365 i zatrzymać zbieranie danych.

1. Zapisz następujący skrypt jako *office365_unsubscribe. ps1*.

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

Rozwiązanie Office 365 nie pobiera danych z żadnego z [agentów log Analytics](../platform/agent-data-sources.md).  Pobiera dane bezpośrednio z pakietu Office 365.

### <a name="collection-frequency"></a>Częstotliwość zbierania

Zebranie danych może potrwać kilka godzin. Po rozpoczęciu zbierania pakiet Office 365 wysyła [powiadomienie elementu webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) z szczegółowymi danymi do Azure monitor przy każdym utworzeniu rekordu. Ten rekord jest dostępny w Azure Monitor w ciągu kilku minut od momentu odebrania.

## <a name="using-the-solution"></a>Użycie rozwiązania

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Po dodaniu rozwiązania pakietu Office 365 do obszaru roboczego Log Analytics, kafelek **pakietu office 365** zostanie dodany do pulpitu nawigacyjnego. Ten kafelek zawiera liczbę oraz graficzną reprezentację liczby komputerów w środowisku wraz z informacjami o ich zgodności aktualizacji.<br><br>
Kafelek podsumowania ![Office 365 ](media/solution-office-365/tile.png)  

Kliknij kafelek **office 365** , aby otworzyć pulpit nawigacyjny **pakietu Office 365** .

![Pulpit nawigacyjny pakietu Office 365](media/solution-office-365/dashboard.png)  

Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera dziesięć najważniejszych alertów według liczby pasujących do kryteriów tej kolumny dla określonego zakresu i zakresu czasu. Możesz uruchomić wyszukiwanie w dzienniku, które udostępnia całą listę, klikając pozycję Zobacz wszystko u dołu kolumny lub klikając nagłówek kolumny.

| Kolumna | Opis |
|:--|:--|
| Operations | Zawiera informacje o aktywnych użytkownikach ze wszystkich monitorowanych subskrypcji pakietu Office 365. Zobaczysz również liczbę działań, które wystąpiły w czasie.
| Wymiana | Pokazuje podział działań programu Exchange Server, takich jak uprawnienie Dodawanie-Skrzynka pocztowa, lub ustawienie-Skrzynka pocztowa. |
| Sharepoint | Przedstawia najważniejsze działania wykonywane przez użytkowników w dokumentach programu SharePoint. Podczas przechodzenia do szczegółów z tego kafelka na stronie wyszukiwania są wyświetlane szczegółowe informacje o tych działaniach, takie jak dokument docelowy i lokalizacja tego działania. Na przykład dla zdarzenia, do którego jest uzyskiwany dostęp do pliku, będzie można wyświetlić dokument, do którego jest uzyskiwany dostęp, jego nazwę skojarzonego konta i adres IP. |
| Usługa Active Directory systemu Azure | Obejmuje działania najważniejszych użytkowników, takie jak resetowanie hasła użytkownika i logowania. Po przejściu do szczegółów będzie można zobaczyć szczegóły tych działań, jak w przypadku stanu wyniku. Jest to szczególnie przydatne, jeśli chcesz monitorować podejrzane działania na Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Azure Monitor rekordy dziennika

Wszystkie rekordy utworzone w obszarze roboczym Log Analytics w Azure Monitor przez rozwiązanie pakietu Office 365 mają **Typ** **biura**.  Właściwość **OfficeWorkload** określa, do której usługi Office 365 odwołuje się rekord — Exchange, usługi azureactivedirectory, SharePoint lub OneDrive.  Właściwość **recordType** określa typ operacji.  Właściwości różnią się w zależności od typu operacji i są wyświetlane w tabelach poniżej.

### <a name="common-properties"></a>Wspólne właściwości

Następujące właściwości są wspólne dla wszystkich rekordów pakietu Office 365.

| Właściwość | Opis |
|:--- |:--- |
| Typ | *Pakiet Office* |
| clientIP | Adres IP urządzenia, który został użyty podczas rejestrowania działania. Adres IP jest wyświetlany w formacie adresu IPv4 lub IPv6. |
| OfficeWorkload | Usługa Office 365, do której odwołuje się rekord.<br><br>Usługi azureactivedirectory<br>Wymiana<br>Sharepoint|
| Operacja | Nazwa działania użytkownika lub administratora.  |
| Identyfikatorem organizationid | Identyfikator GUID dzierżawy pakietu Office 365 w organizacji. Ta wartość będzie zawsze taka sama dla organizacji, niezależnie od usługi Office 365, w której występuje. |
| RecordType | Typ wykonywanej operacji. |
| ResultStatus | Wskazuje, czy akcja (określona we właściwości Operation) zakończyła się powodzeniem, czy nie. Możliwe wartości to sukces, PartiallySucceeded lub niepowodzenie. W przypadku działania administracyjnego programu Exchange wartością jest true lub false. |
| Nazwa | Nazwa UPN użytkownika, który wykonał akcję, która spowodowała zarejestrowanie rekordu; na przykład my_name@my_domain_name. Należy pamiętać, że rekordy dla działania wykonywanego przez konta systemu (takie jak SHAREPOINT\system lub systemowe NTAUTHORITY\SYSTEM) również są uwzględnione. | 
| userKey | Alternatywny identyfikator użytkownika zidentyfikowany we właściwości UserId.  Na przykład ta właściwość jest wypełniana unikatowym IDENTYFIKATORem (PUID) usługi Passport dla zdarzeń wykonywanych przez użytkowników w usługach SharePoint, OneDrive dla firm i Exchange. Ta właściwość może również określać taką samą wartość jak Właściwość UserID dla zdarzeń występujących w innych usługach i zdarzeń wykonywanych przez konta systemowe|
| userType | Typ użytkownika, który wykonał operację.<br><br>Administratora<br>Aplikacja<br>DcAdmin<br>Ustalon<br>Zarezerwowane<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Baza Azure Active Directory

Następujące właściwości są wspólne dla wszystkich Azure Active Directory rekordów.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi azureactivedirectory |
| RecordType     | Usługi azureactivedirectory |
| AzureActiveDirectory_EventType | Typ zdarzenia usługi Azure AD. |
| Właściwości ExtendedProperties | Właściwości rozszerzone zdarzenia usługi Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory logowanie do konta

Te rekordy są tworzone, gdy użytkownik Active Directory próbuje się zalogować.

| Właściwość | Opis |
|:--- |:--- |
| `OfficeWorkload` | Usługi azureactivedirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Aplikacja, która wyzwala zdarzenie logowania do konta, takie jak Office 15. |
| `Client` | Szczegóły dotyczące urządzenia klienckiego, systemu operacyjnego urządzenia i przeglądarki, która była używana na potrzeby zdarzenia logowania do konta. |
| `LoginStatus` | Ta właściwość jest bezpośrednio z OrgIdLogon. stanu logowania. Mapowanie różnych niepowodzeń logowania może odbywać się przy użyciu algorytmów alertów. |
| `UserDomain` | Informacje o tożsamości dzierżawy (TII). | 


### <a name="azure-active-directory"></a>Usługa Active Directory systemu Azure

Te rekordy są tworzone w przypadku wprowadzenia zmian lub uzupełnień do Azure Active Directory obiektów.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi azureactivedirectory |
| RecordType     | Usługi azureactivedirectory |
| AADTarget | Użytkownik wykonał akcję (identyfikowaną przez właściwość operacji). |
| Zewnętrzny | Nazwa główna użytkownika lub usługi, która wykonał akcję. |
| ActorContextId | Identyfikator GUID organizacji, do której należy aktor. |
| ActorIpAddress | Adres IP aktora w formacie adresu IPV4 lub IPV6. |
| InterSystemsId | Identyfikator GUID, który śledzi akcje między składnikami w ramach usługi Office 365. |
| IntraSystemId |   Identyfikator GUID, który jest generowany przez Azure Active Directory do śledzenia akcji. |
| SupportTicketId | Identyfikator biletu obsługi klienta dla akcji w sytuacjach "czynność-w imieniu". |
| TargetContextId | Identyfikator GUID organizacji, do której należy użytkownik dokierowany. |


### <a name="data-center-security"></a>Zabezpieczenia centrum danych

Te rekordy są tworzone na podstawie danych inspekcji zabezpieczeń centrum danych.  

| Właściwość | Opis |
|:--- |:--- |
| EffectiveOrganization | Nazwa dzierżawy, w której nastąpiło podniesienie uprawnień/polecenie cmdlet. |
| ElevationApprovedTime | Sygnatura czasowa dla momentu zatwierdzenia podniesienia uprawnień. |
| ElevationApprover | Nazwa programu Microsoft Manager. |
| ElevationDuration | Czas, przez który podniesienie uprawnień było aktywne. |
| ElevationRequestId |  Unikatowy identyfikator żądania podniesienia uprawnień. |
| ElevationRole | Rola, dla której zażądano podniesienia uprawnień. |
| ElevationTime | Godzina rozpoczęcia podniesienia uprawnień. |
| Start_Time | Godzina rozpoczęcia wykonywania polecenia cmdlet. |


### <a name="exchange-admin"></a>Administrator programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian w konfiguracji programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Wymiana |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Określa, czy polecenie cmdlet zostało uruchomione przez użytkownika w organizacji, przez personel centrum danych firmy Microsoft lub konto usługi centrum danych, czy przez administratora delegowanego. Wartość false wskazuje, że polecenie cmdlet zostało uruchomione przez kogoś w organizacji. Wartość true wskazuje, że polecenie cmdlet zostało uruchomione przez personel centrum danych, konto usługi centrum danych lub administratora delegowanego. |
| ModifiedObjectResolvedName |  Jest to przyjazna dla użytkownika nazwa obiektu, który został zmodyfikowany przez polecenie cmdlet. Jest to rejestrowane tylko wtedy, gdy polecenie cmdlet modyfikuje obiekt. |
| OrganizationName | Nazwa dzierżawy. |
| OriginatingServer | Nazwa serwera, z którego zostało wykonane polecenie cmdlet. |
| Parametry | Nazwa i wartość dla wszystkich parametrów, które były używane z poleceniem cmdlet, które zostały zidentyfikowane we właściwości Operations. |


### <a name="exchange-mailbox"></a>Skrzynka pocztowa programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian lub uzupełnień do skrzynek pocztowych programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Wymiana |
| RecordType     | ExchangeItem |
| ClientInfoString | Informacje dotyczące klienta poczty e-mail użytego do wykonania tej operacji, takie jak wersja przeglądarki, wersja programu Outlook i informacje o urządzeniu przenośnym. |
| Client_IPAddress | Adres IP urządzenia, który został użyty podczas rejestrowania operacji. Adres IP jest wyświetlany w formacie adresu IPv4 lub IPv6. |
| ClientMachineName | Nazwa komputera, który hostuje klienta programu Outlook. |
| ClientProcessName | Klient poczty e-mail, który był używany do uzyskiwania dostępu do skrzynki pocztowej. |
| ClientVersion | Wersja klienta poczty e-mail. |
| InternalLogonType | Zarezerwowane do użytku wewnętrznego. |
| Logon_Type | Wskazuje typ użytkownika, który uzyskał dostęp do skrzynki pocztowej i wykonał operację, która została zarejestrowana. |
| LogonUserDisplayName |    Przyjazna dla użytkownika nazwa użytkownika, który wykonał operację. |
| LogonUserSid | Identyfikator SID użytkownika, który wykonał operację. |
| MailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do której uzyskano dostęp. |
| MailboxOwnerMasterAccountSid | Identyfikator SID konta właściciela skrzynki pocztowej. |
| MailboxOwnerSid | Identyfikator SID właściciela skrzynki pocztowej. |
| MailboxOwnerUPN | Adres e-mail osoby będącej właścicielem skrzynki pocztowej, do której uzyskano dostęp. |


### <a name="exchange-mailbox-audit"></a>Inspekcja skrzynek pocztowych programu Exchange

Te rekordy są tworzone podczas tworzenia wpisu inspekcji skrzynki pocztowej.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Wymiana |
| RecordType     | ExchangeItem |
| Element | Reprezentuje element, na którym wykonano operację | 
| SendAsUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do której uzyskano dostęp do wysyłania wiadomości e-mail. |
| SendAsUserSmtp | Adres SMTP użytkownika, który jest personifikowany. |
| SendonBehalfOfUserMailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, do której uzyskano dostęp do wysyłania poczty w imieniu użytkownika. |
| SendOnBehalfOfUserSmtp | Adres SMTP użytkownika, w którym jest wysyłana wiadomość e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupa inspekcji skrzynek pocztowych programu Exchange

Te rekordy są tworzone po wprowadzeniu zmian lub uzupełnień do grup programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Wymiana |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informacje o każdym elemencie w grupie. |
| CrossMailboxOperations | Wskazuje, czy operacja dotyczyła więcej niż jednej skrzynki pocztowej. |
| DestMailboxId | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa identyfikator GUID docelowej skrzynki pocztowej. |
| DestMailboxOwnerMasterAccountSid | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa identyfikator SID konta głównego właściciela skrzynki pocztowej. |
| DestMailboxOwnerSid | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa identyfikator SID docelowej skrzynki pocztowej. |
| DestMailboxOwnerUPN | Ustawiaj tylko wtedy, gdy parametr CrossMailboxOperations ma wartość true. Określa nazwę UPN właściciela docelowej skrzynki pocztowej. |
| DestFolder | Folder docelowy dla operacji takich jak Przenieś. |
| Folder | Folder, w którym znajduje się grupa elementów. |
| Foldery |     Informacje o folderach źródłowych związanych z operacjami; na przykład jeśli foldery są zaznaczone, a następnie usunięte. |


### <a name="sharepoint-base"></a>Baza programu SharePoint

Te właściwości są wspólne dla wszystkich rekordów programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| EventSource | Wskazuje, że zdarzenie wystąpiło w programie SharePoint. Możliwe wartości to SharePoint lub ObjectModel. |
| ItemType | Typ obiektu, do którego uzyskano dostęp lub zmodyfikowany. Zobacz tabelę ItemType, aby uzyskać szczegółowe informacje na temat typów obiektów. |
| MachineDomainInfo | Informacje o operacjach synchronizacji urządzeń. Te informacje są raportowane tylko wtedy, gdy znajdują się w żądaniu. |
| MachineId |   Informacje o operacjach synchronizacji urządzeń. Te informacje są raportowane tylko wtedy, gdy znajdują się w żądaniu. |
| Lokacji | Identyfikator GUID witryny, w której znajduje się plik lub folder, do którego użytkownik uzyskuje dostęp. |
| Source_Name | Jednostka, która wyzwoliła operację poddane inspekcji. Możliwe wartości to SharePoint lub ObjectModel. |
| UserAgent | Informacje na temat klienta lub przeglądarki użytkownika. Te informacje są udostępniane przez klienta lub przeglądarkę. |


### <a name="sharepoint-schema"></a>Schemat programu SharePoint

Te rekordy są tworzone po wprowadzeniu zmian w konfiguracji programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| CustomEvent | Opcjonalny ciąg dla zdarzeń niestandardowych. |
| Event_Data |  Opcjonalny ładunek dla zdarzeń niestandardowych. |
| ModifiedProperties | Właściwość jest uwzględniana dla zdarzeń administracyjnych, takich jak dodanie użytkownika jako członka lokacji lub grupy administratorów kolekcji witryn. Właściwość zawiera nazwę zmodyfikowanej właściwości (na przykład grupy administratorów lokacji), nową wartość zmodyfikowanej właściwości (takiego użytkownika, który został dodany jako administrator lokacji) i poprzednią wartość zmodyfikowanego obiektu. |


### <a name="sharepoint-file-operations"></a>Operacje na plikach programu SharePoint

Te rekordy są tworzone w odpowiedzi na operacje na plikach w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Rozszerzenie pliku, który jest kopiowany lub przenoszony. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| DestinationFileName | Nazwa pliku, który jest kopiowany lub przenoszony. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| DestinationRelativeUrl | Adres URL folderu docelowego, do którego plik jest kopiowany lub przenoszony. Kombinacja wartości parametrów SiteURL, DestinationRelativeURL i DestinationFileName jest taka sama jak wartość właściwości ObjectID, która jest pełną nazwą ścieżki dla pliku, który został skopiowany. Ta właściwość jest wyświetlana tylko dla zdarzeń FileCopied i FileMoved. |
| Udostępnianietype | Typ uprawnień do udostępniania, które zostały przypisane do użytkownika, z którym udostępniono zasób. Ten użytkownik jest identyfikowany przez parametr UserSharedWith. |
| Site_Url | Adres URL witryny, w której znajduje się plik lub folder, do którego użytkownik uzyskuje dostęp. |
| SourceFileExtension | Rozszerzenie pliku, do którego dostęp jest uzyskiwany przez użytkownika. Ta właściwość jest pusta, jeśli obiekt, do którego uzyskano dostęp, jest folderem. |
| SourceFileName |  Nazwa pliku lub folderu, do którego użytkownik uzyskuje dostęp. |
| SourceRelativeUrl | Adres URL folderu, który zawiera plik, do którego użytkownik uzyskuje dostęp. Kombinacja wartości parametrów SiteURL, SourceRelativeURL i SourceFileName jest taka sama jak wartość właściwości ObjectID, która jest pełną nazwą ścieżki do pliku, do którego uzyskuje dostęp użytkownik. |
| UserSharedWith |  Użytkownik, do którego udostępniono zasób. |




## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania w dzienniku dotyczące rekordów aktualizacji zbieranych przez to rozwiązanie.

| Zapytanie | Opis |
| --- | --- |
|Liczba wszystkich operacji w ramach subskrypcji pakietu Office 365 |Liczba &#124; podsumowań () według operacji () |
|Użycie witryn programu SharePoint|Pakiet Office &#124; , gdzie OfficeWorkload = ~ "SharePoint &#124; " Count () przez SiteUrl \| Sort według liczby ASC|
|Operacje dostępu do plików według typu użytkownika|Wyszukaj w (Office) OfficeWorkload = ~ "usługi azureactivedirectory" i "test"|
|Wyszukiwanie przy użyciu określonego słowa kluczowego|Type = Office OfficeWorkload = usługi azureactivedirectory "test"|
|Monitoruj zewnętrzne działania w programie Exchange|Pakiet Office &#124; , gdzie OfficeWorkload = ~ "Exchange" i ExternalAccess = = true|



## <a name="next-steps"></a>Następne kroki

* Użyj [zapytań dzienników w Azure monitor](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe dane aktualizacji.
* [Utwórz własne pulpity nawigacyjne](../learn/tutorial-logs-dashboards.md) , aby wyświetlić ulubione zapytania wyszukiwania pakietu Office 365.
* [Twórz Alerty](../platform/alerts-overview.md) , aby otrzymywać powiadomienia o ważnych działaniach związanych z pakietem Office 365.  
