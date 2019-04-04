---
title: Przy użyciu bazy danych SQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć bazy danych SQL jako usługi w usłudze Azure Stack i Szybkie kroki, które umożliwia wdrażanie karty dostawcy zasobów programu SQL Server.
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
ms.lastreviewed: 03/18/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: ee64106c97a07e1b3ceb84c4ca932b19bc6d83b8
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652607"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Wdrażanie dostawcy zasobów programu SQL Server w usłudze Azure Stack

Użyj dostawcy zasobów usługi Azure Stack programu SQL Server do udostępnienia baz danych SQL jako usługi Azure Stack. Dostawcy zasobów bazy danych SQL działa jako usługa na maszynie wirtualnej (VM) systemu Windows Server 2016 Server Core.

> [!IMPORTANT]
> Tworzenie elementów na serwerach tego hosta SQL lub MySQL jest obsługiwane tylko dostawcy zasobów. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może prowadzić do niezgodnego stanu.

## <a name="prerequisites"></a>Wymagania wstępne

Istnieje kilka wymagań wstępnych, które muszą zostać spełnione przed wdrożeniem dostawcy zasobów usługi Azure Stack SQL. Aby spełnić te wymagania, wykonaj następujące czynności na komputerze, na których mogą uzyskiwać dostęp uprzywilejowany punktu końcowego maszyny Wirtualnej:

- Jeśli użytkownik jeszcze tego nie zrobiono, [rejestrowania usługi Azure Stack](azure-stack-registration.md) z platformą Azure, dzięki czemu można pobrać elementów portalu Azure marketplace.
- Moduły platformy Azure i usługi Azure Stack PowerShell należy zainstalować w systemie, w którym będzie uruchamiany tej instalacji. Ten system musi być obrazem systemu Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET. Zobacz [zainstalować program PowerShell dla usługi Azure Stack](./azure-stack-powershell-install.md).
- Dodaj wymagane core systemu Windows Server maszyny Wirtualnej w portalu Marketplace usługi Azure Stack, pobierając **systemu Windows Server 2016 Datacenter — instalacja Server Core** obrazu.
- Pobierz binarne dostawcy zasobów bazy danych SQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego. Dostawca zasobów ma minimalne odpowiednie usługi Azure Stack kompilacji.

  |Minimalna wersja usługi Azure Stack|Wersja SQL RP|
  |-----|-----|
  |Wersja 1808 (1.1808.0.97)|[SQL RP wersji 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Wersja 1808 (1.1808.0.97)|[SQL RP wersji 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |W wersji 1804 (1.0.180513.1)|[SQL RP wersji 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

- Upewnij się, że zostały spełnione wymagania wstępne integrację centrum danych:

    |Wymagania wstępne|Informacje ogólne|
    |-----|-----|
    |Przesyłanie warunkowe DNS jest prawidłowo.|[Usługa Azure Stack Integracja z centrum danych — DNS](azure-stack-integrate-dns.md)|
    |Porty dla ruchu przychodzącego dla dostawców zasobów są otwarte.|[Azure Stack — Integracja z centrum danych — publikowanie punktów końcowych](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Podmiot certyfikatu PKI i SAN są prawidłowo ustawione.|[Usługa Azure Stack obowiązkowe infrastruktury kluczy publicznych wymagania wstępne dotyczące wdrażania](azure-stack-pki-certs.md#mandatory-certificates)[wymagania wstępne dotyczące usługi Azure Stack wdrożenia PaaS certyfikatu](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certyfikaty

_Zintegrowane systemy tylko dla instalacji_. Musisz podać certyfikat SQL PaaS PKI opisane w sekcji opcjonalnej certyfikaty PaaS z [wymagania dotyczące infrastruktury kluczy publicznych wdrażania usługi Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Umieść plik pfx w lokalizacji określonej przez **DependencyFilesLocalPath** parametru. Nie są oferowane certyfikatu dla systemów ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Wdrażanie dostawcy zasobów bazy danych SQL

Po zainstalowaniu wszystkich wymagań wstępnych można uruchomić **DeploySqlProvider.ps1** skrypt w celu wdrożenia dostawcy zasobów bazy danych SQL. Skrypt DeploySqlProvider.ps1 jest wyodrębniany jako część SQL plik binarny dostawcy zasobów, który został pobrany dla używanej wersji programu Azure Stack.

 > [!IMPORTANT]
 > Przed wdrożeniem dostawcy zasobów, przejrzyj informacje o wersji, aby dowiedzieć się więcej o nowych funkcjach, poprawek i znanych problemach, które mogą wpłynąć na wdrożenie.
 
Aby wdrożyć dostawcy zasobów bazy danych SQL, otwórz **nowe** z podwyższonym poziomem uprawnień okno programu PowerShell (nie PowerShell ISE) i zmiany do katalogu, w którym zostały wyodrębnione pliki binarne dostawcy zasobów programu SQL. Zalecamy używanie nowe okno programu PowerShell, aby uniknąć potencjalnych problemów, spowodowane przez moduły programu PowerShell, które zostały już załadowane.

Uruchom skrypt DeploySqlProvider.ps1, który wykonuje następujące zadania:

- Służy do przekazywania certyfikatów i innych artefaktów z kontem magazynu w usłudze Azure Stack.
- Publikuje pakiety galerii, aby umożliwić wdrażanie baz danych SQL przy użyciu galerii.
- Publikuje pakietu galerii do wdrażania serwerów hostingu.
- Wdraża maszynę Wirtualną przy użyciu obrazu systemu Windows Server 2016 core pobrany, a następnie instaluje dostawcy zasobów bazy danych SQL.
- Rejestruje lokalne rekord DNS, który jest mapowany do Twojego dostawcę zasobów maszyny Wirtualnej.
- Rejestruje dostawcę zasobów za pomocą lokalnej usługi Azure Resource Manager dla konta operatora.

> [!NOTE]
> Po rozpoczęciu wdrażania dostawcy zasobów programu SQL, **system.local.sqladapter** zostanie utworzona grupa zasobów. Może upłynąć do 75 minut na zakończenie wymaganych wdrożeń do tej grupy zasobów.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters

Można określić następujące parametry, z poziomu wiersza polecenia. Jeśli nie istnieje lub dowolnym Walidacja parametru nie powiedzie się, zostanie wyświetlony monit zapewnić wymagane parametry.

| Nazwa parametru | Opis | Komentarz lub wartość domyślną |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury, niezbędne do uzyskania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów bazy danych SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **AzureEnvironment** | Środowiska platformy Azure konto administratora usługi, które są używane do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, lub jeśli za pomocą (Chiny) usługi Azure Active Directory, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Tylko zintegrowane systemy plik PFX certyfikatu musi być umieszczane w tym katalogu. Opcjonalnie można skopiować jeden pakiet Windows Update MSU tutaj. | _Opcjonalnie_ (_obowiązkowe_ w ramach zintegrowanych systemów) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Liczba przypadków, o których chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcę zasobów i wszystkie powiązane zasoby (zobacz poniższe informacje o). | Nie |
| **DebugMode** | Zapobiega automatycznego czyszczenia po awarii. | Nie |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Wdrażanie dostawcy zasobów bazy danych SQL przy użyciu niestandardowego skryptu

Aby wyeliminować żadnej ręcznej konfiguracji podczas wdrażania dostawcy zasobów, można dostosować poniższy skrypt.  

Zmień domyślne informacje konta i hasła dla danego wdrożenia usługi Azure Stack.


```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Po zakończeniu działania skryptu instalacji dostawcy zasobów, Odśwież przeglądarkę, aby upewnić się, że można sprawdzić najnowsze aktualizacje.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure Stack

Można użyć następujących kroków Sprawdź dostawcy zasobów bazy danych SQL została pomyślnie wdrożona.

1. Zaloguj się do portalu administracyjnego jako administratora usługi.
2. Wybierz **grup zasobów**.
3. Wybierz **systemu.\< Lokalizacja\>.sqladapter** grupy zasobów.
4. Na stronie Podsumowanie dla grupy zasobów — omówienie powinno istnieć żadne wdrożenia nie powiodło się.
      ![Sprawdź wdrażanie dostawcy zasobów bazy danych SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. Na koniec wybierz pozycję **maszyn wirtualnych** w portalu administracyjnym, aby sprawdzić dostawcy zasobów bazy danych SQL, maszyna wirtualna została pomyślnie utworzone i jest uruchomiona.

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie serwerów hostingu](azure-stack-sql-resource-provider-hosting-servers.md)
