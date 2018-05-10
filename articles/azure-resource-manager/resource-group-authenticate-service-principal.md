---
title: Utwórz tożsamość aplikacji usługi Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia aplikacji usługi Azure Active Directory i nazwy głównej usługi i przyznać jej dostęp do zasobów za pomocą kontroli dostępu opartej na rolach przy użyciu programu Azure PowerShell. Widoczny jest sposób uwierzytelniania aplikacji przy użyciu certyfikatu.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 6ab1b2357e88525f4730b5ad550cfcf3acbb906e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Tworzenie nazwy głównej usługi przy użyciu certyfikatu przy użyciu programu Azure PowerShell

Jeśli aplikacji lub skryptu, który ma dostęp do zasobów, można skonfigurować tożsamości aplikacji i uwierzytelniania w aplikacji przy użyciu własne poświadczenia. Ta tożsamość jest określany jako nazwy głównej usługi. Takie podejście umożliwia:

* Przypisanie uprawnień do tożsamości aplikacji, które są inne niż własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone do dokładnie co aplikacja powinna wykonać.
* Użyj certyfikatu do uwierzytelnienia, podczas wykonywania skryptu instalacji nienadzorowanej.

> [!IMPORTANT]
> Zamiast tworzenia nazwy głównej usługi, należy wziąć pod uwagę przy użyciu usługi Azure AD zarządzane tożsamości usługi dla tożsamości użytkownika aplikacji. Azure AD MSI jest w publicznej wersji zapoznawczej usługi Azure Active Directory, które upraszcza tworzenie tożsamości dla kodu. Jeśli kod jest uruchomiony na to usługa, która obsługuje program Azure AD MSI i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie usługi Azure Active Directory, Azure AD MSI jest lepszym rozwiązaniem dla Ciebie. Aby dowiedzieć się więcej na temat usługi Azure AD MSI, łącznie z usług, które obecnie obsługuje, zobacz [zarządzane tożsamość usługi Azure zasobów](../active-directory/managed-service-identity/overview.md).

W tym artykule przedstawiono sposób tworzenia nazwy głównej usługi, który przeprowadza uwierzytelnianie przy użyciu certyfikatu. Aby skonfigurować nazwy głównej usługi z hasłem, zobacz [Tworzenie nazwy głównej usługi platformy Azure przy użyciu programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Musi mieć [najnowszej wersji](/powershell/azure/get-started-azureps) programu PowerShell w tym artykule.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby ukończyć w tym artykule, należy posiadać odpowiednie uprawnienia w usłudze Azure Active Directory i subskrypcji platformy Azure. W szczególności należy utworzyć aplikację w usłudze Azure Active Directory i przypisać nazwę główną usługi do roli.

Najłatwiejszym sposobem sprawdzenia, czy Twoje konto ma odpowiednie uprawnienia, jest skorzystanie z portalu. Zobacz [, czy wymagane uprawnienia](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Tworzenie nazwy głównej usługi o certyfikat z podpisem własnym

Poniższy przykład obejmuje Prosty scenariusz. Używa [AzureRmADServicePrincipal nowy](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) tworzenia nazwy głównej usługi z certyfikatu z podpisem własnym i używa [AzureRmRoleAssignment nowy](/powershell/module/azurerm.resources/new-azurermroleassignment) można przypisać [współautora](../role-based-access-control/built-in-roles.md#contributor)roli do nazwy głównej usługi. Zakres przypisania roli to aktualnie wybranej subskrypcji Azure. Aby wybrać inną subskrypcję, użyj [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Przykład zostanie uśpiony na 20 sekund, pewien czas dla nowej usługi głównej propagację w usłudze Azure Active Directory. Jeśli skrypt nie oczekuje wystarczająco długi, pojawi się o błędzie informujący: "{ID} podmiot zabezpieczeń nie istnieje w katalogu {DIR-ID}." Aby rozwiązać ten problem, zaczekaj chwilę i uruchom **AzureRmRoleAssignment nowy** polecenie ponownie.

Zakres przypisania roli do określonej grupy zasobów można określić za pomocą **ResourceGroupName** parametru. Można określić zakres konkretnego zasobu za pomocą również **ResourceType** i **ResourceName** parametrów. 

Jeśli użytkownik **bez zainstalowanego systemu Windows 10 lub Windows Server 2016**, należy pobrać [generator certyfikatu z podpisem własnym](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) z Microsoft Script Center. Wyodrębnij jego zawartość i zaimportuj polecenia cmdlet, które są potrzebne.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

W skrypcie Zastąp następujące dwa wiersze w celu wygenerowania certyfikatu.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -StoreName My `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Podaj certyfikat przy użyciu zautomatyzowanego skryptu PowerShell

Gdy zalogujesz się jako nazwy głównej usługi, należy podać identyfikator dzierżawcy katalogu dla aplikacji usługi AD. Dzierżawa jest wystąpieniem usługi Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Tworzenie nazwy głównej usługi o certyfikat od urzędu certyfikacji

W poniższym przykładzie użyto certyfikatu wystawionego przez urząd certyfikacji, tworzenie nazwy głównej usługi. Obejmuje przypisania do określonej subskrypcji platformy Azure. Dodaje nazwę główną usługi do [współautora](../role-based-access-control/built-in-roles.md#contributor) roli. Jeśli wystąpi błąd podczas przypisywania roli, ponowi próbę przypisania.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Podaj certyfikat przy użyciu zautomatyzowanego skryptu PowerShell
Gdy zalogujesz się jako nazwy głównej usługi, należy podać identyfikator dzierżawcy katalogu dla aplikacji usługi AD. Dzierżawa jest wystąpieniem usługi Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Identyfikator aplikacji i Identyfikatora dzierżawcy nie liter, więc można go osadzić bezpośrednio w skrypcie. Można pobrać Identyfikatora dzierżawy, należy użyć:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Aby uzyskać identyfikator aplikacji, należy użyć:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Zmiana poświadczeń

Aby zmienić poświadczenia dla aplikacji usługi AD, albo z powodu naruszenia zabezpieczeń lub wygaśnięcia poświadczeń, należy użyć [AzureRmADAppCredential Usuń](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) i [AzureRmADAppCredential nowy](/powershell/module/azurerm.resources/new-azurermadappcredential) polecenia cmdlet.

Aby usunąć wszystkie poświadczenia dla aplikacji, należy użyć:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Aby dodać wartość certyfikatu, Utwórz certyfikat z podpisem własnym, jak pokazano w tym artykule. Następnie należy użyć:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debugowanie

Podczas tworzenia nazwy głównej usługi, mogą wystąpić następujące błędy:

* **"Authentication_Unauthorized"** lub **"subskrypcji nie znaleziono w kontekście".** — Został wyświetlony ten błąd, gdy Twoje konto nie ma [wymagane uprawnienia](#required-permissions) w usłudze Azure Active Directory w celu rejestracji aplikacji. Zwykle zostanie wyświetlony ten błąd, gdy tylko Administrator użytkowników w usłudze Azure Active Directory można zarejestrować aplikacji, a konto użytkownika nie jest administratorem. Skontaktuj się z administratorem, albo przypisanie do roli administratora lub aby użytkownicy mogli zarejestrować aplikacji.

* Twoje konto **"nie ma autoryzacji do wykonania akcji"Microsoft.Authorization/roleAssignments/write"w zakresie"/subscriptions/ {guid}"."**  — Zostanie wyświetlony ten błąd, gdy Twoje konto nie ma wystarczających uprawnień, aby przypisać rolę do tożsamości. Poproś administratora subskrypcji możesz dodać do roli Administrator dostępu użytkowników.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować nazwy głównej usługi z hasłem, zobacz [Tworzenie nazwy głównej usługi platformy Azure przy użyciu programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Aby uzyskać szczegółowe instrukcje dotyczące integrowania aplikacji na platformie Azure do zarządzania zasobami, zobacz [przewodnik dewelopera do autoryzacji przy użyciu interfejsu API Menedżera zasobów Azure](resource-manager-api-authentication.md).
* Aby uzyskać bardziej szczegółowy opis aplikacji i nazwy główne usług, zobacz [obiekty aplikacji i nazwy głównej usługi](../active-directory/active-directory-application-objects.md). 
* Aby uzyskać więcej informacji na temat uwierzytelniania usługi Azure Active Directory, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/active-directory-authentication-scenarios.md).
