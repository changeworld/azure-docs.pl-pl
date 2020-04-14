---
title: Konfigurowanie sieci VPN typu "p2s) typu "point-to-site" w systemie Windows do użytku z plikami platformy Azure | Dokumenty firmy Microsoft
description: Jak skonfigurować sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z usługą Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273481"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu "punkt-lokacja) w systemie Windows do użytku z plikami platformy Azure
Za pomocą połączenia sieci VPN typu punkt-lokacja (P2S) można zainstalować udziały plików platformy Azure za pośrednictwem protokołu SMB spoza platformy Azure bez otwierania portu 445. Połączenie sieci VPN typu punkt-lokacja jest połączeniem sieci VPN między platformą Azure a klientem indywidualnym. Aby korzystać z połączenia sieci VPN P2S z usługą Azure Files, połączenie sieci VPN P2S musi być skonfigurowane dla każdego klienta, który chce się połączyć. Jeśli masz wielu klientów, którzy muszą połączyć się z udziałami plików platformy Azure z sieci lokalnej, możesz użyć połączenia sieci VPN lokacja lokacja (S2S) zamiast połączenia typu punkt-lokacja dla każdego klienta. Aby dowiedzieć się więcej, zobacz [Konfigurowanie sieci VPN między lokacjami do użytku z usługą Azure Files](storage-files-configure-s2s-vpn.md).

Zdecydowanie zaleca się [przeczytanie zagadnień dotyczących sieci bezpośredniego dostępu](storage-files-networking-overview.md) do udziału plików platformy Azure przed kontynuowaniem tego artykułu, aby zapoznać się z pełnym omówieniem opcji sieci dostępnych dla usług Azure Files.

W tym artykule opisano kroki konfigurowania sieci VPN typu punkt-lokacja w systemie Windows (klient Windows i windows server) do instalowania udziałów plików platformy Azure bezpośrednio lokalnie. Jeśli chcesz przekierować ruch usługi Azure File Sync przez sieć VPN, zobacz [konfigurowanie ustawień serwera proxy i zapory usługi Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Najnowsza wersja modułu programu Azure PowerShell. Aby uzyskać więcej informacji na temat instalowania programu Azure PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) i wybieranie systemu operacyjnego. Jeśli wolisz używać interfejsu wiersza polecenia platformy Azure w systemie Windows, może jednak instrukcje poniżej są prezentowane dla programu Azure PowerShell.

- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzania reprezentującymi współużytkową pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Więcej informacji na temat wdrażania udziałów plików i kont magazynu platformy Azure można dowiedzieć się więcej w obszarze [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. Aby dowiedzieć się więcej o tworzeniu prywatnego punktu końcowego, zobacz [Konfigurowanie sieciowych punktów końcowych usługi Azure Files](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Wdrażanie sieci wirtualnej
Aby uzyskać dostęp do udziału plików platformy Azure i innych zasobów platformy Azure z lokalnego za pośrednictwem sieci VPN typu punkt lokacja, należy utworzyć sieć wirtualną lub sieć wirtualną. Połączenie sieci VPN P2S, które zostanie utworzone automatycznie, jest pomostem między lokalnym komputerem z systemem Windows a tą siecią wirtualną platformy Azure.

Następujące programu PowerShell utworzy sieć wirtualną platformy Azure z trzema podsieciami: jedną dla punktu końcowego usługi konta magazynu, jedną dla prywatnego punktu końcowego konta magazynu, która jest wymagana do uzyskania dostępu do konta magazynu lokalnie bez tworzenia niestandardowego routingu dla publicznego adresu IP konta magazynu, które mogą ulec zmianie, i jeden dla bramy sieci wirtualnej, która zapewnia usługę sieci VPN. 

Pamiętaj, `<region>`aby `<resource-group>`zastąpić `<desired-vnet-name>` , i z odpowiednimi wartościami dla środowiska.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Tworzenie certyfikatu głównego dla uwierzytelniania sieci VPN
Aby połączenia sieci VPN z lokalnych maszyn z systemem Windows były uwierzytelniane w celu uzyskania dostępu do sieci wirtualnej, należy utworzyć dwa certyfikaty: certyfikat główny, który zostanie dostarczony do bramy maszyny wirtualnej, oraz certyfikat klienta, który zostanie podpisany za pomocą certyfikatu głównego. Następujący program PowerShell tworzy certyfikat główny; certyfikat klienta zostanie utworzony po utworzeniu bramy sieci wirtualnej platformy Azure z informacjami z bramy. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Wdrażanie bramy sieci wirtualnej
Brama sieci wirtualnej platformy Azure to usługa, z którą będą się łączyć lokalne maszyny z systemem Windows. Wdrożenie tej usługi wymaga dwóch podstawowych składników: publicznego adresu IP, który będzie identyfikował bramę do klientów, gdziekolwiek są na świecie, oraz certyfikatu głównego utworzonego wcześniej, który będzie używany do uwierzytelniania klientów.

Pamiętaj, `<desired-vpn-name-here>` aby zastąpić nazwą, którą chcesz dla tych zasobów.

> [!Note]  
> Wdrożenie bramy sieci wirtualnej platformy Azure może potrwać do 45 minut. Podczas wdrażania tego zasobu ten skrypt programu PowerShell zablokuje wdrożenie do wykonania. Jest to oczekiwane.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Tworzenie certyfikatu klienta
Certyfikat klienta jest tworzony przy za pomocą identyfikatora URI bramy sieci wirtualnej. Ten certyfikat jest podpisany przy poprzednim utworzonym certyfikacie głównym.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Konfigurowanie klienta sieci VPN
Brama sieci wirtualnej platformy Azure utworzy pakiet do pobrania z plikami konfiguracyjnymi wymaganymi do zainicjowania połączenia sieci VPN na lokalnym komputerze z systemem Windows. Skonfigurujemy połączenie VPN za pomocą funkcji [Zawsze na sieci VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) systemu Windows 10/Windows Server 2016+. Ten pakiet zawiera również pakiety wykonywalne, które skonfigurują starszego klienta sieci VPN systemu Windows, jeśli jest to pożądane. W tym przewodniku używa się sieci VPN zawsze włączone, a nie starszego klienta sieci VPN systemu Windows, ponieważ klient zawsze włączonej sieci VPN umożliwia użytkownikom końcowym łączenie się/rozłączanie z siecią VPN platformy Azure bez uprawnień administratora do komputera. 

W poniższym skrypcie zostanie zainstalowany certyfikat klienta wymagany do uwierzytelnienia bramy sieci wirtualnej, pobierze i zainstaluje pakiet sieci VPN. Pamiętaj, `<computer1>` aby `<computer2>` zastąpić i na żądanych komputerach. Ten skrypt można uruchomić na dowolną liczbę komputerów, dodając więcej `$sessions` sesji programu PowerShell do tablicy. Konto użytkowania musi być administratorem na każdym z tych komputerów. Jeśli jednym z tych komputerów jest komputer lokalny, z którego uruchamiasz skrypt, należy uruchomić skrypt z podwyższonej liczby sesji programu PowerShell. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Udostępnianie plików platformy Azure
Po skonfigurowaniu sieci VPN typu punkt-lokacja można jej użyć do zainstalowania udziału plików platformy Azure na komputerach skonfigurowanych za pośrednictwem programu PowerShell. Poniższy przykład spowoduje zainstalowanie udziału, wyświetl listę katalogu głównego udziału, aby udowodnić, że udział jest faktycznie zainstalowany, i odinstalować udział. Niestety nie jest możliwe do zainstalowania udziału uporczywie przez powershell komunikacji zdalnej. Aby stale montować, zobacz [Używanie udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Zobacz też
- [Zagadnienia dotyczące sieci bezpośredniego dostępu do udziału plików platformy Azure](storage-files-networking-overview.md)
- [Konfigurowanie sieci VPN typu "point-to-site) w systemie Linux do użytku z plikami azure](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurowanie sieci VPN lokacja lokacja (S2S) do użytku z usługą Azure Files](storage-files-configure-s2s-vpn.md)
