---
title: Tworzenie tożsamości aplikacji platformy Azure przy użyciu programu PowerShell | Microsoft Docs
description: Opis tworzenia aplikacji i jednostki usługi Azure Active Directory oraz przyznawania jej dostępu do zasobów za pośrednictwem kontroli dostępu opartej na rolach przy użyciu programu Azure PowerShell. Ten temat zawiera również informacje na temat uwierzytelniania aplikacji przy użyciu certyfikatu.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73033f91e9d20c56fedc6b4faf26dcf312fce1e1
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321111"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Instrukcje: Tworzenie jednostki usługi z certyfikatem przy użyciu programu Azure PowerShell

Jeśli masz aplikację lub skrypt, które potrzebują dostępu do zasobów, możesz skonfigurować tożsamość aplikacji i uwierzytelnić aplikację przy użyciu jej własnych poświadczeń. Ta tożsamość jest określana jako jednostka usługi. Takie podejście umożliwia:

* Przypisywanie uprawnień do tożsamości aplikacji — są to uprawnienia inne niż Twoje. Zazwyczaj te uprawnienia są ograniczone tylko do czynności, które aplikacja musi wykonywać.
* Używanie certyfikatu do uwierzytelnienia podczas wykonywania skryptu nienadzorowanego.

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie zarządzanych tożsamości dla zasobów platformy Azure dla Twojej tożsamości aplikacji. Jeśli kod jest uruchamiany w usłudze, która obsługuje zarządzane tożsamości i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie Azure Active Directory (Azure AD), tożsamości zarządzane są dla Ciebie lepszym rozwiązaniem. Aby dowiedzieć się więcej na temat tożsamości zarządzanych dla zasobów platformy Azure, w tym usług, które są obecnie obsługiwane, zobacz temat [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

W tym artykule przedstawiono sposób tworzenia jednostki usługi uwierzytelnianej przy użyciu certyfikatu. Aby skonfigurować jednostkę przy użyciu hasła, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Aby wykonać czynności z tego artykułu, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby ukończyć ten artykuł, musisz mieć wystarczające uprawnienia zarówno w usłudze Azure AD, jak i w ramach subskrypcji platformy Azure. W tym celu należy mieć możliwość tworzenia aplikacji w usłudze Azure AD i przypisywania jednostki usługi do roli.

Najłatwiejszym sposobem sprawdzenia, czy Twoje konto ma odpowiednie uprawnienia, jest skorzystanie z portalu. Zobacz [Sprawdzanie wymaganego uprawnienia](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Tworzenie jednostki usługi z certyfikatem z podpisem własnym

Poniższy przykład przedstawia prosty scenariusz. Używa polecenie [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) , aby utworzyć jednostkę usługi z certyfikatem z podpisem własnym, a następnie za pomocą właściwości [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) przypisać rolę [współautor](../../role-based-access-control/built-in-roles.md#contributor) do jednostki usługi. Zakres przypisania roli to aktualnie wybrana subskrypcja platformy Azure. Aby wybrać inną subskrypcję, użyj polecenie [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Przykład uśpienia przez 20 sekund, aby umożliwić pewien czas propagacji nowej jednostki usługi w usłudze Azure AD. Jeśli skrypt nie czeka wystarczająco długo, zobaczysz komunikat o błędzie: "Podmiot zabezpieczeń {ID}" nie istnieje w katalogu {DIR-ID} ". Aby rozwiązać ten problem, poczekaj chwilę, a następnie ponownie uruchom polecenie **New-AzRoleAssignment** .

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

Za każdym razem, gdy logujesz się jako jednostka usługi, musisz podać identyfikator dzierżawy katalogu aplikacji usługi AD. Dzierżawca jest wystąpieniem usługi Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Tworzenie jednostki usługi z certyfikatem od urzędu certyfikacji

W poniższym przykładzie użyto certyfikatu wystawionego przez urząd certyfikacji w celu utworzenia jednostki usługi. Zakres przypisania obejmuje określoną subskrypcję platformy Azure. Dodaje on jednostkę usługi do roli [Współautor](../../role-based-access-control/built-in-roles.md#contributor). Jeśli podczas przypisywania roli wystąpi błąd, zostanie ponownie podjęta próba przypisania.

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

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Udostępnianie certyfikatu za pośrednictwem zautomatyzowanego skryptu programu PowerShell
Za każdym razem, gdy logujesz się jako jednostka usługi, musisz podać identyfikator dzierżawy katalogu aplikacji usługi AD. Dzierżawca jest wystąpieniem usługi Azure AD.

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

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

W identyfikatorach aplikacji i dzierżawy nie jest uwzględniana wielkość liter, więc można je osadzać bezpośrednio w skrypcie. Jeśli musisz pobrać identyfikator dzierżawy, użyj polecenia:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Jeśli musisz pobrać identyfikator aplikacji, użyj polecenia:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Zmienianie poświadczeń

Aby zmienić poświadczenia dla aplikacji usługi AD z powodu naruszenia zabezpieczeń lub wygaśnięcia poświadczeń, Użyj poleceń cmdlet [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) i [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) .

Aby usunąć wszystkie poświadczenia aplikacji, należy użyć polecenia:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Aby dodać wartość certyfikatu, należy utworzyć certyfikat z podpisem własnym, jak pokazano w tym artykule. Następnie należy użyć polecenia:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debugowanie

Podczas tworzenia jednostki usługi mogą wystąpić następujące błędy:

* **„Authentication_Unauthorized”** lub **„W kontekście nie znaleziono subskrypcji”** . — Ten błąd jest wyświetlany, gdy konto nie ma [wymaganych uprawnień](#required-permissions) do zarejestrowania aplikacji w usłudze Azure AD. Zazwyczaj ten błąd jest wyświetlany, gdy tylko użytkownicy administracyjni w Azure Active Directory mogą rejestrować aplikacje, a Twoje konto nie jest kontem administratora. Poproś administratora o przypisanie Cię do roli administratora lub o umożliwienie użytkownikom rejestrowania aplikacji.

* Twoje konto **"nie ma autoryzacji do wykonania akcji" Microsoft. Authorization/roleAssignments/Write "w zakresie"/subscriptions/{GUID} "."** — ten błąd jest wyświetlany, gdy konto nie ma wystarczających uprawnień do przypisania roli do Identity. Poproś administratora subskrypcji o dodanie Cię do roli Administrator dostępu użytkowników.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować jednostkę przy użyciu hasła, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Aby uzyskać szczegółowe instrukcje dotyczące integrowania aplikacji na platformie Azure na potrzeby zarządzania zasobami, zobacz [Developer's guide to authorization with the Azure Resource Manager API (Przewodnik dewelopera dotyczący autoryzowania przy użyciu interfejsu API usługi Azure Resource Manager)](../../azure-resource-manager/resource-manager-api-authentication.md).
* Aby uzyskać bardziej szczegółowy opis aplikacji i jednostek usługi, zobacz [Application Objects and Service Principal Objects (Obiekty aplikacji i obiekty jednostki usługi)](app-objects-and-service-principals.md).
* Aby uzyskać więcej informacji na temat uwierzytelniania usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md).
