---
title: Przy użyciu baz danych MySQL na stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak mogą wdrażać baz danych MySQL jako usługa na stosie Azure i Szybkie kroki wdrażania karty dostawcy zasobów serwera MySQL.
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
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938117"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Wdrażanie dostawcy zasobów MySQL na stosie Azure

Dostawcy zasobów programu MySQL Server umożliwia uwidocznić baz danych MySQL jako usługa Azure stosu. Dostawca zasobów MySQL działa jako usługa na maszynie wirtualnej systemu Windows Server 2016 Server Core (VM).

## <a name="prerequisites"></a>Wymagania wstępne

Istnieje kilka wymagań wstępnych, które muszą zostać spełnione przed wdrożeniem MySQL stosu Azure dostawcy zasobów. Aby spełnić te wymagania, należy wykonać kroki opisane w tym artykule na komputerze, który można uzyskać dostępu do uprzywilejowanych punktu końcowego maszyny Wirtualnej.

* Jeśli jeszcze tego nie zrobiono, [zarejestrować stosu Azure](.\azure-stack-registration.md) z platformy Azure, aby pobrać elementów portalu Azure marketplace.
* Dodaj wymagane core systemu Windows Server maszyny Wirtualnej do stosu Azure marketplace, pobierając **systemu Windows Server Datacenter 2016 - Server Core** obrazu. Umożliwia także skrypt do utworzenia [obrazu systemu Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Upewnij się, że zostanie wybrana opcja core po uruchomieniu skryptu.

  >[!NOTE]
  >Jeśli musisz zainstalować aktualizację, należy zaznaczyć jeden. Pakiet MSU w ścieżce lokalnej zależności. Jeśli więcej niż jeden. MSU plik zostanie odnaleziony, spowoduje to niepowodzenie instalacji dostawcy zasobów MySQL.

* Pobierz dostawcę zasobów MySQL binarnego, a następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego.

  >[!NOTE]
  >Aby wdrożyć dostawcy MySQL na komputerze, który nie ma dostępu do Internetu, skopiuj [mysql — łącznik net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) plików do udziału lokalnego. Podaj nazwę udziału, gdy zostanie wyświetlony monit o jej. Należy zainstalować moduły Azure oraz Azure PowerShell stosu.

* Dostawca zasobów ma minimalną odpowiedniego stosu Azure kompilacji. Upewnij się, że możesz pobrać poprawne dane binarne dla wersji stosu Azure, której używasz.

    | Wersja Azure stosu | Wersja MySQL RP|
    | --- | --- |
    | Wersja 1804 (1.0.180513.1)|[MySQL RP wersji 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | Wersja 1802 (1.0.180302.1) | [MySQL RP wersji 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Wersja 1712 (1.0.180102.3 lub 1.0.180106.1 (zintegrowanych systemów)) | [MySQL RP wersji 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certyfikaty

Dla ASDK certyfikatu z podpisem własnym jest tworzony w ramach tego procesu instalacji. System Azure stosu zintegrowane musisz podać odpowiedni certyfikat. Jeśli musisz dostarczyć własny certyfikat, należy umieścić plik pfx w **DependencyFilesLocalPath** który spełnia następujące kryteria:

* Certyfikat uniwersalny dla \*.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\> lub certyfikat w jednej lokacji z nazwą pospolitą mysqladapter.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\>.
* Certyfikat musi być zaufany. Łańcuch zaufania musi istnieć bez konieczności używania certyfikatów pośrednich.
* Istnieje tylko jeden certyfikat plik w DependencyFilesLocalPath.
* Nazwa pliku nie może mieć żadnych znaków specjalnych ani spacji.

## <a name="deploy-the-resource-provider"></a>Wdrażanie dostawcy zasobów

Po skonfigurowaniu wszystkie wymagania wstępne zainstalowane, uruchom **DeployMySqlProvider.ps1** skrypt do wdrażania MYSQL dostawcy zasobów. Skrypt DeployMySqlProvider.ps1 jest wyodrębniany jako część binarnego dostawcy zasobów MySQL, który został pobrany dla używanej wersji programu Azure stosu.

> [!IMPORTANT]
> Skrypt uruchomiony w systemie musi być system Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET zainstalowane.

Aby wdrożyć dostawcy zasobów MySQL, Otwórz okno konsoli programu PowerShell nowych z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym wyodrębniono pliki binarne dostawcy zasobów MySQL. Zalecamy używanie nowe okno programu PowerShell, aby uniknąć potencjalnych problemów powodowanych przez moduły programu PowerShell, które zostały już załadowane.

Uruchom **DeployMySqlProvider.ps1** skrypt, który wykonuje następujące zadania:

* Przekazuje certyfikaty i inne artefaktów na konto magazynu Azure stosu.
* Publikuje pakietów galerii, dzięki czemu można wdrożyć za pomocą galerii baz danych MySQL.
* Publikuje pakietu galerii wdrażania serwerami hostingu.
* Wdraża maszynę Wirtualną przy użyciu obrazu core systemu Windows Server 2016 pobrana, a następnie instaluje dostawcę zasobów MySQL.
* Rejestruje rekord DNS lokalnego mapy do dostawcy zasobów maszyny Wirtualnej.
* Rejestruje dostawcę zasobów z lokalnej usługi Azure Resource Manager dla operatora i kontami użytkowników.
* Opcjonalnie instaluje pojedynczej aktualizacji systemu Windows Server podczas instalacji dostawcy zasobów.

> [!NOTE]
> Po uruchomieniu wdrożenia dostawcy zasobów MySQL, **system.local.mysqladapter** utworzona grupa zasobów. Może upłynąć do 75 minut na zakończenie cztery wdrożenia wymagane do tej grupy zasobów.

### <a name="deploymysqlproviderps1-parameters"></a>Parametry DeployMySqlProvider.ps1

Można określić następujące parametry wiersza polecenia. Jeśli nie istnieje lub wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów MySQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Ścieżka do udziału lokalnego, który zawiera [mysql — łącznik net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Jeśli podasz jednej z tych ścieżek pliku certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ dla jednego węzła, _obowiązkowe_ dla wieloma węzłami. |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |
| **AcceptLicense** | Pomija wiersz, aby zaakceptować licencji License.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Nie można utworzyć bazy danych, dopóki jednostka SKU jest wdrożone i działa.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Wdrażanie przy użyciu skryptu niestandardowego dostawcę zasobów MySQL

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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Po zakończeniu działania skryptu instalacji dostawcy zasobów, Odśwież przeglądarkę, aby upewnić się, że zostaną wyświetlone najnowsze aktualizacje.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure stosu

1. Zaloguj się do portalu administratora jako administratora usługi.
2. Wybierz **grup zasobów**
3. Wybierz **systemu.\< Lokalizacja\>.mysqladapter** grupy zasobów.
4. Na stronie Podsumowanie dla zasobu omówienie, komunikat w obszarze grupy **wdrożeń** powinien być **3 zakończyło się pomyślnie**.
5. Można uzyskać więcej szczegółowych informacji o wdrażaniu dostawcy zasobów w obszarze **ustawienia**. Wybierz **wdrożeń** uzyskanie informacji, takich jak: stan, sygnatura CZASOWA i czasu trwania dla każdego wdrożenia.

## <a name="next-steps"></a>Kolejne kroki

[Dodaj serwery hostingu](azure-stack-mysql-resource-provider-hosting-servers.md)
