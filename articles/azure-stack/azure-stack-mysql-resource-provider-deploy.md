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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295758"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych MySQL na Microsoft Azure stosu
Dostawcy zasobów Azure stosu MySQL serwera umożliwia udostępnianie baz danych MySQL jako usługa Azure stosu. Uruchamia usługę dostawcy zasobów MySQL na MySQL dostawcy zasobów maszyny Wirtualnej, który jest maszyną wirtualną systemu Windows Server core.

## <a name="deploy-the-resource-provider"></a>Wdrażanie dostawcy zasobów 
1. Jeśli jeszcze nie zarejestrować Twojego zestawu programowanie i pobranie obrazu systemu Windows Server 2016 Datacenter Core do pobrania za pośrednictwem zarządzania Marketplace. Należy użyć obrazu systemu Windows Server 2016 Core. Umożliwia także skrypt do utworzenia [obrazu systemu Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Pamiętaj wybrać opcję core.) 

2. Zaloguj się do hosta, który można uzyskać dostępu do uprzywilejowanych punktu końcowego maszyny Wirtualnej:
    - W przypadku instalacji zestawu Azure SDK Zaloguj się do hosta fizycznego.  
    - W systemach zintegrowanego host musi być systemu, w którym można uzyskać dostępu do uprzywilejowanych punktu końcowego. 
    
    >[!NOTE] 
    > System, na którym skrypt jest uruchamiany *musi* jako system Windows 10 lub Windows Server 2016 z najnowszą wersją środowiska uruchomieniowego .NET zainstalowane. W przeciwnym razie niepowodzenia instalacji. Host ASDK stosu Azure spełnia tego kryterium. 
    
3. Pobierz dostawcę zasobów MySQL binarnego. Następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego. 
    >[!NOTE]  
    > Dostawca zasobów ma minimalną odpowiedniego stosu Azure kompilacji. Pamiętaj pobrać poprawne dane binarne dla wersji stosu Azure, w którym jest uruchomiony.

    | Wersja Azure stosu | Wersja MySQL RP| 
    | --- | --- | 
    | Wersja 1804 (1.0.180513.1)|[MySQL RP wersji 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | Wersja 1802 (1.0.180302.1) | [MySQL RP wersji 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Wersja 1712 (1.0.180102.3 lub 1.0.180106.1 (zintegrowanych systemów)) | [MySQL RP wersji 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  Dla ASDK certyfikatu z podpisem własnym jest tworzony w ramach tego procesu. Dla zintegrowanych systemów należy podać odpowiedni certyfikat. Jeśli musisz dostarczyć własny certyfikat, należy umieścić plik pfx w **DependencyFilesLocalPath** który spełnia następujące kryteria: 
    - Certyfikat uniwersalny dla \*.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\> lub certyfikat w jednej lokacji z nazwą pospolitą mysqladapter.dbadapter.\< region\>.\< nazwy fqdn zewnętrznej\>. 
    - Ten certyfikat musi być zaufany. Oznacza to łańcuch zaufania musi istnieć bez konieczności używania certyfikatów pośrednich. 
    - Istnieje tylko jeden certyfikat plik w DependencyFilesLocalPath. 
    - Nazwa pliku nie może zawierać żadnych znaków specjalnych ani spacji. 

5. Otwórz **nowe** z podwyższonym poziomem uprawnień (administracyjnych) konsoli programu PowerShell. Następnie przejdź do katalogu, w którym zostały wyodrębnione pliki. Użyj nowego okna, aby uniknąć problemów, które mogą wystąpić z niepoprawne moduły programu PowerShell, które zostały już załadowane w systemie. 

6. Uruchom **DeployMySqlProvider.ps1** skryptu. Skrypt wykonuje następujące czynności: 
    - Pobiera dane binarne MySQL łącznika (to można podać w trybie offline). 
    - Przekazuje certyfikaty i inne artefaktów na konto magazynu Azure stosu. 
    - Publikowanie pakietów galerii tak, aby umożliwić wdrożenie bazy danych SQL za pomocą galerii. 
    - Publikuje pakietu galerii wdrażania serwerami hostingu. 
    - Wdraża maszynę Wirtualną przy użyciu systemu Windows Server 2016 Azure stosu obrazu z witryny marketplace i instaluje dostawcę zasobów. 
    - Rejestruje rekord DNS lokalnego mapy do dostawcy zasobów maszyny Wirtualnej. 
    - Rejestruje dostawcę zasobów z lokalnej usługi Azure Resource Manager (dzierżawcy i administratora). 

    Można zmiennoprzecinkową wymaganych parametrów w wierszu polecenia lub uruchom skrypt bez żadnych parametrów, a następnie wprowadź je po wyświetleniu monitu. 

    Oto przykład można uruchamiać w wierszu polecenia programu PowerShell. Pamiętaj zmienić informacje o koncie i hasła w razie potrzeby: 

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
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
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

> [!NOTE] 
> Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Nie można utworzyć bazy danych, dopóki nie zostanie utworzona jednostka SKU. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Weryfikacja wdrożenia przy użyciu portalu Azure stosu 
> [!NOTE] 
>  Po zakończeniu skrypt instalacji należy odświeżyć portalu, zobacz blok administratora. 

1. Zaloguj się do portalu administratora jako administratora usługi. 
2. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Przejdź do **grup zasobów**, a następnie wybierz **systemu.\< Lokalizacja\>.mysqladapter** grupy zasobów. Sprawdź, czy wszystkie cztery wdrożenia powiodło się:

      ![Sprawdź wdrożenie MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>Kolejne kroki
[Dodaj serwery hostingu](azure-stack-mysql-resource-provider-hosting-servers.md)
