---
title: Aktualizowanie dostawcy zasobów usługi Azure SQL stosu | Dokumentacja firmy Microsoft
description: Dowiedz się więcej, aktualizowania dostawcy zasobów usługi Azure SQL stosu.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 3a7656e54181c8e8e7b6b1bd39f80ce8ed01c807
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294864"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizuj dostawcę zasobów SQL
Nowego dostawcę zasobów programu SQL może być zwolnione po zaktualizowaniu stosu Azure kompilacji. Gdy istniejącej karty nadal działać, zaleca się jak najszybciej aktualizacja do nowszej kompilacji. Aktualizacje muszą być zainstalowane w kolejności: nie można pominąć wersje (zapoznaj się z listą wersji w [dostawcy zasobów wymagania wstępne dotyczące wdrażania](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Do aktualizacji, należy użyć dostawcy zasobów *UpdateSQLProvider.ps1* skryptu. Proces jest podobny do procesu używane do instalowania dostawcy zasobów, zgodnie z opisem w [wdrażanie dostawcy zasobów](.\azure-stack-sql-resource-provider-deploy.md) artykułu. Skrypt jest dołączana do pobierania dostawcy zasobów.

*UpdateSQLProvider.ps1* skrypt tworzy nową maszynę Wirtualną z najnowszą kod dostawcy zasobów i przeprowadzanie migracji ustawień za pomocą starego maszyny Wirtualnej do nowej maszyny Wirtualnej. Ustawienia do zmigrowania obejmują bazy danych i informacji o serwerze hosting i rekordu DNS niezbędne.

Skrypt wymaga użycia te same argumenty, które są opisane DeploySqlProvider.ps1 skryptu. Podaj certyfikat tutaj również. 

Zalecamy pobranie najnowszych obrazu systemu Windows Server 2016 Core z witryny Marketplace zarządzania. Jeśli musisz zainstalować aktualizację, należy zaznaczyć jeden. Pakiet MSU w ścieżce lokalnej zależności. Jeśli więcej niż jeden. Plik MSU zostanie znaleziony, skrypt zakończy się niepowodzeniem.

Poniżej przedstawiono przykład *UpdateSQLProvider.ps1* skrypt, który można uruchomić w wierszu polecenia programu PowerShell. Pamiętaj zmienić informacje o koncie i hasła w razie potrzeby: 

> [!NOTE]
> Proces aktualizacji dotyczy tylko systemów zintegrowanego.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

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

## <a name="updatesqlproviderps1-parameters"></a>Parametry UpdateSQLProvider.ps1
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


## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów SQL](azure-stack-sql-resource-provider-maintain.md)
