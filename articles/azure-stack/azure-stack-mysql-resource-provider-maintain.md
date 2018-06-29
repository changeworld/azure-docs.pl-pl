---
title: Obsługa dostawcy zasobów MySQL na stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zachować usługi Dostawca zasobów MySQL na stosie Azure.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: a1fadd0cfdb38452e86cfce643bebbfd746e8643
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085786"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Operacje konserwacji dostawcy zasobów MySQL

Dostawca zasobów programu MySQL działa na zablokowanym maszyny wirtualnej. Aby włączyć operacji konserwacji, musisz zaktualizować zabezpieczeń maszyny wirtualnej. Aby to zrobić za pomocą zasadą najniższych uprawnień, można punktu końcowego programu PowerShell tylko tyle administracyjnej (JEA) DBAdapterMaintenance. Pakiet instalacyjny dostawcy zasobów zawiera skrypt do wykonania tej operacji.

## <a name="update-the-virtual-machine-operating-system"></a>Zaktualizuj system operacyjny maszyny wirtualnej

Ponieważ dostawca zasobów jest uruchamiana na *użytkownika* maszyny wirtualnej, należy zastosować wymagane poprawki i aktualizacje, gdy są one wydane. Pakiety aktualizacji systemu Windows, które znajdują się w ramach cyklu poprawek i aktualizacji służy do stosowania aktualizacji do maszyny Wirtualnej.

Zaktualizuj maszynę wirtualną dostawcy przy użyciu jednej z następujących metod:

- Zainstaluj najnowszy pakiet dostawcy zasobów przy użyciu obecnie poprawioną obrazu systemu Windows Server 2016 Core.
- Instalowanie pakietów usługi Windows Update podczas instalacji lub aktualizacji do dostawcy zasobów.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizacji definicji programu Windows Defender maszyny wirtualnej

Aby zaktualizować definicje Defender, wykonaj następujące kroki:

1. Pobieranie aktualizacji definicji programu Windows Defender [Windows Defender definicji](https://www.microsoft.com/en-us/wdsi/definitions).

    Na stronie Definicje przewiń w dół do "Ręcznie pobrać i zainstalować definicje". Pobierz plik 64-bitowe "Windows Defender oprogramowanie antywirusowe dla systemu Windows 10 i Windows 8.1".

    Można również użyć [tego łącza bezpośrednie](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) do pobierania/Uruchom plik fpam fe.exe.

2. Otwórz sesję programu PowerShell do punktu końcowego obsługi MySQL zasobów dostawcy karty maszyny wirtualnej.

3. Skopiuj plik aktualizacji definicji do karty maszyny Wirtualnej przy użyciu sesji punktu końcowego obsługi dostawcy zasobów.

4. W tej sesji programu PowerShell konserwacji Uruchom _DBAdapterWindowsDefenderDefinitions aktualizacji_ polecenia.

5. Po zainstalowaniu definicje, zaleca się usunięcie pliku aktualizacji definicji przy użyciu _ItemOnUserDrive Usuń)_ polecenia.

**Przykładowy skrypt programu PowerShell dla aktualizacji definicji.**

Można edytować i uruchomić następujący skrypt w celu aktualizacji definicji programu Defender. Zastąp wartości w skrypcie wartości ze środowiska.

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
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Rotacją kluczy tajnych

*Te instrukcje dotyczą tylko 1804 wersji systemów zintegrowanego stosu Azure i później. Nie należy próbować Obróć kluczy tajnych w wersji pre-1804 stosu Azure.*

Gdy przy użyciu dostawców zasobów SQL i MySQL stosu Azure zintegrowanych systemów, można obracać następujące klucze tajne infrastruktury (wdrożenia):

- Certyfikat SSL zewnętrzny [podana podczas wdrażania](azure-stack-pki-certs.md).
- Zasób dostawcy wirtualna hasło administratora lokalnego konta podana podczas wdrażania.
- Hasło użytkownika diagnostyczne (dbadapterdiag) dostawcy zasobów.

### <a name="powershell-examples-for-rotating-secrets"></a>Przykłady programu PowerShell związany z rotacją kluczy tajnych

**Zmień wszystkich kluczy tajnych w tym samym czasie.**

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

**Zmień hasło użytkownika diagnostycznych.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**Zmień hasło konta administratora lokalnego maszyny Wirtualnej.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Zmień hasło certyfikatu SSL.**

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
|AzCredential|Azure poświadczeń dla konta administratora usługi stosu.|
|CloudAdminCredential|Azure stosu chmury administratora domeny poświadczeń dla konta.|
|PrivilegedEndpoint|Uprzywilejowany punktu końcowego dostępu Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostyka hasło do konta użytkownika.|
|VMLocalCredential|Konto administratora lokalnego na Maszynie wirtualnej MySQLAdapter.|
|DefaultSSLCertificatePassword|Domyślny certyfikat SSL (* pfx) hasła.|
|DependencyFilesLocalPath|Ścieżka lokalna plików zależności.|
|     |     |

### <a name="known-issues"></a>Znane problemy

**Problem:**<br>
Automatycznie nie są zbierane w dziennikach rotacją kluczy tajnych, jeśli skrypt tajny obrotu zakończy się niepowodzeniem, po jego uruchomieniu.

**Obejście problemu:**<br>
Zbieranie wszystkich zasobów dostawcy dzienników, w tym AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log zapisane w C:\Logs za pomocą polecenia cmdlet Get-AzsDBAdapterLogs.

## <a name="collect-diagnostic-logs"></a>Zbieranie dzienników diagnostycznych

Do zbierania dzienników z zablokowanym maszyny wirtualnej, używając punktu końcowego programu PowerShell tylko tyle administracyjnej (JEA) DBAdapterDiagnostics. Ten punkt końcowy zawiera następujące polecenia:

- **Get-AzsDBAdapterLog**. To polecenie tworzy pakiet zip dzienników diagnostycznych dostawcy zasobów i zapisuje go na dysku użytkownika tej sesji. Można uruchomić tego polecenia, bez żadnych parametrów i ostatnich czterech godzin dzienniki są zbierane.

- **Usuń AzsDBAdapterLog**. To polecenie usuwa istniejące pakiety dziennika na dostawcy zasobów maszyny Wirtualnej.

### <a name="endpoint-requirements-and-process"></a>Punkt końcowy wymagań i procesu

Po zainstalowaniu dostawcy zasobów lub zaktualizowaniu, utworzono konto użytkownika dbadapterdiag. To konto będzie używać do zbierania dzienników diagnostycznych.

>[!NOTE]
>Hasło konta dbadapterdiag jest taka sama jak hasło używane na potrzeby lokalnego administratora na maszynę wirtualną, która jest tworzona podczas wdrażania dostawcy lub aktualizacji.

Aby użyć _DBAdapterDiagnostics_ poleceń, utwórz sesję zdalną programu PowerShell do maszyny wirtualnej dostawcy zasobów i uruchom **Get-AzsDBAdapterLog** polecenia.

Należy określić przedział czasu zbierania dzienników przy użyciu **FromDate** i **ToDate** parametrów. Jeśli nie określisz jedno lub oba te parametry są używane następujące wartości domyślne:

* Data rozpoczęcia odpowiada czterem godzinom przed bieżącym czasem.
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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Kolejne kroki

[Usuń dostawcę zasobów MySQL](azure-stack-mysql-resource-provider-remove.md)
