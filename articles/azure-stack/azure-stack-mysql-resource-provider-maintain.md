---
title: Obsługa dostawcy zasobów MySQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak możesz zachować usługi dostawcy zasobów MySQL w usłudze Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 76a164ffcf918ebedcf4647f24a61ca3a271e967
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361919"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Operacje obsługi dostawcy zasobów MySQL

Dostawcy zasobów bazy danych MySQL działa na maszynie wirtualnej zablokowane. Aby włączyć operacji konserwacji, musisz zaktualizować zabezpieczenia maszyn wirtualnych. Aby to zrobić za pomocą zasadę najmniejszych uprawnień, można użyć programu PowerShell tylko tyle administracji (JEA) punktu końcowego DBAdapterMaintenance. Pakiet instalacyjny dostawcy zasobu zawiera skrypt do wykonania tej operacji.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizowanie systemu operacyjnego maszyny wirtualnej

Ponieważ dostawca zasobów jest uruchamiana na *użytkownika* maszyny wirtualnej, należy zastosować wymaganych poprawek i aktualizacji, po ich wydaniu. Pakiety aktualizacji Windows, które są dostarczane jako część cyklu poprawek i aktualizacji służy do stosowania aktualizacji do maszyny Wirtualnej.

Aktualizowanie dostawcy maszyny wirtualnej przy użyciu jednej z następujących metod:

- Zainstaluj najnowszy pakiet dostawcy zasobów przy użyciu aktualnie poprawionego obrazu systemu Windows Server 2016 Core.
- Zainstalować pakiet aktualizacji Windows podczas instalacji lub aktualizacji na potrzeby dostawcy zasobów.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizacja definicji usługi Windows Defender maszyny wirtualnej

Do aktualizacji definicji programu Defender, wykonaj następujące kroki:

1. Pobieranie aktualizacji definicji usługi Windows Defender [Windows Defender definicji](https://www.microsoft.com/en-us/wdsi/definitions).

    Na stronie Definicje przewiń w dół do "Ręcznie Pobierz i zainstaluj definicje". Pobierz plik 64-bitowy "Programu antywirusowego Windows Defender dla systemu Windows 10 i Windows 8.1".

    Można również użyć [to bezpośredni link](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) pobierania/Uruchom plik fpam fe.exe.

2. Otwórz sesję programu PowerShell do punktu końcowego obsługi MySQL zasobów dostawcy karty maszyny wirtualnej.

3. Skopiuj plik aktualizacji definicji do karty dostawcy zasobów maszyny Wirtualnej przy użyciu sesji punktu końcowego obsługi.

4. W sesji programu PowerShell konserwacji Uruchom _DBAdapterWindowsDefenderDefinitions aktualizacji_ polecenia.

5. Po zainstalowaniu definicje, zaleca się usuwanie plików aktualizacji definicji przy użyciu _Remove-ItemOnUserDrive)_ polecenia.

**Przykładowy skrypt programu PowerShell do aktualizacji definicji.**

Można edytować i uruchomić następujący skrypt w celu aktualizacji definicji programu Defender. Zastąp wartości w skrypcie wartościami z używanego środowiska.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Obrót kluczy tajnych

*Te instrukcje dotyczą tylko zintegrowane systemy usługi Azure Stack.*

Gdy za pomocą dostawcy zasobów SQL i bazy danych MySQL z usługą Azure Stack zintegrowane systemy, operatora infrastruktury Azure Stack jest odpowiedzialny za obracanie następujących zasobów dostawcy infrastruktury kluczy tajnych upewnij się, że ich wygaśnie:

- Certyfikat SSL zewnętrzny [podana podczas wdrażania](azure-stack-pki-certs.md).
- Zasób dostawcy maszyny Wirtualnej hasło administratora lokalnego konta podana podczas wdrażania.
- Hasło użytkownika diagnostyczne (dbadapterdiag) dostawcy zasobów.

### <a name="powershell-examples-for-rotating-secrets"></a>Przykłady programu PowerShell do rotacji kluczy tajnych

**W tym samym czasie, należy zmienić wszystkich wpisów tajnych.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Zmień hasło użytkownika do diagnozowania.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**Zmień hasło konta administratora lokalnego maszyn wirtualnych.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Zmień hasło certyfikatu protokołu SSL.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>Parametry SecretRotationMySQLProvider.ps1

|Parametr|Opis|
|-----|-----|
|AzCredential|Poświadczeń konta w usłudze Azure Stack administratora usługi.|
|CloudAdminCredential|Usługa Azure Stack chmury administratora poświadczenie konta domeny.|
|PrivilegedEndpoint|Uprzywilejowane punkt końcowy dostępu do Get AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostyka hasło do konta użytkownika.|
|VMLocalCredential|Konto administratora lokalnego na maszynie Wirtualnej MySQLAdapter.|
|DefaultSSLCertificatePassword|Domyślny certyfikat SSL (* pfx) hasła.|
|DependencyFilesLocalPath|Ścieżka lokalna plików zależności.|
|     |     |

### <a name="known-issues"></a>Znane problemy

**Problem:**<br>
Dzienniki Aby uzyskać rotacji kluczy tajnych nie są automatycznie zbierane, jeśli po uruchomieniu skryptu rotacji wpisu tajnego zakończy się niepowodzeniem.

**Obejście:**<br>
Użyj polecenia cmdlet Get-AzsDBAdapterLogs do zbierania wszystkich zasobów dostawcy dzienników, w tym AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, zapisane w C:\Logs.

## <a name="collect-diagnostic-logs"></a>Zbieranie dzienników diagnostycznych

Aby zebrać dzienników zablokowanym maszyny wirtualnej, można użyć punktu końcowego DBAdapterDiagnostics PowerShell tylko tyle administracji (JEA). Ten punkt końcowy zawiera następujące polecenia:

- **Get-AzsDBAdapterLog**. To polecenie tworzy pakiet zip dzienniki diagnostyczne dostawcy zasobów i zapisuje plik na dysku użytkownika tej sesji. Możesz uruchomić to polecenie bez parametrów, a ostatnie cztery godziny dzienniki są zbierane.

- **Usuń AzsDBAdapterLog**. To polecenie usuwa istniejące pakiety dziennika dla dostawcy zasobów maszyny Wirtualnej.

### <a name="endpoint-requirements-and-process"></a>Punkt końcowy wymagania i proces

Po zainstalowaniu lub zaktualizowaniu dostawcy zasobów, konto użytkownika dbadapterdiag jest tworzone. To konto będzie używane do zbierania dzienników diagnostycznych.

>[!NOTE]
>Hasło do konta dbadapterdiag jest taka sama jak hasło używane do administratora lokalnego na maszynie wirtualnej, utworzonego podczas wdrażania dostawcy lub aktualizacji.

Aby użyć _DBAdapterDiagnostics_ poleceń, Utwórz zdalną sesję programu PowerShell do maszyny wirtualnej dostawcy zasobów i uruchom **Get AzsDBAdapterLog** polecenia.

Należy określić przedział czasu dla zbieranie dzienników przy użyciu **FromDate** i **ToDate** parametrów. Jeśli nie określisz jedno lub oba te parametry są używane następujące wartości domyślne:

* FromDate odpowiada czterem godzinom przed aktualną godziną.
* ToDate jest bieżący czas.

**Przykładowy skrypt programu PowerShell do zbierania dzienników.**

Poniższy skrypt pokazuje, jak zbierać dzienniki diagnostyczne od dostawcy zasobów maszyny Wirtualnej.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Kolejne kroki

[Usuwanie dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-remove.md)
