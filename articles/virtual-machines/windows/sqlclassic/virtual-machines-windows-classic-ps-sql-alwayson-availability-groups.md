---
title: Konfigurowanie zawsze włączonej grupy dostępności na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Ten samouczek używa zasobów, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Utwórz zawsze włączonej grupy dostępności na platformie Azure za pomocą programu PowerShell.
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
ms.openlocfilehash: c089d54544217cf72f81a2535ceede50d25b9b61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362190"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurowanie zawsze włączonej grupy dostępności na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Klasyczne: INTERFEJS UŻYTKOWNIKA](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasyczne: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Przed rozpoczęciem należy wziąć pod uwagę, możesz teraz wykonać to zadanie w modelu usługi Azure resource manager. Firma Microsoft zaleca modelu usługi Azure resource manager dla nowych wdrożeń. Zobacz [programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Zaleca się, że większości nowych wdrożeń korzystać z modelu usługi Resource Manager. Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia.

Maszyn wirtualnych (VM) platformy Azure może pomóc administratorom bazy danych zmniejszyć koszt systemu programu SQL Server o wysokiej dostępności. Ten samouczek pokazuje, jak wdrożyć do grupy dostępności przy użyciu programu SQL Server Always On end-to-end w środowisku platformy Azure. Na końcu tego samouczka rozwiązania SQL Server Always On na platformie Azure będzie składać się z następujących elementów:

* Sieć wirtualna, zawierający wiele podsieci, w tym frontonu i podsieć zaplecza.
* Kontroler domeny z domeny usługi Active Directory.
* Dwie maszyny wirtualne SQL Server, które są wdrażane w podsieci zaplecza i przyłączone do domeny usługi Active Directory.
* Trzema węzłami klastra pracy awaryjnej Windows za pomocą modelu kworum Większość węzłów.
* Grupa dostępności o dwóch replik z zatwierdzaniem synchronicznym bazy danych dostępności.

Ten scenariusz jest dobrym wyborem dla uproszczenia, jej na platformie Azure, a nie jego efektywność kosztową lub innych czynników. Na przykład można zminimalizować liczbę maszyn wirtualnych dla grupy dwie repliki dostępności zaoszczędzić na godziny obliczeniowe na platformie Azure przy użyciu kontrolera domeny jako monitor udziału plików kworum w klastrze trybu failover z dwoma węzłami. Ta metoda zmniejsza liczbę maszyn wirtualnych za pomocą jednej z powyższych konfiguracji.

Ten samouczek jest przeznaczony Wam czynności, które są wymagane do skonfigurowania rozwiązania opisane powyżej, bez opracowanie szczegóły każdego kroku. W związku z tym zamiast podawać kroki konfiguracji graficznego interfejsu użytkownika, używa programu PowerShell, skryptów, aby szybko przejść przez kolejne kroki. Ten samouczek zakłada, że:

* Masz już konto platformy Azure z subskrypcją maszyny wirtualnej.
* Po zainstalowaniu [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview).
* Masz już to pełny opis zawsze włączonych grup dostępności dla rozwiązań w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Połącz z subskrypcją platformy Azure i Utwórz sieć wirtualną
1. W oknie programu PowerShell na komputerze lokalnym Zaimportuj moduł platformy Azure, pobrać pliku ustawień publikowania na komputer i sesji programu PowerShell połączyć się z subskrypcją platformy Azure przez importowanie ustawień publikowania pobranego.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get AzurePublishSettingsFile** polecenia automatycznie generuje certyfikat zarządzania z platformą Azure i pobranie go na komputer. Jest automatycznie otwarte w przeglądarce i zostanie wyświetlony monit o wprowadzenie poświadczeń konta Microsoft dla subskrypcji platformy Azure. Pobrany **.publishsettings** plik zawiera wszystkie informacje, które są potrzebne do zarządzania subskrypcją platformy Azure. Po zapisaniu tego pliku do katalogu lokalnego, zaimportuj go za pomocą **AzurePublishSettingsFile importu** polecenia.

   > [!NOTE]
   > Plik .publishsettings zawiera swoje poświadczenia (Niezakodowane:), które są używane do administrowania subskrypcjami platformy Azure i usług. Najlepszym rozwiązaniem bezpieczeństwa dla tego pliku jest tymczasowo przechowywane poza katalogów źródła (na przykład w folderze Libraries\Documents) i usunąć go po zakończeniu importowania. Złośliwy użytkownik, który uzyskuje dostęp do pliku .publishsettings można edytować, tworzenie i usuwanie usług platformy Azure.

2. Zdefiniuj szereg zmiennych, które będziesz używać do tworzenia infrastruktury chmury.

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

    Należy zwrócić uwagę na następujące czynności, aby upewnić się, że poleceń powiedzie się później:

   * Zmienne **$storageAccountName** i **$dcServiceName** musi być unikatowa, ponieważ służą one do identyfikowania chmury konta i w chmurze serwer magazynu, odpowiednio w Internecie.
   * Nazwy, które określisz dla zmiennych **$affinityGroupName** i **$virtualNetworkName** są konfigurowane w dokumencie konfiguracji sieci wirtualnej, który będzie potrzebny później.
   * **$sqlImageName** określa zaktualizowana nazwa obrazu maszyny Wirtualnej, który zawiera program SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Dla uproszczenia **Contoso! 000** to samo hasło, które jest używane w całej samouczku.

3. Tworzenie grupy koligacji.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Tworzenie sieci wirtualnej, importując plik konfiguracji.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Plik konfiguracji zawiera następujące dokumentu XML. Krótko mówiąc, określa on sieci wirtualnej o nazwie **ContosoNET** w grupie koligacji o nazwie **ContosoAG**. Ma ona przestrzeń adresową **10.10.0.0/16** i ma dwie podsieci, **10.10.1.0/24** i **10.10.2.0/24**, służą do pierwszej podsieci i podsieć Wstecz, odpowiednio. Podsieci frontonu jest umieszczane aplikacji klienckich, takich jak Microsoft SharePoint. Wstecz podsieci jest, gdzie będzie umieszczać maszyny wirtualne programu SQL Server. Jeśli zmienisz **$affinityGroupName** i **$virtualNetworkName** zmienne wcześniej, należy także zmienić odpowiadających im nazw poniżej.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

5. Utwórz konto magazynu, który jest skojarzony z grupą koligacji, utworzona i ustawiona jako bieżące konto magazynu, w ramach subskrypcji.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Utwórz serwer kontrolera domeny w nowym zestawie usług i dostępność chmury.

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

   * **Nowe AzureVMConfig** tworzy konfigurację maszyny Wirtualnej.
   * **Dodaj-AzureProvisioningConfig** udostępnia parametry konfiguracyjne autonomicznego serwera Windows.
   * **Dodaj AzureDataDisk** dysk danych, które będzie używane do przechowywania danych usługi Active Directory przy użyciu opcji buforowania, wartość None.
   * **Polecenie New-AzureVM** tworzy nową usługę w chmurze i utworzenie nowej maszyny Wirtualnej platformy Azure w nowej usługi w chmurze.

7. Poczekaj, aż nowej maszyny Wirtualnej one pełnego zainicjowania obsługi i pobrania pliku pulpitu zdalnego do katalogu roboczego. Ponieważ nowej maszyny Wirtualnej platformy Azure zajmuje dużo czasu, obsługę administracyjną i `while` pętli kontynuuje sondowanie nowej maszyny Wirtualnej, dopóki nie jest gotowa do użycia.

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

Serwer kontrolera domeny teraz zostanie pomyślnie aprowizowane. Następnie należy skonfigurować domeny usługi Active Directory na tym serwerze kontrolera domeny. Pozostaw otwarte okno programu PowerShell na komputerze lokalnym. Użyjesz jej później utworzyć dwie maszyny wirtualne SQL Server.

## <a name="configure-the-domain-controller"></a>Konfigurowanie kontrolera domeny
1. Nawiązać połączenia z serwerem kontrolera domeny przez uruchomienie pliku pulpitu zdalnego. Użyj AzureAdmin nazwy użytkownika i hasło administratora maszyny **Contoso! 000**, które określono podczas tworzenia nowej maszyny Wirtualnej.
2. Otwórz okno programu PowerShell w trybie administratora.
3. Uruchom następujące polecenie **DCPROMO. Plik EXE** polecenie, aby skonfigurować **corp.contoso.com** domenie, przy użyciu katalogi danych na dysku M.

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

    Po zakończeniu działania polecenia automatycznie uruchamia ponownie maszyny Wirtualnej.

4. Ponownie nawiąż połączenie z serwerem kontrolera domeny przez uruchomienie pliku pulpitu zdalnego. Teraz, zaloguj się jako **CORP\Administrator**.
5. Otwórz okno programu PowerShell w trybie administratora, a następnie zaimportuj moduł środowiska PowerShell usługi Active Directory za pomocą następującego polecenia:

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

    **CORP\Install** służy do konfigurowania wszystkie elementy powiązane ze wystąpienia usługi SQL Server, klastra trybu failover i grupy dostępności. **CORP\SQLSvc1** i **CORP\SQLSvc2** są używane jako konta usług SQL Server dla dwóch maszyn wirtualnych programu SQL Server.
7. Następnie uruchom następujące polecenia, aby nadać **CORP\Install** uprawnienia do tworzenia obiektów komputerów w domenie.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Wymienione powyżej identyfikator GUID jest identyfikator GUID dla typu obiektu komputera. **CORP\Install** konta potrzeb **Odczyt wszystkich właściwości** i **tworzenia obiektów komputerów** uprawnienia do tworzenia obiektów Active bezpośrednie dla klastra trybu failover. **Odczyt wszystkich właściwości** uprawnienie jest już używana CORP\Install domyślnie, dzięki czemu nie trzeba jawnie przyznać. Aby uzyskać więcej informacji na temat uprawnień, które są niezbędne do utworzenia klastra trybu failover, zobacz [przewodnik krok po kroku klastra trybu Failover: Konfigurowanie kont w usłudze Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Teraz, gdy zakończysz, skonfigurowanie usługi Active Directory i obiektów użytkowników, utworzysz dwie maszyny wirtualne programu SQL Server i dołącz je do tej domeny.

## <a name="create-the-sql-server-vms"></a>Utwórz maszyny wirtualne programu SQL Server
1. W dalszym ciągu korzystanie z okna programu PowerShell, która jest otwarta na komputerze lokalnym. Zdefiniuj następujące dodatkowe zmienne:

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

    Adres IP **10.10.0.4** zwykle jest przypisany do pierwszej maszyny Wirtualnej, który zostanie utworzony w **10.10.0.0/16** podsieci sieci wirtualnej platformy Azure. Należy sprawdzić, czy jest to adres serwera kontrolera domeny, uruchamiając **IPCONFIG**.
2. Uruchom następujące potokiem polecenia, aby utworzyć pierwszą maszynę Wirtualną w klastrze trybu failover, o nazwie **ContosoQuorum**:

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

    Należy pamiętać o następujących dotyczących powyższego polecenia:

   * **Nowe AzureVMConfig** tworzy konfigurację maszyny Wirtualnej o nazwie zestawu dostępności żądaną. Kolejnych maszyn wirtualnych zostanie utworzony o takiej samej nazwie zestawu dostępności, dzięki czemu zostaną one przyłączone do tego samego zestawu dostępności.
   * **Dodaj-AzureProvisioningConfig** dołączania maszyny Wirtualnej do domeny usługi Active Directory, który został utworzony.
   * **Zestaw AzureSubnet** umieszcza maszynę Wirtualną w podsieci Wstecz.
   * **Polecenie New-AzureVM** tworzy nową usługę w chmurze i utworzenie nowej maszyny Wirtualnej platformy Azure w nowej usługi w chmurze. **DnsSettings** parametr określa, czy serwer DNS dla serwerów w nowej usługi w chmurze ma adres IP **10.10.0.4**. Jest to adres IP serwera kontrolera domeny. Ten parametr jest wymagane do włączenia nowych maszyn wirtualnych w usłudze w chmurze do przyłączania do domeny usługi Active Directory pomyślnie. Bez tego parametru należy ręcznie skonfigurować ustawienia IPv4 na maszynie wirtualnej, aby serwer kontrolera domeny jest używany jako podstawowy serwer DNS, po zaaprowizowaniu maszyny Wirtualnej, a następnie przyłącz ją do domeny usługi Active Directory.
3. Uruchom następujące potokiem polecenia, aby utworzyć maszyny wirtualne SQL Server, o nazwie **ContosoSQL1** i **ContosoSQL2**.

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

    Należy pamiętać o następujących dotyczących powyższe polecenia:

   * **Nowe AzureVMConfig** korzysta z taką samą nazwę zestawu dostępności jako serwer kontrolera domeny i używa obrazu programu SQL Server 2012 Service Pack 1 Enterprise Edition w galerii maszyn wirtualnych. Ustawia również dysk systemu operacyjnego do odczytu buforowanie tylko (nie buforowanie zapisu). Zalecamy migrację pliki bazy danych do dysku osobne dane, który można dołączyć do maszyny Wirtualnej i skonfigurować go bez odczytu lub zapisu w pamięci podręcznej. Następnym krokiem jest jednak usunąć buforowanie zapisu na dysku systemu operacyjnego, ponieważ nie można usunąć pamięci podręcznej odczytu na dysku systemu operacyjnego.
   * **Dodaj-AzureProvisioningConfig** dołączania maszyny Wirtualnej do domeny usługi Active Directory, który został utworzony.
   * **Zestaw AzureSubnet** umieszcza maszynę Wirtualną w podsieci Wstecz.
   * **Dodaj-AzureEndpoint** dodaje punkty końcowe dostępu, dzięki czemu aplikacje klienckie mają dostęp do tych wystąpień usługi programu SQL Server w Internecie. Inne porty są podane ContosoSQL1 i ContosoSQL2.
   * **Polecenie New-AzureVM** tworzy nową maszynę Wirtualną programu SQL Server w tej samej usługi w chmurze jako ContosoQuorum. Maszyny wirtualne należy umieścić w tej samej usługi w chmurze, jeśli chcesz, aby znajdować się w tym samym zestawie dostępności.
4. Poczekaj, dla każdej maszyny Wirtualnej one pełnego zainicjowania obsługi i dla każdej maszyny Wirtualnej pobrać jego pliku pulpitu zdalnego do katalogu roboczego. `for` Pętli, aby przewijać trzy nowe maszyny wirtualne i wykonuje polecenia znajduje się wewnątrz nawiasów klamrowych najwyższego poziomu dla każdego z nich.

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

    Maszyny wirtualne programu SQL Server jest teraz udostępniony i uruchomiona, ale są one zainstalowane z programem SQL Server z użyciem opcji domyślnych.

## <a name="initialize-the-failover-cluster-vms"></a>Inicjowanie maszyn wirtualnych z klastra trybu failover
W tej sekcji należy zmodyfikować trzy serwery, których można używać w klastrze trybu failover i instalacji programu SQL Server. W szczególności:

* Wszystkie serwery: Musisz zainstalować **klastra trybu Failover** funkcji.
* Wszystkie serwery: Należy dodać **CORP\Install** maszyny **administratora**.
* ContosoSQL1 i ContosoSQL2 tylko: Należy dodać **CORP\Install** jako **sysadmin** rolę w domyślnej bazy danych.
* ContosoSQL1 i ContosoSQL2 tylko: Należy dodać **NT AUTHORITY\System** jako logowania z następującymi uprawnieniami:

  * Instrukcja ALTER żadnej grupy dostępności
  * Połączenia SQL
  * Wyświetl stan serwera
* ContosoSQL1 i ContosoSQL2 tylko: **TCP** na maszynę Wirtualną programu SQL Server jest już włączony protokół. Jednak nadal musisz otworzyć zaporę dla dostępu zdalnego programu SQL Server.

Teraz jesteś gotowy do uruchomienia. Począwszy od **ContosoQuorum**, wykonaj następujące czynności:

1. Połączyć się z **ContosoQuorum** , uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika administratora maszyny **AzureAdmin** i hasło **Contoso! 000**, które określono podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie dołączone do **corp.contoso.com**.
3. Poczekaj na Zakończ uruchomione zadania automatyczne inicjowanie przed kontynuowaniem instalacji programu SQL Server.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję Klaster pracy awaryjnej Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Install** jako administrator lokalny.

        net localgroup administrators "CORP\Install" /Add
7. Wyloguj ContosoQuorum. To wszystko na tym serwerze teraz.

        logoff.exe

Następnie zainicjuj **ContosoSQL1** i **ContosoSQL2**. Wykonaj poniższe kroki, które są takie same dla obu maszynach wirtualnych serwera SQL.

1. Nawiązać dwie maszyny wirtualne SQL Server, uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika administratora maszyny **AzureAdmin** i hasło **Contoso! 000**, które określono podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie dołączone do **corp.contoso.com**.
3. Poczekaj na Zakończ uruchomione zadania automatyczne inicjowanie przed kontynuowaniem instalacji programu SQL Server.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję Klaster pracy awaryjnej Windows.

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
9. Dodaj **NT AUTHORITY\System** jako Zaloguj się przy użyciu trzech uprawnienia opisane powyżej.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otwierająca zaporę dla dostępu zdalnego programu SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Wyloguj się z obu maszyn wirtualnych.

         logoff.exe

Na koniec możesz przystąpić do konfigurowania grupy dostępności. Użyjesz dostawcy do programu SQL Server programu PowerShell do wykonywania wszystkich prac na **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurowanie grupy dostępności
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
7. Pobierz **CreateAzureFailoverCluster.ps1** z [Tworzenie klastra trybu Failover dla zawsze włączonych grup dostępności na maszynie Wirtualnej platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do lokalnego katalogu roboczego. Ten skrypt będzie używany, ułatwiające tworzenie klastra trybu failover funkcjonalności. Aby uzyskać ważne informacje na klaster pracy awaryjnej Windows współdziałania z siecią platformy Azure, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Przejdź do katalogu roboczego, a następnie utworzyć klaster trybu failover przy użyciu pobranego skryptu.

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
10. Tworzenie kopii zapasowej katalogu i przyznawanie uprawnień dla konta usługi programu SQL Server. Użyjesz tego katalogu w celu przygotowania bazy danych dostępności w replice pomocniczej.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Tworzenie bazy danych na **ContosoSQL1** o nazwie **MyDB1**, wykonać kopię zapasową dziennika i pełną kopię zapasową i przywrócić je na **ContosoSQL2** z **WITH NORECOVERY**  opcji.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Tworzenie punktów końcowych grupy dostępności na maszynach wirtualnych programu SQL Server i ustaw odpowiednie uprawnienia w punktach końcowych.

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
14. Na koniec Utwórz grupę dostępności i Dołącz do repliki pomocniczej do grupy dostępności.

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
Już teraz pomyślnie zaimplementowano program SQL Server Always On, tworząc grupy dostępności na platformie Azure. Aby skonfigurować odbiornik grupy dostępności, zobacz [Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

Aby uzyskać inne informacje o korzystaniu z programu SQL Server na platformie Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
