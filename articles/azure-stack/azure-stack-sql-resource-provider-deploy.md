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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938336"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Wdrażanie dostawcy zasobów programu SQL Server na stosie Azure

Umożliwia udostępnianie baz danych jako usługę Azure stosu dostawcy zasobów Azure stosu programu SQL Server. Dostawca zasobów SQL działa jako usługa na maszynie wirtualnej systemu Windows Server 2016 Server Core (VM).

## <a name="prerequisites"></a>Wymagania wstępne

Istnieje kilka wymagań wstępnych, które muszą zostać spełnione przed wdrożeniem dostawcy zasobów usługi Azure SQL stosu. Aby spełnić te wymagania, wykonaj następujące czynności na komputerze, który można uzyskać dostępu do uprzywilejowanych punktu końcowego maszyny Wirtualnej:

- Jeśli jeszcze tego nie zrobiono, [zarejestrować stosu Azure](.\azure-stack-registration.md) z platformy Azure, aby pobrać elementów portalu Azure marketplace.
- Dodaj wymagane core systemu Windows Server maszyny Wirtualnej do stosu Azure marketplace, pobierając **systemu Windows Server Datacenter 2016 - Server Core** obrazu. Umożliwia także skrypt do utworzenia [obrazu systemu Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Upewnij się, że zostanie wybrana opcja core po uruchomieniu skryptu.

  >[!NOTE]
  >Jeśli musisz zainstalować aktualizację, pojedynczy pakiet MSU można umieścić w ścieżce lokalnej zależności. Jeśli zostanie znaleziony więcej niż jeden plik MSU, instalację dostawcy zasobów SQL zakończy się niepowodzeniem.

- Pobierz dostawcę zasobów SQL binarnego, a następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego. Dostawca zasobów ma minimalną odpowiedniego stosu Azure kompilacji. Upewnij się, że możesz pobrać poprawne dane binarne dla wersji stosu Azure, której używasz.

    |Wersja Azure stosu|Wersja SQL RP|
    |-----|-----|
    |Wersja 1804 (1.0.180513.1)|[RP SQL w wersji 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Wersja 1802 (1.0.180302.1)|[RP SQL w wersji 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Wersja 1712 (1.0.180102.3, 1.0.180103.2 lub 1.0.180106.1 (zintegrowanych systemów))|[RP SQL w wersji 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Certyfikaty

Tylko na potrzeby zintegrowanych systemów instalacji. Należy podać certyfikat SQL PaaS PKI, opisane w sekcji opcjonalnej certyfikaty PaaS z [wymagania dotyczące infrastruktury kluczy publicznych wdrażania stosu Azure](.\azure-stack-pki-certs.md#optional-paas-certificates). Umieść plik pfx w lokalizacji określonej przez **DependencyFilesLocalPath** parametru.

## <a name="deploy-the-sql-resource-provider"></a>Wdrażanie dostawcy zasobów SQL

Po skonfigurowaniu wszystkie wymagania wstępne zainstalowane, uruchom **DeploySqlProvider.ps1** skrypt do wdrażania dostawcy zasobów SQL. Skrypt DeploySqlProvider.ps1 jest wyodrębniany jako część pliku binarnego dostawcy zasobów SQL pobrany dla używanej wersji programu Azure stosu.

> [!IMPORTANT]
> Skrypt uruchomiony w systemie musi być system Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET zainstalowane.

Aby wdrożyć dostawcy zasobów SQL, otwórz **nowe** oknie konsoli programu PowerShell z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym wyodrębniono pliki binarne dostawcy zasobów SQL. Zalecamy używanie nowe okno programu PowerShell, aby uniknąć potencjalnych problemów powodowanych przez moduły programu PowerShell, które zostały już załadowane.

Uruchom skrypt DeploySqlProvider.ps1, który realizuje następujące zadania:

- Przekazuje certyfikaty i inne artefaktów na konto magazynu Azure stosu.
- Publikuje galerii pakietów, aby umożliwić wdrażanie baz danych SQL za pomocą galerii.
- Publikuje pakietu galerii wdrażania serwerami hostingu.
- Wdraża maszynę Wirtualną przy użyciu obrazu core systemu Windows Server 2016 pobrana, a następnie instaluje dostawcę zasobów SQL.
- Rejestruje rekord DNS lokalnego mapy do dostawcy zasobów maszyny Wirtualnej.
- Rejestruje dostawcę zasobów z lokalnej usługi Azure Resource Manager dla operatora i kontami użytkowników.
- Opcjonalnie instaluje pojedynczej aktualizacji systemu Windows Server podczas instalacji dostawcy zasobów.

> [!NOTE]
> Po uruchomieniu wdrożenia dostawcy zasobów SQL, **system.local.sqladapter** utworzona grupa zasobów. Może upłynąć do 75 minut na zakończenie cztery wymaganych wdrożeń do tej grupy zasobów.

### <a name="deploysqlproviderps1-parameters"></a>Parametry DeploySqlProvider.ps1

Można określić następujące parametry wiersza polecenia. Jeśli nie istnieje lub wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Plik .pfx certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ (_obowiązkowe_ zintegrowanych systemów) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |

>[!NOTE]
> Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Nie można utworzyć bazy danych, dopóki jednostka SKU jest wdrożone i działa.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Wdrażanie przy użyciu skryptu niestandardowego dostawcę zasobów SQL

Aby usunąć wszystkie ręczne konfiguracje podczas wdrażania dostawcy zasobów, można dostosować poniższy skrypt. Zmień domyślny informacje o koncie i hasła dla danego wdrożenia stosu Azure.

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

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Po zakończeniu działania skryptu instalacji dostawcy zasobów, Odśwież przeglądarkę, aby upewnić się, że zostaną wyświetlone najnowsze aktualizacje.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure stosu

Można użyć następujących kroków Sprawdź dostawcy zasobów SQL zostanie pomyślnie wdrożona.

1. Zaloguj się do portalu administratora jako administratora usługi.
2. Wybierz **grup zasobów**.
3. Wybierz **systemu.\< Lokalizacja\>.sqladapter** grupy zasobów.
4. Komunikat w obszarze **wdrożeń**, pokazano w następnym Przechwytywanie ekranu, powinny być **4 zakończyło się pomyślnie**.

      ![Sprawdź wdrożenie dostawcy zasobów SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Można uzyskać więcej szczegółowych informacji o wdrażaniu dostawcy zasobów w obszarze **ustawienia**. Wybierz **wdrożeń** uzyskanie informacji, takich jak: stan, sygnatura CZASOWA i czasu trwania dla każdego wdrożenia.

## <a name="next-steps"></a>Kolejne kroki

[Dodaj serwery hostingu](azure-stack-sql-resource-provider-hosting-servers.md)
