---
title: Tworzenie tożsamości aplikacji platformy Azure przy użyciu programu PowerShell | Microsoft Docs
description: Opis tworzenia aplikacji i jednostki usługi Azure Active Directory oraz przyznawania jej dostępu do zasobów za pośrednictwem kontroli dostępu opartej na rolach przy użyciu programu Azure PowerShell. Ten temat zawiera również informacje na temat uwierzytelniania aplikacji przy użyciu certyfikatu.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: aec79655dde48a18891776f40c51aa5a5c859ef8
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577268"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Tworzenie jednostki usługi z certyfikatem przy użyciu programu Azure PowerShell

Jeśli masz aplikację lub skrypt, które potrzebują dostępu do zasobów, możesz skonfigurować tożsamość aplikacji i uwierzytelnić aplikację przy użyciu jej własnych poświadczeń. Ta tożsamość jest określana jako jednostka usługi. Takie podejście umożliwia:

* Przypisywanie uprawnień do tożsamości aplikacji — są to uprawnienia inne niż Twoje. Zazwyczaj te uprawnienia są ograniczone tylko do czynności, które aplikacja musi wykonywać.
* Używanie certyfikatu do uwierzytelnienia podczas wykonywania skryptu nienadzorowanego.

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie tożsamości usługi zarządzanej w usłudze Azure AD na potrzeby tożsamości aplikacji. Azure AD MSI to dostępna w publicznej wersji zapoznawczej funkcja usługi Azure Active Directory, która upraszcza tworzenie tożsamości kodu. Jeśli kod jest uruchamiany w usłudze obsługującej funkcję Azure AD MSI i uzyskującej dostęp do zasobów, które obsługują uwierzytelnianie w usłudze Azure Active Directory, funkcja Azure AD MSI jest lepszym rozwiązaniem. Aby dowiedzieć się więcej na temat funkcji Azure AD MSI i zapoznać się z listą usług, które ją obecnie obsługują, zobacz [Tożsamość usługi zarządzanej dla zasobów platformy Azure](../active-directory/managed-service-identity/overview.md).

W tym artykule przedstawiono sposób tworzenia jednostki usługi uwierzytelnianej przy użyciu certyfikatu. Aby skonfigurować jednostkę przy użyciu hasła, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Aby wykonać czynności z tego artykułu, musisz mieć [najnowszą wersję](/powershell/azure/get-started-azureps) programu PowerShell.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby ukończyć czynności z tego artykułu, musisz mieć wystarczające uprawnienia zarówno w usłudze Azure Active Directory, jak i subskrypcji platformy Azure. W szczególności musisz mieć możliwość tworzenia aplikacji w usłudze Azure Active Directory i przypisywania jednostki usługi do roli.

Najłatwiejszym sposobem sprawdzenia, czy Twoje konto ma odpowiednie uprawnienia, jest skorzystanie z portalu. Zobacz [Sprawdzanie wymaganego uprawnienia](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Tworzenie jednostki usługi z certyfikatem z podpisem własnym

Poniższy przykład przedstawia prosty scenariusz. Jest w nim używane polecenie [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) do tworzenia jednostki usługi z certyfikatem z podpisem własnym oraz polecenie [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) do przypisywania roli [współautora](../role-based-access-control/built-in-roles.md#contributor) do jednostki usługi. Zakres przypisania roli to aktualnie wybrana subskrypcja platformy Azure. Aby wybrać inną subskrypcję, użyj polecenia [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

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

Przykład zostanie uśpiony na 20 sekund, aby zezwolić na wypełnienie usługi Azure Active Directory przy użyciu nowej jednostki usługi. Jeśli skrypt nie będzie oczekiwać wystarczająco długo, pojawi się następujący komunikat o błędzie: „Identyfikator {ID} jednostki usługi nie istnieje w katalogu {DIR-ID}”. Aby usunąć ten problem, zaczekaj chwilę i ponownie uruchom polecenie **New-AzureRmRoleAssignment**.

Zakres przypisania roli do określonej grupy zasobów można określić za pomocą parametru **ResourceGroupName**. Zakres konkretnego zasobu można również określić za pomocą parametrów **ResourceType** i **ResourceName**. 

Jeśli nie masz **zainstalowanego systemu Windows 10 lub Windows Server 2016**, musisz pobrać [generator certyfikatu z podpisem własnym](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) z witryny Microsoft Script Center. Wyodrębnij jego zawartość i zaimportuj potrzebne polecenie cmdlet.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

W skrypcie zastąp dwa poniższe wiersze w celu wygenerowania certyfikatu.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Udostępnianie certyfikatu za pośrednictwem zautomatyzowanego skryptu programu PowerShell

Za każdym razem, gdy logujesz się jako jednostka usługi, musisz podać identyfikator dzierżawy katalogu aplikacji usługi AD. Dzierżawa to wystąpienie usługi Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Tworzenie jednostki usługi z certyfikatem od urzędu certyfikacji

W poniższym przykładzie użyto certyfikatu wystawionego przez urząd certyfikacji w celu utworzenia jednostki usługi. Zakres przypisania obejmuje określoną subskrypcję platformy Azure. Dodaje on jednostkę usługi do roli [Współautor](../role-based-access-control/built-in-roles.md#contributor). Jeśli podczas przypisywania roli wystąpi błąd, zostanie ponownie podjęta próba przypisania.

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

### <a name="provide-certificate-through-automated-powershell-script"></a>Udostępnianie certyfikatu za pośrednictwem zautomatyzowanego skryptu programu PowerShell
Za każdym razem, gdy logujesz się jako jednostka usługi, musisz podać identyfikator dzierżawy katalogu aplikacji usługi AD. Dzierżawa to wystąpienie usługi Azure Active Directory.

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

W identyfikatorach aplikacji i dzierżawy nie jest uwzględniana wielkość liter, więc można je osadzać bezpośrednio w skrypcie. Jeśli musisz pobrać identyfikator dzierżawy, użyj polecenia:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Jeśli musisz pobrać identyfikator aplikacji, użyj polecenia:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Zmienianie poświadczeń

Aby zmienić poświadczenia aplikacji usługi AD z powodu naruszenia zabezpieczeń lub wygaśnięcia poświadczeń, należy użyć poleceń cmdlet [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) i [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Aby usunąć wszystkie poświadczenia aplikacji, należy użyć polecenia:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Aby dodać wartość certyfikatu, należy utworzyć certyfikat z podpisem własnym, jak pokazano w tym artykule. Następnie należy użyć polecenia:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debugowanie

Podczas tworzenia jednostki usługi mogą wystąpić następujące błędy:

* **„Authentication_Unauthorized”** lub **„W kontekście nie znaleziono subskrypcji”**. Ten błąd jest wyświetlany, gdy Twoje konto nie ma w usłudze Azure Active Directory [wymaganych uprawnień](#required-permissions) do rejestrowania aplikacji. Przeważnie ten komunikat o błędzie jest wyświetlany, gdy tylko administratorzy w usłudze Azure Active Directory mogą rejestrować aplikacje, a konto użytkownika nie należy do administratora. Poproś administratora o przypisanie Cię do roli administratora lub o umożliwienie użytkownikom rejestrowania aplikacji.

* Twoje konto **„nie ma autoryzacji do wykonania akcji „Microsoft.Authorization/roleAssignments/write” w zakresie „/subscriptions/{guid}””.** Ten komunikat zobaczysz, jeśli Twoje konto nie ma wystarczających uprawnień do przypisywania roli do tożsamości. Poproś administratora subskrypcji o dodanie Cię do roli Administrator dostępu użytkowników.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować jednostkę przy użyciu hasła, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Aby uzyskać szczegółowe instrukcje dotyczące integrowania aplikacji na platformie Azure na potrzeby zarządzania zasobami, zobacz [Developer's guide to authorization with the Azure Resource Manager API (Przewodnik dewelopera dotyczący autoryzowania przy użyciu interfejsu API usługi Azure Resource Manager)](resource-manager-api-authentication.md).
* Aby uzyskać bardziej szczegółowy opis aplikacji i jednostek usługi, zobacz [Application Objects and Service Principal Objects (Obiekty aplikacji i obiekty jednostki usługi)](../active-directory/develop/app-objects-and-service-principals.md). 
* Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Active Directory, zobacz [Authentication Scenarios for Azure AD (Scenariusze uwierzytelniania dla usługi Azure AD)](../active-directory/develop/authentication-scenarios.md).
