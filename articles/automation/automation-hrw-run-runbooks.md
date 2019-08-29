---
title: Uruchamianie elementów Runbook na Azure Automation hybrydowego procesu roboczego elementu Runbook
description: Ten artykuł zawiera informacje na temat uruchamiania elementów Runbook na maszynach w lokalnym centrum danych lub dostawcy chmury z rolą hybrydowego procesu roboczego elementu Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e726b60a08f234ef021b2901f8ed29ec74f0ff47
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087574"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook

Nie ma różnicy w strukturze elementów Runbook, które działają w Azure Automation i elementach Runbook, które działają w hybrydowym procesie roboczym elementu Runbook. Elementy Runbook, które są używane z każdą najbardziej prawdopodobną różnicą. Ta różnica wynika z faktu, że elementy Runbook przeznaczone dla hybrydowego procesu roboczego elementu Runbook zwykle zarządzają zasobami na komputerze lokalnym lub z zasobami w środowisku lokalnym, w których zostały wdrożone. Elementy Runbook w Azure Automation zazwyczaj zarządzają zasobami w chmurze platformy Azure.

Podczas tworzenia elementów Runbook do działania w hybrydowym procesie roboczym elementu Runbook należy edytować i testować elementy Runbook na komputerze, na którym jest hostowany hybrydowy proces roboczy. Na komputerze hosta znajdują się wszystkie moduły programu PowerShell i dostęp do sieci, które są potrzebne do zarządzania zasobami lokalnymi i uzyskiwania do nich dostępu. Po przetestowaniu elementu Runbook na maszynie hybrydowej procesu roboczego można przekazać go do środowiska Azure Automation, w którym będzie dostępny do uruchamiania w hybrydowym procesie roboczym. Ważne jest, aby wiedzieć, że zadania uruchamiane na koncie systemu lokalnego systemu Windows lub specjalne konto `nxautomation` użytkownika w systemie Linux. W systemie Linux oznacza to, że użytkownik musi upewnić `nxautomation` się, że konto ma dostęp do lokalizacji, w której przechowywane są moduły. W przypadku korzystania z polecenia cmdlet [Install-module](/powershell/module/powershellget/install-module) należy określić **ALLUSERS** do `-Scope` parametru, aby potwierdzić, `naxautomation` że konto ma dostęp.

Aby uzyskać więcej informacji na temat programu PowerShell w systemie Linux, zobacz [znane problemy dotyczące programu PowerShell na platformach innych niż Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Uruchamianie elementu Runbook w hybrydowym procesie roboczym elementu Runbook

[Uruchamianie elementu Runbook w Azure Automation](automation-starting-a-runbook.md) opisuje różne metody uruchamiania elementu Runbook. Hybrydowy proces roboczy elementu Runbook dodaje opcję **Uruchom** , w której można określić nazwę grupy hybrydowych procesów roboczych elementu Runbook. Gdy grupa zostanie określona, element Runbook zostanie pobrany i uruchomiony przez jednego z procesów roboczych w tej grupie. Jeśli ta opcja nie jest określona, jest uruchamiana w Azure Automation jak zwykle.

Po uruchomieniu elementu Runbook w Azure Portal zostanie wyświetlona opcja **Uruchom na** , w której można wybrać **platformę Azure** lub **hybrydowy proces roboczy**. W przypadku wybrania opcji **hybrydowy proces roboczy**można wybrać grupę z listy rozwijanej.

Użyj parametru **Uruchom** . Następujące polecenie służy do uruchamiania elementu Runbook o nazwie test-Runbook w grupie hybrydowych procesów roboczych elementu Runbook o nazwie Moja hybrydowa przy użyciu programu Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Parametr **Uruchom** został dodany do polecenia cmdlet **Start-AzureAutomationRunbook** w wersji od 0.9.1 programu Microsoft Azure PowerShell. Należy [pobrać najnowszą wersję,](https://azure.microsoft.com/downloads/) jeśli jest zainstalowana wcześniejsza wersja. Wystarczy zainstalować tę wersję na stacji roboczej, w której jest uruchamiany element Runbook z programu PowerShell. Nie trzeba go instalować na komputerze roboczym, chyba że zamierzasz uruchomić elementy Runbook z tego komputera "

## <a name="runbook-permissions"></a>Uprawnienia elementu Runbook

Elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook nie mogą korzystać z tej samej metody, która jest zwykle używana w przypadku elementów Runbook uwierzytelnianych w zasobach platformy Azure, ponieważ uzyskują dostęp do zasobów, które nie są na platformie Azure Element Runbook może albo zapewnić własne uwierzytelnianie do zasobów lokalnych, albo skonfigurować uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
)platformy Azure. Możesz również określić konto RunAs, aby zapewnić kontekst użytkownika dla wszystkich elementów Runbook.

### <a name="runbook-authentication"></a>Uwierzytelnianie elementu Runbook

Domyślnie elementy Runbook są uruchamiane w kontekście konta System lokalny dla systemu Windows i specjalne konto `nxautomation` użytkownika w systemie Linux na komputerze lokalnym, dlatego muszą zapewnić własne uwierzytelnianie do zasobów, do których uzyskują dostęp.

W elemencie Runbook można używać zasobów [poświadczeń](automation-credentials.md) i [certyfikatów](automation-certificates.md) z poleceniami cmdlet, które umożliwiają określenie poświadczeń, dzięki czemu można uwierzytelniać się w różnych zasobach. Poniższy przykład przedstawia część elementu Runbook, który uruchamia ponownie komputer. Pobiera poświadczenia z zasobu poświadczeń i nazwy komputera z zasobu zmiennej, a następnie używa tych wartości za pomocą polecenia cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można również użyć [InlineScript](automation-powershell-workflow.md#inlinescript), który umożliwia uruchamianie bloków kodu na innym komputerze z poświadczeniami, które są określone przez [wspólny parametr PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Konto Uruchom jako

Domyślnie hybrydowy proces roboczy elementu Runbook używa systemu lokalnego dla systemu Windows, a specjalne konto `nxautomation` użytkownika w systemie Linux do wykonywania elementów Runbook. Zamiast elementów Runbook udostępniają własne uwierzytelnianie do zasobów lokalnych, można określić konto **Uruchom jako** dla grupy hybrydowych procesów roboczych. Należy określić [zasób poświadczeń](automation-credentials.md) , który ma dostęp do zasobów lokalnych, a wszystkie elementy Runbook będą uruchamiane w ramach tych poświadczeń podczas działania w hybrydowym procesie roboczym elementu Runbook w grupie.

Nazwa użytkownika dla poświadczenia musi mieć jeden z następujących formatów:

* użytkownika
* username@domain
* Nazwa użytkownika (dla kont lokalnych dla komputera lokalnego)

Aby określić konto Uruchom jako dla grupy hybrydowych procesów roboczych, wykonaj czynności opisane w poniższej procedurze:

1. Utwórz [zasób poświadczeń](automation-credentials.md) z dostępem do zasobów lokalnych.
2. Otwórz konto usługi Automation w Azure Portal.
3. Wybierz kafelek **grupy hybrydowych procesów roboczych** , a następnie wybierz grupę.
4. Wybierz opcję **wszystkie ustawienia** , a następnie **Ustawienia grupy hybrydowych procesów roboczych**.
5. Zmień ustawienie **Uruchom jako** **domyślne** na **niestandardowe**.
6. Wybierz poświadczenie, a następnie kliknij przycisk **Zapisz**.

### <a name="managed-identities-for-azure-resources"></a>Zarządzane tożsamości dla zasobów platformy Azure

Hybrydowe procesy robocze elementów Runbook działające w usłudze Azure Virtual Machines mogą używać tożsamości zarządzanych dla zasobów platformy Azure do uwierzytelniania w zasobach platformy Azure. Istnieje wiele korzyści związanych z używaniem tożsamości zarządzanych dla zasobów platformy Azure za pośrednictwem kont Uruchom jako.

* Nie trzeba eksportować certyfikatu Uruchom jako, a następnie zaimportować go do hybrydowego procesu roboczego elementu Runbook
* Nie trzeba odnawiać certyfikatu używanego przez konto Uruchom jako
* Nie trzeba obsługiwać obiektu run as w kodzie elementu Runbook

Aby użyć zarządzanej tożsamości dla zasobów platformy Azure w hybrydowym procesie roboczym elementu Runbook, należy wykonać następujące czynności:

1. Tworzenie maszyny wirtualnej platformy Azure
2. [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Przyznaj maszynie wirtualnej dostęp do grupy zasobów w Menedżer zasobów](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Uzyskiwanie tokenu dostępu przy użyciu tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Zainstaluj hybrydowy proces roboczy elementu Runbook systemu Windows](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) na maszynie wirtualnej.

Po ukończeniu powyższych kroków można użyć `Connect-AzureRmAccount -Identity` elementu Runbook do uwierzytelniania w zasobach platformy Azure. Ta konfiguracja zmniejsza konieczność użycia konta Uruchom jako i zarządzania certyfikatem dla konta Uruchom jako.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Konto Uruchom jako usługi Automation

W ramach zautomatyzowanego procesu kompilacji na potrzeby wdrażania zasobów na platformie Azure może być konieczne uzyskanie dostępu do systemów lokalnych w celu obsługi zadania lub zestawu kroków w sekwencji wdrożenia. Aby można było obsługiwać uwierzytelnianie na platformie Azure przy użyciu konta Uruchom jako, należy zainstalować certyfikat konta Uruchom jako.

Następujący element Runbook programu PowerShell, **Export-RunAsCertificateToHybridWorker**, eksportuje certyfikat Uruchom jako z konta Azure Automation i pobiera i importuje je do magazynu certyfikatów na komputerze lokalnym w ramach hybrydowego procesu roboczego, który jest połączony na to samo konto. Po zakończeniu tego kroku sprawdza on, czy proces roboczy może zostać pomyślnie uwierzytelniony na platformie Azure przy użyciu konta Uruchom jako.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** jest teraz aliasem dla polecenia **Connect-AzureRmAccount**. Podczas wyszukiwania elementów biblioteki, jeśli nie widzisz pozycji **Connect-AzureRMAccount**, możesz użyć polecenie **Add-AzureRMAccount**lub zaktualizować moduły na koncie usługi Automation.

Zapisz element Runbook *Export-RunAsCertificateToHybridWorker* na komputerze z `.ps1` rozszerzeniem. Zaimportuj ją na konto usługi Automation i Edytuj element Runbook, zmieniając wartość zmiennej `$Password` przy użyciu własnego hasła. Opublikuj, a następnie Uruchom element Runbook. Wybierz grupę hybrydowych procesów roboczych, która będzie uruchamiać i uwierzytelniać elementy Runbook przy użyciu konta Uruchom jako. Strumień zadania zgłasza próbę zaimportowania certyfikatu do lokalnego magazynu maszyn i postępuje zgodnie z wieloma wierszami. To zachowanie zależy od liczby kont usługi Automation definiowanych w ramach subskrypcji i w przypadku pomyślnego uwierzytelnienia.

## <a name="job-behavior"></a>Zachowanie zadania

Zadania są obsługiwane nieco inaczej w hybrydowych procesach roboczych elementu Runbook, które nie są uruchamiane w piaskownicach platformy Azure. Jedną z kluczowych różnic polega na tym, że nie ma limitu czasu trwania zadania dla hybrydowych procesów roboczych elementów Runbook. Elementy Runbook uruchomione w piaskownicach platformy Azure są ograniczone do 3 godzin z powodu [sprawiedliwego udziału](automation-runbook-execution.md#fair-share). W przypadku długotrwałego elementu Runbook trzeba upewnić się, że jest on odporny na możliwe ponowne uruchomienie. Na przykład, jeśli komputer obsługujący ponowne uruchomienie hybrydowego procesu roboczego. Po ponownym uruchomieniu maszyny hosta hybrydowego procesu roboczego wszystkie uruchomione zadania elementu Runbook są uruchamiane od początku lub z ostatniego punktu kontrolnego dla elementów Runbook przepływu pracy programu PowerShell. Po ponownym uruchomieniu zadania elementu Runbook ponad 3 razy zostanie ono zawieszone.

## <a name="run-only-signed-runbooks"></a>Uruchom tylko podpisane elementy Runbook

Hybrydowe procesy robocze elementów Runbook można skonfigurować tak, aby uruchamiały tylko podpisane elementy Runbook z niektórymi konfiguracjami. W poniższej sekcji opisano sposób konfigurowania hybrydowych procesów roboczych elementów Runbook do uruchamiania podpisanego hybrydowego procesu roboczego [](#linux-hybrid-runbook-worker) elementu Runbook [systemu Windows](#windows-hybrid-runbook-worker)

> [!NOTE]
> Po skonfigurowaniu hybrydowego procesu roboczego elementu Runbook do uruchamiania tylko podpisanych elementów Runbook, elementy Runbook, które **nie** zostały podpisane, nie będą wykonywane w procesie roboczym.

### <a name="windows-hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook systemu Windows

#### <a name="create-signing-certificate"></a>Utwórz certyfikat podpisywania

Poniższy przykład tworzy certyfikat z podpisem własnym, który może służyć do podpisywania elementów Runbook. Przykład tworzy certyfikat i eksportuje go. Certyfikat zostanie zaimportowany do hybrydowych procesów roboczych elementów Runbook później. Odcisk palca jest również zwracany. Ta wartość jest używana później do odwoływania się do certyfikatu.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

#### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurowanie hybrydowych procesów roboczych elementów Runbook

Skopiuj certyfikat utworzony do każdego hybrydowego procesu roboczego elementu Runbook w grupie. Uruchom następujący skrypt, aby zaimportować certyfikat i skonfigurować proces roboczy hybrydowy do korzystania z walidacji podpisu w elementach Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Podpisywanie elementów Runbook przy użyciu certyfikatu

Hybrydowe procesy robocze elementu Runbook skonfigurowane do używania tylko podpisanych elementów Runbook muszą podpisywać elementy Runbook, które mają być używane w hybrydowym procesie roboczym elementu Runbook. Aby podpisać elementy Runbook, użyj następującego przykładowego programu PowerShell.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Gdy element Runbook został podpisany, musi zostać zaimportowany do konta usługi Automation i opublikowany za pomocą bloku podpisu. Aby dowiedzieć się, jak importować elementy Runbook, zobacz [Importowanie elementu Runbook z pliku do Azure Automation](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook systemu Linux

Aby podpisać elementy Runbook w hybrydowym procesie roboczym systemu Linux, hybrydowy proces roboczy elementu Runbook musi mieć na tym komputerze plik wykonywalny [GPG](https://gnupg.org/index.html) .

#### <a name="create-a-gpg-keyring-and-keypair"></a>Tworzenie GPG i pary kluczy

Aby utworzyć dzwonek i parę kluczy, należy użyć konta `nxautomation`hybrydowego procesu roboczego elementu Runbook.

Użyj `sudo` , aby zalogować się `nxautomation` jako konto.

```bash
sudo su – nxautomation
```

Po użyciu `nxautomation` konta Wygeneruj parę kluczy GPG.

```bash
sudo gpg --generate-key
```

GPG przeprowadzi Cię przez kroki tworzenia pary kluczy. Musisz podać nazwę, adres e-mail, godzinę wygaśnięcia i hasło, a następnie poczekać na wystarczającą ilość entropii na komputerze, aby klucz został wygenerowany.

Ponieważ katalog GPG został wygenerowany z sudo, należy zmienić jego właściciela na `nxautomation`.

Uruchom następujące polecenie, aby zmienić właściciela.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Udostępnianie kluczy dla hybrydowego procesu roboczego elementu Runbook

Po utworzeniu dzwonka kluczy należy udostępnić go dla hybrydowego procesu roboczego elementu Runbook. Zmodyfikuj plik `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` ustawień, aby uwzględnić Poniższy przykład w sekcji`[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Sprawdź, czy sprawdzanie poprawności podpisu jest włączone

Jeśli walidacja podpisu została wyłączona na komputerze, należy ją włączyć. Uruchom następujące polecenie, aby włączyć weryfikację podpisu. Zastępowanie `<LogAnalyticsworkspaceId>` identyfikatorem obszaru roboczego.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Podpisywanie elementu Runbook

Po skonfigurowaniu weryfikacji podpisu można użyć następującego polecenia w celu podpisania elementu Runbook:

```bash
gpg –-clear-sign <runbook name>
```

Podpisany element Runbook będzie miał nazwę `<runbook name>.asc`.

Podpisany element Runbook można teraz przekazać do Azure Automation i można go wykonać jak zwykłego elementu Runbook.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat różnych metod, których można użyć do uruchomienia elementu Runbook, zobacz [Uruchamianie elementu Runbook w Azure Automation](automation-starting-a-runbook.md).
* Aby poznać różne sposoby pracy z elementami Runbook programu PowerShell w programie Azure Automation przy użyciu edytora tekstów, zobacz [Edytowanie elementu Runbook w programie Azure Automation](automation-edit-textual-runbook.md)
* Jeśli elementy Runbook nie zakończą się pomyślnie, zapoznaj się z przewodnikiem rozwiązywania problemów z [błędami wykonywania elementu Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
