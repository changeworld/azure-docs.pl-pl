---
title: Aktualizowanie dostawcy zasobów usługi Azure Stack SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zaktualizować dostawcy zasobów usługi Azure Stack SQL.
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
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 5238c60493820fe6d784049da9862b4347e563c4
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650913"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizowanie dostawcy zasobów bazy danych SQL

*Dotyczy: Zintegrowane systemy usługi Azure Stack.*

Nowego dostawcę zasobów programu SQL może być zwalniany, gdy usługi Azure Stack został zaktualizowany do nowej kompilacji. Mimo że istniejącego dostawcy zasobów będzie nadal działać, zaleca się aktualizowanie do najnowszej kompilacji tak szybko, jak to możliwe. 

Począwszy od wydania wersji 1.1.33.0 dostawcy zasobów SQL są aktualizacjami zbiorczymi i nie muszą być zainstalowane w kolejności, w której zostały wydane; tak długo, jak wszystko, poczynając od wersji 1.1.24.0 lub nowszej. Na przykład jeśli używasz wersji 1.1.24.0 dostawcy zasobów bazy danych SQL, następnie przeprowadzić uaktualnienie do wersji 1.1.33.0 lub później, bez konieczności najpierw zainstaluj wersję 1.1.30.0. Aby zapoznać się z wersji dostawcy dostępnych zasobów i wersję usługi Azure Stack, są one obsługiwane w, można znaleźć na liście wersji w [wdrażanie wstępnie wymaganych składników dla dostawcy zasobów](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Aby zaktualizować dostawcy zasobów, użyj *UpdateSQLProvider.ps1* skryptu. Ten skrypt jest dołączona do pakietu instalacyjnego nowego dostawcy zasobu języka SQL. Proces aktualizacji jest podobny do procesu umożliwiającego [wdrażanie dostawcy zasobów](./azure-stack-sql-resource-provider-deploy.md). Skrypt aktualizacji zawiera te same argumenty co skrypt DeploySqlProvider.ps1 i musisz podać informacje o certyfikacie.

 > [!IMPORTANT]
 > Przed uaktualnieniem dostawcy zasobów, przejrzyj informacje o wersji, aby dowiedzieć się więcej o nowych funkcjach, poprawek i znanych problemach, które mogą wpłynąć na wdrożenie.

## <a name="update-script-processes"></a>Zaktualizuj skrypt procesów

*UpdateSQLProvider.ps1* skrypt umożliwia utworzenie nowej maszyny wirtualnej (VM) z najnowszym kodem dostawcy zasobów.

> [!NOTE]
> Firma Microsoft zaleca, Pobierz najnowszego obrazu systemu Windows Server 2016 Core z zarządzania przy użyciu portalu Marketplace. Jeśli musisz zainstalować aktualizację, możesz umieścić **pojedynczego** MSU pakietu w ścieżce lokalnej zależności. Skrypt zakończy się niepowodzeniem, jeśli istnieje więcej niż jeden plik MSU w tej lokalizacji.

Po *UpdateSQLProvider.ps1* skrypt umożliwia utworzenie nowej maszyny Wirtualnej, skrypt u starego dostawcy usług maszyny Wirtualnej jest migrowana następujące ustawienia:

* informacje o bazie danych
* informacje o serwerze hostingu
* wymagane rekordu DNS

## <a name="update-script-parameters"></a>Zaktualizuj Parametry skryptu

Można określić następujące parametry, z poziomu wiersza polecenia, po uruchomieniu **UpdateSQLProvider.ps1** skrypt programu PowerShell. Jeśli nie istnieje lub dowolnym Walidacja parametru nie powiedzie się, zostanie wyświetlony monit zapewnić wymagane parametry.

| Nazwa parametru | Opis | Komentarz lub wartość domyślną |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury, niezbędne do uzyskania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów bazy danych SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **AzureEnvironment** | Środowiska platformy Azure z konta administratora usługi, które używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, lub jeśli za pomocą (Chiny) usługi Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Możesz również umieścić plik PFX certyfikatu w tym katalogu. | _Opcjonalnie na potrzeby jednego węzła, lecz obowiązkowa w przypadku wielu węzłów_ |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Liczba przypadków, o których chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** |Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcę zasobów i wszystkie powiązane zasoby. | Nie |
| **DebugMode** | Zapobiega automatycznego czyszczenia po awarii. | Nie |

## <a name="update-script-powershell-example"></a>Zaktualizuj skrypt przykładowy programu PowerShell
Oto przykład użycia *UpdateSQLProvider.ps1* skrypt, który można uruchomić z konsoli programu PowerShell z podwyższonym poziomem uprawnień. Pamiętaj zmienić informacje o zmiennych i hasła, zgodnie z potrzebami:  

> [!NOTE]
> Ten proces aktualizacji dotyczy tylko systemów zintegrowanych w usłudze Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

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
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert 

 ```

## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów bazy danych SQL](azure-stack-sql-resource-provider-maintain.md)
