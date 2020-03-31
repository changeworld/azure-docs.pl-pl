---
title: Konfigurowanie sieci VPN typu "point-to-site) w systemie Linux do użytku z plikami azure | Dokumenty firmy Microsoft
description: Jak skonfigurować sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użytku z usługą Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cfff05ed52258ee448d83a521b99dca7d356a0f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061046"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu "point-to-site) w systemie Linux do użytku z plikami azure
Za pomocą połączenia sieci VPN typu punkt-lokacja (P2S) można zainstalować udziały plików platformy Azure za pośrednictwem protokołu SMB spoza platformy Azure bez otwierania portu 445. Połączenie sieci VPN typu punkt-lokacja jest połączeniem sieci VPN między platformą Azure a klientem indywidualnym. Aby korzystać z połączenia sieci VPN P2S z usługą Azure Files, połączenie sieci VPN P2S musi być skonfigurowane dla każdego klienta, który chce się połączyć. Jeśli masz wielu klientów, którzy muszą połączyć się z udziałami plików platformy Azure z sieci lokalnej, możesz użyć połączenia sieci VPN lokacja lokacja (S2S) zamiast połączenia typu punkt-lokacja dla każdego klienta. Aby dowiedzieć się więcej, zobacz [Konfigurowanie sieci VPN między lokacjami do użytku z usługą Azure Files](storage-files-configure-s2s-vpn.md).

Zdecydowanie zaleca się [przeczytanie przeglądu sieci usługi Azure Files](storage-files-networking-overview.md) przed kontynuowaniem tego artykułu, aby uzyskać pełną dyskusję na temat opcji sieci dostępnych dla usług Azure Files.

W artykule opisano kroki konfigurowania sieci VPN typu punkt-lokacja w systemie Linux do instalowania udziałów plików platformy Azure bezpośrednio lokalnie. Jeśli chcesz przekierować ruch usługi Azure File Sync przez sieć VPN, zobacz [konfigurowanie ustawień serwera proxy i zapory usługi Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Najnowsza wersja interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia programu Azure PowerShell](https://docs.microsoft.com/cli/azure/install-azure-cli) i wybieranie systemu operacyjnego. Jeśli wolisz używać modułu programu Azure PowerShell w systemie Linux, może jednak poniższe instrukcje są prezentowane dla interfejsu wiersza polecenia platformy Azure.

- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzania reprezentującymi współużytkową pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Więcej informacji na temat wdrażania udziałów plików i kont magazynu platformy Azure można dowiedzieć się więcej w obszarze [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. Aby dowiedzieć się więcej o tworzeniu prywatnego punktu końcowego, zobacz [Konfigurowanie sieciowych punktów końcowych usługi Azure Files](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Zainstaluj wymagane oprogramowanie
Brama sieci wirtualnej platformy Azure może dostarczać połączenia VPN przy użyciu kilku protokołów VPN, w tym IPsec i OpenVPN. W tym przewodniku pokazano, jak używać protokołu IPsec i używa pakietu strongSwan, aby zapewnić obsługę w systemie Linux. 

> Zweryfikowano z Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Wdrażanie sieci wirtualnej 
Aby uzyskać dostęp do udziału plików platformy Azure i innych zasobów platformy Azure z lokalnego za pośrednictwem sieci VPN typu punkt lokacja, należy utworzyć sieć wirtualną lub sieć wirtualną. Połączenie sieci VPN P2S, które automatycznie utworzysz, jest pomostem między lokalnym komputerem z systemem Linux a tą siecią wirtualną platformy Azure.

Poniższy skrypt utworzy sieć wirtualną platformy Azure z trzema podsieciami: jedną dla punktu końcowego usługi konta magazynu, jedną dla prywatnego punktu końcowego konta magazynu, która jest wymagana do uzyskania dostępu do konta magazynu lokalnie bez tworzenia niestandardowych routingu dla publicznego adresu IP konta magazynu, które mogą ulec zmianie, i jeden dla bramy sieci wirtualnej, która zapewnia usługę sieci VPN. 

Pamiętaj, `<region>`aby `<resource-group>`zastąpić `<desired-vnet-name>` , i z odpowiednimi wartościami dla środowiska.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Tworzenie certyfikatów dla uwierzytelniania sieci VPN
Aby połączenia sieci VPN z lokalnych maszyn z systemem Linux były uwierzytelnione w celu uzyskania dostępu do sieci wirtualnej, należy utworzyć dwa certyfikaty: certyfikat główny, który zostanie dostarczony do bramy maszyny wirtualnej, oraz certyfikat klienta, który zostanie podpisana certyfikatem głównym. Poniższy skrypt tworzy wymagane certyfikaty.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Wdrażanie bramy sieci wirtualnej
Brama sieci wirtualnej platformy Azure to usługa, z którą będą łączeć się lokalne maszyny z systemem Linux. Wdrożenie tej usługi wymaga dwóch podstawowych składników: publicznego adresu IP, który będzie identyfikował bramę do klientów, gdziekolwiek są na świecie, oraz certyfikatu głównego utworzonego wcześniej, który będzie używany do uwierzytelniania klientów.

Pamiętaj, `<desired-vpn-name-here>` aby zastąpić nazwą, którą chcesz dla tych zasobów.

> [!Note]  
> Wdrożenie bramy sieci wirtualnej platformy Azure może potrwać do 45 minut. Gdy ten zasób jest wdrażany, ten skrypt skrypt bash zablokuje dla wdrożenia do ukończenia. Jest to oczekiwane.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Konfigurowanie klienta sieci VPN
Brama sieci wirtualnej platformy Azure utworzy pakiet do pobrania z plikami konfiguracyjnymi wymaganymi do zainicjowania połączenia sieci VPN na lokalnym komputerze z systemem Linux. Poniższy skrypt umieści utworzone certyfikaty we właściwym miejscu `ipsec.conf` i skonfiguruje plik z poprawnymi wartościami z pliku konfiguracyjnego w pakiecie do pobrania.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Udostępnianie plików platformy Azure
Po skonfigurowaniu sieci VPN typu punkt-lokacja możesz zainstalować udział plików platformy Azure. Poniższy przykład spowoduje zainstalowanie udziału nietrwałe. Aby stale montować, zobacz [Używanie udziału plików platformy Azure w systemie Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Zobacz też
- [Omówienie sieci usług Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie sieci VPN typu "punkt-lokacja) w systemie Windows do użytku z plikami platformy Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurowanie sieci VPN lokacja lokacja (S2S) do użytku z usługą Azure Files](storage-files-configure-s2s-vpn.md)