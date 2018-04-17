---
title: Przy użyciu bazy danych SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć baz danych jako usługa na stosie Azure i Szybkie kroki wdrażania karty dostawcy zasobów programu SQL Server.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: bd3618367f91fe043cc8412481b38a9c996a5275
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych SQL Microsoft Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Użyj karty dostawcy zasobów programu SQL Server do udostępnienia baz danych jako usługa [stosu Azure](azure-stack-poc.md). Po zainstalowaniu dostawcy zasobów i połącz go z co najmniej jedno wystąpienie programu SQL Server, można utworzyć i użytkowników:
- Bazy danych dla chmury natywnych aplikacji.
- Witryny sieci Web, które są oparte na SQL.
- Obciążenia, które są oparte na SQL.
Nie trzeba udostępnić maszynę wirtualną (VM) obsługującego program SQL Server zawsze.

Dostawca zasobów nie obsługuje wszystkie funkcje zarządzania bazy danych z [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/). Na przykład elastyczne pule baz danych i możliwości wybierania wydajność bazy danych w górę i w dół automatycznie nie są dostępne. Jednak podobnie Obsługa ma dostawcy zasobów tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD). Interfejs API nie jest zgodny z bazy danych SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura karty dostawcy zasobów SQL
Dostawca zasobów składa się z trzech składników:

- **Karta Dostawca zasobów SQL maszyny Wirtualnej**, która jest maszyny wirtualnej systemu Windows, która uruchamia dostawcy usług.
- **Dostawca zasobów sam**, który przetwarza żądania alokacji i ujawnia bazy danych zasobów.
- **Serwery obsługujące program SQL Server**, które zapewniają pojemność baz danych o nazwie serwerami hostingu.

Należy utworzyć jedną (lub więcej) wystąpienia programu SQL Server i zapewniają dostęp do zewnętrznych wystąpień programu SQL Server.

> [!NOTE]
> Hosting serwerów, które są zainstalowane na stosie Azure zintegrowanych systemów musi zostać utworzona z subskrypcji dzierżawcy. Nie można ich utworzyć z subskrypcji dostawcy domyślnego. Muszą one zostać utworzone w portalu dzierżawcy lub w sesji programu PowerShell z odpowiednią logowania. Wszystkie serwery hostingu maszyn wirtualnych mogą być obciążane i musi mieć odpowiednie licencje. Administrator usługi może być właścicielem subskrypcji dzierżawcy.

## <a name="deploy-the-resource-provider"></a>Wdrażanie dostawcy zasobów

1. Jeśli jeszcze nie zarejestrować Twojego zestawu programowanie i pobranie obrazu systemu Windows Server 2016 Datacenter Core do pobrania za pośrednictwem zarządzania Marketplace. Należy użyć obrazu systemu Windows Server 2016 Core. Umożliwia także skrypt do utworzenia [obrazu systemu Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Pamiętaj wybrać opcję core.)

2. Zaloguj się do hosta, który można uzyskać dostępu do uprzywilejowanych punktu końcowego maszyny Wirtualnej.

    - W instalacjach Azure stosu Development Kit Zaloguj się do hosta fizycznego.

    - W systemach z wieloma węzłami host musi być systemu, w którym można uzyskać dostępu do uprzywilejowanych punktu końcowego.
    
    >[!NOTE]
    > System, w którym skrypt jest uruchamiany *musi* jako system Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET zainstalowane. W przeciwnym razie niepowodzenia instalacji. Host zestawu SDK platformy Azure stosu spełnia tego kryterium.


3. Pobierz dostawcę zasobów SQL binarnego. Następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego.

    >[!NOTE] 
    > Dostawca zasobów ma minimalną odpowiedniego stosu Azure kompilacji. Pamiętaj pobrać poprawne dane binarne dla wersji stosu Azure, w którym jest uruchomiony.

    | Azure kompilacji stosu | Instalator dostawcy zasobów SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [RP SQL w wersji 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 lub 1.0.180106.1 (wielowęzłowego) | [RP SQL w wersji 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [RP SQL w wersji 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [RP SQL w wersji 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Dla tego zestawu SDK stosu Azure certyfikatu z podpisem własnym jest tworzony w ramach tego procesu. Dla zintegrowanych systemów należy podać odpowiedni certyfikat.

   Aby dostarczyć własny certyfikat, należy umieścić plik pfx w **DependencyFilesLocalPath** w następujący sposób:

    - Certyfikat uniwersalny dla \*.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\> lub certyfikat w jednej lokacji z nazwą pospolitą sqladapter.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\>.

    - Ten certyfikat musi być zaufany. Oznacza to łańcuch zaufania musi istnieć bez konieczności używania certyfikatów pośrednich.

    - W katalogu wskazywanego przez parametr DependencyFilesLocalPath może istnieć tylko jeden certyfikat pliku.

    - Nazwa pliku nie może zawierać żadnych znaków specjalnych ani spacji.


5. Otwórz **nowe** z podwyższonym poziomem uprawnień (administracyjnych) konsoli programu PowerShell i zmian w katalogu, w którym zostały wyodrębnione pliki. Użyj nowego okna, aby uniknąć problemów, które mogą wystąpić z niepoprawne moduły programu PowerShell, które zostały już załadowane w systemie.

6. [Zainstaluj program Azure PowerShell w wersji 1.2.11](azure-stack-powershell-install.md).

7. Uruchom skrypt DeploySqlProvider.ps1, która wykonuje następujące czynności:

    - Przekazuje certyfikaty i inne artefaktów na konto magazynu Azure stosu.
    - Publikowanie pakietów galerii tak, aby umożliwić wdrożenie bazy danych SQL za pomocą galerii.
    - Publikuje pakietu galerii wdrażania serwerami hostingu.
    - Wdraża maszynę Wirtualną za pomocą obrazu systemu Windows Server 2016, który został utworzony w kroku 1, a następnie instaluje dostawcę zasobów.
    - Rejestruje rekord DNS lokalnego mapy do dostawcy zasobów maszyny Wirtualnej.
    - Rejestruje dostawcę zasobów z lokalnej usługi Azure Resource Manager (administratora i użytkowników).
    - Opcjonalnie instaluje pojedynczej aktualizacji systemu Windows podczas instalacji planu odzyskiwania

8. Zalecamy pobranie najnowszych obrazu systemu Windows Server 2016 Core z witryny Marketplace zarządzania. Jeśli musisz zainstalować aktualizację, należy zaznaczyć jeden. Pakiet MSU w ścieżce lokalnej zależności. Jeśli więcej niż jeden. Plik MSU zostanie znaleziony, skrypt zakończy się niepowodzeniem.


Oto przykład można uruchamiać w wierszu polecenia programu PowerShell. (Pamiętaj zmienić informacje o koncie i hasła zgodnie z potrzebami.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parametry DeploySqlProvider.ps1
Te parametry można określić w wierszu polecenia. Jeśli nie chcesz, lub jeśli wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, używane do wdrażania usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Plik .pfx certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ (_obowiązkowe_ dla wielowęzłowego) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure stosu

> [!NOTE]
>  Po zakończeniu skrypt instalacji należy odświeżyć portalu, zobacz blok administratora.


1. Zaloguj się do portalu administratora jako administratora usługi.

2. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Przejdź do **grup zasobów**. Następnie wybierz **systemu.\< Lokalizacja\>.sqladapter** grupy zasobów. Sprawdź, czy wszystkie cztery wdrożenia zakończyło się pomyślnie.

      ![Sprawdź wdrożenie dostawcy zasobów SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Zaktualizuj kartę Dostawca zasobów SQL (wielowęzłowego tylko kompilacje 1710 i nowsze)
Nowa karta dostawcy zasobów programu SQL może być zwolnione po zaktualizowaniu kompilacje stosu Azure. Gdy istniejącej karty nadal działać, zaleca się jak najszybciej aktualizacja do nowszej kompilacji. Aktualizacje muszą być zainstalowane w kolejności: nie można pominąć wersje (zobacz tabelę w kroku 3 [wdrażanie dostawcy zasobów](#deploy-the-resource-provider)).

Do aktualizacji, należy użyć dostawcy zasobów *UpdateSQLProvider.ps1* skryptu. Proces jest podobny do procesu używane do instalowania dostawcy zasobów, zgodnie z opisem w [wdrażanie dostawcy zasobów](#deploy-the-resource-provider) sekcji tego artykułu. Skrypt jest dołączana do pobierania dostawcy zasobów.

*UpdateSQLProvider.ps1* skrypt tworzy nową maszynę Wirtualną z najnowszą kod dostawcy zasobów i przeprowadzanie migracji ustawień za pomocą starego maszyny Wirtualnej do nowej maszyny Wirtualnej. Ustawienia do zmigrowania obejmują bazy danych i informacji o serwerze hosting i rekordu DNS niezbędne.

Skrypt wymaga użycia te same argumenty, które są opisane DeploySqlProvider.ps1 skryptu. Podaj certyfikat tutaj również. 

Zalecamy pobranie najnowszych obrazu systemu Windows Server 2016 Core z witryny Marketplace zarządzania. Jeśli musisz zainstalować aktualizację, należy zaznaczyć jeden. Pakiet MSU w ścieżce lokalnej zależności. Jeśli więcej niż jeden. Plik MSU zostanie znaleziony, skrypt zakończy się niepowodzeniem.

Poniżej przedstawiono przykład *UpdateSQLProvider.ps1* skrypt, który można uruchomić w wierszu polecenia programu PowerShell. Pamiętaj zmienić informacje o koncie i hasła w razie potrzeby: 

> [!NOTE]
> Proces aktualizacji dotyczy tylko systemów zintegrowanego.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>Parametry UpdateSQLProvider.ps1
Te parametry można określić w wierszu polecenia. Jeśli nie chcesz, lub jeśli wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Plik .pfx certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ (_obowiązkowe_ dla wielowęzłowego) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** |Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |


## <a name="collect-diagnostic-logs"></a>Zbieranie dzienników diagnostycznych
Dostawca zasobów SQL jest zablokowane maszynę wirtualną. Jeśli okaże się konieczne zbieranie dzienników z maszyny wirtualnej, a punkt końcowy programu PowerShell tylko tyle administracyjnej (JEA) _DBAdapterDiagnostics_ znajduje się w tym celu. Dostępne są dwa polecenia za pomocą tego punktu końcowego:

* Get-AzsDBAdapterLog - przygotowuje pakiet zip zawierający RP dzienników diagnostyki i umieszcza je na stacji użytkownika sesji. Polecenie można wywołać bez parametrów i zbierze ostatnich czterech godzin dzienników.
* Remove-AzsDBAdapterLog - czyści istniejące pakiety dziennika na dostawcy zasobów maszyny Wirtualnej

Konto użytkownika o nazwie _dbadapterdiag_ jest tworzony podczas aktualizacji do połączenia z punktem końcowym diagnostyki dla wyodrębniania dzienniki RP lub wdrożenia planu odzyskiwania. Hasło tego konta jest taka sama jak hasło konta administratora lokalnego podczas wdrażania/aktualizacji.

Aby używać tych poleceń, należy utworzyć sesję zdalną programu PowerShell do maszyny wirtualnej dostawcy zasobów i wywołaj polecenie. Opcjonalnie można podać datę rozpoczęcia obowiązywania wybiegającą i ToDate parametrów. Jeśli nie określisz jeden lub oba te Data rozpoczęcia będzie czterech godzin przed bieżącym czasem i ToDate będzie bieżącego czasu.

Ten przykładowy skrypt pokazuje użycie tych poleceń:

```
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

## <a name="maintenance-operations-integrated-systems"></a>Operacje konserwacji (systemy zintegrowanym)
Dostawca zasobów SQL jest zablokowane maszynę wirtualną. Aktualizacja zabezpieczeń maszyny wirtualnej dostawcy zasobów może odbywać się za pośrednictwem punktu końcowego programu PowerShell tylko tyle administracyjnej (JEA) _DBAdapterMaintenance_.

Skrypt jest dostarczany z pakietem instalacyjnym RP ułatwiające te operacje.

### <a name="update-the-virtual-machine-operating-system"></a>Zaktualizuj system operacyjny maszyny wirtualnej
Istnieje kilka sposobów, aby zaktualizować maszyny Wirtualnej systemu Windows Server:
* Zainstaluj najnowszy pakiet dostawcy zasobów przy użyciu obecnie poprawioną obrazu systemu Windows Server 2016 Core
* Instalowanie pakietów usługi Windows Update podczas instalacji lub aktualizacji planu odzyskiwania


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizacji definicji programu Windows Defender maszyny wirtualnej

Wykonaj następujące kroki w celu aktualizacji definicji programu Defender:

1. Pobieranie aktualizacji definicji programu Windows Defender [Windows Defender definicji](https://www.microsoft.com/en-us/wdsi/definitions)

    Na tej stronie w obszarze "Ręcznie pobrać i zainstalować definicje" Pobierz "Windows Defender Antivirus systemu Windows 10 i Windows 8.1" 64-bitowy plik. 
    
    Bezpośredniego łącza: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Tworzenie sesji programu PowerShell do punktu końcowego obsługi maszyny wirtualnej karty SQL RP
3. Skopiuj plik aktualizacji definicji maszynie karty bazy danych za pomocą sesji punktu końcowego konserwacji
4. W obsłudze programu PowerShell wywołania sesji _DBAdapterWindowsDefenderDefinitions aktualizacji_ polecenia
5. Po przeprowadzeniu instalacji zaleca się usunięcie pliku aktualizacji definicji używanych. Może ono zostać usunięte przy użyciu sesji konserwacji _ItemOnUserDrive Usuń)_ polecenia.


Poniżej przedstawiono przykładowy skrypt do aktualizacji definicji programu Defender (zastępuje adres lub nazwa maszyny wirtualnej z wartością rzeczywistą):

```
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

## <a name="remove-the-sql-resource-provider-adapter"></a>Usuń kartę Dostawca zasobów SQL

Aby usunąć dostawcę zasobów, konieczne jest najpierw usunąć wszelkie zależności.

1. Upewnij się, że masz oryginalny pakiet wdrożeniowy, który został pobrany dla tej wersji karty dostawcy zasobów SQL.

2. Należy usunąć wszystkie bazy danych użytkownika od dostawcy zasobów. (Usunięcie bazy danych użytkownika nie powoduje usunięcia danych.) To zadanie powinno być wykonywane przez użytkowników, samodzielnie.

3. Administrator musi usunąć serwerami hostingu z karty dostawcy zasobów SQL.

4. Administrator, należy usunąć wszystkie plany odwołujące się do karty dostawcy zasobów SQL.

5. Administrator musi usunąć wszystkie jednostki SKU i przydziałów, które są skojarzone z kartą dostawcy zasobów programu SQL.

6. Uruchom ponownie skrypt wdrożenia wraz z następującymi elementami:
    - Odinstalować parametru
    - Punkty końcowe usługi Azure Resource Manager
    - DirectoryTenantID
    - Poświadczenia dla konta administratora usługi


## <a name="next-steps"></a>Kolejne kroki

[Dodaj serwery hostingu](azure-stack-sql-resource-provider-hosting-servers.md) i [Tworzenie baz danych](azure-stack-sql-resource-provider-databases.md).

Spróbuj innych [usług PaaS](azure-stack-tools-paas-services.md) jak [dostawcy zasobów serwer MySQL](azure-stack-mysql-resource-provider-deploy.md) i [dostawcy zasobów usługi aplikacji](azure-stack-app-service-overview.md).
