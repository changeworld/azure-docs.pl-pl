---
title: Zarządzaj kontami Azure Automation Uruchom jako
description: W tym artykule opisano sposób zarządzania kontami Uruchom jako przy użyciu programu PowerShell lub portalu.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: a1761eebe51c5ccb4d30b93ad4122dfc185d216e
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028260"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Zarządzaj kontami Azure Automation Uruchom jako

Konta Uruchom jako w Azure Automation są używane do uwierzytelniania zasobów na platformie Azure za pomocą poleceń cmdlet platformy Azure.

Podczas tworzenia konta Uruchom jako zostaje utworzony nowy użytkownik głównej nazwy usługi w Azure Active Directory i przypisze rolę współautor do tego użytkownika na poziomie subskrypcji. W przypadku elementów Runbook, które używają hybrydowych procesów roboczych elementów Runbook na maszynach wirtualnych platformy Azure, można używać [tożsamości zarządzanych dla zasobów platformy Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) zamiast kont Uruchom jako do uwierzytelniania w zasobach platformy Azure.

Istnieją dwa typy kont Uruchom jako:

* **Konto Uruchom jako platformy Azure** — to konto służy do zarządzania zasobami [modelu wdrażania Menedżer zasobów](../azure-resource-manager/management/deployment-models.md) .
  * Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę Współautor dla konta w bieżącej subskrypcji. To ustawienie możesz zmienić na rolę Właściciel lub inną. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
  * Tworzy zasób certyfikatu usługi Automation o nazwie *AzureRunAsCertificate* na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez aplikację Azure AD.
  * Tworzy zasób połączenia usługi Automation o nazwie *AzureRunAsConnection* na określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, identyfikator subskrypcji i odcisk palca certyfikatu.

* **Klasyczne konto Uruchom jako platformy Azure** — to konto służy do zarządzania zasobami [klasycznego modelu wdrażania](../azure-resource-manager/management/deployment-models.md) .
  * Tworzy certyfikat zarządzania w subskrypcji
  * Tworzy zasób certyfikatu usługi Automation o nazwie *AzureClassicRunAsCertificate* na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.
  * Tworzy zasób połączenia usługi Automation o nazwie *AzureClassicRunAsConnection* na określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, identyfikator subskrypcji i nazwę zasobu certyfikatu.
  * Aby można było utworzyć lub odnowić subskrypcję, musi być współadministratorem

  > [!NOTE]
  > Subskrypcje dostawcy rozwiązań w chmurze platformy Azure (CSP) obsługują tylko model Azure Resource Manager, usługi nieAzure Resource Managerowe nie są dostępne w programie. W przypadku korzystania z subskrypcji programu CSP klasyczne konto Uruchom jako platformy Azure nie zostanie utworzone. Nadal można utworzyć konto Uruchom jako platformy Azure. Aby dowiedzieć się więcej o subskrypcjach dostawcy usług kryptograficznych, zobacz [dostępne usługi w subskrypcjach programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

  > [!NOTE]
  > Nazwa główna usługi dla konta Uruchom jako nie ma uprawnień do odczytania Azure Active Directory domyślnie. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure Active Directory, musisz udzielić tego uprawnienia w jednostce usługi w obszarze **uprawnienia interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Uprawnienia do konfigurowania kont Uruchom jako

W celu utworzenia lub zaktualizowania konta Uruchom jako wymagane są określone uprawnienia. Administrator aplikacji w Azure Active Directory i właściciel subskrypcji może ukończyć wszystkie zadania. W przypadku rozdzielenia obowiązków w poniższej tabeli przedstawiono listę zadań, potrzebne odpowiednie polecenie cmdlet i uprawnienia:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Ustawianie uprawnień|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Rola dewelopera aplikacji<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Domowe Azure Active Directory > rejestracji aplikacji |
|Dodaj poświadczenie do aplikacji.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Domowe Azure Active Directory > rejestracji aplikacji|
|Tworzenie i pobieranie jednostki usługi Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Domowe Azure Active Directory > rejestracji aplikacji|
|Przypisz lub uzyskaj rolę RBAC dla określonego podmiotu zabezpieczeń|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Wymagane są następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Lub:</br></br>Administrator lub właściciel dostępu użytkowników        | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Subskrypcje > domu > \<nazw subskrypcji\>-Access Control (IAM)|
|Utwórz lub Usuń certyfikat usługi Automation|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Współautor w grupie zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia automatyzacji|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Współautor w grupie zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> Użytkownicy niebędący administratorami w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , jeśli opcja **Użytkownicy** dzierżawy usługi Azure AD na stronie **Ustawienia użytkownika** ma wartość **tak**. Jeśli ustawienie rejestracje aplikacji ma wartość **nie**, użytkownik wykonujący tę akcję musi być zdefiniowany w powyższej tabeli.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli **administratora globalnego** subskrypcji, zostanie on dodany jako gość. W takiej sytuacji pojawi się ostrzeżenie `You do not have permissions to create…` na stronie **Dodawanie konta usługi Automation** . Użytkownicy, którzy zostali dodani do roli **administratora globalnego** , mogą zostać usunięci z wystąpienia Active Directory subskrypcji i dodany do nich jako pełny użytkownik w Active Directory. Aby zweryfikować tę sytuację, w okienku **Azure Active Directory** w witrynie Azure Portal wybierz kolejno pozycje **Użytkownicy i grupy** i **Wszyscy użytkownicy**, a po wybraniu określonego użytkownika wybierz pozycję **Profil**. Wartość atrybutu **Typ użytkownika** na liście profilów użytkowników nie powinna być równa **Gość**.

## <a name="permissions-classic"></a>Uprawnienia do konfigurowania klasycznych kont Uruchom jako

Aby skonfigurować lub odnowić klasyczne konta Uruchom jako, musisz mieć rolę **współadministrator** na poziomie subskrypcji. Aby dowiedzieć się więcej na temat uprawnień klasycznych, zobacz [klasycznej subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Tworzenie konta Uruchom jako w portalu

W tej części wykonaj poniższe kroki, aby zaktualizować konto usługi Azure Automation w witrynie Azure Portal. Utwórz osobno konta Uruchom jako i klasyczne konto Uruchom jako. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.

1. Zaloguj się do witryny Azure Portal przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
2. W Azure Portal Wyszukaj i wybierz pozycję **konta usługi Automation**.
3. Na stronie **Konta usługi Automation** wybierz swoje konto usługi Automation z listy kont usługi Automation.
4. W okienku po lewej stronie wybierz pozycję **Konta Uruchom jako** w sekcji **Ustawienia konta**.
5. W zależności od tego, które konto jest wymagane, wybierz pozycję **Konto Uruchom jako platformy Azure** lub **Klasyczne konto Uruchom jako platformy Azure**. Po wybraniu danej pozycji zostanie wyświetlone okienko **Dodawanie konta Uruchom jako platformy Azure** lub **Dodawanie klasycznego konta Uruchom jako platformy Azure**. Po zapoznaniu się z omówieniem kliknij pozycję **Utwórz**, aby kontynuować tworzenie konta Uruchom jako.
6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Wyświetlany jest również baner z informacją o utworzeniu konta. Ten proces może potrwać kilka minut.

## <a name="create-run-as-account-using-powershell"></a>Tworzenie konta Uruchom jako przy użyciu programu PowerShell

## <a name="prerequisites"></a>Wymagania wstępne

Poniższa lista zawiera wymagania dotyczące tworzenia konta Uruchom jako w programie PowerShell:

* System Windows 10 lub Windows Server 2016 z Azure Resource Manager modules i nowszymi. Skrypt programu PowerShell nie obsługuje starszych wersji systemu Windows.
* Program Azure PowerShell 1.0 lub nowszy. Informacje o wersji PowerShell 1.0 można znaleźć w temacie [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Konto usługi Automation, które jest przywoływane jako wartość parametrów *–AutomationAccountName* i *-ApplicationDisplayName*.
* Uprawnienia równoważne z informacjami wymienionymi w [wymaganych uprawnieniach do konfigurowania kont Uruchom jako](#permissions)

Aby uzyskać wartości dla identyfikatora *subskrypcji*, *zasobów*i *AutomationAccountName*, które są wymagane parametry skryptu, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **konta usługi Automation**.
1. Na stronie konta usługi Automation wybierz konto usługi Automation, a następnie w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.
1. Zanotuj wartości **Identyfikator subskrypcji**, **nazwę**i **grupę zasobów** na stronie **Właściwości** .

   ![Strona "właściwości" konta usługi Automation](media/manage-runas-account/automation-account-properties.png)

Ten skrypt programu PowerShell obsługuje następujące konfiguracje:

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government.

>[!NOTE]
> Jeśli wybierzesz dowolną opcję tworzenia klasycznego konta Uruchom jako, po wykonaniu skryptu przekaż certyfikat publiczny (z rozszerzeniem nazwy pliku CER) do magazynu zarządzania dla subskrypcji, w ramach której zostało utworzone konto usługi Automation.

1. Zapisz na komputerze poniższy skrypt. W tym przykładzie zapisz plik pod nazwą *New-RunAsAccount.ps1*.

   Skrypt używa wielu Azure Resource Manager poleceń cmdlet do tworzenia zasobów. Powyższa tabela [uprawnień](#permissions) zawiera polecenia cmdlet i ich uprawnienia.

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
    > **Add-AzureRmAccount** jest teraz aliasem dla polecenia **Connect-AzureRmAccount**. Podczas wyszukiwania elementów biblioteki, jeśli nie widzisz pozycji **Connect-AzureRMAccount**, możesz użyć polecenie **Add-AzureRMAccount**lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation.

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

* Jeśli zostało utworzone klasyczne konto Uruchom jako z publicznym certyfikatem przedsiębiorstwa (plik CER), użyj tego certyfikatu. Postępuj zgodnie z instrukcjami dotyczącymi [przekazywania certyfikatu interfejsu API zarządzania do Azure Portal](../azure-api-management-certs.md).

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

W pewnym momencie przed wygaśnięciem konta Uruchom jako należy odnowić certyfikat. Jeśli uważasz, że bezpieczeństwo konta Uruchom jako zostało naruszone, możesz je usunąć i utworzyć ponownie. W tej sekcji opisano kroki umożliwiające wykonanie tych operacji.

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa rok od daty utworzenia. Można go odnowić w dowolnym momencie przed wygaśnięciem jego ważności. Po odnowieniu bieżący prawidłowy certyfikat jest zachowywany, aby upewnić się, że wszystkie elementy Runbook, które są w kolejce lub aktywnie uruchomione, i które uwierzytelniają się przy użyciu konta Uruchom jako, nie mają negatywnego wpływu. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

> [!NOTE]
> Jeśli konto Uruchom jako usługi Automation skonfigurowano do użycia certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa, w przypadku użycia tej opcji certyfikat przedsiębiorstwa jest zastępowany certyfikatem z podpisem własnym.

Aby odnowić certyfikat, wykonaj następujące czynności:

1. W witrynie Azure Portal otwórz konto usługi Automation.

1. Wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**.

    ![Okienko właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stronie właściwości **Konta Uruchom jako** wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, dla którego chcesz odnowić certyfikat.

1. W okienku **Właściwości** wybranego konta kliknij pozycję **Odnów certyfikat**.

    ![Odnawianie certyfikatu konta Uruchom jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="auto-cert-renewal"></a>Konfigurowanie automatycznego odnawiania certyfikatów przy użyciu elementu Runbook usługi Automation

Aby automatycznie odnowić certyfikaty, można użyć elementu Runbook usługi Automation. Poniższy skrypt w serwisie [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) umożliwia korzystanie z tej funkcji na koncie usługi Automation.

- Skrypt `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` tworzy tygodniowy harmonogram odnawiania certyfikatów konta Uruchom jako.
- Skrypt dodaje element Runbook **Update-AutomationRunAsCredential** do konta usługi Automation.
  - Kod elementu Runbook można także wyświetlić w witrynie GitHub w skrypcie: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - W razie potrzeby można także użyć kodu programu PowerShell w pliku do ręcznego odnowienia certyfikatów.

Aby natychmiast przetestować proces odnawiania, wykonaj następujące czynności:

1. Edytuj element Runbook **Update-AutomationRunAsCredential** i umieść znak komentarza (`#`) w wierszu 122 przed poleceniem `Exit(1)`, jak pokazano poniżej.

   ```powershell
   #Exit(1)
   ```

2. Opublikuj element Runbook.
3. Uruchom element Runbook.
4. Zweryfikuj pomyślne odnowienie przy użyciu następującego kodu:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Po zakończeniu testu Edytuj element Runbook, a następnie Usuń znak komentarza dodany w **kroku 1**.
6. **Opublikuj** element Runbook.

> [!NOTE]
> Aby wykonać skrypt, musisz być **administratorem globalnym** lub **administratorem firmy** w Azure Active Directory.

## <a name="limiting-run-as-account-permissions"></a>Ograniczanie uprawnień konta Uruchom jako

Aby kontrolować cel automatyzacji względem zasobów na platformie Azure, można uruchomić skrypt [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) w galerii programu PowerShell w celu zmiany istniejącej nazwy głównej usługi konta Uruchom jako w celu utworzenia i użycia niestandardowej definicji roli. Ta rola będzie miała uprawnienia do wszystkich zasobów, z wyjątkiem [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> Po uruchomieniu skryptu `Update-AutomationRunAsAccountRoleAssignments.ps1` elementy Runbook, które uzyskują dostęp do magazynu kluczy za pomocą kont Uruchom jako, nie będą już działać. Elementy Runbook na koncie należy przeglądać na potrzeby wywołań usługi Azure — Magazyn kluczy.
>
> Aby umożliwić dostęp do magazynu kluczy z Azure Automation elementów Runbook, należy [dodać konto Uruchom jako do uprawnień magazynu](#add-permissions-to-key-vault)kluczy.

Jeśli konieczne jest ograniczenie działania jednostki usługi RunAs, można dodać inne typy zasobów do `NotActions` definicji roli niestandardowej. Poniższy przykład ogranicza dostęp do `Microsoft.Compute`. W przypadku dodania tego elementu do **nienaruszonych** definicji roli ta rola nie będzie w stanie uzyskać dostępu do żadnego zasobu obliczeniowego. Aby dowiedzieć się więcej na temat definicji ról, zobacz [Omówienie definicji ról dla zasobów platformy Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Aby określić, czy nazwa główna usługi używana przez konto Uruchom jako znajduje się w **współautor** , czy niestandardowa definicja roli przejdź do konta usługi Automation i w obszarze **Ustawienia konta**wybierz pozycję **konta Uruchom jako** , > **konto Uruchom jako platformy Azure**. W obszarze **rola** znajdziesz definicję roli, która jest używana.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Aby określić definicję roli używaną przez konta Uruchom jako usługi Automation dla wielu subskrypcji lub kont usługi Automation, można użyć skryptu [check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) w Galeria programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do Key Vault

Jeśli chcesz zezwolić Azure Automation na zarządzanie Key Vault, a nazwa główna usługi konta Uruchom jako używa niestandardowej definicji roli, musisz podjąć dodatkowe kroki, aby zezwolić na to zachowanie:

* Udziel uprawnień do Key Vault
* Ustawianie zasad dostępu

Możesz użyć skryptu [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) w Galeria programu PowerShell, aby nadać uprawnienia konta Uruchom jako do magazynu kluczy, lub odwiedzić [dostęp do aplikacji, aby](../key-vault/key-vault-group-permissions-for-apps.md) uzyskać więcej szczegółowych informacji na temat ustawień uprawnień dla magazynu.

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

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat jednostek usługi, zobacz [obiekty aplikacji i obiekty główne usługi](../active-directory/develop/app-objects-and-service-principals.md).
* Aby uzyskać więcej informacji na temat certyfikatów i usług Azure, zobacz [Omówienie certyfikatów dla platformy Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
