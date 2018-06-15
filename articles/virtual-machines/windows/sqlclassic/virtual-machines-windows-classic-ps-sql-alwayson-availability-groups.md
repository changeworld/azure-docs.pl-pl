---
title: Konfigurowanie zawsze włączonej grupy dostępności na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: W tym samouczku korzysta z zasobów, które zostały utworzone z klasycznym modelu wdrażania. Tworzenie zawsze włączonej grupy dostępności na platformie Azure za pomocą programu PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: fe7384baa6740d316fb5a8ec7b1894f337d88080
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401295"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurowanie zawsze włączonej grupy dostępności na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Klasycznym: interfejsu użytkownika](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasycznym: środowiska PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Przed rozpoczęciem należy wziąć pod uwagę może teraz ukończyć tego zadania w modelu Menedżera zasobów Azure. Firma Microsoft zaleca model Menedżera zasobów Azure dla nowych wdrożeń. Zobacz [programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Zaleca się, że większości nowych wdrożeń Użyj modelu Resource Manager. Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia.

Azure maszynach wirtualnych (VM) może pomóc administratorom bazy danych na niższy koszt systemu wysokiej dostępności programu SQL Server. W tym samouczku przedstawiono sposób wdrażania grupy dostępności za pomocą programu SQL Server AlwaysOn end-to-end wewnątrz środowiska platformy Azure. Na koniec samouczka rozwiązania programu SQL Server zawsze na platformie Azure będzie składać się z następujących elementów:

* Sieć wirtualna zawiera wiele podsieci, w tym frontonu i zaplecza podsieci.
* Kontroler domeny z domeny usługi Active Directory.
* Dwa programu SQL Server maszyny wirtualne, które są wdrażane do podsieci wewnętrznej i przyłączone do domeny usługi Active Directory.
* Trzy węzeł klastra pracy awaryjnej systemu Windows z modelu kworum Większość węzłów.
* Grupa dostępności o dwóch replik z zatwierdzaniem synchronicznym bazy danych dostępności.

Ten scenariusz jest dobrym rozwiązaniem dla uproszczenia jej na platformie Azure, a nie dla jego efektywność kosztowa lub innych czynników. Na przykład można zminimalizować liczbę maszyn wirtualnych dla dwóch repliki dostępności grupy do zapisywania godziny obliczeń na platformie Azure przy użyciu kontrolera domeny jako monitor udziału plików kworum w klastrze trybu failover z dwoma węzłami. Ta metoda zmniejsza liczbę maszyn wirtualnych przez jedną z powyższych konfiguracji.

Ten samouczek jest przeznaczony do opisano kroki, które są wymagane, aby skonfigurować rozwiązanie opisane powyżej, bez opracowanie szczegóły każdego kroku. W związku z tym etapy konfiguracji graficznego interfejsu użytkownika, a nie używa programu PowerShell skrypty umożliwiające szybkie przejście przez kolejne kroki. Ten samouczek zakłada następujące czynności:

* Masz już konto platformy Azure z subskrypcją maszyny wirtualnej.
* Po zainstalowaniu [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview).
* Masz już pełny opis zawsze włączonych grup dostępności dla rozwiązań lokalnych. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Łączenie z subskrypcją platformy Azure i tworzenie sieci wirtualnej
1. W oknie programu PowerShell na komputerze lokalnym Zaimportuj moduł Azure, pobieranie pliku ustawień publikowania do maszyny i sesji programu PowerShell połączyć się z subskrypcją platformy Azure przez zaimportowanie pobrany ustawień publikowania.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get AzurePublishSettingsFile** polecenie automatycznie generuje certyfikat zarządzania platformy Azure i pliki do pobrania na komputerze. Przeglądarką jest automatycznie otwierane i zostanie wyświetlony monit o podanie poświadczeń konta Microsoft dla subskrypcji platformy Azure. Pobrany **.publishsettings** plik zawiera wszystkich informacji potrzebnych do zarządzania subskrypcją platformy Azure. Po zapisaniu tego pliku do katalogu lokalnego, zaimportuj go za pomocą **AzurePublishSettingsFile importu** polecenia.

   > [!NOTE]
   > Plik .publishsettings zawiera swoje poświadczenia (Niezakodowane), które są używane do administrowania subskrypcji platformy Azure i usługi. Najlepszym rozwiązaniem bezpieczeństwa dla tego pliku jest tymczasowo przechowywane poza katalogów źródła (na przykład w folderze Libraries\Documents), a następnie usuń ją po zakończeniu importowania. Złośliwy użytkownik, który uzyskuje dostęp do pliku .publishsettings można edytować, tworzenia i usuwania usług Azure.

2. Zdefiniuj szereg zmiennych, które będą używane do tworzenia infrastruktury chmury.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Należy zwrócić uwagę na następujące, aby upewnić się, że poleceniach powiedzie się później:

   * Zmienne **$storageAccountName** i **$dcServiceName** musi być unikatowa, ponieważ są używane do identyfikowania chmury konta i w chmurze serwer magazynu, odpowiednio w Internecie.
   * Nazwy, które określisz zmiennych **$affinityGroupName** i **$virtualNetworkName** są skonfigurowane w dokumencie konfiguracji sieci wirtualnej, który będzie potrzebny później.
   * **$sqlImageName** Określa zaktualizowaną nazwę obrazu maszyny Wirtualnej, która zawiera SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Dla uproszczenia **Contoso! 000** to samo hasło, które jest używane w całej samouczka.

3. Utwórz grupę koligacji.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Tworzenie sieci wirtualnej przez zaimportowanie pliku konfiguracji.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Plik konfiguracji zawiera następujący dokument XML. Krótko mówiąc, określa sieć wirtualną o nazwie **ContosoNET** w grupie koligacji o nazwie **ContosoAG**. Przestrzeń adresowa ma **10.10.0.0/16** i ma dwie podsieci **10.10.1.0/24** i **10.10.2.0/24**, będące odpowiednio podsieci frontonu i Wstecz podsieci. Front podsieci jest umieszczane aplikacji klienckich, takich jak Microsoft SharePoint. Wstecz podsieci będą umieszczane są maszynach wirtualnych serwera SQL. Jeśli zmienisz **$affinityGroupName** i **$virtualNetworkName** zmienne wcześniej, należy również zmienić nazwy poniżej.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Utwórz konto magazynu, który został skojarzony z grupą koligacji utworzyć i ustawić go jako bieżącego konta magazynu w ramach subskrypcji.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Utwórz serwer kontrolera domeny w nowym zestawie dostępności i usługi chmury.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Te polecenia gazociągami wykonaj następujące czynności:

   * **Nowy AzureVMConfig** tworzy konfiguracji maszyny Wirtualnej.
   * **Dodaj AzureProvisioningConfig** daje parametry konfiguracji autonomicznym serwerze z systemem Windows.
   * **Dodaj AzureDataDisk** dodaje dysk danych, które będzie używane do przechowywania danych usługi Active Directory przy użyciu opcji buforowania, wartość None.
   * **Nowy AzureVM** tworzy nową usługę w chmurze i utworzenie nowej maszyny Wirtualnej platformy Azure w nowej usługi w chmurze.

7. Poczekaj, aż nowej maszyny Wirtualnej być w pełni obsługiwany i pobranie pliku pulpitu zdalnego do katalogu roboczego. Ponieważ nowej maszyny Wirtualnej Azure zajmuje dużo czasu, aby udostępnić, `while` pętli kontynuuje sondowanie nowej maszyny Wirtualnej, dopóki nie jest gotowa do użycia.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Teraz pomyślnie obsługiwanej przez serwer kontrolera domeny. Następnie należy skonfigurować domeny usługi Active Directory na tym serwerze kontrolera domeny. Pozostaw otwarte okno programu PowerShell na komputerze lokalnym. Użyjesz go ponownie później można utworzyć dwóch maszyn wirtualnych serwera SQL.

## <a name="configure-the-domain-controller"></a>Konfiguracja kontrolera domeny
1. Połączyć się z serwerem kontrolera domeny przez uruchomienie pliku pulpitu zdalnego. Użyj AzureAdmin nazwy użytkownika i hasło administratora maszyny **Contoso! 000**, która została określona podczas tworzenia nowej maszyny Wirtualnej.
2. Otwórz okno programu PowerShell w trybie administratora.
3. Uruchom następujące polecenie **DCPROMO. EXE** polecenie, aby skonfigurować **corp.contoso.com** domeny z katalogami danych na dysku M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Po zakończeniu działania polecenia do automatycznego uruchomienia maszyny Wirtualnej.

4. Ponownie nawiąż połączenie z serwerem kontrolera domeny przez uruchomienie pliku pulpitu zdalnego. Teraz, zaloguj się jako **CORP\Administrator**.
5. Otwórz okno programu PowerShell w trybie administratora, a następnie zaimportować moduł środowiska PowerShell usługi Active Directory przy użyciu następującego polecenia:

        Import-Module ActiveDirectory

6. Uruchom następujące polecenia, aby dodać trzech użytkowników do domeny.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** służy do konfigurowania wszystkich związanych z wystąpień usługi programu SQL Server i klastra trybu failover oraz grupy dostępności. **CORP\SQLSvc1** i **CORP\SQLSvc2** są używane jako konto usługi programu SQL Server dla dwóch maszyn wirtualnych serwera SQL.
7. Następnie uruchom następujące polecenia, aby zapewnić **CORP\Install** uprawnienia do tworzenia obiektów komputerów w domenie.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Wymienione powyżej identyfikator GUID jest identyfikatorem GUID dla typu obiektu komputera. **CORP\Install** konta potrzeb **Odczyt wszystkich właściwości** i **tworzenia obiektów komputerów** uprawnienia do tworzenia obiektów Active bezpośrednie dla klastra trybu failover. **Odczyt wszystkich właściwości** uprawnienie jest już używana CORP\Install domyślnie, dzięki czemu nie trzeba go jawnie udzielić. Aby uzyskać więcej informacji dotyczących uprawnień, które są niezbędne do utworzenia klastra trybu failover, zobacz [przewodnik krok po kroku klastra pracy awaryjnej: Konfigurowanie kont w usłudze Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Teraz, po zakończeniu konfigurowania usługi Active Directory i obiektów użytkowników, będzie dwóch maszyn wirtualnych programu SQL Server Utwórz i dołącz je do tej domeny.

## <a name="create-the-sql-server-vms"></a>Tworzenie maszyn wirtualnych serwera SQL
1. Nadal używać okno programu PowerShell, który jest otwarty na komputerze lokalnym. Zdefiniuj następujące zmienne dodatkowe:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    Adres IP **10.10.0.4** zwykle jest przypisany do pierwszej maszyny Wirtualnej utworzone w **10.10.0.0/16** podsieci sieci wirtualnej platformy Azure. Należy sprawdzić, czy jest to adres serwera kontrolera domeny, uruchamiając **IPCONFIG**.
2. Uruchom następujące przetwarzana potokowo poleceń, aby utworzyć pierwszy maszyny Wirtualnej w klastrze trybu failover o nazwie **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Należy pamiętać, że dotyczących powyższego polecenia:

   * **Nowy AzureVMConfig** tworzy konfiguracji maszyny Wirtualnej o nazwie zestaw żądaną dostępności. Kolejnych maszyn wirtualnych o takiej samej nazwie zestawu dostępności zostanie utworzony, dzięki czemu są one przyłączone do tego samego zestawu dostępności.
   * **Dodaj AzureProvisioningConfig** dołącza maszynę Wirtualną do domeny usługi Active Directory, który został utworzony.
   * **Zestaw AzureSubnet** umieszcza maszynę Wirtualną do tyłu podsieci.
   * **Nowy AzureVM** tworzy nową usługę w chmurze i utworzenie nowej maszyny Wirtualnej platformy Azure w nowej usługi w chmurze. **DnsSettings** parametr określa, że serwer DNS dla serwerów w nowej usługi w chmurze ma adres IP **10.10.0.4**. Jest to adres IP serwera kontrolera domeny. Ten parametr jest wymagane do włączenia nowych maszyn wirtualnych w usłudze w chmurze do przyłączania do domeny usługi Active Directory pomyślnie. Bez tego parametru musi ręcznie skonfigurować ustawienia protokołu IPv4 maszynie wirtualnej, aby użyć serwera kontrolera domeny jako podstawowy serwer DNS po udostępnieniu maszyny Wirtualnej, a następnie dołącz maszynę Wirtualną do domeny usługi Active Directory.
3. Uruchom następujące przetwarzana potokowo poleceń, aby utworzyć maszynach wirtualnych serwera SQL, o nazwie **ContosoSQL1** i **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Należy pamiętać, że dotyczących powyższego polecenia:

   * **Nowy AzureVMConfig** korzysta taką samą nazwę zestawu dostępności jako serwer kontrolera domeny, a SQL Server 2012 Service Pack 1 Enterprise Edition obraz w galerii maszyn wirtualnych. Ustawia również dysku systemu operacyjnego do odczytu buforowania tylko (nie buforowania zapisu). Firma Microsoft zaleca migracji pliki bazy danych na dysku odrębne dołączonej do maszyny Wirtualnej i skonfiguruj ją bez odczytu lub zapisu w pamięci podręcznej. Jednakże następnym krokiem jest usunięcie buforowanie zapisu na dysku systemu operacyjnego, ponieważ nie można usunąć buforowania odczytu na dysku systemu operacyjnego.
   * **Dodaj AzureProvisioningConfig** dołącza maszynę Wirtualną do domeny usługi Active Directory, który został utworzony.
   * **Zestaw AzureSubnet** umieszcza maszynę Wirtualną do tyłu podsieci.
   * **Dodaj AzureEndpoint** dodaje punkty końcowe dostępu, dzięki czemu aplikacje klienckie mogą uzyskiwać dostęp do tych wystąpień usługi programu SQL Server w Internecie. Inne porty są podane ContosoSQL1 i ContosoSQL2.
   * **Nowy AzureVM** tworzy nową maszynę Wirtualną programu SQL Server w tej samej usłudze w chmurze jako ContosoQuorum. Maszyny wirtualne należy umieścić w tej samej usłudze w chmurze, jeśli użytkownicy powinni być w tym samym zestawie dostępności.
4. Poczekaj na każdej maszynie Wirtualnej, aby być w pełni obsługiwany i dla każdej maszyny Wirtualnej pobrać jego pliku pulpitu zdalnego do katalogu roboczego. `for` Pętli przełączanie po kolei trzech nowych maszyn wirtualnych i wykonuje polecenia wewnątrz najwyższego poziomu nawiasów klamrowych dla każdego z nich.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Teraz obsługi administracyjnej maszyn wirtualnych serwera SQL i uruchomiona, ale są zainstalowane z programem SQL Server z opcji domyślnych.

## <a name="initialize-the-failover-cluster-vms"></a>Inicjowanie klastra trybu failover maszyny wirtualne
W tej sekcji należy zmodyfikować trzy serwery, które będą używane w klastrze trybu failover i instalacji programu SQL Server. W szczególności:

* Wszystkie serwery: należy zainstalować **klaster pracy awaryjnej** funkcji.
* Wszystkie serwery: należy dodać **CORP\Install** jako maszynę **administratora**.
* ContosoSQL1 i ContosoSQL2 tylko: należy dodać **CORP\Install** jako **sysadmin** roli w domyślnej bazy danych.
* ContosoSQL1 i ContosoSQL2 tylko: należy dodać **NT AUTHORITY\System** jako logowanie z następującymi uprawnieniami:

  * Instrukcja ALTER żadnej grupy dostępności
  * Połączenia SQL
  * Widok stanu serwera
* ContosoSQL1 i ContosoSQL2 tylko: **TCP** na maszynie Wirtualnej serwera SQL jest już włączony protokół. Jednakże nadal konieczne otwarcie zapory dla dostępu zdalnego programu SQL Server.

Teraz możesz przystąpić do uruchomienia. Począwszy od **ContosoQuorum**, wykonaj następujące czynności:

1. Połączyć się z **ContosoQuorum** uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika administratora maszyny **AzureAdmin** i hasło **Contoso! 000**, która została określona podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie przyłączone do **corp.contoso.com**.
3. Poczekaj na zakończenie uruchomionych zadań inicjowania automatycznego przed kontynuowaniem instalacji programu SQL Server.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję Klaster pracy awaryjnej systemu Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Install** jako administrator lokalny.

        net localgroup administrators "CORP\Install" /Add
7. Wyloguj ContosoQuorum. Gotowe z tym serwerem teraz.

        logoff.exe

Następnie należy zainicjować **ContosoSQL1** i **ContosoSQL2**. Wykonaj poniższe kroki, które są takie same dla obu maszynach wirtualnych serwera SQL.

1. Nawiązać dwóch maszyn wirtualnych serwera SQL, uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika administratora maszyny **AzureAdmin** i hasło **Contoso! 000**, która została określona podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie przyłączone do **corp.contoso.com**.
3. Poczekaj na zakończenie uruchomionych zadań inicjowania automatycznego przed kontynuowaniem instalacji programu SQL Server.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję Klaster pracy awaryjnej systemu Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Install** jako administrator lokalny.

        net localgroup administrators "CORP\Install" /Add
7. Importuj dostawcy programu PowerShell programu SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Dodaj **CORP\Install** jako rola administratora systemu dla domyślnego wystąpienia programu SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Dodaj **NT AUTHORITY\System** jako logowanie przy użyciu uprawnień trzech opisanych powyżej.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otwórz Zaporę dostępu zdalnego programu SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Wyloguj obie maszyny wirtualne.

         logoff.exe

Ponadto możesz przystąpić do konfigurowania grupy dostępności. Dostawcy programu PowerShell programu SQL Server używanego do wykonywania pracy na **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Skonfiguruj grupy dostępności
1. Połączyć się z **ContosoSQL1** ponownie, uruchamiając pliki pulpitu zdalnego. Zamiast logowanie się przy użyciu konta komputera, zaloguj się przy użyciu **CORP\Install**.
2. Otwórz okno programu PowerShell w trybie administratora.
3. Zdefiniuj następujące zmienne:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importuj dostawcy programu PowerShell programu SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Zmień konto usługi programu SQL Server dla ContosoSQL1 na CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Zmień konto usługi programu SQL Server dla ContosoSQL2 na CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Pobierz **CreateAzureFailoverCluster.ps1** z [Tworzenie klastra trybu Failover dla zawsze włączonych grup dostępności w maszynie Wirtualnej platformy Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do lokalny katalog roboczy. Tworzenie klastra trybu failover funkcjonalności użyjesz tego skryptu. Ważne informacje dotyczące jak klaster pracy awaryjnej systemu Windows współdziała z sieci platformy Azure znajdują się w temacie [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Zmień katalog roboczy i utworzyć klaster trybu failover z pobranego skryptu.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Włącz zawsze włączone grupy dostępności dla domyślnego wystąpienia programu SQL Server na **ContosoSQL1** i **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Utwórz katalog kopii zapasowej i przydziel uprawnienia dla kont usług programu SQL Server. Ten katalog będzie umożliwia przygotowanie bazy danych dostępności w replice pomocniczej.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Utwórz bazę danych na **ContosoSQL1** o nazwie **MyDB1**przełączyć pełnej kopii zapasowej i kopii zapasowej dziennika i przywróci je na **ContosoSQL2** z **WITH NORECOVERY**  opcji.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Tworzenie punktów końcowych grupy dostępności na maszynach wirtualnych serwera SQL i ustaw odpowiednie uprawnienia w punktach końcowych.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Tworzenie replik dostępności.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Na koniec należy utworzyć grupy dostępności i przyłączania repliki pomocniczej do grupy dostępności.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz pomyślnie zaimplementowano program SQL Server AlwaysOn przez utworzenie grupy dostępności na platformie Azure. Aby skonfigurować odbiornik grupy dostępności, zobacz [skonfigurować odbiornik ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

Aby uzyskać inne informacje o korzystaniu z programu SQL Server na platformie Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
