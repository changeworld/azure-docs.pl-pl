---
title: Zarządzanie kontami Uruchom jako usługi Azure Automation
description: W tym artykule opisano sposób zarządzania konta Uruchom jako przy użyciu programu PowerShell lub w portalu.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3afe27bf71d112b53c31ab696f71d4e1a0cf6b79
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002502"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Zarządzanie kontami Uruchom jako usługi Azure Automation

Konta Uruchom jako w usłudze Azure Automation są używane do zapewnienia uwierzytelniania do zarządzania zasobami na platformie Azure przy użyciu poleceń cmdlet platformy Azure.

Podczas tworzenia konta Uruchom jako tworzy nowego użytkownika nazwy głównej usługi w usłudze Azure Active Directory i przypisuje rolę Współautor do tego użytkownika na poziomie subskrypcji. W przypadku elementów runbook, użyj hybrydowych procesów roboczych Runbook na maszynach wirtualnych platformy Azure, można użyć [zarządzanych tożsamości dla zasobów platformy Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) zamiast konta Uruchom jako do uwierzytelniania do zasobów platformy Azure.

Istnieją dwa typy kont Uruchom jako:

* **Konto platformy Azure Uruchom jako** — to konto jest używane do zarządzania [modelu wdrażania usługi Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) zasobów.
  * Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę Współautor dla konta w bieżącej subskrypcji. To ustawienie możesz zmienić na rolę Właściciel lub inną. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
  * Tworzy zasób certyfikatu usługi Automation o nazwie *AzureRunAsCertificate* na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez aplikację Azure AD.
  * Tworzy zasób połączenia usługi Automation o nazwie *AzureRunAsConnection* na określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, identyfikator subskrypcji i odcisk palca certyfikatu.

* **Usługa Azure klasycznego konta Uruchom jako** — to konto jest używane do zarządzania [klasycznego modelu wdrażania](../azure-resource-manager/resource-manager-deployment-model.md) zasobów.
  * Tworzy certyfikat zarządzania w ramach subskrypcji
  * Tworzy zasób certyfikatu usługi Automation o nazwie *AzureClassicRunAsCertificate* na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.
  * Tworzy zasób połączenia usługi Automation o nazwie *AzureClassicRunAsConnection* na określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, identyfikator subskrypcji i nazwę zasobu certyfikatu.
  * Musi być administrator współpracujący dla subskrypcji do utworzenia lub Odnów
  
  > [!NOTE]
  > Subskrypcje dostawcy rozwiązań w chmurze (Azure CSP) platformy Azure obsługują tylko model usługi Azure Resource Manager, usługi — z usługi Azure Resource Manager nie są dostępne w programie. Korzystając z subskrypcją dostawcy CSP Azure klasycznego konta Uruchom jako nie utworzyć. Azure konta Uruchom jako nadal tworzona. Aby dowiedzieć się więcej na temat subskrypcji dostawcy usług Kryptograficznych, zobacz [usług dostępnych w ramach subskrypcji programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > Jednostka usługi dla konta Uruchom jako nie ma uprawnień do odczytu usługi Azure Active Directory domyślnie. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzać usługą Azure Active directory, należy udzielić tego uprawnienia podmiotu zabezpieczeń w usłudze **uprawnienia do interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Uprawnienia do konfigurowania konta Uruchom jako

Aby utworzyć lub zaktualizować konto Uruchom jako, musi mieć określone uprawnienia i uprawnienia. Administrator globalny usługi Azure Active Directory i właściciel subskrypcji może wykonanie wszystkich zadań. W sytuacji, w którym masz rozdzielenia obowiązków w poniższej tabeli przedstawiono listę zadań, równoważne polecenia cmdlet i wymagane uprawnienia:

|Zadanie|Polecenie cmdlet  |Minimalny poziom uprawnień  |Gdzie można ustawić uprawnienia|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Rola dla deweloperów aplikacji<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Strona główna > Usługa Azure Active Directory > Rejestracje aplikacji |
|Dodawanie poświadczeń do aplikacji.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Administrator aplikacji lub administrator GLOBALNY<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Strona główna > Usługa Azure Active Directory > Rejestracje aplikacji|
|Utwórz i Pobierz jednostkę usługi Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Administrator aplikacji lub administrator GLOBALNY<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Strona główna > Usługa Azure Active Directory > Rejestracje aplikacji|
|Przypisz lub uzyskać rolę RBAC dla określonego podmiotu zabezpieczeń|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Musi mieć następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Lub ODP.:</br></br>Administrator dostępu użytkownika lub właściciela        | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Strona główna > Subskrypcje > \<Nazwa subskrypcji\> — kontrola dostępu (IAM)|
|Tworzenie lub usuwanie certyfikatu usługi Automation|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Współautor dla grupy zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia usługi Automation|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Współautor dla grupy zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> użytkownicy inni niż administratorzy w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) Jeśli dzierżawy usługi Azure AD **użytkownicy mogą rejestrować aplikacje** opcji **ustawienia użytkownika**strona jest ustawiona na **tak**. Jeśli wartością ustawienia rejestracji aplikacji jest równa **nie**, użytkownik wykonujący tę akcję musi być **administratora globalnego** w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem do **administratora globalnego** roli w subskrypcji dodawane jako Gość. W takiej sytuacji pojawi się `You do not have permissions to create…` ostrzeżenia na **Dodawanie konta usługi Automation** strony. Użytkownicy dodani do **administratora globalnego** roli najpierw może być usunięty z wystąpienia usługi Active Directory dla subskrypcji i ponownie dodani, aby zostali pełnymi użytkownikami w usłudze Active Directory. Aby zweryfikować tę sytuację, w okienku **Azure Active Directory** w witrynie Azure Portal wybierz kolejno pozycje **Użytkownicy i grupy** i **Wszyscy użytkownicy**, a po wybraniu określonego użytkownika wybierz pozycję **Profil**. Wartość atrybutu **Typ użytkownika** na liście profilów użytkowników nie powinna być równa **Gość**.

## <a name="permissions-classic"></a>Uprawnienia do konfigurowania konta klasycznego konta Uruchom jako

Aby skonfigurować lub odnawiania konta klasycznego konta Uruchom jako, musisz mieć **kontem administratora współpracującego** roli na poziomie subskrypcji. Aby dowiedzieć się więcej o uprawnieniach klasycznym, zobacz [Administratorzy subskrypcji platformy Azure classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Utwórz konto Uruchom jako w portalu

W tej części wykonaj poniższe kroki, aby zaktualizować konto usługi Azure Automation w witrynie Azure Portal. Utwórz osobno konta Uruchom jako i klasyczne konto Uruchom jako. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.  

1. Zaloguj się do witryny Azure Portal przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Automation**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz opcję **Konta automatyzacji**.
3. Na stronie **Konta usługi Automation** wybierz swoje konto usługi Automation z listy kont usługi Automation.
4. W okienku po lewej stronie wybierz pozycję **Konta Uruchom jako** w sekcji **Ustawienia konta**.  
5. W zależności od tego, które konto jest wymagane, wybierz pozycję **Konto Uruchom jako platformy Azure** lub **Klasyczne konto Uruchom jako platformy Azure**. Po wybraniu danej pozycji zostanie wyświetlone okienko **Dodawanie konta Uruchom jako platformy Azure** lub **Dodawanie klasycznego konta Uruchom jako platformy Azure**. Po zapoznaniu się z omówieniem kliknij pozycję **Utwórz**, aby kontynuować tworzenie konta Uruchom jako.  
6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Wyświetlany jest również baner z informacją o utworzeniu konta. Ten proces może potrwać kilka minut.  

## <a name="create-run-as-account-using-powershell"></a>Tworzenie konta Uruchom jako przy użyciu programu PowerShell

## <a name="prerequisites"></a>Wymagania wstępne

Poniższa lista zawiera wymagania, aby utworzyć konto Uruchom jako w programie PowerShell:

* Windows 10 lub Windows Server 2016 z modułami usługi Azure Resource Manager 3.4.1 i nowszych. Skrypt programu PowerShell nie obsługuje starszych wersji systemu Windows.
* Program Azure PowerShell 1.0 lub nowszy. Informacje o wersji PowerShell 1.0 można znaleźć w temacie [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Konto usługi Automation, które jest przywoływane jako wartość parametrów *–AutomationAccountName* i *-ApplicationDisplayName*.
* Uprawnieniami odpowiadającymi wymieniony w [wymagane uprawnienia, aby skonfigurować konta Uruchom jako](#permissions)

Aby uzyskać wartości *SubscriptionID*, *ResourceGroup*, i *AutomationAccountName*, które są wymagane parametry skryptu, wykonaj następujące czynności:

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Automation**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz opcję **Konta automatyzacji**.
1. Na stronie konta usługi Automation wybierz konto usługi Automation, a następnie w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.  
1. Uwaga **identyfikator subskrypcji**, **nazwa**, i **grupy zasobów** wartości na **właściwości** strony.

   ![Strona "Właściwości" konta usługi Automation](media/manage-runas-account/automation-account-properties.png)

Ten skrypt programu PowerShell obsługuje następujące konfiguracje:

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government.

>[!NOTE]
> Jeśli wybierzesz dowolną opcję tworzenia klasycznego konta Uruchom jako, po wykonaniu skryptu przekaż certyfikat publiczny (z rozszerzeniem nazwy pliku CER) do magazynu zarządzania dla subskrypcji, w ramach której zostało utworzone konto usługi Automation.

1. Zapisz na komputerze poniższy skrypt. W tym przykładzie zapisz plik pod nazwą *New-RunAsAccount.ps1*.

   Skrypt używa wielu poleceń cmdlet usługi Azure Resource Manager do tworzenia zasobów. W poniższej tabeli przedstawiono polecenia cmdlet i ich uprawnienia potrzebne.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, możesz też [Aktualizuj moduły](automation-update-azure-modules.md) w automatyzacji Konto.

1. Na komputerze uruchom program **Windows PowerShell** z ekranu **Start** z podwyższonym poziomem uprawnień użytkownika.
1. Z powłoki wiersza polecenia z podwyższonym poziomem uprawnień użytkownika przejdź do folderu zawierającego skrypt utworzony w kroku 1.  
1. Wykonaj skrypt, używając wartości parametrów wymaganej konfiguracji.

    **Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu przedsiębiorstwa**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Po wykonaniu skryptu pojawi się monit o uwierzytelnienie na platformie Azure. Zaloguj się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

Po pomyślnym wykonaniu skryptu pamiętaj o następujących kwestiach:

* Jeśli zostało utworzone klasyczne konto Uruchom jako z certyfikatem publicznym z podpisem własnym (plik CER), skrypt tworzy i zapisuje je w folderze plików tymczasowych na komputerze w ramach profilu użytkownika *%USERPROFILE%\AppData\Local\Temp* użytego do uruchomienia sesji programu PowerShell.

* Jeśli zostało utworzone klasyczne konto Uruchom jako z publicznym certyfikatem przedsiębiorstwa (plik CER), użyj tego certyfikatu. Postępuj zgodnie z instrukcjami dotyczącymi [przekazywania certyfikatu interfejsu API zarządzania do witryny Azure portal](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako

W tej sekcji opisano sposób usuwania i ponownego tworzenia konta Uruchom jako lub klasycznego konta Uruchom jako. Konto usługi Automation jest zachowywane podczas wykonywania tej akcji. Po usunięciu konta Uruchom jako lub klasycznego konta Uruchom jako możesz je ponownie utworzyć w witrynie Azure Portal.

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. Na stronie **Konto usługi Automation** wybierz pozycję **Konta Uruchom jako**.

3. Na stronie właściwości **Konta Uruchom jako** wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, które chcesz usunąć. Następnie w okienku **Właściwości** wybranego konta kliknij pozycję **Usuń**.

   ![Usuwanie konta Uruchom jako](media/manage-runas-account/automation-account-delete-runas.png)

1. W trakcie usuwania konta postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

1. Po usunięciu konta możesz je ponownie utworzyć na stronie właściwości **Konta Uruchom jako**, wybierając opcję tworzenia **Konto Uruchom jako platformy Azure**.

   ![Ponowne tworzenie konta Uruchom jako usługi Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Odnawianie certyfikatu z podpisem własnym

W pewnym momencie przed wygaśnięciem ważności konta Uruchom jako należy odnowić certyfikat. Jeśli uważasz, że bezpieczeństwo konta Uruchom jako zostało naruszone, możesz je usunąć i utworzyć ponownie. W tej sekcji opisano kroki umożliwiające wykonanie tych operacji.

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa rok od daty utworzenia. Można go odnowić w dowolnym momencie przed wygaśnięciem jego ważności. Po odnowieniu bieżący ważny certyfikat zostanie zachowany w celu zapewnienia, że wszelkie elementy runbook, które są umieszczane w kolejce lub aktywnie działające i które uwierzytelniają się przy użyciu konta Uruchom jako nie ma negatywny wpływ na. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

> [!NOTE]
> Jeśli konto Uruchom jako usługi Automation skonfigurowano do użycia certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa, w przypadku użycia tej opcji certyfikat przedsiębiorstwa jest zastępowany certyfikatem z podpisem własnym.

Aby odnowić certyfikat, wykonaj następujące czynności:

1. W witrynie Azure Portal otwórz konto usługi Automation.

1. Wybierz **konta Uruchom jako** w obszarze **ustawienia konta**.

    ![Okienko właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stronie właściwości **Konta Uruchom jako** wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, dla którego chcesz odnowić certyfikat.

1. W okienku **Właściwości** wybranego konta kliknij pozycję **Odnów certyfikat**.

    ![Odnawianie certyfikatu konta Uruchom jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="limiting-run-as-account-permissions"></a>Ograniczenie uprawnień konta — Uruchom jako

Aby kontrolować, przeznaczone dla usługi Automation w odniesieniu do zasobów w usłudze Azure Automation, konto Uruchom jako, domyślnie otrzymuje uprawnienia współautora w subskrypcji. Jeśli musisz ograniczyć, co zrobić, Uruchom jako jednostki usługi, możesz usunąć konto z roli współautora do subskrypcji i dodać go jako współautora do grupy zasobów, które chcesz określić.

W witrynie Azure portal wybierz **subskrypcje** i wybierz subskrypcję, konta usługi Automation. Wybierz **kontrola dostępu (IAM)** , a następnie wybierz **przypisań ról** kartę. Wyszukiwania dla jednostki usługi dla konta usługi Automation (wygląda jak \<AutomationAccountName\>identyfikator _unique). Wybierz konto, a następnie kliknij przycisk **Usuń** usunąć go z subskrypcji.

![Współautorzy subskrypcji](media/manage-runas-account/automation-account-remove-subscription.png)

Aby dodać nazwę główną usługi do grupy zasobów, wybierz grupę zasobów w witrynie Azure portal i wybierz pozycję **kontrola dostępu (IAM)**. Wybierz **Dodaj przypisanie roli**, spowoduje to otwarcie **Dodaj przypisanie roli** strony. Aby uzyskać **roli**, wybierz opcję **Współautor**. W **wybierz** tekstu wpisz nazwę jednostki usługi dla konta Uruchom jako i wybierz ją z listy. Kliknij przycisk **Zapisz**, aby zapisać zmiany. Wykonaj te kroki dla grupy zasobów, którą chcesz nadać usługi Automation Uruchom jako platformy Azure jednostce usługi dostępu do.

## <a name="misconfiguration"></a>Błąd konfiguracji

Niektóre elementy konfiguracji niezbędne do poprawnego działania konta Uruchom jako lub klasycznego konta Uruchom jako zostały usunięte lub nie zostały utworzone prawidłowo podczas początkowej konfiguracji. Te elementy to na przykład:

* Zasób certyfikatu
* Zasób połączenia
* Konto Uruchom jako zostało usunięte z roli współautora
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W poprzednim i innych przypadkach błędnej konfiguracji konto usługi Automation wykrywa te zmiany i wyświetla stan *Niekompletne* w okienku właściwości **Konta Uruchom jako** dla konta.

![Stan Niekompletne dla konfiguracji konta Uruchom jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Po wybraniu konta Uruchom jako w okienku **Właściwości** konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Te problemy związane z kontem Uruchom jako można szybko rozwiązać, usuwając konto i tworząc je ponownie.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat nazw głównych usług, zobacz [obiekty aplikacji i jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md).
* Aby uzyskać więcej informacji na temat certyfikatów i usług platformy Azure, zobacz [Omówienie certyfikatów usług Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).