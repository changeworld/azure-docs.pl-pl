---
title: Rozwiązanie do zarządzania usługi Office 365 na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje o konfiguracji i korzystanie z rozwiązania usługi Office 365 na platformie Azure.  Zawiera szczegółowy opis rekordów usługi Office 365 utworzone w analizy dzienników.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: e93860328ed6a31b7a06cc3420fb50ab4af9a00c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236107"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Rozwiązanie do zarządzania usługi Office 365 na platformie Azure (wersja zapoznawcza)

![Logo usługi Office 365](media/oms-solution-office-365/icon.png)

Rozwiązanie do zarządzania usługi Office 365 służy do monitorowania środowiska usługi Office 365 w analizy dzienników.

- Monitorowanie aktywności użytkowników na swoje konta usługi Office 365, aby analizować wzorce użycia, a także behawioralnej trendów. Na przykład można wyodrębnić scenariusze użycia określonego, takich jak pliki, które są udostępniane poza organizację lub najbardziej popularnych witryn programu SharePoint.
- Monitorować działania administratora, aby śledzić zmiany w konfiguracji lub operacji wysokiego poziomu uprawnień.
- Wykrywanie i zbadaj zachowanie niechciane użytkownika, które można dostosować do własnych potrzeb organizacji.
- Prezentacja inspekcji i zgodności. Na przykład można monitorować operacji uzyskiwania dostępu do pliku na poufne pliki, które ułatwiają wykonywanie procesu inspekcji i zgodności.
- Rozwiązywania problemów operacyjnych za pomocą [dziennika wyszukiwania](../log-analytics/log-analytics-log-search.md) na podstawie danych działania usługi Office 365 w organizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej znajduje się przed to rozwiązanie jest zainstalowana i skonfigurowana.

- Organizacyjnej subskrypcji usługi Office 365.
- Poświadczenia dla konta użytkownika, który jest administratorem globalnym.
- Aby odbierać dane inspekcji, musisz [należy skonfigurować inspekcję](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) w ramach subskrypcji usługi Office 365.  Należy pamiętać, że [inspekcji skrzynki pocztowej](https://technet.microsoft.com/library/dn879651.aspx) skonfigurowano oddzielnie.  Można nadal zainstalować rozwiązania i zbierać innych danych, jeśli inspekcja nie jest skonfigurowany.
 

## <a name="management-packs"></a>Pakiety administracyjne
To rozwiązanie nie instalować żadnych pakietów administracyjnych w [podłączone grupy zarządzania](../log-analytics/log-analytics-om-agents.md).
  
## <a name="install-and-configure"></a>Instalowanie i konfigurowanie
Rozpocznij od dodania [rozwiązanie Office 365 do subskrypcji](/monitoring/monitoring-solutions.md#install-a-management-solution). Po dodaniu, wykonaj kroki konfiguracji w tej sekcji, aby umożliwić dostęp do subskrypcji usługi Office 365.

### <a name="required-information"></a>Wymagane informacje
Przed rozpoczęciem tej procedury należy zebrać następujące informacje.

Z obszaru roboczego analizy dzienników:

- Nazwa obszaru roboczego: obszar roboczy, gdzie będą zbierane dane usługi Office 365.
- Nazwa grupy zasobów: grupy zasobów, która zawiera obszar roboczy.
- Identyfikator subskrypcji platformy Azure: subskrypcji, która zawiera obszar roboczy.

Z subskrypcji usługi Office 365:

- Nazwa użytkownika: Adres E-mail konta administracyjnego.
- Identyfikator dzierżawy: Unikatowy identyfikator subskrypcji usługi Office 365.
- Identyfikator klienta: 16-znakowym ciągiem, który reprezentuje klienta usługi Office 365.
- Klucz tajny klienta: Zaszyfrowanego ciągu niezbędne do uwierzytelniania.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Tworzenie aplikacji usługi Office 365 w usłudze Azure Active Directory
Pierwszym krokiem jest do tworzenia aplikacji w usłudze Azure Active Directory, który rozwiązania do zarządzania będzie używany do dostępu rozwiązania usługi Office 365.

1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com/).
1. Wybierz **usługi Azure Active Directory** , a następnie **rejestracji aplikacji**.
1. Kliknij pozycję **Rejestrowanie nowej aplikacji**.

    ![Dodaj rejestracji aplikacji](media/oms-solution-office-365/add-app-registration.png)
1. Wprowadź aplikacji **nazwa** i **adres URL logowania**.  Nazwa powinna być opisowe.  Użyj _http://localhost_ dla adresu URL i Zachowaj _aplikacji sieci Web / interfejs API_ dla **typ aplikacji**
    
    ![Utwórz aplikację](media/oms-solution-office-365/create-application.png)
1. Kliknij przycisk **Utwórz** i zweryfikować informacje o aplikacji.

    ![Zarejestrowana aplikacja](media/oms-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurowanie aplikacji dla usługi Office 365

1. Kliknij przycisk **ustawienia** otworzyć **ustawienia** menu.
1. Wybierz **właściwości**. Zmień **dzierżawcza Multi** do _tak_.

    ![Ustawienia wielodostępnej](media/oms-solution-office-365/settings-multitenant.png)

1. Wybierz **wymagane uprawnienia** w **ustawienia** menu, a następnie kliknij przycisk **Dodaj**.
1. Kliknij przycisk **wybierz interfejs API** , a następnie **interfejsów API usługi Office 365 Management**. Kliknij przycisk **interfejsów API usługi Office 365 Management**. Kliknij pozycję **Wybierz**.

    ![Wybieranie interfejsu API](media/oms-solution-office-365/select-api.png)

1. W obszarze **wybierz uprawnienia** zaznacz poniżej odpowiednie opcje dla obu **uprawnienia aplikacji** i **delegowane uprawnienia**:
    - Odczytuj informacje o kondycji usług dla organizacji
    - Odczytaj dane aktywności dla Twojej organizacji
    - Odczytuj raporty aktywności dotyczące organizacji

    ![Wybieranie interfejsu API](media/oms-solution-office-365/select-permissions.png)

1. Kliknij przycisk **wybierz** , a następnie **gotowe**.
1. Kliknij przycisk **udzielić uprawnień** , a następnie kliknij przycisk **tak** monit do weryfikacji.

    ![Udzielenie uprawnień](media/oms-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Dodaj klucz dla aplikacji

1. Wybierz **klucze** w **ustawienia** menu.
1. Wpisz w **opis** i **czas trwania** nowego klucza.
1. Kliknij przycisk **zapisać** , a następnie skopiuj **wartość** generowany.

    ![Klucze](media/oms-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Dodaj zgody administratora
Aby włączyć konto administracyjne, które po raz pierwszy, musi dostarczyć zgodę administracyjne dla aplikacji. Można to zrobić za pomocą skryptu programu PowerShell. 

1. Zapisz poniższy skrypt jako *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
        
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    $mmsDomain = 'login.mms.microsoft.com'
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocationShort
    $WorkspaceLocation
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
                             $domain='login.microsoftonline.us';
                             $mmsDomain = 'usbn1.login.oms.microsoft.us'; break} # US Gov Virginia
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $OfficeAppRedirectUrl="https://$($WorkspaceLocationShort).$($mmsDomain)/Office365/Authorize"
        $OfficeAppRedirectUrl
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345&redirect_uri=$($OfficeAppRedirectUrl)"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Uruchom skrypt przy użyciu następującego polecenia.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Przykład:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Zostaną wyświetlone z oknem podobny do przedstawionego poniżej. Kliknij przycisk **zaakceptować**.
    
    ![Zgoda administratora](media/oms-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Subskrypcja do obszaru roboczego analizy dzienników
Ostatnim krokiem jest jej zasubskrybowanie aplikacji do swojego obszaru roboczego analizy dzienników. Można to zrobić również przy użyciu skryptu programu PowerShell.

1. Zapisz poniższy skrypt jako *office365_subscription.ps1*.

    ```
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
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
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
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory'
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

2. Uruchom skrypt przy użyciu następującego polecenia:
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    Przykład:

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troublshooting"></a>Troublshooting

Może zostać wyświetlony następujący błąd przy próbie Utwórz subskrypcję po subskrypcji już istnieje.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Jeśli podano nieprawidłowy parametr wartości może zostać wyświetlony następujący błąd.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Dezinstalacja
Możesz usunąć rozwiązania do zarządzania usługi Office 365 przy użyciu procesu w [usunąć rozwiązania do zarządzania](../monitoring/monitoring-solutions.md#remove-a-management-solution). Nie spowoduje to zatrzymanie zbieranych danych z usługi Office 365 do analizy dzienników jednak. Wykonaj kroki procedury poniżej, aby anulować subskrypcję usługi Office 365 i zatrzymać zbieranie danych.

1. Zapisz poniższy skrypt jako *office365_unsubscribe.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
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

2. Uruchom skrypt przy użyciu następującego polecenia:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Przykład:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Zbieranie danych
### <a name="supported-agents"></a>Obsługiwani agenci
Rozwiązanie Office 365 nie pobierania danych z dowolnej [agentów OMS](../log-analytics/log-analytics-data-sources.md).  Pobiera on dane bezpośrednio z usługi Office 365.

### <a name="collection-frequency"></a>Częstotliwość zbierania
Może upłynąć kilka godzin początkowo zbierane dane. Po jej rozpoczęciu zbierania, wysyła usługi Office 365 [powiadomienia elementu webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) z szczegółowych danych do analizy dzienników zawsze jest tworzony rekord. Ten rekord jest dostępna w analizy dzienników za kilka minut po odbierane.

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązanie Office 365 do obszaru roboczego analizy dzienników **usługi Office 365** kafelka zostaną dodane do pulpitu nawigacyjnego. Ten kafelek zawiera liczbę oraz graficzną reprezentację liczby komputerów w środowisku wraz z informacjami o ich zgodności aktualizacji.<br><br>
![Kafelek podsumowania usługi Office 365](media/oms-solution-office-365/tile.png)  

Polecenie **usługi Office 365** Kafelek, aby otworzyć **usługi Office 365** pulpitu nawigacyjnego.

![Pulpit nawigacyjny usługi Office 365](media/oms-solution-office-365/dashboard.png)  

Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera listę top dziesięciu alertów według liczby spełniających kryteria tej kolumny. dla określonego zakresu i zakres czasu. Można uruchomić wyszukiwania dziennika, która zawiera całą listę, klikając Zobacz wszystkie w dolnej części kolumny lub przez kliknięcie nagłówka kolumny.

| Kolumna | Opis |
|:--|:--|
| Operacje | Zawiera informacje o aktywnych użytkowników z subskrypcji usługi Office 365 wszystkie monitorowane. Ponadto można zobaczyć liczbę działań, które się tak zdarzyć w czasie.
| Exchange | Pokazuje podział działania serwera Exchange, takie jak uprawnienie Add-Mailbox lub Set-Mailbox. |
| Sharepoint | Pokazuje top działania czy użytkownicy wykonywać na dokumentów programu SharePoint. Gdy przechodzenie z tego kafelka strony wyszukiwania zawiera szczegółowe informacje o tych działań, takich jak dokument docelowy i lokalizację tego działania. Na przykład dla zdarzenia dostępu do pliku, będzie mógł wyświetlić dokument, który jest dostępny, jego nazwa skojarzone konto i adres IP. |
| Usługa Azure Active Directory | Zawiera użytkownika najwyższego działań, takich jak zresetować hasło użytkownika i prób logowania. Gdy możesz przejść do szczegółów, można wyświetlić szczegóły tych działań, takich jak stan wyniku. Najczęściej jest to przydatne, jeśli chcesz monitorować podejrzanych działań w usłudze Azure Active Directory. |




## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Wszystkie rekordy w obszarze roboczym analizy dzienników przy użyciu rozwiązania usługi Office 365 mają **typu** z **OfficeActivity**.  **OfficeWorkload** właściwość określa usługi Office 365, która rekord odwołuje się do - programu Exchange, usługi AzureActiveDirectory, SharePoint lub usługi OneDrive.  **RecordType** właściwość określa typ operacji.  Właściwości będą się różnić dla poszczególnych typów operacji i przedstawiono w poniższych tabelach.

### <a name="common-properties"></a>Wspólne właściwości
Następujące właściwości są wspólne dla wszystkich rekordów usługi Office 365.

| Właściwość | Opis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | Adres IP urządzenia, która była używana, gdy działanie zostało zarejestrowane. Adres IP jest wyświetlany w formacie adres IPv4 lub IPv6. |
| OfficeWorkload | Usługa Office 365, odnoszący się do rekordu.<br><br>Usługi AzureActiveDirectory<br>Exchange<br>Sharepoint|
| Operacja | Nazwa użytkownika lub administratora aktywności.  |
| Identyfikatora organizacji | Identyfikator GUID dzierżawy usługi Office 365 w organizacji. Ta wartość zawsze będzie taka sama dla organizacji, niezależnie od tego, usługę Office 365, w którym występuje. |
| recordType | Typ działania wykonywane. |
| ResultStatus | Wskazuje, czy akcja (określony we właściwości operacji) zakończyła się powodzeniem. Możliwe wartości to zakończyło się pomyślnie, PartiallySucceded lub nie powiodło się. Aktywność administratora programu Exchange, wartość jest wartość PRAWDA lub FAŁSZ. |
| UserId | Nazwa UPN (główna nazwa użytkownika) użytkownika, który wykonał akcję, która spowodowała rekord rejestrowany; na przykład my_name@my_domain_name. Należy pamiętać, że rejestruje działania wykonywane przez kont systemowych (np. SHAREPOINT\system lub NTAUTHORITY\SYSTEM) dostępne są również. | 
| UserKey | Alternatywny identyfikator użytkownika określonego we właściwości identyfikatora użytkownika.  Na przykład ta właściwość jest wypełniana passport Unikatowy identyfikator (PUID) zdarzenia wykonywane przez użytkowników w programie SharePoint, usłudze OneDrive dla firm i Exchange. Ta właściwość może także określić tę samą wartość jako wartość właściwości identyfikator użytkownika dla zdarzenia występujące w innych usługach i wydarzeniach wykonywane przez kont systemu|
| UserType | Typ użytkownika, który wykonać operację.<br><br>Jednostka administracyjna<br>Aplikacja<br>DcAdmin<br>Regularne<br>Zarezerwowane<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory base
Następujące właściwości są wspólne dla wszystkich rekordów w usłudze Azure Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi AzureActiveDirectory |
| recordType     | Usługi AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ zdarzenia usługi Azure AD. |
| właściwości rozszerzone | Rozszerzone właściwości zdarzeń usługi Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure logowania konto usługi Active Directory
Te rekordy są tworzone, gdy próbuje zalogować użytkownika usługi Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi AzureActiveDirectory |
| recordType     | AzureActiveDirectoryAccountLogon |
| Aplikacja | Aplikacja, która wyzwala zdarzenia logowania, konta, takie jak pakietu Office 15. |
| Klient | Szczegółowe informacje o kliencie urządzenia, systemu operacyjnego urządzenia i przeglądarki urządzenia, które było używane dla zdarzenia logowania konta. |
| stanu logowania | Ta właściwość jest bezpośrednio z OrgIdLogon.LoginStatus. Mapowania różnych interesujące niepowodzeń logowania może być wykonywane przez alerty algorytmów. |
| UserDomain | Informacje tożsamości dzierżawy (TII). | 


### <a name="azure-active-directory"></a>Usługa Azure Active Directory
Te rekordy są tworzone podczas dodatkami lub zmiany zostały wprowadzone do usługi Azure Active Directory obiektów.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi AzureActiveDirectory |
| recordType     | Usługi AzureActiveDirectory |
| AADTarget | Użytkownik wykonanej akcji (określone przez właściwość operacji). |
| Aktor | Nazwa użytkownika lub nazwy głównej usługi, który wykonał akcję. |
| ActorContextId | Identyfikator GUID aktora należącej do organizacji. |
| ActorIpAddress | Adres IP aktora w formacie adres IPV4 lub IPV6. |
| InterSystemsId | Identyfikator GUID śledzić akcje dotyczące składników w usłudze Office 365. |
| IntraSystemId |   Identyfikator GUID jest generowany przez usługę Azure Active Directory do śledzenia akcji. |
| SupportTicketId | Klient obsługuje identyfikator biletu działań w sytuacjach "act na imieniu of". |
| TargetContextId | Identyfikator GUID wybrany użytkownik należy do organizacji. |


### <a name="data-center-security"></a>Zabezpieczeń w centrum danych
Te rekordy są tworzone na podstawie danych inspekcji zabezpieczeń w centrum danych.  

| Właściwość | Opis |
|:--- |:--- |
| EffectiveOrganization | Nazwa dzierżawy, która była celem podniesienia uprawnień/polecenia cmdlet. |
| ElevationApprovedTime | Znacznik czasu po została zatwierdzona podniesienia uprawnień. |
| ElevationApprover | Nazwa menedżera firmy Microsoft. |
| ElevationDuration | Czas trwania, dla którego była aktywna podniesienia uprawnień. |
| ElevationRequestId |  Unikatowy identyfikator dla żądania podniesienia uprawnień. |
| ElevationRole | Zażądano roli podniesienia uprawnień. |
| ElevationTime | Godzina rozpoczęcia podniesienia uprawnień. |
| Godzina_rozpoczęcia | Godzina rozpoczęcia wykonywania poleceń cmdlet. |


### <a name="exchange-admin"></a>Administrator programu Exchange
Te rekordy są tworzone podczas wprowadzania zmian do konfiguracji programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Określa, czy polecenie cmdlet zostało uruchomione przez użytkowników w organizacji przez personel centrum danych firmy Microsoft lub konta usługi Centrum danych lub administratora delegowanego. Wartość False wskazuje, czy polecenie cmdlet zostało uruchomione przez osobę będącą w Twojej organizacji. Wartość True wskazuje, że polecenie cmdlet zostało uruchomione przez personel centrum danych, konto usługi Centrum danych lub administratora delegowanego. |
| ModifiedObjectResolvedName |  Jest to przyjazny dla użytkownika nazwa obiektu, który został zmodyfikowany przez polecenie cmdlet. To jest rejestrowane tylko wtedy, gdy polecenie cmdlet modyfikuje obiektu. |
| Nazwa_organizacji | Nazwa dzierżawcy. |
| OriginatingServer | Nazwa serwera, z którego wykonano polecenia cmdlet. |
| Parametry | Nazwa i wartość dla wszystkich parametrów, które były używane z poleceniem cmdlet identyfikowany we właściwości operacji. |


### <a name="exchange-mailbox"></a>Skrzynek pocztowych programu Exchange
Te rekordy są tworzone podczas zmiany lub dodatki zostały wprowadzone do skrzynek pocztowych programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| ClientInfoString | Informacje o kliencie poczty e-mail, którego użyto do wykonania tej operacji, takich jak wersja przeglądarki, wersja programu Outlook i informacje o urządzeniach przenośnych. |
| Client_IPAddress | Adres IP urządzenia, który został użyty podczas operacji został zapisany w dzienniku. Adres IP jest wyświetlany w formacie adres IPv4 lub IPv6. |
| ClientMachineName | Nazwa komputera hostującego klienta programu Outlook. |
| ClientProcessName | Klient poczty e-mail, który został użyty do uzyskania dostępu do skrzynki pocztowej. |
| ClientVersion | Wersja klienta poczty e-mail. |
| InternalLogonType | Zarezerwowany do użytku wewnętrznego. |
| Logon_Type | Wskazuje typ użytkownika, który dostęp do skrzynki pocztowej i wykonać operację, której został zapisany w dzienniku. |
| LogonUserDisplayName |    Przyjazna dla użytkownika nazwa użytkownika, który wykonał działanie. |
| LogonUserSid | Identyfikator SID użytkownika, który wykonał działanie. |
| MailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, którego próbował uzyskać dostęp. |
| MailboxOwnerMasterAccountSid | Skrzynka pocztowa właściciela konta głównego identyfikatora SID konta. |
| MailboxOwnerSid | Identyfikator SID właściciela skrzynki pocztowej. |
| MailboxOwnerUPN | Adres e-mail osoby, która jest właścicielem skrzynki pocztowej, którego próbował uzyskać dostęp. |


### <a name="exchange-mailbox-audit"></a>Inspekcji skrzynki pocztowej programu Exchange
Te rekordy są tworzone po utworzeniu wpisu inspekcji skrzynki pocztowej.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| Element | Reprezentuje element, na którym została wykonana operacja | 
| SendAsUserMailboxGuid | Skrzynka pocztowa, do którego próbował uzyskać dostęp do wysyłania wiadomości e-mail jako identyfikator GUID programu Exchange. |
| SendAsUserSmtp | Adresu SMTP użytkownika, który jest Personifikowany. |
| SendonBehalfOfUserMailboxGuid | Skrzynki pocztowej, którego próbował uzyskać dostęp do wysyłania wiadomości e-mail dla identyfikatora GUID programu Exchange. |
| SendOnBehalfOfUserSmtp | Adresu SMTP użytkownika, w imieniu którego są wysyłane wiadomości e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupy inspekcji skrzynki pocztowej programu Exchange
Te rekordy są tworzone podczas zmiany lub dodatki zostały wprowadzone do grupy programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informacje o każdym elemencie w grupie. |
| CrossMailboxOperations | Wskazuje, czy operacja związane z więcej niż jedną skrzynkę pocztową. |
| DestMailboxId | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa skrzynki pocztowej docelowy identyfikator GUID. |
| DestMailboxOwnerMasterAccountSid | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa identyfikator SID dla konta głównego identyfikator SID właściciela skrzynki pocztowej docelowej. |
| DestMailboxOwnerSid | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa identyfikator SID skrzynki pocztowej docelowej. |
| DestMailboxOwnerUPN | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa nazwę UPN właściciela skrzynki pocztowej docelowej. |
| DestFolder | Folder docelowy dla operacji, takich jak przeniesienie. |
| Folder | Folder, w której znajduje się grupa elementów. |
| Foldery |     Informacje o folderach źródła uwzględnionego w operacji; na przykład, jeśli foldery są zaznaczone, a następnie usuwany. |


### <a name="sharepoint-base"></a>Podstawa programu SharePoint
Te właściwości są wspólne dla wszystkich rekordów z programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| Źródła zdarzeń | Określa, czy wystąpiło zdarzenie w programie SharePoint. Możliwe wartości to SharePoint lub ObjectModel. |
| ItemType | Typ obiektu, który został dostępne lub zmodyfikowany. Poniższa tabela ItemType szczegółowe na typach obiektów. |
| MachineDomainInfo | Informacje o operacji synchronizacji urządzenia. Te informacje są dostarczane tylko wtedy, gdy znajduje się on na żądanie. |
| MachineId |   Informacje o operacji synchronizacji urządzenia. Te informacje są dostarczane tylko wtedy, gdy znajduje się on na żądanie. |
| Site_ | Identyfikator GUID lokacji, w którym znajduje się plik lub folder, dostępne dla użytkownika. |
| Source_Name | Obiekt, który wywołał operację inspekcji. Możliwe wartości to SharePoint lub ObjectModel. |
| UserAgent | Informacje dotyczące klienta lub przeglądarki użytkownika. Informacje te są dostarczane przez klienta lub przeglądarki. |


### <a name="sharepoint-schema"></a>Schemat programu SharePoint
Te rekordy są tworzone podczas zmiany konfiguracji zostały wprowadzone w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| CustomEvent | Opcjonalny ciąg dla niestandardowych zdarzeń. |
| Event_Data |  Opcjonalne ładunek dla niestandardowych zdarzeń. |
| ModifiedProperties | Właściwość jest uwzględniony w zdarzenia administrator, takie jak dodanie użytkownika jako członka lokację lub grupy administracyjnej kolekcji witryn. Właściwość zawiera nazwę właściwości, która została zmodyfikowana (na przykład grupy administratora witryny), nowa wartość zmodyfikowanej właściwości (takie użytkownik, który został dodany jako administratora witryny), a wartość poprzedniego zmodyfikowanego obiektu. |


### <a name="sharepoint-file-operations"></a>Operacje na plikach programu SharePoint
Te rekordy są tworzone w odpowiedzi na operacje na plikach w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Rozszerzenie pliku, który jest kopiowany lub przenieść. Ta właściwość jest wyświetlany tylko w przypadku zdarzeń FileCopied i FileMoved. |
| Parametr DestinationFileName | Nazwa pliku, który jest skopiowany lub przeniesiony. Ta właściwość jest wyświetlany tylko w przypadku zdarzeń FileCopied i FileMoved. |
| DestinationRelativeUrl | Adres URL folderu docelowego, w którym skopiować lub przenieść pliku. Kombinacja wartości parametrów SiteURL DestinationRelativeURL i parametr DestinationFileName jest taka sama jak wartość właściwości identyfikator obiektu, który jest pełna nazwa ścieżki do pliku, który został skopiowany. Ta właściwość jest wyświetlany tylko w przypadku zdarzeń FileCopied i FileMoved. |
| SharingType | Typ uprawnienia, które zostały przypisane do użytkownika, który został udostępniony zasób udostępniania. Ten użytkownik jest identyfikowany przez parametr UserSharedWith. |
| Site_Url | Adres URL witryny, w którym znajduje się plik lub folder, dostępne dla użytkownika. |
| SourceFileExtension | Rozszerzenie pliku, którego użytkownik próbował uzyskać dostęp. Ta właściwość jest pusta, jeśli obiekt, który uzyskano jest folderem. |
| SourceFileName |  Nazwa pliku lub folderu dostępne dla użytkownika. |
| SourceRelativeUrl | Adres URL folderu, który zawiera plik dostępne dla użytkownika. Kombinacja wartości parametrów SiteURL, SourceRelativeURL i SourceFileName jest taka sama jak wartość właściwości identyfikator obiektu jest pełna nazwa ścieżki dla pliku dostępne dla użytkownika. |
| UserSharedWith |  Użytkownik, który został udostępniony zasób. |




## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania w dzienniku dotyczące rekordów aktualizacji zbieranych przez to rozwiązanie.

| Zapytanie | Opis |
| --- | --- |
|Liczba wszystkich operacji w ramach subskrypcji usługi Office 365 |OfficeActivity &#124; Podsumowanie funkcji count() przez operację |
|Użycie witryny programu SharePoint|OfficeActivity &#124; gdzie OfficeWorkload = ~ "sharepoint" &#124; Podsumowanie funkcji count() przez SiteUrl | Sortuj według liczby asc|
|Operacje na plikach dostępu przez użytkownika typu|Wyszukaj w OfficeWorkload (OfficeActivity) = ~ "usługi azureactivedirectory" i "Test"|
|Wyszukiwanie z określone słowo kluczowe|Typ = OfficeActivity OfficeWorkload = usługi azureactivedirectory "Test"|
|Monitor działań zewnętrznych na serwerze Exchange|OfficeActivity &#124; gdzie OfficeWorkload = ~ "programu exchange" i ExternalAccess == true|



## <a name="next-steps"></a>Kolejne kroki
* Korzystanie z wyszukiwania w dzienniku usługi [Log Analytics](../log-analytics/log-analytics-log-searches.md) w celu wyświetlania szczegółowych danych aktualizacji.
* [Tworzenie własnych pulpity nawigacyjne](../log-analytics/log-analytics-dashboards.md) Aby wyświetlić ulubione zapytania wyszukiwania usługi Office 365.
* [Tworzenie alertów](../log-analytics/log-analytics-alerts.md) aktywnego powiadamianych ważne działań usługi Office 365.  
