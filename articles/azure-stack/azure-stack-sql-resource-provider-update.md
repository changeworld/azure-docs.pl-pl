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
ms.openlocfilehash: ac5073d1abc32b7598a869750f9c5a801559e9e6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264081"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizuj dostawcę zasobów SQL

*Dotyczy: Azure stosu zintegrowanych systemów.*

Nowego dostawcę zasobów programu SQL może być zwolnione po zaktualizowaniu stosu Azure do nowej kompilacji. Mimo że istniejącej karty w dalszym ciągu działać, zaleca się jak najszybciej aktualizacja do nowszej kompilacji.

>[!IMPORTANT]
>Należy zainstalować aktualizacji w kolejności, w których są one wydane. Nie można pominąć wersji. Można znaleźć na liście wersji w [dostawcy zasobów wymagania wstępne dotyczące wdrażania](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Przegląd

Aby zaktualizować dostawcy zasobów, użyj *UpdateSQLProvider.ps1* skryptu. Ten skrypt jest dołączona do pakietu instalacyjnego nowego dostawcę zasobów SQL. Proces aktualizacji jest podobny do procesu używane do [wdrażanie dostawcy zasobów](.\azure-stack-sql-resource-provider-deploy.md). Skrypt aktualizacji używa te same argumenty co skrypt DeploySqlProvider.ps1 i musisz podać informacje o certyfikacie.

### <a name="update-script-processes"></a>Procesy skryptu aktualizacji

*UpdateSQLProvider.ps1* skrypt tworzy nową maszynę wirtualną (VM) z kodem najnowsze dostawcy zasobów.

>[!NOTE]
>Zalecamy pobranie najnowszych obrazu systemu Windows Server 2016 Core z witryny Marketplace zarządzania. Jeśli musisz zainstalować aktualizację, możesz umieścić **pojedynczego** MSU pakietu w ścieżce lokalnej zależności. Skrypt zakończy się niepowodzeniem, jeśli istnieje więcej niż jeden plik MSU w tej lokalizacji.

Po *UpdateSQLProvider.ps1* skrypt tworzy nową maszynę Wirtualną, skrypt migruje starego dostawcy maszyny Wirtualnej następujące ustawienia:

* Informacje o bazie danych
* informacje o serwerze hostingu
* wymagane rekord DNS

### <a name="update-script-powershell-example"></a>Zaktualizuj przykładowy skrypt programu PowerShell

Możesz edytować i uruchom następujący skrypt z podwyższonym poziomem uprawnień PowerShell ISE. Pamiętaj, aby zmienić informacje o koncie i hasła, zgodnie z wymaganiami środowiska.

> [!NOTE]
> Ten proces aktualizacji ma zastosowanie tylko do stosu Azure zintegrowanych systemów.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>Parametry UpdateSQLProvider.ps1

Po uruchomieniu skryptu można określić następujące parametry wiersza polecenia. Jeśli nie istnieje lub wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Możesz również umieścić plik .pfx certyfikatu w tym katalogu. | _Opcjonalne dla jednego węzła, ale obowiązkowe dla wieloma węzłami._ |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** |Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby. | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |

## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów SQL](azure-stack-sql-resource-provider-maintain.md)
