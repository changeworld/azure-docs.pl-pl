---
title: Aktualizowanie dostawcy zasobów bazy danych MySQL usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zaktualizować dostawcy zasobów usługi Azure Stack bazy danych MySQL.
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
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 976c05449704b0ecbc48ee5bd4799f300fcac0aa
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651689"
---
# <a name="update-the-mysql-resource-provider"></a>Aktualizowanie dostawcy zasobów bazy danych MySQL 

*Dotyczy: Zintegrowane systemy usługi Azure Stack.*

Nowe MySQL, które karty dostawcy zasobów może być zwolnione, podczas tworzenia usługi Azure Stack zostały zaktualizowane. Chociaż istniejącej karty w dalszym ciągu działać, zaleca się aktualizowanie do najnowszej kompilacji tak szybko, jak to możliwe. 

Począwszy od wydania wersji 1.1.33.0 dostawcy zasobów MySQL, są aktualizacjami zbiorczymi i nie muszą być zainstalowane w kolejności, w której zostały wydane; tak długo, jak wszystko, poczynając od wersji 1.1.24.0 lub nowszej. Na przykład jeśli używasz wersji 1.1.24.0 dostawcy zasobów bazy danych MySQL, następnie przeprowadzić uaktualnienie do wersji 1.1.33.0 lub później, bez konieczności najpierw zainstaluj wersję 1.1.30.0. Aby zapoznać się z wersji dostawcy dostępnych zasobów i wersję usługi Azure Stack, są one obsługiwane w, można znaleźć na liście wersji w [wdrażanie wstępnie wymaganych składników dla dostawcy zasobów](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Aby zaktualizować dostawcy zasobów, możesz użyć **UpdateMySQLProvider.ps1** skryptu. Proces jest podobny do procesu, który został użyty do zainstalowania dostawcy zasobów, zgodnie z opisem w wdrażanie dostawcy zasobów części tego artykułu. Skrypt jest dostarczonego z pobranymi dostawcy zasobów. 

 > [!IMPORTANT]
 > Przed uaktualnieniem dostawcy zasobów, przejrzyj informacje o wersji, aby dowiedzieć się więcej o nowych funkcjach, poprawek i znanych problemach, które mogą wpłynąć na wdrożenie.

## <a name="update-script-processes"></a>Zaktualizuj skrypt procesów

**UpdateMySQLProvider.ps1** skrypt tworzy nową maszynę Wirtualną z najnowszym kodem dostawcy zasobów i migruje ustawień starej maszyny Wirtualnej do nowej maszyny Wirtualnej. Ustawienia są migrowane obejmują bazy danych i informacji o serwerze hostingu i rekordu DNS to konieczne. 

>[!NOTE]
>Firma Microsoft zaleca, Pobierz najnowszego obrazu systemu Windows Server 2016 Core z zarządzania przy użyciu portalu Marketplace. Jeśli musisz zainstalować aktualizację, możesz umieścić **pojedynczego** MSU pakietu w ścieżce lokalnej zależności. Skrypt zakończy się niepowodzeniem, jeśli istnieje więcej niż jeden plik MSU w tej lokalizacji.

Skrypt wymaga użycia tych samych argumentów, które są opisane dla skryptu DeployMySqlProvider.ps1. Zapewniają także certyfikatu w tym miejscu.  


## <a name="update-script-parameters"></a>Zaktualizuj Parametry skryptu 
Można określić następujące parametry, z poziomu wiersza polecenia, po uruchomieniu **UpdateMySQLProvider.ps1** skrypt programu PowerShell. Jeśli nie istnieje lub dowolnym Walidacja parametru nie powiedzie się, zostanie wyświetlony monit zapewnić wymagane parametry. 

| Nazwa parametru | Opis | Komentarz lub wartość domyślną | 
| --- | --- | --- | 
| **CloudAdminCredential** | Poświadczenia dla administratora chmury, niezbędne do uzyskania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ | 
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, używane do wdrażania usługi Azure Stack. | _Wymagane_ | 
| **VMLocalCredential** |Poświadczenia dla konta administratora lokalnego dostawcy zasobów bazy danych SQL maszyny Wirtualnej. | _Wymagane_ | 
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ | 
| **AzureEnvironment** | Środowiska platformy Azure z konta administratora usługi, które używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, lub jeśli za pomocą (Chiny) usługi Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Plik PFX certyfikatu należy umieścić w tym katalogu, jak również. | _Opcjonalnie_ (_obowiązkowe_ dla wielowęzłowymi) | 
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ | 
| **MaxRetryCount** | Liczba przypadków, o których chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 | 
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 | 
| **Dezinstalacja** | Usuń dostawcę zasobów i wszystkie powiązane zasoby (zobacz poniższe informacje o). | Nie | 
| **DebugMode** | Zapobiega automatycznego czyszczenia po awarii. | Nie | 
| **AcceptLicense** | Pomija monit o zaakceptowanie licencji GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Przykładowy skrypt aktualizacji
Oto przykład użycia *UpdateMySQLProvider.ps1* skrypt, który można uruchomić z konsoli programu PowerShell z podwyższonym poziomem uprawnień. Pamiętaj zmienić informacje o zmiennych i hasła, zgodnie z potrzebami:  

> [!NOTE] 
> Proces aktualizacji dotyczy tylko systemów zintegrowanych. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>Kolejne kroki
[Obsługa dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-maintain.md)
