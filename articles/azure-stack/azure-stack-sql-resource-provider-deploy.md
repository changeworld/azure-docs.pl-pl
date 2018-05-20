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
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych SQL Microsoft Azure stosu
Dostawcy zasobów Azure stosu programu SQL Server umożliwia udostępnianie baz danych jako usługa Azure stosu. Uruchamia usługę dostawcy zasobów programu SQL Server na dostawcy zasobów SQL maszyny Wirtualnej, który jest maszyną wirtualną systemu Windows Server core.

## <a name="prerequisites"></a>Wymagania wstępne
Istnieje kilka wymagań wstępnych, które muszą zostać spełnione przed wdrożeniem dostawcy zasobów usługi Azure SQL stosu. Na komputerze, który można uzyskać dostępu do uprzywilejowanych punktu końcowego maszyny Wirtualnej, wykonaj następujące czynności:

- Jeśli użytkownik nie zostało jeszcze zrobione, [zarejestrować stosu Azure](.\azure-stack-registration.md) z platformy Azure, tak, aby można pobrać elementów portalu Azure marketplace.
- Dodaj wymagane core systemu Windows Server maszyny Wirtualnej do stosu Azure marketplace, pobierając **2016 serwera Windows Server core** obrazu. Jeśli musisz zainstalować aktualizację, należy zaznaczyć jeden. Pakiet MSU w ścieżce lokalnej zależności. Jeśli więcej niż jeden. MSU plik zostanie odnaleziony, spowoduje to niepowodzenie instalacji dostawcy zasobów SQL.
- Pobierz dostawcę zasobów SQL binarnego, a następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego. Dostawca zasobów ma minimalną odpowiedniego stosu Azure kompilacji. Pamiętaj pobrać poprawne dane binarne dla wersji stosu Azure, które są uruchomione:
    - Azure stosu wersji 1802 (1.0.180302.1): [RP SQL w wersji 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Azure stosu wersji 1712 (1.0.180102.3, 1.0.180103.2 lub 1.0.180106.1 (zintegrowanych systemów)): [RP SQL w wersji 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- Tylko na potrzeby zintegrowanych systemów instalacji, należy podać certyfikat SQL PaaS PKI zgodnie z opisem w sekcji opcjonalnej certyfikaty PaaS z [wymagania dotyczące infrastruktury kluczy publicznych wdrażania stosu Azure](.\azure-stack-pki-certs.md#optional-paas-certificates), umieszczając plik pfx w lokalizacji określony przez **DependencyFilesLocalPath** parametru.
- Upewnij się, że masz [najnowszą wersję programu Azure PowerShell stosu](.\azure-stack-powershell-install.md) zainstalowany (v1.2.11). 

## <a name="deploy-the-sql-resource-provider"></a>Wdrażanie dostawcy zasobów SQL
Po przygotowaniu pomyślnie zainstalować dostawcę zasobów SQL przez spełnia wszystkie wymagania wstępne, można teraz uruchomić **DeploySqlProvider.ps1** skrypt do wdrażania dostawcy zasobów SQL. Skryptu DeploySqlProvider.ps1 jest wyodrębniany w ramach dostawcy zasobów SQL binarne pobrane odpowiednie do wersji Azure stosu. 

> [!IMPORTANT]
> System, w którym skrypt jest uruchamiany musi być system Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET zainstalowane.


Aby wdrożyć dostawcy zasobów SQL, otwórz konsolę programu PowerShell (administracyjnej) nowych z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym wyodrębniono pliki binarne dostawcy zasobów SQL.

> [!NOTE]
> Użyj nowego okna konsoli programu PowerShell, aby uniknąć problemów, które mogą wystąpić z niepoprawne moduły programu PowerShell, które zostały już załadowane w systemie.

Uruchom skrypt DeploySqlProvider.ps1, który wykonuje następujące czynności:
- Przekazuje certyfikaty i inne artefaktów na konto magazynu Azure stosu.
- Publikowanie pakietów galerii tak, aby umożliwić wdrożenie bazy danych SQL za pomocą galerii.
- Publikuje pakietu galerii wdrażania serwerami hostingu.
- Wdraża maszynę Wirtualną za pomocą obrazu systemu Windows Server 2016, który został utworzony w kroku 1, a następnie instaluje dostawcę zasobów.
- Rejestruje rekord DNS lokalnego mapy do dostawcy zasobów maszyny Wirtualnej.
- Rejestruje dostawcę zasobów z lokalnej usługi Azure Resource Manager (administratora i użytkowników).
- Opcjonalnie instaluje pojedynczej aktualizacji systemu Windows podczas instalacji planu odzyskiwania.

Wdrożenie dostawcy zasobów SQL rozpoczyna się i tworzy system.local.sqladapter grupy zasobów. Może upłynąć do 75 minut cztery wymaganych wdrożeń do tej grupy zasobów.

### <a name="deploysqlproviderps1-parameters"></a>Parametry DeploySqlProvider.ps1
Te parametry można określić w wierszu polecenia. Jeśli nie chcesz, lub jeśli wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, używane do wdrażania usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Plik .pfx certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ (_obowiązkowe_ zintegrowanych systemów) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |

>[!NOTE]
> Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Nie można utworzyć bazy danych, dopóki nie zostanie utworzona jednostka SKU.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Wdrażanie przy użyciu skryptu niestandardowego dostawcę zasobów SQL
Aby uniknąć ręcznego wprowadzania informacji wymaganych po uruchomieniu skryptu DeploySqlProvider.ps1, przez zmianę domyślnych informacji o koncie i hasła w razie potrzeby można dostosować Poniższy przykładowy skrypt:

```powershell
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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure stosu
Kroki opisane w tej sekcji może służyć do zapewnienia dostawcy zasobów SQL została pomyślnie wdrożona.

> [!NOTE]
>  Po zakończeniu skrypt instalacji należy odświeżyć portal administratora bloku i Galeria elementów.

1. Zaloguj się do portalu administratora jako administratora usługi.

2. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Przejdź do **grup zasobów**. Następnie wybierz **systemu.\< Lokalizacja\>.sqladapter** grupy zasobów. Sprawdź, czy wszystkie cztery wdrożenia zakończyło się pomyślnie.

      ![Sprawdź wdrożenie dostawcy zasobów SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Kolejne kroki

[Dodaj serwery hostingu](azure-stack-sql-resource-provider-hosting-servers.md)
