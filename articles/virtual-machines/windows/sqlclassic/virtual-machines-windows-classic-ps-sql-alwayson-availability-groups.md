---
title: Konfigurowanie grupy dostępności zawsze włączone na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell | Dokumenty firmy Microsoft
description: W tym samouczku użyto zasobów utworzonych przy użyciu klasycznego modelu wdrażania. Program PowerShell służy do tworzenia grupy dostępności zawsze włączone na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: ba6f1300353247ef2de99b2bd903bc82665d9a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978158"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurowanie grupy dostępności zawsze włączone na maszynie Wirtualnej platformy Azure za pomocą programu PowerShell
> [!div class="op_single_selector"]
> * [Klasyczny: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasyczny: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Przed rozpoczęciem należy wziąć pod uwagę, że można teraz wykonać to zadanie w modelu menedżera zasobów platformy Azure. Zalecamy model menedżera zasobów platformy Azure dla nowych wdrożeń. Zobacz [SQL Server Always On grupy dostępności na maszynach wirtualnych platformy Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Zaleca się, aby większość nowych wdrożeń używała modelu Menedżera zasobów. Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../../azure-resource-manager/management/deployment-models.md). Ten artykuł dotyczy klasycznego modelu wdrożenia.

Maszyny wirtualne platformy Azure (VM) może pomóc administratorom bazy danych obniżyć koszt systemu SQL Server o wysokiej dostępności. W tym samouczku pokazano, jak zaimplementować grupę dostępności przy użyciu programu SQL Server Always On end-to-end wewnątrz środowiska platformy Azure. Na końcu samouczka rozwiązanie programu SQL Server Always On na platformie Azure będzie składać się z następujących elementów:

* Sieć wirtualna zawierająca wiele podsieci, w tym front-end i podsieć zaplecza.
* Kontroler domeny z domeną usługi Active Directory.
* Dwie maszyny wirtualne programu SQL Server, które są wdrażane w podsieci zaplecza i przyłączone do domeny usługi Active Directory.
* Trzywęzłowy klaster trybu failover systemu Windows z modelem kworum kworum większościowego węzła.
* Grupa dostępności z dwiema synchroniczowymi replikami bazy danych dostępności.

Ten scenariusz jest dobrym wyborem dla jego prostoty na platformie Azure, a nie dla jego opłacalności lub innych czynników. Na przykład można zminimalizować liczbę maszyn wirtualnych dla grupy dostępności dwóch replik, aby zaoszczędzić na godzinach obliczeniowych na platformie Azure przy użyciu kontrolera domeny jako monitora udziału plików kworum w klastrze trybu failover dwóch węzłów. Ta metoda zmniejsza liczbę maszyn wirtualnych o jeden z powyższej konfiguracji.

Ten samouczek ma na celu pokazanie kroków, które są wymagane do skonfigurowania opisanego rozwiązania powyżej, bez opracowywania szczegółów każdego kroku. W związku z tym zamiast podawania kroków konfiguracji interfejsu użytkownika, używa skryptów programu PowerShell, aby szybko przejść przez każdy krok. W tym samouczku przyjęto następujące kwestie:

* Masz już konto platformy Azure z subskrypcją maszyny wirtualnej.
* Zainstalowano polecenia [cmdlet programu Azure PowerShell](/powershell/azure/overview).
* Masz już solidne zrozumienie zawsze na dostępności grup dla rozwiązań lokalnych. Aby uzyskać więcej informacji, zobacz [Zawsze włączone grupy dostępności (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Połącz się z subskrypcją platformy Azure i utwórz sieć wirtualną
1. W oknie programu PowerShell na komputerze lokalnym zaimportuj moduł platformy Azure, pobierz plik ustawień publikowania na komputerze i połącz sesję programu PowerShell z subskrypcją platformy Azure, importując pobrane ustawienia publikowania.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Polecenie **Get-AzurePublishSettingsFile** automatycznie generuje certyfikat zarządzania za pomocą platformy Azure i pobiera go na komputer. Przeglądarka jest automatycznie otwierana i zostanie wyświetlony monit o wprowadzenie poświadczeń konta Microsoft dla subskrypcji platformy Azure. Pobrany plik **.publishsettings** zawiera wszystkie informacje potrzebne do zarządzania subskrypcją platformy Azure. Po zapisaniu tego pliku w katalogu lokalnym zaimportuj go za pomocą polecenia **Import-AzurePublishSettingsFile.**

   > [!NOTE]
   > Plik .publishsettings zawiera poświadczenia (niekodowane), które są używane do administrowania subskrypcjami i usługami platformy Azure. Najlepszym rozwiązaniem w zakresie zabezpieczeń dla tego pliku jest tymczasowe przechowywanie go poza katalogami źródłowymi (na przykład w folderze Biblioteki\Dokumenty), a następnie usunięcie go po zakończeniu importowania. Złośliwy użytkownik, który uzyskuje dostęp do pliku .publishsettings, może edytować, tworzyć i usuwać usługi platformy Azure.

2. Zdefiniuj serię zmiennych, które będą używane do tworzenia infrastruktury IT w chmurze.

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

    Należy zwrócić uwagę na następujące kwestie, aby upewnić się, że polecenia zostaną pomyślnie powiodą się później:

   * Zmienne **$storageAccountName** i **$dcServiceName** muszą być unikatowe, ponieważ są używane do identyfikowania konta magazynu w chmurze i serwera w chmurze, odpowiednio, w Internecie.
   * Nazwy określone dla zmiennych **$affinityGroupName** i **$virtualNetworkName** są konfigurowane w dokumencie konfiguracji sieci wirtualnej, który będzie używany później.
   * **$sqlImageName** określa zaktualizowaną nazwę obrazu maszyny Wirtualnej zawierającego program SQL Server 2012 z dodatkiem Service Pack 1 Enterprise Edition.
   * Dla uproszczenia **Contoso!000** jest tym samym hasłem, które jest używane w całym samouczku.

3. Tworzenie grupy koligacji.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Utwórz sieć wirtualną, importując plik konfiguracyjny.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Plik konfiguracyjny zawiera następujący dokument XML. W skrócie określa sieć wirtualną o nazwie **ContosoNET** w grupie koligacji o nazwie **ContosoAG**. Ma przestrzeń adresową **10.10.0.0/16** i ma dwie podsieci, **odpowiednio 10.10.1.0/24** i **10.10.2.0/24**, które są podsiecią przednią i tylną. Podsieć przednia to miejsce, w którym można umieszczać aplikacje klienckie, takie jak Microsoft SharePoint. W podsieci tylnej znajduje się miejsce, w którym umieścisz maszyny wirtualne programu SQL Server. Jeśli wcześniej zmienisz **zmienne $affinityGroupName** i **$virtualNetworkName,** musisz również zmienić odpowiednie nazwy poniżej.

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

5. Utwórz konto magazynu skojarzone z utworzoną grupą koligacji i ustaw je jako bieżące konto magazynu w ramach subskrypcji.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Utwórz serwer kontrolera domeny w nowym zestawie usług i dostępności usługi w chmurze.

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

    Te potokowe polecenia wykonują następujące czynności:

   * **New-AzureVMConfig** tworzy konfigurację maszyny Wirtualnej.
   * **Add-AzureProvisioningConfig** udostępnia parametry konfiguracji autonomicznego serwera Windows.
   * **Add-AzureDataDisk** dodaje dysk danych, który będzie używany do przechowywania danych usługi Active Directory, z opcją buforowania ustawioną na Brak.
   * **New-AzureVM** tworzy nową usługę w chmurze i tworzy nową maszynę wirtualną platformy Azure w nowej usłudze w chmurze.

7. Poczekaj, aż nowa maszyna wirtualna zostanie w pełni aprowizowana, i pobierz plik pulpitu zdalnego do katalogu roboczego. Ponieważ nowa maszyna wirtualna platformy Azure zajmuje `while` dużo czasu, aby aprowizować, pętla kontynuuje sondowania nowej maszyny Wirtualnej, dopóki nie jest gotowy do użycia.

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

Serwer kontrolera domeny został pomyślnie zainicjowany. Następnie skonfigurujesz domenę usługi Active Directory na tym serwerze kontrolera domeny. Pozostaw otwarte okno programu PowerShell na komputerze lokalnym. Użyjesz go ponownie później, aby utworzyć dwie maszyny wirtualne programu SQL Server.

## <a name="configure-the-domain-controller"></a>Konfigurowanie kontrolera domeny
1. Połącz się z serwerem kontrolera domeny, uruchamiając plik pulpitu zdalnego. Użyj nazwy użytkownika AzureAdmin administratora komputera i hasła **Contoso!000**, które zostały określone podczas tworzenia nowej maszyny wirtualnej.
2. Otwórz okno programu PowerShell w trybie administratora.
3. Uruchom następujący **model DCPROMO. Polecenie EXE,** aby skonfigurować domenę **corp.contoso.com,** z katalogami danych na dysku M.

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

    Po zakończeniu polecenia maszyna wirtualna zostanie automatycznie uruchomiona.

4. Ponownie połącz się z serwerem kontrolera domeny, uruchamiając plik pulpitu zdalnego. Tym razem zaloguj się jako **CORP\Administrator**.
5. Otwórz okno programu PowerShell w trybie administratora i zaimportuj moduł programu PowerShell usługi Active Directory za pomocą następującego polecenia:

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

    **Corp\Install** służy do konfigurowania wszystkiego, co związane z wystąpieniami usługi programu SQL Server, klastrem trybu failover i grupą dostępności. **CORP\SQLSvc1** i **CORP\SQLSvc2** są używane jako konta usługi SQL Server dla dwóch maszyn wirtualnych programu SQL Server.
7. Następnie uruchom następujące polecenia, aby nadać **corp\Install** uprawnienia do tworzenia obiektów komputera w domenie.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Identyfikator GUID określony powyżej jest identyfikatorem GUID dla typu obiektu komputera. Konto **CORP\Install** wymaga uprawnienia **Odczyt wszystkich właściwości** i Tworzenie obiektów **komputera,** aby utworzyć obiekty Active Direct dla klastra trybu failover. Uprawnienie **Odczyt wszystkich właściwości** jest już domyślnie naone corp\install, więc nie trzeba jej udzielać jawnie. Aby uzyskać więcej informacji na temat uprawnień potrzebnych do utworzenia klastra trybu failover, zobacz Przewodnik krok po kroku klastra trybu [failover: Konfigurowanie kont w usłudze Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Po zakończeniu konfigurowania usługi Active Directory i obiektów użytkownika utworzysz dwie maszyny wirtualne programu SQL Server i dołączysz je do tej domeny.

## <a name="create-the-sql-server-vms"></a>Tworzenie maszyn wirtualnych programu SQL Server
1. Kontynuuj korzystanie z okna programu PowerShell, które jest otwarte na komputerze lokalnym. Zdefiniuj następujące zmienne dodatkowe:

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

    Adres IP **10.10.0.4** jest zazwyczaj przypisany do pierwszej maszyny Wirtualnej utworzonej w podsieci **10.10.0.0/16** sieci wirtualnej platformy Azure. Należy sprawdzić, czy jest to adres serwera kontrolera domeny, uruchamiając **program IPCONFIG**.
2. Uruchom następujące polecenia potokowe, aby utworzyć pierwszą maszynę wirtualną w klastrze trybu failover o nazwie **ContosoQuorum**:

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

    Należy zwrócić uwagę na następujące kwestie dotyczące powyższego polecenia:

   * **New-AzureVMConfig** tworzy konfigurację maszyny Wirtualnej o żądanej nazwie zestawu dostępności. Kolejne maszyny wirtualne zostaną utworzone z taką samą nazwą zestawu dostępności, dzięki czemu są one połączone z tym samym zestawem dostępności.
   * **Add-AzureProvisioningConfig** łączy maszynę wirtualną z domeną usługi Active Directory, która została utworzona.
   * **Set-AzureSubnet** umieszcza maszynę wirtualną w tylnej podsieci.
   * **New-AzureVM** tworzy nową usługę w chmurze i tworzy nową maszynę wirtualną platformy Azure w nowej usłudze w chmurze. **Parametr DnsSettings** określa, że serwer DNS serwerów w nowej usłudze w chmurze ma adres IP **10.10.0.4**. Jest to adres IP serwera kontrolera domeny. Ten parametr jest potrzebny, aby umożliwić nowe maszyny wirtualne w usłudze w chmurze, aby pomyślnie dołączyć do domeny usługi Active Directory. Bez tego parametru należy ręcznie ustawić ustawienia IPv4 na maszynie wirtualnej, aby używał serwera kontrolera domeny jako podstawowego serwera DNS po zainicjowaniu obsługi administracyjnej maszyny wirtualnej, a następnie dołączyć maszynę wirtualną do domeny usługi Active Directory.
3. Uruchom następujące polecenia potokowe, aby utworzyć maszyny wirtualne programu SQL Server o nazwach **ContosoSQL1** i **ContosoSQL2**.

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

    Należy zwrócić uwagę na powyższe polecenia:

   * **New-AzureVMConfig** używa tej samej nazwy zestawu dostępności co serwer kontrolera domeny i używa obrazu programu SQL Server 2012 z dodatkiem Service Pack 1 Enterprise Edition w galerii maszyn wirtualnych. Ustawia również dysk systemu operacyjnego tylko do odczytu buforowania (bez buforowania zapisu). Zaleca się migrowanie plików bazy danych na oddzielny dysk danych dołączony do maszyny Wirtualnej i skonfigurowanie jej bez buforowania odczytu lub zapisu. Jednak następną najlepszą rzeczą jest usunięcie buforowania zapisu na dysku systemu operacyjnego, ponieważ nie można usunąć buforowania odczytu na dysku systemu operacyjnego.
   * **Add-AzureProvisioningConfig** łączy maszynę wirtualną z domeną usługi Active Directory, która została utworzona.
   * **Set-AzureSubnet** umieszcza maszynę wirtualną w tylnej podsieci.
   * **Add-AzureEndpoint** dodaje punkty końcowe dostępu, dzięki czemu aplikacje klienckie mogą uzyskiwać dostęp do tych wystąpień usług programu SQL Server w Internecie. Różne porty są podane do ContosoSQL1 i ContosoSQL2.
   * **New-AzureVM** tworzy nową maszynę wirtualną programu SQL Server w tej samej usłudze w chmurze co ContosoQuorum. Maszyny wirtualne należy umieścić w tej samej usłudze w chmurze, jeśli chcesz, aby były w tym samym zestawie dostępności.
4. Poczekaj, aż każda maszyna wirtualna zostanie w pełni aprowizowana i aby każda maszyna wirtualna pobrać plik pulpitu zdalnego do katalogu roboczego. Pętla `for` przechodzi przez trzy nowe maszyny wirtualne i wykonuje polecenia wewnątrz nawiasów kręconych najwyższego poziomu dla każdego z nich.

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

    Maszyny wirtualne programu SQL Server są teraz aprowizowane i uruchomione, ale są instalowane z programem SQL Server z opcjami domyślnymi.

## <a name="initialize-the-failover-cluster-vms"></a>Inicjowanie maszyn wirtualnych klastra trybu failover
W tej sekcji należy zmodyfikować trzy serwery, które będą używane w klastrze trybu failover i instalacji programu SQL Server. Są to:

* Wszystkie serwery: Należy zainstalować funkcję **klastrowania trybu failover.**
* Wszystkie serwery: Musisz dodać **CORP\Install** jako **administratora**komputera .
* ContosoSQL1 i ContosoSQL2 tylko: Należy dodać **CORP\Install** jako rolę **sysadmin** w domyślnej bazie danych.
* Tylko contosoSQL1 i ContosoSQL2: Musisz dodać **nt authority\system** jako logowanie z następującymi uprawnieniami:

  * Zmienianie dowolnej grupy dostępności
  * Łączenie języka SQL
  * Wyświetlanie stanu serwera
* Tylko contosoSQL1 i ContosoSQL2: Protokół **TCP** jest już włączony na maszynie wirtualnej programu SQL Server. Jednak nadal trzeba otworzyć zaporę dostępu zdalnego programu SQL Server.

Teraz możesz zacząć. Począwszy od **contosoQuorum,** wykonaj poniższe czynności:

1. Połącz się **z contosoQuorum,** uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika **AzureAdmin** administratora komputera i hasła **Contoso!000**, które zostały określone podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie połączone z **corp.contoso.com**.
3. Przed kontynuowaniem poczekaj na zakończenie instalacji programu SQL Server z uruchamianiem zadań automatycznego inicjowania.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję klastrowania trybu failover systemu Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Zainstaluj** jako administratora lokalnego.

        net localgroup administrators "CORP\Install" /Add
7. Wyloguj się z contosoQuorum. Gotowe z tym serwerem teraz.

        logoff.exe

Następnie należy zainicjować **contosoSQL1** i **ContosoSQL2**. Wykonaj poniższe kroki, które są identyczne dla obu maszyn wirtualnych programu SQL Server.

1. Połącz się z dwiema maszynami wirtualnymi programu SQL Server, uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika **AzureAdmin** administratora komputera i hasła **Contoso!000**, które zostały określone podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie połączone z **corp.contoso.com**.
3. Przed kontynuowaniem poczekaj na zakończenie instalacji programu SQL Server z uruchamianiem zadań automatycznego inicjowania.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję klastrowania trybu failover systemu Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Zainstaluj** jako administratora lokalnego.

        net localgroup administrators "CORP\Install" /Add
7. Zaimportowanie dostawcy programu SQL Server PowerShell.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Dodaj **CORP\Install** jako rolę sysadmin dla domyślnego wystąpienia programu SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Dodaj **NT AUTHORITY\System** jako logowanie z trzema uprawnieniami opisanymi powyżej.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otwórz zaporę dostępu zdalnego programu SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Wyloguj się z obu maszyn wirtualnych.

         logoff.exe

Na koniec możesz skonfigurować grupę dostępności. Dostawca programu SQL Server PowerShell będzie używany do wykonywania wszystkich prac nad **programem ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurowanie grupy dostępności
1. Połącz się ponownie z **urządzeniem ContosoSQL1,** uruchamiając pliki pulpitu zdalnego. Zamiast logować się przy użyciu konta komputera, zaloguj się za pomocą **corp\Install**.
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
4. Zaimportowanie dostawcy programu SQL Server PowerShell.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Zmień konto usługi PROGRAMU SQL Server dla contosoSQL1 na CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Zmień konto usługi PROGRAMU SQL Server dla contosoSQL2 na CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Pobierz **CreateAzureFailoverCluster.ps1** z [tworzenia klastra trybu failover dla grup dostępności zawsze włączone w usłudze Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do lokalnego katalogu roboczego. Użyjesz tego skryptu, aby ułatwić tworzenie funkcjonalnego klastra trybu failover. Aby uzyskać ważne informacje dotyczące interakcji klastrowania trybu failover systemu Windows z siecią platformy Azure, zobacz [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server w platformie Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Zmień katalog roboczy i utwórz klaster trybu failover za pomocą pobranego skryptu.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Włącz zawsze włączone grupy dostępności dla domyślnych wystąpień programu SQL Server w **językach ContosoSQL1** i **ContosoSQL2**.

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
10. Utwórz katalog kopii zapasowych i udziel uprawnień dla kont usługi PROGRAMU SQL Server. Ten katalog służy do przygotowania bazy danych dostępności w replice pomocniczej.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Utwórz bazę danych na **ContosoSQL1** o nazwie **MyDB1**, wykonaj zarówno pełną kopię zapasową, jak i kopię zapasową dziennika i przywróć je na **ContosoSQL2** za pomocą opcji **WITH NORECOVERY.**

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Utwórz punkty końcowe grupy dostępności na maszynach wirtualnych programu SQL Server i ustaw odpowiednie uprawnienia do punktów końcowych.

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
14. Na koniec utwórz grupę dostępności i dołącz do repliki pomocniczej do grupy dostępności.

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

## <a name="next-steps"></a>Następne kroki
Pomyślnie zaimplementowano program SQL Server Always On, tworząc grupę dostępności na platformie Azure. Aby skonfigurować odbiornik dla tej grupy dostępności, zobacz [Konfigurowanie odbiornika równoważenia obciążenia sieciowego dla grup dostępności Zawsze włączone na platformie Azure](../classic/ps-sql-int-listener.md).

Aby uzyskać inne informacje dotyczące korzystania z programu SQL Server na platformie Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
