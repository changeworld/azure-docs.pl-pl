---
title: Przy użyciu bazy danych SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć baz danych jako usługa na stosie Azure i Szybkie kroki wdrażania karty dostawcy zasobów programu SQL Server.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="maintenance-operations"></a>Operacji konserwacji 
Dostawca zasobów SQL jest zablokowane maszynę wirtualną. Aktualizacja zabezpieczeń maszyny wirtualnej dostawcy zasobów może odbywać się za pośrednictwem punktu końcowego programu PowerShell tylko tyle administracyjnej (JEA) _DBAdapterMaintenance_. Skrypt jest dostarczany z pakietem instalacyjnym RP ułatwiające te operacje.

## <a name="patching-and-updating"></a>Stosowanie poprawek i aktualizacji
Dostawcy zasobów SQL nie jest obsługiwany jako część stosu Azure jako dodatkowy składnik jest. Microsoft będzie udostępniać aktualizacje dostawcy zasobów SQL, w razie potrzeby. Dostawca zasobów SQL zostanie uruchomiony na _użytkownika_ maszyny wirtualnej w ramach subskrypcji domyślny dostawca. W związku z tym jest niezbędne do zapewnienia poprawki systemu Windows, sygnatur oprogramowania antywirusowego,... itd. Systemu Windows aktualizację pakietów, które są dostarczane jako część cyklu poprawek i aktualizacji mogą być używane do stosowania aktualizacji do maszyny Wirtualnej systemu Windows. Po wydaniu zaktualizowanej karty skrypt jest dostarczany w celu zastosowania aktualizacji. Ten skrypt tworzy nową maszynę Wirtualną planu odzyskiwania i migracji tutaj stan, który już istnieje.

 ## <a name="backuprestoredisaster-recovery"></a>Odzyskiwanie kopii zapasowej/przywracania/po awarii
 Dostawcy zasobów SQL nie jest kopii zapasowej jako część procesu Azure stosu BC-odzyskiwania po awarii, ponieważ jest dodatkowy składnik. Skrypty będzie świadczona w celu ułatwienia:
- Tworzenie kopii zapasowej informacji niezbędnych stanu (przechowywane na koncie magazynu Azure stosu)
- Przywracanie planu odzyskiwania, w przypadku odzyskiwania cały stos staje się koniecznością.
Serwery bazy danych musi zostać odzyskana najpierw (Jeśli to konieczne), przed zasobu jest przywracany dostawcy.

## <a name="updating-sql-credentials"></a>Aktualizowanie poświadczeń programu SQL
Użytkownik jest odpowiedzialny za tworzenie i obsługę kont administratora systemu na serwerach SQL. Dostawca zasobów wymaga konta z tych uprawnień do zarządzania bazami danych w imieniu użytkowników — nie wymaga dostępu do danych w tych baz danych. Jeśli musisz zaktualizować hasła administratora systemu na serwerach SQL, można użyć możliwości aktualizacji interfejsu administrator dostawcy zasobów do zmiany hasła przechowywane używane przez dostawcę zasobów. Te hasła są przechowywane w Key Vault w wystąpieniu usługi Azure stosu.

Aby zmodyfikować ustawienia, kliknij przycisk **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **Hosting serwerów SQL** &gt; **Kont logowania SQL** i wybierz nazwę logowania. Zmiana musi zostać wykonana w wystąpieniu programu SQL najpierw (i wszystkie repliki, jeśli to konieczne). W **ustawienia** panelu, kliknij pozycję **hasło**.

![Zaktualizuj hasło administratora](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="update-the-virtual-machine-operating-system"></a>Zaktualizuj system operacyjny maszyny wirtualnej
Istnieje kilka sposobów, aby zaktualizować maszyny Wirtualnej systemu Windows Server:
* Zainstaluj najnowszy pakiet dostawcy zasobów przy użyciu obecnie poprawioną obrazu systemu Windows Server 2016 Core
* Instalowanie pakietów usługi Windows Update podczas instalacji lub aktualizacji planu odzyskiwania

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizacji definicji programu Windows Defender maszyny wirtualnej
Wykonaj następujące kroki w celu aktualizacji definicji programu Defender:

1. Pobieranie aktualizacji definicji programu Windows Defender [Windows Defender definicji](https://www.microsoft.com/en-us/wdsi/definitions)

    Na tej stronie w obszarze "Ręcznie pobrać i zainstalować definicje" Pobierz "Windows Defender Antivirus systemu Windows 10 i Windows 8.1" 64-bitowy plik. 
    
    Bezpośredniego łącza: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Tworzenie sesji programu PowerShell do punktu końcowego obsługi maszyny wirtualnej karty SQL RP
3. Skopiuj plik aktualizacji definicji maszynie karty bazy danych za pomocą sesji punktu końcowego konserwacji
4. W obsłudze programu PowerShell wywołania sesji _DBAdapterWindowsDefenderDefinitions aktualizacji_ polecenia
5. Po przeprowadzeniu instalacji zaleca się usunięcie pliku aktualizacji definicji używanych. Może ono zostać usunięte przy użyciu sesji konserwacji _ItemOnUserDrive Usuń)_ polecenia.


Poniżej przedstawiono przykładowy skrypt do aktualizacji definicji programu Defender (zastępuje adres lub nazwa maszyny wirtualnej z wartością rzeczywistą):

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>Zbieranie dzienników diagnostycznych
Dostawca zasobów SQL jest zablokowane maszynę wirtualną. Jeśli okaże się konieczne zbieranie dzienników z maszyny wirtualnej, a punkt końcowy programu PowerShell tylko tyle administracyjnej (JEA) _DBAdapterDiagnostics_ znajduje się w tym celu. Dostępne są dwa polecenia za pomocą tego punktu końcowego:

* Get-AzsDBAdapterLog - przygotowuje pakiet zip zawierający RP dzienników diagnostyki i umieszcza je na stacji użytkownika sesji. Polecenie można wywołać bez parametrów i zbierze ostatnich czterech godzin dzienników.
* Remove-AzsDBAdapterLog - czyści istniejące pakiety dziennika na dostawcy zasobów maszyny Wirtualnej

Konto użytkownika o nazwie _dbadapterdiag_ jest tworzony podczas aktualizacji do połączenia z punktem końcowym diagnostyki dla wyodrębniania dzienniki RP lub wdrożenia planu odzyskiwania. Hasło tego konta jest taka sama jak hasło konta administratora lokalnego podczas wdrażania/aktualizacji.

Aby używać tych poleceń, należy utworzyć sesję zdalną programu PowerShell do maszyny wirtualnej dostawcy zasobów i wywołaj polecenie. Opcjonalnie można podać datę rozpoczęcia obowiązywania wybiegającą i ToDate parametrów. Jeśli nie określisz jeden lub oba te Data rozpoczęcia będzie czterech godzin przed bieżącym czasem i ToDate będzie bieżącego czasu.

Ten przykładowy skrypt pokazuje użycie tych poleceń:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie serwerów hosta serwera SQL](azure-stack-sql-resource-provider-hosting-servers.md)
