---
title: Użyj bazy danych MySQL jako PaaS w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć MySQL dostawcy zasobów i zapewnić baz danych MySQL jako usługa na stosie Azure.
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
ms.date: 04/24/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: bc88140bf1adea49ff4bc76667d30a379f829bbc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych MySQL na Microsoft Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Dostawca zasobów MySQL na stosie Azure można wdrożyć. Po wdrożeniu dostawcy zasobów, można utworzyć MySQL serwerów i baz danych za pośrednictwem usługi Azure Resource Manager deployment szablonów. Można też podać baz danych MySQL jako usługa. 

Bazy danych MySQL, które są wspólne w witrynach sieci web, obsługuje wiele platform witryny sieci Web. Na przykład po wdrożeniu dostawcy zasobów można tworzenia witryn sieci Web WordPress z platformy aplikacji sieci Web jako dodatek usługa (PaaS) Azure stosu.

Aby wdrożyć dostawcy MySQL na komputerze, który nie ma dostępu do Internetu, skopiuj plik [mysql — łącznik net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) do udziału lokalnego. Następnie podaj nazwę tego udziału po wyświetleniu monitu dla niego. Należy zainstalować moduły Azure oraz Azure PowerShell stosu.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Architektura karty dostawcy zasobów serwer MySQL

Dostawca zasobów składa się z trzech składników:

- **Karta Dostawca zasobów MySQL maszyny Wirtualnej**, która jest maszyny wirtualnej systemu Windows, w którym są uruchomione usługi dostawcy.

- **Dostawca zasobów sam**, który przetwarza żądania alokacji i ujawnia bazy danych zasobów.

- **Serwery obsługujące serwer MySQL**, które zapewniają pojemność baz danych, które są nazywane serwerów hosta.

Ta wersja nie tworzy już wystąpień MySQL. Oznacza to, że trzeba je utworzyć samodzielnie i/lub zapewniają dostęp do zewnętrznych wystąpień programu SQL. Odwiedź stronę [galerii Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) przykład szablonu, który można:
- Tworzy serwer MySQL.
- Pobieranie i wdrażanie serwera MySQL z portalu Azure Marketplace.

> [!NOTE]
> Hosting serwerów, które są zainstalowane na stosie Azure zintegrowanych systemów musi zostać utworzona z subskrypcji dzierżawcy. Nie można ich utworzyć z subskrypcji dostawcy domyślnego. Muszą one zostać utworzone w portalu dzierżawcy lub w sesji programu PowerShell z odpowiednią logowania. Wszystkie serwery hostingu maszyn wirtualnych mogą być obciążane i musi mieć odpowiednie licencje. Administrator usługi może być właścicielem subskrypcji dzierżawcy.

### <a name="required-privileges"></a>Wymagane uprawnienia
Konto system musi mieć następujące uprawnienia:

1.  Baza danych: Utwórz, Porzuć
2.  Logowania: Tworzenie, ustawić, porzucić, przydzielić, odwołać

## <a name="deploy-the-resource-provider"></a>Wdrażanie dostawcy zasobów

1. Jeśli jeszcze nie zarejestrować Twojego zestawu programowanie i pobranie obrazu systemu Windows Server 2016 Datacenter Core do pobrania za pośrednictwem zarządzania Marketplace. Należy użyć obrazu systemu Windows Server 2016 Core. Umożliwia także skrypt do utworzenia [obrazu systemu Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Pamiętaj wybrać opcję core.)


2. Zaloguj się do hosta, który można uzyskać dostępu do uprzywilejowanych punktu końcowego maszyny Wirtualnej.

    - W przypadku instalacji zestawu Azure SDK Zaloguj się do hosta fizycznego. 
    - W systemach z wieloma węzłami host musi być systemu, w którym można uzyskać dostępu do uprzywilejowanych punktu końcowego.
    
    >[!NOTE]
    > System, na którym skrypt jest uruchamiany *musi* jako system Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET zainstalowane. W przeciwnym razie niepowodzenia instalacji. Host zestawu SDK platformy Azure stosu spełnia tego kryterium.
    

3. Pobierz dostawcę zasobów MySQL binarnego. Następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego.

    >[!NOTE] 
    > Dostawca zasobów ma minimalną odpowiedniego stosu Azure kompilacji. Pamiętaj pobrać poprawne dane binarne dla wersji stosu Azure, w którym jest uruchomiony.

    | Azure kompilacji stosu | Instalator MySQL RP |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP wersji 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 lub 1.0.180106.1 (wielowęzłowego) | [MySQL RP wersji 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP wersji 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP wersji 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Dla zestawu Azure SDK certyfikatu z podpisem własnym jest tworzony w ramach tego procesu. Wieloma węzłami trzeba podać odpowiedni certyfikat.

    Jeśli musisz dostarczyć własny certyfikat, należy umieścić plik pfx w **DependencyFilesLocalPath** który spełnia następujące kryteria:

    - Certyfikat uniwersalny dla \*.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\> lub certyfikat w jednej lokacji z nazwą pospolitą mysqladapter.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\>.

    - Ten certyfikat musi być zaufany. Oznacza to łańcuch zaufania musi istnieć bez konieczności używania certyfikatów pośrednich.

    - Istnieje tylko jeden certyfikat plik w DependencyFilesLocalPath.
    
    - Nazwa pliku nie może zawierać żadnych znaków specjalnych ani spacji.


5. Otwórz **nowe** z podwyższonym poziomem uprawnień (administracyjnych) konsoli programu PowerShell. Następnie przejdź do katalogu, w którym zostały wyodrębnione pliki. Użyj nowego okna, aby uniknąć problemów, które mogą wystąpić z niepoprawne moduły programu PowerShell, które zostały już załadowane w systemie.

6. [Zainstaluj program Azure PowerShell w wersji 1.2.11](azure-stack-powershell-install.md).

7. Uruchom skrypt `DeployMySqlProvider.ps1`.

Skrypt wykonuje następujące czynności:

* Pobiera dane binarne MySQL łącznika (to można podać w trybie offline).
* Przekazuje certyfikaty i inne artefaktów na konto magazynu Azure stosu.
* Publikowanie pakietów galerii tak, aby umożliwić wdrożenie bazy danych SQL za pomocą galerii.
* Publikuje pakietu galerii wdrażania serwerami hostingu.
* Wdraża maszynę Wirtualną za pomocą obrazu systemu Windows Server 2016, który został utworzony w kroku 1. Instaluje dostawcę zasobów.
* Rejestruje rekord DNS lokalnego mapy do dostawcy zasobów maszyny Wirtualnej.
* Rejestruje dostawcę zasobów z lokalnej usługi Azure Resource Manager (dzierżawcy i administratora).


Możesz:
- Określ wymagane parametry w wierszu polecenia.
- Uruchom bez żadnych parametrów, a następnie wprowadź je po wyświetleniu monitu.

Oto przykład można uruchamiać w wierszu polecenia programu PowerShell. Pamiętaj zmienić informacje o koncie i hasła w razie potrzeby:


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
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>Parametry DeployMySqlProvider.ps1
Te parametry można określić w wierszu polecenia. Jeśli nie chcesz, lub jeśli wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit o zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** | Poświadczenia dla konta administratora lokalnego dostawcy zasobów MySQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Ścieżka do udziału lokalnego, który zawiera [mysql — łącznik net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Jeśli podasz jednej z tych ścieżek pliku certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ (_obowiązkowe_ dla wielowęzłowego) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx. | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuwa dostawcy zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |
| **AcceptLicense** | Pomija wiersz, aby zaakceptować licencji License.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |

>[!NOTE]
> Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Nie można utworzyć bazy danych, dopóki nie zostanie utworzona jednostka SKU.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure stosu

> [!NOTE]
>  Po zakończeniu skrypt instalacji należy odświeżyć portalu, zobacz blok administratora.


1. Zaloguj się do portalu administratora jako administratora usługi.

2. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Przejdź do **grup zasobów**, a następnie wybierz **systemu.\< Lokalizacja\>.mysqladapter** grupy zasobów. Sprawdź, czy wszystkie cztery wdrożenia zakończyło się pomyślnie.

      ![Sprawdź wdrożenie MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Zapewniają pojemność, łącząc się z serwerem hostingowym MySQL

1. Zaloguj się do portalu Azure stosu jako administratora usługi.

2. Wybierz **zasobów administracyjnych** > **MySQL serwerów hosta** > **+ Dodaj**.

    Na **Hosting serwerów MySQL** bloku można nawiązać dostawcy zasobów serwer MySQL bieżące wystąpienia serwera MySQL służące jako wewnętrzna dostawcy zasobów.

    ![Serwerów hosta](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Podaj szczegóły połączenia z wystąpieniem serwera MySQL. Należy podać pełną nazwę domeny (FQDN) lub prawidłowy adres IPv4, a nie krótką nazwę maszyny Wirtualnej. Ta instalacja nie jest już zawiera domyślne wystąpienie MySQL. Rozmiar, który został dostarczony ułatwia zarządzanie wydajnością bazy danych dostawcy zasobów. Należy go blisko fizycznych zdolności produkcyjnych serwera bazy danych.

    > [!NOTE]
    >Jeśli dzierżawcy i administratora usługi Azure Resource Manager można uzyskać dostępu do wystąpienia MySQL, mogą być umieszczone pod kontrolą dostawcy zasobów. Wystąpienie programu MySQL *musi* można przydzielić wyłącznie do dostawcy zasobów.

4. W miarę dodawania serwerów, należy je przypisać do nowej lub istniejącej jednostki SKU umożliwiają rozróżnienie ofert usług.
  Na przykład można mieć wystąpieniem przedsiębiorstwa dostarczanie:
    - wydajność bazy danych
    - Automatyczne kopie zapasowe
    - Zarezerwuj serwerów o wysokiej wydajności dla poszczególnych działów


  > [!IMPORTANT]
  > Nie można mieszać serwerów autonomicznych z wystąpieniami zawsze włączone w tej samej jednostki SKU. Próba mieszać typów po dodaniu pierwszego hostingu wyników z serwera błąd.
 

Nazwa jednostki SKU powinien odzwierciedlać właściwości, dzięki czemu dzierżaw odpowiednio umieszczać swoje bazy danych. Wszystkie serwery hostingu w jednostce SKU powinny mieć takie same możliwości.

![Utwórz MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)





## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Przetestować wdrożenie, tworząc pierwszą bazę danych MySQL


1. Zaloguj się do portalu Azure stosu jako administratora usługi.

2. Wybierz **+ nowy** > **dane i magazyn** > **baza danych MySQL**.

3. Podaj szczegóły bazy danych.

    ![Tworzenie nowego testu baza danych MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Wybierz jednostki SKU.

    ![Wybierz jednostki SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Utwórz ustawienie logowania. Można ponownie użyć istniejącego ustawienia logowania lub Utwórz nową. To ustawienie zawiera nazwę użytkownika i hasło dla bazy danych.

    ![Utwórz nowe nazwy logowania bazy danych](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    Ciąg połączenia zawiera nazwę serwera bazy danych rzeczywistych. Skopiuj go z portalu.

    ![Pobierz ciąg połączenia bazy danych MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> Długość nazwy użytkownika nie może przekraczać 32 znaków MySQL 5.7. We wcześniejszych wersjach go nie może przekraczać 16 znaków.


## <a name="add-capacity"></a>Zwiększanie pojemności

Dodaj pojemność przez dodanie dodatkowych serwerów MySQL w portalu Azure stosu. Dodatkowe serwery można dodać do nowej lub istniejącej jednostki SKU. Upewnij się, że właściwości serwera są takie same.


## <a name="make-mysql-databases-available-to-tenants"></a>Udostępnij dzierżawcom baz danych MySQL
Tworzenie planów i oferty, aby udostępnić baz danych MySQL dla dzierżawców. Na przykład Dodaj usługę Microsoft.MySqlAdapter, Dodaj limit przydziału i tak dalej.

![Tworzenie planów i ofert do dołączenia bazy danych](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Zaktualizuj hasło administracyjne
Hasło można modyfikować, zmieniając go pierwszym wystąpieniu serwera MySQL. Wybierz **zasobów administracyjnych** > **MySQL serwerów hosta**. Następnie wybierz serwer hostingu. W **ustawienia** panelu, wybierz opcję **hasło**.

![Zaktualizuj hasło administratora](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Zaktualizuj kartę Dostawca zasobów MySQL (wielowęzłowego tylko kompilacje 1710 i nowsze)
Nowa karta dostawcy zasobów programu SQL może być zwolnione po zaktualizowaniu kompilacje stosu Azure. Gdy istniejącej karty nadal działać, zaleca się jak najszybciej aktualizacja do nowszej kompilacji. 

Do aktualizacji, należy użyć dostawcy zasobów *UpdateMySQLProvider.ps1* skryptu. Proces jest podobny do procesu używane do instalowania dostawcy zasobów, zgodnie z opisem w [wdrażanie dostawcy zasobów](#deploy-the-resource-provider) sekcji tego artykułu. Skrypt jest dołączana do pobierania dostawcy zasobów.

*UpdateMySQLProvider.ps1* skrypt tworzy nową maszynę Wirtualną z najnowszą kod dostawcy zasobów i przeprowadzanie migracji ustawień za pomocą starego maszyny Wirtualnej do nowej maszyny Wirtualnej. Ustawienia do zmigrowania obejmują bazy danych i informacji o serwerze hosting i rekordu DNS niezbędne.

Skrypt wymaga użycia te same argumenty, które są opisane DeployMySqlProvider.ps1 skryptu. Podaj certyfikat tutaj również. 

Poniżej przedstawiono przykład *UpdateMySQLProvider.ps1* skrypt, który można uruchomić w wierszu polecenia programu PowerShell. Pamiętaj zmienić informacje o koncie i hasła w razie potrzeby: 

> [!NOTE]
> Proces aktualizacji dotyczy tylko systemów zintegrowanego.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Parametry UpdateMySQLProvider.ps1
Te parametry można określić w wierszu polecenia. Jeśli nie istnieje lub wszystkich parametrów sprawdzania poprawności zakończy się niepowodzeniem, zostanie wyświetlony monit zapewnić wymagane parametry.

| Nazwa parametru | Opis | Wartość domyślna lub komentarz |
| --- | --- | --- |
| **CloudAdminCredential** | Poświadczenia dla administratora chmury niezbędne do uzyskiwania dostępu do uprzywilejowanych punktu końcowego. | _Wymagane_ |
| **AzCredential** | Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, używane do wdrażania usługi Azure stosu. | _Wymagane_ |
| **VMLocalCredential** |Poświadczenia dla konta administratora lokalnego dostawcy zasobów SQL maszyny Wirtualnej. | _Wymagane_ |
| **PrivilegedEndpoint** | Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego. |  _Wymagane_ |
| **DependencyFilesLocalPath** | Plik .pfx certyfikatu muszą znajdować się w tym również katalogu. | _Opcjonalne_ (_obowiązkowe_ dla wielowęzłowego) |
| **DefaultSSLCertificatePassword** | Hasło dla certyfikatu pfx | _Wymagane_ |
| **MaxRetryCount** | Ile razy chcesz ponowić próbę każdej operacji w przypadku awarii.| 2 |
| **RetryDuration** | Interwał limitu czasu między kolejnymi próbami w sekundach. | 120 |
| **Dezinstalacja** | Usuń dostawcę zasobów i wszystkie powiązane zasoby (zobacz uwagi następujące). | Nie |
| **DebugMode** | Uniemożliwia automatyczne czyszczenie w przypadku awarii. | Nie |
| **AcceptLicense** | Pomija wiersz, aby zaakceptować licencji License.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>Zbieranie dzienników diagnostycznych
Dostawca zasobów MySQL jest zablokowane maszynę wirtualną. Jeśli okaże się konieczne zbieranie dzienników z maszyny wirtualnej, a punkt końcowy programu PowerShell tylko tyle administracyjnej (JEA) _DBAdapterDiagnostics_ znajduje się w tym celu. Dostępne są dwa polecenia za pomocą tego punktu końcowego:

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
Dostawca zasobów MySQL jest zablokowane maszynę wirtualną. Aktualizacja zabezpieczeń maszyny wirtualnej dostawcy zasobów może odbywać się za pośrednictwem punktu końcowego programu PowerShell tylko tyle administracyjnej (JEA) _DBAdapterMaintenance_.

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

2. Tworzenie sesji programu PowerShell do punktu końcowego obsługi maszyny wirtualnej karty MySQL RP
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>Usuń kartę Dostawca zasobów MySQL

Aby usunąć dostawcę zasobów, konieczne jest najpierw usunąć wszelkie zależności.

1. Upewnij się, że masz oryginalny pakiet wdrożeniowy, który został pobrany dla tej wersji dostawcy zasobów.

2. Należy usunąć wszystkie dzierżawy bazy danych od dostawcy zasobów. (Usuwanie baz danych dzierżawy nie powoduje usunięcia danych.) To zadanie należy wykonać przez dzierżawców, samodzielnie.

3. Dzierżawcy wyrejestrować z przestrzeni nazw.

4. Administrator musi usunąć serwerami hostingu z karty MySQL.

5. Administrator, należy usunąć wszystkie plany, które odwołują się do karty MySQL.

6. Administrator musi usunąć wszelkie przydziały, które są skojarzone z kartą MySQL.

7. Uruchom ponownie skrypt wdrożenia wraz z następującymi elementami:
    - Odinstalować parametru
    - Punkty końcowe usługi Azure Resource Manager
    - DirectoryTenantID
    - Poświadczenia dla konta administratora usługi
