---
title: Tworzenie kont automatyzacji Uruchom jako platformy Azure
description: W tym artykule opisano sposób aktualizowania konta usługi Automation i tworzenia kont Uruchom jako przy użyciu programu PowerShell lub w portalu.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c9180b3f6bf6b151909ab681d0f33bc6b3583ce0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714667"
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Aktualizowanie uwierzytelniania konta usługi Automation przy użyciu kont Uruchom jako 
Istniejące konto usługi Automation można zaktualizować w witrynie Azure Portal lub przy użyciu programu PowerShell, gdy:

* Użytkownik utworzył konto usługi Automation, ale zrezygnował z tworzenia konta Uruchom jako.
* Użytkownik używa już konta usługi Automation do zarządzania zasobami usługi Resource Manager i chce je zaktualizować, aby umożliwiało uwierzytelnianie elementu runbook za pomocą konta Uruchom jako.
* Użytkownik używa już konta usługi Automation do zarządzania zasobami klasycznymi i chce je zaktualizować, aby używać klasycznego konta Uruchom jako, zamiast tworzenia nowego konta i migrowania do niego elementów runbook i zasobów.   

W trakcie procesu na koncie usługi Automation są tworzone opisane poniżej elementy.

**W przypadku kont Uruchom jako:**

* Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę Współautor dla konta w bieżącej subskrypcji. To ustawienie możesz zmienić na rolę Właściciel lub inną. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
* Tworzy zasób certyfikatu usługi Automation o nazwie *AzureRunAsCertificate* na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez aplikację Azure AD.
* Tworzy zasób połączenia usługi Automation o nazwie *AzureRunAsConnection* na określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, identyfikator subskrypcji i odcisk palca certyfikatu.

**W przypadku klasycznych kont Uruchom jako:**

* Tworzy zasób certyfikatu usługi Automation o nazwie *AzureClassicRunAsCertificate* na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.
* Tworzy zasób połączenia usługi Automation o nazwie *AzureClassicRunAsConnection* na określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, identyfikator subskrypcji i nazwę zasobu certyfikatu.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli użytkownik chce [użyć programu PowerShell do utworzenia konta Uruchom jako](#create-run-as-account-using-powershell), ten proces wymaga następujących składników:

* Systemy Windows 10 i Windows Server 2016 z modułami usługi Azure Resource Manager w wersji 3.4.1 lub nowszej. Skrypt programu PowerShell nie obsługuje starszych wersji systemu Windows.
* Program Azure PowerShell 1.0 lub nowszy. Informacje o wersji PowerShell 1.0 można znaleźć w temacie [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Konto usługi Automation, które jest przywoływane jako wartość parametrów *–AutomationAccountName* i *-ApplicationDisplayName*.

Aby uzyskać wartości parametrów *SubscriptionID*, *ResourceGroup* i *AutomationAccountName*, które są wymagane w przypadku skryptu, wykonaj następujące czynności:

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Automation**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz opcję **Konta automatyzacji**.
2. Na stronie konta usługi Automation wybierz konto usługi Automation, a następnie w obszarze **Ustawienia konta** wybierz pozycję **Właściwości**.  
3. Zwróć uwagę na wartości na stronie **Właściwości**.<br><br> ![Blok „Właściwości” konta usługi Automation](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Uprawnienia wymagane do aktualizacji konta usługi Automation
Aby zaktualizować konto usługi Automation, musisz mieć następujące określone uprawnienia i uprawnienia wymagane do wykonania zadań opisanych w tym temacie.   
 
* Twoje konto użytkownika AD musi można dodać do roli uprawnienia równorzędne do roli współautora dla zasobów Microsoft.Automation w sposób opisany w artykule [kontroli dostępu opartej na rolach w automatyzacji Azure](automation-role-based-access-control.md#contributor).  
* Użytkownicy inni niż administratorzy w Twojej dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions), jeśli opcja **Użytkownicy mogą rejestrować aplikacje** dzierżawy usługi Azure AD na stronie **Ustawienia użytkownika** została ustawiona na **Tak**. Jeśli wartością ustawienia rejestracji aplikacji jest **Nie**, użytkownik wykonujący tę akcję musi być administratorem globalnym w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji, przed dodaniem do roli administratora globalnego/współadministratora subskrypcji, dodamy Cię do usługi Active Directory jako gościa. W tej usłudze zobaczysz ostrzeżenie „Nie masz uprawnień do utworzenia...” w bloku **Dodawanie konta usługi Automation**. Użytkownicy, którzy najpierw zostali dodani do roli administratora globalnego/współadministratora, mogą zostać usunięci z wystąpienia usługi Active Directory dla subskrypcji i ponownie dodani, aby zostać pełnymi użytkownikami w usłudze Active Directory. Aby zweryfikować tę sytuację, w okienku **Azure Active Directory** w witrynie Azure Portal wybierz kolejno pozycje **Użytkownicy i grupy** i **Wszyscy użytkownicy**, a po wybraniu określonego użytkownika wybierz pozycję **Profil**. Wartość atrybutu **Typ użytkownika** na liście profilów użytkowników nie powinna być równa **Gość**.

## <a name="create-run-as-account-from-the-portal"></a>Tworzenie konta Uruchom jako w portalu
W tej części wykonaj poniższe kroki, aby zaktualizować konto usługi Azure Automation w witrynie Azure Portal. Utwórz osobno konta Uruchom jako i klasyczne konto Uruchom jako. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.  

1. Zaloguj się do witryny Azure Portal przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Automation**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz opcję **Konta automatyzacji**.
3. Na stronie **Konta usługi Automation** wybierz swoje konto usługi Automation z listy kont usługi Automation.
4. W okienku po lewej stronie wybierz pozycję **Konta Uruchom jako** w sekcji **Ustawienia konta**.  
5. W zależności od tego, które konto jest wymagane, wybierz pozycję **Konto Uruchom jako platformy Azure** lub **Klasyczne konto Uruchom jako platformy Azure**. Po wybraniu danej pozycji zostanie wyświetlone okienko **Dodawanie konta Uruchom jako platformy Azure** lub **Dodawanie klasycznego konta Uruchom jako platformy Azure**. Po zapoznaniu się z omówieniem kliknij pozycję **Utwórz**, aby kontynuować tworzenie konta Uruchom jako.  
6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Wyświetlany jest również baner z informacją o utworzeniu konta. Ten proces może potrwać kilka minut.  

## <a name="create-run-as-account-using-powershell-script"></a>Tworzenie konta Uruchom jako przy użyciu skryptu programu PowerShell
Ten skrypt programu PowerShell obsługuje następujące konfiguracje:

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government.

>[!NOTE]
> Jeśli wybierzesz dowolną opcję tworzenia klasycznego konta Uruchom jako, po wykonaniu skryptu przekaż certyfikat publiczny (z rozszerzeniem nazwy pliku CER) do magazynu zarządzania dla subskrypcji, w ramach której zostało utworzone konto usługi Automation.
> 

1. Zapisz na komputerze poniższy skrypt. W tym przykładzie zapisz plik pod nazwą *New-RunAsAccount.ps1*.

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
        [String] $EnterpriseCertPathForRunAsAccount,
        
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
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
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

2. Na komputerze uruchom program **Windows PowerShell** z ekranu **Start** z podwyższonym poziomem uprawnień użytkownika.
3. Z powłoki wiersza polecenia z podwyższonym poziomem uprawnień użytkownika przejdź do folderu zawierającego skrypt utworzony w kroku 1.  
4. Wykonaj skrypt, używając wartości parametrów wymaganej konfiguracji.

    **Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu przedsiębiorstwa**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Po wykonaniu skryptu pojawi się monit o uwierzytelnienie na platformie Azure. Zaloguj się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
    >
    >

Po pomyślnym wykonaniu skryptu pamiętaj o następujących kwestiach:
* Jeśli zostało utworzone klasyczne konto Uruchom jako z certyfikatem publicznym z podpisem własnym (plik CER), skrypt tworzy i zapisuje je w folderze plików tymczasowych na komputerze w ramach profilu użytkownika *%USERPROFILE%\AppData\Local\Temp* użytego do uruchomienia sesji programu PowerShell.
* Jeśli zostało utworzone klasyczne konto Uruchom jako z publicznym certyfikatem przedsiębiorstwa (plik CER), użyj tego certyfikatu. Postępuj zgodnie z instrukcjami dotyczącymi [przekazywanie certyfikatu zarządzania interfejsu API w portalu Azure](../azure-api-management-certs.md), a następnie zweryfikować konfigurację poświadczeń przy użyciu zasobów klasycznych wdrożenia przy użyciu [przykładowy kod w celu uwierzytelnienia z zasobów platformy Azure wdrożenia klasycznego](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Jeśli klasyczne konto Uruchom jako *nie* zostało utworzone, uwierzytelnij się za pomocą zasobów usługi Resource Manager i zweryfikuj konfigurację poświadczeń, używając [przykładowego kodu do uwierzytelniania za pośrednictwem zasobów usługi Service Management](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="limiting-run-as-account-permissions"></a>Ograniczanie uprawnień konta — Uruchom jako

Aby kontrolować wartości docelowej automatyzacji względem zasobów usługi Automatyzacja Azure, konta Uruchom jako domyślnie otrzymuje uprawnienia współautora w subskrypcji. Jeśli chcesz ograniczyć, co można zrobić nazwy głównej usługi Uruchom jako, można usunąć konto z roli współautora do subskrypcji i dodaj go jako współautor grupy zasobów, które ma zostać określony.

W portalu Azure wybierz **subskrypcje** i wybierz subskrypcję konta automatyzacji. Wybierz **(IAM) kontroli dostępu** i wyszukaj nazwę główną usługi dla konta automatyzacji (prawdopodobnie \<AutomationAccountName\>identyfikator _unique). Wybierz konto, a następnie kliknij przycisk **Usuń** Aby usunąć go z subskrypcji.

![Współautorzy subskrypcji](media/automation-create-runas-account/automation-account-remove-subscription.png)

Aby dodać nazwy głównej usługi do grupy zasobów, wybierz grupę zasobów na platformie Azure, portalu i wybierz pozycję **(IAM) kontroli dostępu**. Wybierz **Dodaj**, spowoduje to otwarcie **dodać uprawnienia** strony. Aby uzyskać **roli**, wybierz pozycję **współautora**. W **wybierz** tekstu wpisz nazwę główną usługi dla konta Uruchom jako i wybierz go z listy. Kliknij przycisk **Zapisz**, aby zapisać zmiany. W tym dla grup zasobów chcesz nadać automatyzacji Uruchom jako platformy Azure usługa główna dostęp do.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji dotyczących nazwy główne usług, zobacz [obiekty aplikacji i nazwy głównej usługi](../active-directory/active-directory-application-objects.md).
* Aby uzyskać więcej informacji na temat certyfikatów i usług Azure, zobacz [Omówienie certyfikatów dla usług w chmurze Azure](../cloud-services/cloud-services-certs-create.md).
