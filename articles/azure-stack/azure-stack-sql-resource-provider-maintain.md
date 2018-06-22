---
title: Obsługa dostawcy zasobów SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można obsługiwać usługę dostawcy zasobów SQL na stosie Azure.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300914"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operacje konserwacji dostawcy zasobów SQL

Dostawca zasobów SQL działa na zablokowanym maszyny wirtualnej. Aby włączyć operacji konserwacji, musisz zaktualizować zabezpieczeń maszyny wirtualnej. Aby to zrobić przy użyciu zasadą najniższych uprawnień, można użyć [PowerShell tylko tyle administracyjnej (JEA)](https://docs.microsoft.com/en-us/powershell/jea/overview) punktu końcowego *DBAdapterMaintenance*. Pakiet instalacyjny dostawcy zasobów zawiera skrypt do wykonania tej operacji.

## <a name="patching-and-updating"></a>Stosowanie poprawek i aktualizacji

Dostawcy zasobów programu SQL nie jest obsługiwane jako część stosu Azure, ponieważ jest dodatkowy składnik. Firma Microsoft udostępnia aktualizacje dostawcy zasobów SQL, w razie potrzeby. Po wydaniu zaktualizowanej karty SQL skrypt jest dostarczany w celu zastosowania aktualizacji. Ten skrypt tworzy nowego dostawcę zasobów maszyny Wirtualnej, migracja stanu starego dostawcy maszyny Wirtualnej do nowej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [aktualizacji dostawcy zasobów SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Dostawca maszyny wirtualnej

Ponieważ dostawca zasobów jest uruchamiana na *użytkownika* maszyny wirtualnej, należy zastosować wymagane poprawki i aktualizacje, gdy są one wydane. Pakiety aktualizacji systemu Windows, które znajdują się w ramach cyklu poprawek i aktualizacji służy do stosowania aktualizacji do maszyny Wirtualnej.

## <a name="backuprestoredisaster-recovery"></a>Odzyskiwanie kopii zapasowej/przywracania/po awarii

 Ponieważ to składnik dodatkowy dostawcy zasobów SQL nie ma kopii zapasowej jako część procesu Azure stosu firm ciągłości awaryjnego odzyskiwania (BCDR). Skrypty przewiduje się następujące operacje:

- Tworzenie kopii zapasowej informacji o stanie (przechowywane na koncie magazynu Azure stosu).
- Przywracanie dostawcy zasobów, jeśli wymagane jest jej odzyskanie pełnego stosu.

>[!NOTE]
>Jeśli musisz wykonać odzyskiwanie, serwery bazy danych musi zostać odzyskana przed przywróceniem dostawcy zasobów.

## <a name="updating-sql-credentials"></a>Aktualizowanie poświadczeń programu SQL

Wszystko jest odpowiedzialny za tworzenie i obsługę kont administratora systemu na serwerach SQL. Dostawca zasobów wymaga konta z tych uprawnień do zarządzania bazy danych dla użytkowników, ale nie wymaga dostępu do danych użytkowników. Jeśli musisz zaktualizować hasła administratora systemu na serwerach SQL, umożliwia dostawcy zasobów Administratorze zmienić hasła. Te hasła są przechowywane w Key Vault w wystąpieniu usługi Azure stosu.

Aby zmodyfikować ustawienia, wybierz **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **Hosting serwerów SQL** &gt; **Kont logowania SQL** i wybierz nazwę użytkownika. Zmiana musi zostać wykonana w wystąpieniu programu SQL najpierw (i wszystkie repliki, jeśli to konieczne.) W obszarze **ustawienia**, wybierz pozycję **hasło**.

![Zaktualizuj hasło administratora](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotacją kluczy tajnych

*Te instrukcje dotyczą tylko 1804 wersji systemów zintegrowanego stosu Azure i później. Nie należy próbować Obróć kluczy tajnych w wersji pre-1804 Azure stosu wersjach.*

Gdy przy użyciu dostawców zasobów SQL i MySQL stosu Azure zintegrowanych systemów, można obracać następujące klucze tajne infrastruktury (wdrożenia):

- Certyfikat SSL zewnętrzny [podana podczas wdrażania](azure-stack-pki-certs.md).
- Zasób dostawcy wirtualna hasło administratora lokalnego konta podana podczas wdrażania.
- Hasło użytkownika diagnostyczne (dbadapterdiag) dostawcy zasobów.

### <a name="powershell-examples-for-rotating-secrets"></a>Przykłady programu PowerShell związany z rotacją kluczy tajnych

**Zmień wszystkich kluczy tajnych w tym samym czasie.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Zmień hasło użytkownika diagnostycznych.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Zmień hasło konta administratora lokalnego maszyny Wirtualnej.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Zmień hasło certyfikatu SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parametry SecretRotationSQLProvider.ps1

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

**Problem**: kluczy tajnych rotacji dzienników.<br>
Automatycznie nie są zbierane w dziennikach rotacją kluczy tajnych, jeśli tajny obrotu niestandardowego skryptu zakończy się niepowodzeniem, po jego uruchomieniu.

**Obejście**:<br>
Aby zbierać wszystkie dzienniki dostawcy zasobów, w tym AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log zapisane w C:\Logs, należy użyć polecenia cmdlet Get-AzsDBAdapterLogs.

## <a name="update-the-virtual-machine-operating-system"></a>Zaktualizuj system operacyjny maszyny wirtualnej

Użyj jednej z następujących metod można zaktualizować system operacyjny maszyny wirtualnej.

- Zainstaluj najnowszy pakiet dostawcy zasobów przy użyciu obecnie poprawioną obrazu systemu Windows Server 2016 Core.
- Instalowanie pakietów usługi Windows Update podczas instalacji lub aktualizacji do dostawcy zasobów.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizacji definicji programu Windows Defender maszyny wirtualnej

Aby zaktualizować definicji usługi Windows Defender:

1. Pobieranie aktualizacji definicji programu Windows Defender [Windows Defender definicji](https://www.microsoft.com/en-us/wdsi/definitions).

   W definicji aktualizacji strony, przewiń w dół do "Ręcznie pobrać i zainstalować definicje". Pobierz plik 64-bitowe "Windows Defender oprogramowanie antywirusowe dla systemu Windows 10 i Windows 8.1".

   Można również użyć [tego łącza bezpośrednie](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) do pobierania/Uruchom plik fpam fe.exe.

2. Tworzenie sesji programu PowerShell do punktu końcowego obsługi SQL zasobów dostawcy karty maszyny wirtualnej.

3. Skopiuj plik aktualizacji definicji do maszyny wirtualnej przy użyciu sesji punktu końcowego konserwacji.

4. W tej sesji programu PowerShell konserwacji Uruchom *DBAdapterWindowsDefenderDefinitions aktualizacji* polecenia.

5. Po zainstalowaniu definicje, zaleca się usunięcie pliku aktualizacji definicji przy użyciu *ItemOnUserDrive Usuń* polecenia.

**Przykładowy skrypt programu PowerShell dla aktualizacji definicji.**

Można edytować i uruchomić następujący skrypt w celu aktualizacji definicji programu Defender. Zastąp wartości w skrypcie wartości ze środowiska.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
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

## <a name="collect-diagnostic-logs"></a>Zbieranie dzienników diagnostycznych

Aby zebrać dzienniki z zablokowanym maszyny wirtualnej, można użyć punktu końcowego programu PowerShell tylko tyle administracyjnej (JEA) *DBAdapterDiagnostics*. Ten punkt końcowy zawiera następujące polecenia:

- **Get-AzsDBAdapterLog**. To polecenie tworzy pakiet zip dzienników diagnostycznych dostawcy zasobów i zapisuje go na dysku użytkownika tej sesji. Można uruchomić tego polecenia, bez żadnych parametrów i ostatnich czterech godzin dzienniki są zbierane.
- **Usuń AzsDBAdapterLog**. To polecenie usuwa istniejące pakiety dziennika na dostawcy zasobów maszyny Wirtualnej.

### <a name="endpoint-requirements-and-process"></a>Punkt końcowy wymagań i procesu

Gdy dostawca zasobów jest zainstalowane lub zaktualizowane, **dbadapterdiag** utworzone konto użytkownika. To konto będzie używać do zbierania dzienników diagnostycznych.

>[!NOTE]
>Hasło konta dbadapterdiag jest taka sama jak hasło używane na potrzeby lokalnego administratora na maszynę wirtualną, która jest tworzona podczas wdrażania dostawcy lub aktualizacji.

Aby użyć *DBAdapterDiagnostics* poleceń, utwórz sesję zdalną programu PowerShell do maszyny wirtualnej dostawcy zasobów i uruchom **Get-AzsDBAdapterLog** polecenia.

Należy określić przedział czasu zbierania dzienników przy użyciu **FromDate** i **ToDate** parametrów. Jeśli nie określisz jedno lub oba te parametry są używane następujące wartości domyślne:

- Data rozpoczęcia odpowiada czterem godzinom przed bieżącym czasem.
- ToDate jest bieżący czas.

**Przykładowy skrypt programu PowerShell do zbierania dzienników.**

Poniższy skrypt pokazuje, jak zbierać dzienniki diagnostyczne od dostawcy zasobów maszyny Wirtualnej.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
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

[Dodawanie serwerów hosta serwera SQL](azure-stack-sql-resource-provider-hosting-servers.md)
