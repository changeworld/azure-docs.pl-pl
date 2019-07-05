---
title: Uruchamianie elementów runbook w usłudze Azure Automation hybrydowego Runbook Worker
description: Ten artykuł zawiera informacje dotyczące uruchamiania elementów runbook na maszynach w lokalnym centrum danych lub dostawca chmury z roli hybrydowego procesu roboczego Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88e3c0514861da0bd11acffd26cced54717e4418
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478485"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Uruchamianie elementów runbook w hybrydowym procesie roboczym elementu Runbook

Nie ma różnic w strukturze elementów runbook, które są uruchamiane w usłudze Azure Automation i elementów runbook, które są uruchamiane hybrydowy proces roboczy elementu Runbook. Elementy Runbook, które można używać z poszczególnymi najprawdopodobniej różni się znacznie. Różnica ta ma, ponieważ elementy runbook, których platformą docelową hybrydowy proces roboczy elementu Runbook, zwykle zarządzać zasobami na komputerze lokalnym lub w odniesieniu do zasobów w środowisku lokalnym, na którym jest wdrażany. Elementy Runbook w usłudze Azure Automation zwykle zarządzają zasobami w chmurze platformy Azure.

Podczas tworzenia elementów runbook do uruchamiania w hybrydowym procesie roboczym elementu Runbook, należy edytować i testowania elementów runbook na maszynie, który jest hostem hybrydowy proces roboczy. Komputer hosta ma wszystkie moduły programu PowerShell i dostępu do sieci, które musisz zarządzać i uzyskiwać dostęp do zasobów lokalnych. Gdy element runbook jest testowany na maszynie hybrydowego procesu roboczego, można przesłać go do środowiska usługi Azure Automation, gdy są one dostępne do działania w hybrydowym procesie roboczym. Ważne jest, aby wiedzieć, że zadania z programem na koncie systemu lokalnego dla Windows lub specjalne konto użytkownika `nxautomation` w systemie Linux. To zachowanie można wprowadzać niewielkie różnice, podczas tworzenia elementów runbook dla hybrydowego procesu roboczego elementu Runbook. Zmiany te powinny być weryfikowane podczas pisania elementów runbook.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Uruchamianie elementu runbook w hybrydowym procesie roboczym z elementu Runbook

[Uruchamianie elementu Runbook w usłudze Azure Automation](automation-starting-a-runbook.md) w tym artykule opisano różne metody uruchamiania elementu runbook. Dodaje hybrydowego procesu roboczego Runbook **RunOn** opcji, w którym można określić nazwę grupy hybrydowych procesów roboczych elementu Runbook. Jeśli zostanie określona grupa, element runbook jest pobierana i uruchom przez jeden z procesów roboczych w tej grupie. Jeśli ta opcja nie jest określona, następnie jest ono uruchamiane w usłudze Azure Automation jako normalny.

Po uruchomieniu elementu runbook w portalu Azure pojawi się informacja o **systemem** opcji, w którym można wybrać **Azure** lub **hybrydowy proces roboczy**. Jeśli wybierzesz **hybrydowy proces roboczy**, a następnie wybraniu grupy z listy rozwijanej.

Użyj **RunOn** parametru. Następujące polecenie służy do uruchamiania elementu runbook o nazwie Test-Runbook w hybrydowych grupa procesu roboczego elementu Runbook o nazwie MyHybridGroup przy użyciu programu Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> **RunOn** parametr został dodany do **Start AzureAutomationRunbook** polecenia cmdlet w wersji od 0.9.1 programu Microsoft Azure PowerShell. Należy [Pobierz najnowszą wersję](https://azure.microsoft.com/downloads/) Jeśli masz wcześniejszą zainstalowane. Musisz zainstalować tę wersję na stacji roboczej, w którym jest uruchamiany element runbook za pomocą programu PowerShell. Nie trzeba go zainstalować na komputerze pracownika, chyba że zamierzasz uruchamiać elementy runbook z tego komputera"

## <a name="runbook-permissions"></a>Uprawnienia elementu Runbook

Elementy Runbook uruchomione w hybrydowym procesie roboczym elementu Runbook nie można użyć tej samej metody, która jest zwykle używana do uwierzytelniania elementów runbook do zasobów platformy Azure, ponieważ uzyskujesz dostęp do zasobów nie na platformie Azure. Element runbook albo podać swój własny uwierzytelniania do zasobów lokalnych lub można skonfigurować uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
). Można również określić konto Uruchom jako w celu zapewnienia kontekstu użytkownika dla wszystkich elementów runbook.

### <a name="runbook-authentication"></a>Uwierzytelnianie elementu Runbook

Domyślnie, elementy runbook działają w kontekście konta systemu lokalnego dla Windows i specjalne konto użytkownika `nxautomation` dla systemu Linux na komputerze lokalnym, więc muszą zapewnić własny mechanizm uwierzytelniania do zasobów, z których korzystają.

Możesz użyć [poświadczeń](automation-credentials.md) i [certyfikatu](automation-certificates.md) zasobów w elemencie runbook za pomocą poleceń cmdlet, które pozwalają określić poświadczenia, więc istnieje możliwość uwierzytelnienia się różnymi zasobami. Poniższy przykład pokazuje części elementu runbook, który powoduje ponowne uruchomienie komputera. Jej pobiera poświadczenia z zasób poświadczeń i nazwę komputera z zasobem zmiennej, a następnie używa tych wartości w poleceniu cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można również użyć [InlineScript](automation-powershell-workflow.md#inlinescript), co pozwala na uruchamianie bloków kodu na innym komputerze przy użyciu poświadczeń, które są określone przez [typowy parametr PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Konto Uruchom jako

Domyślnie hybrydowego procesu roboczego Runbook korzysta z systemu lokalnego dla Windows i specjalne konto użytkownika `nxautomation` dla systemu Linux do wykonywania elementów runbook. Zamiast elementów runbook, Podaj własny mechanizm uwierzytelniania do zasobów lokalnych, można określić **RunAs** konto do grupy hybrydowych procesów roboczych. Należy określić [zasób poświadczeń](automation-credentials.md) , ma dostęp do zasobów lokalnych, a wszystkie działania elementów runbook tych poświadczeń podczas uruchamiania na hybrydowy proces roboczy elementu Runbook w grupie.

Nazwa użytkownika dla poświadczenia musi być w jednym z następujących formatów:

* domain\username
* username@domain
* Nazwa użytkownika (dla kont lokalnych dla komputera lokalnego)

Aby określić konto Uruchom jako dla grupy hybrydowych procesów roboczych, należy użyć następującej procedury:

1. Tworzenie [zasób poświadczeń](automation-credentials.md) z dostępem do zasobów lokalnych.
2. Otwórz konto usługi Automation w witrynie Azure portal.
3. Wybierz **grupy hybrydowych procesów roboczych** kafelka, a następnie wybierz grupę.
4. Wybierz **wszystkie ustawienia** i następnie **hybrydowego procesu roboczego grupy**.
5. Zmiana **Uruchom jako** z **domyślne** do **niestandardowe**.
6. Wybierz poświadczenie, a następnie kliknij przycisk **Zapisz**.

### <a name="managed-identities-for-azure-resources"></a>Zarządzanych tożsamości dla zasobów platformy Azure

Hybrydowe procesy robocze elementów Runbook uruchomionych na maszynach wirtualnych platformy Azure można użyć zarządzanych tożsamości dla zasobów platformy Azure do uwierzytelniania do zasobów platformy Azure. Istnieje wiele korzyści zarządzanych tożsamości dla zasobów platformy Azure za pośrednictwem konta Uruchom jako.

* Nie trzeba wyeksportować certyfikat Uruchom jako, a następnie zaimportować go do hybrydowego procesu roboczego Runbook
* Nie musisz odnowić certyfikat używany przez konto Uruchom jako
* Nie trzeba obsługiwać Uruchom jako obiekt połączenia w kodzie elementu runbook

Do korzystania z tożsamości zarządzanej dla zasobów platformy Azure w ramach procesu roboczego hybrydowego elementu Runbook, należy wykonać następujące czynności:

1. Tworzenie maszyny Wirtualnej platformy Azure
2. [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Udzielanie dostępu do sieci maszyny Wirtualnej do grupy zasobów w usłudze Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Uzyskiwanie tokenu dostępu przy użyciu tożsamości zarządzanej przypisany systemowo maszyny Wirtualnej](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Instalowanie Windows hybrydowego procesu roboczego Runbook](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) na maszynie wirtualnej.

Po zakończeniu powyższych kroków możesz używać `Connect-AzureRmAccount -Identity` w elemencie runbook do uwierzytelniania do zasobów platformy Azure. Ta konfiguracja ogranicza konieczność użycia konta Uruchom jako i zarządzania certyfikat konta Uruchom jako.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Konto Uruchom jako usługi Automation

W ramach procesu automatycznej kompilacji do wdrażania zasobów na platformie Azure mogą wymagać dostępu do systemów lokalnych do obsługi zadań lub zestawu kroków w sekwencji wdrażania. Aby zapewnić obsługę uwierzytelniania na platformie Azure przy użyciu konta Uruchom jako, musisz zainstalować certyfikat konta Uruchom jako.

Następujący element runbook programu PowerShell **RunAsCertificateToHybridWorker eksportu**, eksportuje certyfikat Uruchom jako na koncie usługi Azure Automation i pliki do pobrania i importuje je do magazynu certyfikatów komputera lokalnego na hybrydowego proces roboczy, który jest podłączony do tego samego konta. Po ukończeniu tego kroku sprawdza to, czy Proces roboczy pomyślnie przeprowadzić uwierzytelnienie na platformie Azure przy użyciu konta Uruchom jako.

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
> **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

Zapisz *RunAsCertificateToHybridWorker eksportu* elementu runbook do komputera za pomocą `.ps1` rozszerzenia. Zaimportuj go do konta usługi Automation i edytować element runbook, zmieniając wartość zmiennej `$Password` przy użyciu własnego hasła. Publikowanie, a następnie uruchom element runbook. Cel grupy hybrydowych procesów roboczych, które będą uruchamiane i uwierzytelnianie elementów runbook przy użyciu konta Uruchom jako. Strumień zadań zgłasza próba zaimportuj certyfikat do magazynu komputer lokalny i jest zgodna z wieloma wierszami. To zachowanie zależy od liczby kont usługi Automation, należy zdefiniować w ramach subskrypcji i jeśli uwierzytelnianie zakończy się pomyślnie.

## <a name="job-behavior"></a>Zachowanie zadania

Zadania są obsługiwane nieco inaczej na hybrydowych procesów roboczych Runbook, nie są one, gdy są one uruchomione na piaskownic usługi Azure. Jedną kluczową różnicą jest to, że nie ma żadnego limitu na czas trwania zadania na hybrydowych procesów roboczych Runbook. Elementy Runbook uruchomione w systemie Azure piaskownice mogą zawierać maksymalnie 3 godziny z powodu [udział](automation-runbook-execution.md#fair-share). Dla elementu runbook długotrwałych chcesz upewnij się, że jest odporna na możliwe ponowne uruchomienie. Na przykład, jeśli komputer obsługujący hybrydowego procesu roboczego wykonuje ponowny rozruch. Jeśli ponowne uruchomienie maszyny hosta hybrydowego procesu roboczego, wszystkie uruchomione zadania elementu runbook uruchamia ponownie od samego początku, lub z ostatniego punktu kontrolnego dla elementów runbook przepływu pracy programu PowerShell. Element runbook — po ponownym uruchomieniu zadania więcej niż 3 razy, a następnie jest zawieszony.

## <a name="run-only-signed-runbooks"></a>Uruchom tylko podpisane elementów Runbook

Hybrydowych procesów roboczych Runbook można skonfigurować do uruchamiania tylko podpisem elementy runbook przy użyciu pewnych czynności konfiguracyjnych. W poniższej sekcji opisano sposób konfigurowania hybrydowych procesów roboczych elementu Runbook do uruchamiania podpisanych [Windows hybrydowego procesu roboczego Runbook](#windows-hybrid-runbook-worker) i [Linux hybrydowego procesu roboczego Runbook](#linux-hybrid-runbook-worker)

> [!NOTE]
> Po skonfigurowaniu hybrydowego procesu roboczego elementu Runbook do uruchamiania tylko podpisem elementy runbook, elementy runbook mają **nie** zostały podpisane spowoduje wykonanie w procesie roboczym nie powiodło się.

### <a name="windows-hybrid-runbook-worker"></a>Windows hybrydowego procesu roboczego Runbook

#### <a name="create-signing-certificate"></a>Tworzenie certyfikatu podpisywania

Poniższy przykład tworzy certyfikat z podpisem własnym, który może służyć do podpisywania elementów runbook. Próbka tworzy certyfikat i eksportuje go. Certyfikat został zaimportowany do hybrydowych procesów roboczych Runbook później. Odcisk palca jest zwracany, ta wartość jest używana w dalszej części można odwoływać się do certyfikatu.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurowanie hybrydowych procesów roboczych Runbook

Skopiować certyfikat utworzony do każdego hybrydowego procesu roboczego Runbook w grupie. Uruchom następujący skrypt, aby zaimportować certyfikat i skonfiguruj hybrydowy proces roboczy, aby użyć Weryfikacja podpisu w elementach runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Zaloguj się w elementach Runbook przy użyciu certyfikatu

Z elementem Runbook hybrydowe procesy robocze skonfigurowane do używania tylko podpisane elementów runbook, należy zalogować się elementy runbook, które mają być używane w hybrydowym procesie roboczym elementu Runbook. Użyj poniższego przykładu z programu PowerShell do podpisania elementów runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Gdy element runbook został podpisany, należy zaimportować do konta usługi Automation i opublikowana z użyciem blok podpisu. Aby dowiedzieć się, jak zaimportować elementy runbook, zobacz [importowanie elementu runbook z pliku do usługi Azure Automation](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Linux hybrydowego procesu roboczego Runbook

Aby utworzyć elementy runbook na procesu roboczego elementu Runbook dla hybrydowych w systemie Linux, hybrydowy proces roboczy elementu Runbook musi mieć [GPG](https://gnupg.org/index.html) pliku wykonywalnego istnieje na maszynie.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Tworzenie pęku kluczy GPG i kluczy

Aby utworzyć zestaw kluczy i kluczy, musisz użyć konta hybrydowego procesu roboczego Runbook `nxautomation`.

Użyj `sudo` zalogowania się jako `nxautomation` konta.

```bash
sudo su – nxautomation
```

Raz przy użyciu `nxautomation` konta, generowanie kluczy gpg.

```bash
sudo gpg --generate-key
```

GPG przeprowadzi Cię przez kroki, aby utworzyć prze. Należy podać nazwę, adres e-mail, czas wygaśnięcia, hasło i zaczekaj, aż wystarczającej entropii na tym komputerze dla klucza do wygenerowania.

Ponieważ katalog GPG został wygenerowany przy użyciu programu sudo, musisz zmienić jej właściciela `nxautomation`. 

Uruchom następujące polecenie, aby zmienić właściciela.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Udostępnianie pęku kluczy hybrydowy proces roboczy elementu Runbook

Po utworzeniu pęku kluczy należy udostępnić hybrydowy proces roboczy elementu Runbook. Zmodyfikuj plik ustawień `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` do uwzględnienia w poniższym przykładzie w sekcji `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Sprawdź, czy Weryfikacja podpisu znajduje się na

Weryfikacja podpisu została wyłączona na komputerze, należy włączyć tę funkcję. Uruchom następujące polecenie, aby włączyć weryfikację podpisu. Zastępowanie `<LogAnalyticsworkspaceId>` przy użyciu swojego identyfikatora obszaru roboczego.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Utwórz element runbook

Po skonfigurowaniu Weryfikacja podpisu służy następujące polecenie do podpisania elementu runbook:

```bash
gpg –-clear-sign <runbook name>
```

Podpisany element runbook będzie mieć taką nazwę `<runbook name>.asc`.

Podpisany element runbook można teraz przekazana do usługi Azure Automation i mogą być wykonywane, takich jak zwykły element runbook.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat różnych metod, które mogą służyć do uruchamiania elementu runbook, zobacz [uruchamianie elementu Runbook w usłudze Azure Automation](automation-starting-a-runbook.md).
* Aby poznać różne sposoby pracy z elementami runbook programu PowerShell w usłudze Azure Automation za pomocą Edytor tekstów, zobacz [edytowanie elementu Runbook w usłudze Azure Automation](automation-edit-textual-runbook.md)
* Jeśli Twoje elementy runbook nie są pomyślne zakończenie działania, zapoznaj się z przewodnik rozwiązywania problemów na [błędy wykonania elementu runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).