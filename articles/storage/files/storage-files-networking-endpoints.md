---
title: Konfigurowanie sieciowych punktów końcowych usługi Azure Files | Dokumenty firmy Microsoft
description: Omówienie opcji sieciowych dla usług Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082507"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurowanie sieciowych punktów końcowych usługi Azure Files
Usługa Azure Files udostępnia dwa główne typy punktów końcowych dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w sieci wirtualnej i mają prywatny adres IP z przestrzeni adresowej tej sieci wirtualnej.

Publiczne i prywatne punkty końcowe istnieją na koncie magazynu platformy Azure. Konto magazynu jest konstrukcją zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki.

W tym artykule skupiono się na tym, jak skonfigurować punkty końcowe konta magazynu do bezpośredniego uzyskiwania dostępu do udziału plików platformy Azure. Większość szczegółów podanych w tym dokumencie dotyczy również sposobu współpracy usługi Azure File Sync z publicznymi i prywatnymi punktami końcowymi dla konta magazynu, jednak aby uzyskać więcej informacji na temat zagadnień dotyczących sieci wdrożenia usługi Azure File Sync, zobacz [konfigurowanie ustawień serwera proxy i zapory usługi Azure File Sync](storage-sync-files-firewall-and-proxy.md).

Przed przeczytaniem tego przewodnika [zalecamy przeczytanie zagadnień dotyczących sieci usługi Azure Files.](storage-files-networking-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne
- W tym artykule przyjęto założenie, że subskrypcja platformy Azure została już utworzona. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- W tym artykule przyjęto założenie, że utworzono już udział plików platformy Azure na koncie magazynu, z którym chcesz się połączyć z lokalnymi. Aby dowiedzieć się, jak utworzyć udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).
- Jeśli zamierzasz korzystać z programu Azure PowerShell, [zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
Utworzenie prywatnego punktu końcowego dla konta magazynu spowoduje wdrożenie następujących zasobów platformy Azure:

- **Prywatny punkt końcowy:** zasób platformy Azure reprezentujący prywatny punkt końcowy konta magazynu. Można to potraktować jako zasób łączący konto magazynu i interfejs sieciowy.
- **Interfejs sieciowy (NIC)**: Interfejs sieciowy, który utrzymuje prywatny adres IP w określonej sieci wirtualnej/podsieci. Jest to dokładnie ten sam zasób, który zostanie wdrożony podczas wdrażania maszyny wirtualnej, jednak zamiast być przypisany do maszyny wirtualnej, jest własnością prywatnego punktu końcowego.
- **Prywatna strefa DNS:** Jeśli nigdy wcześniej nie wdrożono prywatnego punktu końcowego dla tej sieci wirtualnej, zostanie wdrożona nowa prywatna strefa DNS dla sieci wirtualnej. Zostanie również utworzony rekord DNS A dla konta magazynu w tej strefie DNS. Jeśli już wdrożono prywatny punkt końcowy w tej sieci wirtualnej, do istniejącej strefy DNS zostanie dodany nowy rekord A dla konta magazynu. Wdrażanie strefy DNS jest opcjonalne, jednak wysoce zalecane i wymagane, jeśli montujesz udziały plików platformy Azure z jednostką usługi AD lub przy użyciu interfejsu API FileREST.

> [!Note]  
> W tym artykule użyto sufiksu DNS `core.windows.net`konta magazynu dla regionów publicznych platformy Azure . Ten komentarz dotyczy również chmur suwerennych platformy Azure, takich jak chmura azure dla instytucji rządowych i chmura Azure China — po prostu zastąp odpowiednie sufiksy dla danego środowiska. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do konta magazynu, dla którego chcesz utworzyć prywatny punkt końcowy. W spisie treści dla konta magazynu wybierz **opcję Prywatne połączenia punktów końcowych**, a następnie + Prywatny punkt **końcowy,** aby utworzyć nowy prywatny punkt końcowy. 

![Zrzut ekranu przedstawiający element połączeń prywatnych punktów końcowych w spisie zawartości konta magazynu](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Wynikowo-kreator ma wiele stron do ukończenia.

W **obszarze Podstawowe strony** bloku wybierz żądaną grupę zasobów, nazwę i region dla prywatnego punktu końcowego. Mogą to być cokolwiek chcesz, nie muszą odpowiadać konta magazynu w każdym razie, chociaż należy utworzyć prywatny punkt końcowy w tym samym regionie, w którym chcesz utworzyć prywatny punkt końcowy.

![Zrzut ekranu przedstawiający sekcję Podstawy sekcji tworzenie prywatnego punktu końcowego](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

W **bloku Zasób** wybierz przycisk opcji **Połącz z zasobem platformy Azure w moim katalogu**. W obszarze **Typ zasobu**wybierz pozycję **Microsoft.Storage/storageAccounts** dla typu zasobu. Pole **Zasób** to konto magazynu z udziałem plików platformy Azure, z którym chcesz się połączyć. Docelowy podsób jest **plik,** ponieważ jest to dla usługi Azure Files.

**Blok Konfiguracji** umożliwia wybranie określonej sieci wirtualnej i podsieci, do której chcesz dodać prywatny punkt końcowy. Wybierz utworzoną sieć wirtualną powyżej. Należy wybrać odrębną podsieć z podsieci, do której dodano punkt końcowy usługi powyżej. Blok Konfiguracji zawiera również informacje dotyczące tworzenia/aktualizowania prywatnej strefy DNS. Zalecamy użycie `privatelink.file.core.windows.net` strefy domyślnej.

![Zrzut ekranu przedstawiający sekcję Konfiguracja](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Kliknij **przycisk Przejrzyj + utwórz,** aby utworzyć prywatny punkt końcowy. 

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie dalej DNS zgodnie z opisem [w tym miejscu,](storage-files-networking-dns.md)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany, uruchamiając następujące polecenia z programu PowerShell, wiersza polecenia lub terminala (działa dla systemu Windows, Linux lub macOS). Należy zastąpić `<storage-account-name>` odpowiednią nazwą konta magazynu:

```
nslookup <storage-account-name>.file.core.windows.net
```

Jeśli wszystko działało pomyślnie, powinieneś zobaczyć `192.168.0.5` następujące dane wyjściowe, gdzie jest prywatny adres IP prywatnego punktu końcowego w sieci wirtualnej (dane wyjściowe wyświetlane dla systemu Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby utworzyć prywatny punkt końcowy dla konta magazynu, należy najpierw uzyskać odwołanie do konta magazynu i podsieci sieci wirtualnej, do której chcesz dodać prywatny punkt końcowy. `<storage-account-resource-group-name>`Zastąp `<vnet-name>`, `<vnet-subnet-name>` `<storage-account-name>`, `<vnet-resource-group-name>`, i poniżej:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Aby utworzyć prywatny punkt końcowy, należy utworzyć połączenie usługi łącza prywatnego z kontem magazynu. Połączenie usługi łącza prywatnego jest dane wejściowe do tworzenia prywatnego punktu końcowego. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Utworzenie prywatnej strefy DNS platformy Azure umożliwia oryginalną `storageaccount.file.core.windows.net` nazwę konta magazynu, na przykład do rozpoznawania prywatnego adresu IP wewnątrz sieci wirtualnej. Chociaż opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest jawnie wymagane do instalowania udziału plików platformy Azure przy użyciu jednostki użytkownika usługi AD lub uzyskiwania dostępu za pośrednictwem interfejsu API REST.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

Teraz, gdy masz odwołanie do prywatnej strefy DNS, musisz utworzyć rekord A dla konta magazynu.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie dns zgodnie z opisem [w tym miejscu,](storage-files-networking-dns.md)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany za pomocą następujących poleceń:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Jeśli wszystko działało pomyślnie, należy zobaczyć następujące `192.168.0.5` dane wyjściowe, gdzie jest prywatny adres IP prywatnego punktu końcowego w sieci wirtualnej:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć prywatny punkt końcowy dla konta magazynu, należy najpierw uzyskać odwołanie do konta magazynu i podsieci sieci wirtualnej, do której chcesz dodać prywatny punkt końcowy. `<storage-account-resource-group-name>`Zastąp `<vnet-name>`, `<vnet-subnet-name>` `<storage-account-name>`, `<vnet-resource-group-name>`, i poniżej:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Aby utworzyć prywatny punkt końcowy, należy najpierw upewnić się, że zasady sieci prywatnego punktu końcowego podsieci są wyłączone. Następnie można utworzyć prywatny punkt `az network private-endpoint create` końcowy za pomocą polecenia

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Utworzenie prywatnej strefy DNS platformy Azure umożliwia oryginalną `storageaccount.file.core.windows.net` nazwę konta magazynu, na przykład do rozpoznawania prywatnego adresu IP wewnątrz sieci wirtualnej. Chociaż opcjonalne z punktu widzenia tworzenia prywatnego punktu końcowego, jest jawnie wymagane do instalowania udziału plików platformy Azure przy użyciu jednostki użytkownika usługi AD lub uzyskiwania dostępu za pośrednictwem interfejsu API REST.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Teraz, gdy masz odwołanie do prywatnej strefy DNS, musisz utworzyć rekord A dla konta magazynu.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie dns zgodnie z opisem [w tym miejscu,](storage-files-networking-dns.md)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany za pomocą następujących poleceń:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Jeśli wszystko działało pomyślnie, należy zobaczyć następujące `192.168.0.5` dane wyjściowe, gdzie jest prywatny adres IP prywatnego punktu końcowego w sieci wirtualnej:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Ograniczanie dostępu do publicznego punktu końcowego
Można ograniczyć dostęp do publicznego punktu końcowego przy użyciu ustawień zapory konta magazynu. Ogólnie rzecz biorąc większość zasad zapory dla konta magazynu ograniczy dostęp do sieci do jednej lub więcej sieci wirtualnych. Istnieją dwa podejścia do ograniczania dostępu do konta magazynu do sieci wirtualnej:

- [Utwórz jeden lub więcej prywatnych punktów końcowych dla konta magazynu](#create-a-private-endpoint) i ogranicz cały dostęp do publicznego punktu końcowego. Gwarantuje to, że tylko ruch pochodzący z żądanych sieci wirtualnych można uzyskać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do jednej lub więcej sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. Po ograniczeniu ruchu do konta magazynu za pośrednictwem punktu końcowego usługi, nadal uzyskujesz dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Ograniczanie dostępu do publicznego punktu końcowego
Gdy cały dostęp do publicznego punktu końcowego jest ograniczony, konto magazynu nadal można uzyskać dostęp za pośrednictwem jego prywatnych punktów końcowych. W przeciwnym razie prawidłowe żądania do publicznego punktu końcowego konta magazynu zostaną odrzucone. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do konta magazynu, dla którego chcesz ograniczyć dostęp do publicznego punktu końcowego. W spisie treści dla konta magazynu wybierz pozycję **Zapory i sieci wirtualne**.

U góry strony wybierz przycisk opcji **Wybrane sieci.** Spowoduje to odsunienie szeregu ustawień do kontrolowania ograniczenia publicznego punktu końcowego. Zaznacz **pole wyboru Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta usługi,** aby umożliwić zaufanym usługom firmy Microsoft, takim jak Azure File Sync, dostęp do konta magazynu.

![Zrzut ekranu przedstawiający przyłącza Zapory sieciowe i sieci wirtualne z odpowiednimi ograniczeniami](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Następujące polecenie programu PowerShell odmówi całego ruchu do publicznego punktu końcowego konta magazynu. Należy zauważyć, że `-Bypass` to `AzureServices`polecenie ma parametr ustawiony na . Umożliwi to zaufane usługi pierwszej firmy, takie jak Azure File Sync, aby uzyskać dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Następujące polecenie interfejsu wiersza polecenia spowoduje odmowę całego ruchu do publicznego punktu końcowego konta magazynu. Należy zauważyć, że `-bypass` to `AzureServices`polecenie ma parametr ustawiony na . Umożliwi to zaufane usługi pierwszej firmy, takie jak Azure File Sync, aby uzyskać dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Ograniczanie dostępu do publicznego punktu końcowego do określonych sieci wirtualnych
Po ograniczeniu konta magazynu do określonych sieci wirtualnych zezwalasz na żądania do publicznego punktu końcowego z określonych sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. Może to być używane z lub bez prywatnych punktów końcowych.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przejdź do konta magazynu, dla którego chcesz ograniczyć publiczny punkt końcowy do określonych sieci wirtualnych. W spisie treści dla konta magazynu wybierz pozycję **Zapory i sieci wirtualne**. 

U góry strony wybierz przycisk opcji **Wybrane sieci.** Spowoduje to odsunienie szeregu ustawień do kontrolowania ograniczenia publicznego punktu końcowego. Kliknij **+Dodaj istniejącą sieć wirtualną,** aby wybrać określoną sieć wirtualną, która powinna mieć możliwość uzyskania dostępu do konta magazynu za pośrednictwem publicznego punktu końcowego. Będzie to wymagało wybrania sieci wirtualnej i podsieci dla tej sieci wirtualnej. 

Zaznacz **pole wyboru Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta usługi,** aby umożliwić zaufanym usługom firmy Microsoft, takim jak Azure File Sync, dostęp do konta magazynu.

![Zrzut ekranu przedstawiający blok Zapory i sieci wirtualne z określoną siecią wirtualną, która może uzyskać dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby ograniczyć dostęp do publicznego punktu końcowego konta magazynu do określonych sieci wirtualnych przy użyciu punktów końcowych usługi, najpierw musimy zebrać informacje o koncie magazynu i sieci wirtualnej. Wypełnij `<storage-account-resource-group>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<subnet-name>` , i zebrać te informacje.

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

Aby ruch z sieci wirtualnej był dozwolony przez sieć szkieletową platformy Azure, aby uzyskać dostęp do publicznego punktu końcowego konta magazynu, podsieć sieci wirtualnej `Microsoft.Storage` musi mieć punkt końcowy usługi narażony. Następujące polecenia programu PowerShell doda `Microsoft.Storage` punktu końcowego usługi do podsieci, jeśli jeszcze go tam nie ma.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

Ostatnim krokiem w ograniczaniu ruchu do konta magazynu jest utworzenie reguły sieciowej i dodanie do zestawu reguł sieciowych konta magazynu.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby ograniczyć dostęp do publicznego punktu końcowego konta magazynu do określonych sieci wirtualnych przy użyciu punktów końcowych usługi, najpierw musimy zebrać informacje o koncie magazynu i sieci wirtualnej. Wypełnij `<storage-account-resource-group>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<subnet-name>` , i zebrać te informacje.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Aby ruch z sieci wirtualnej był dozwolony przez sieć szkieletową platformy Azure, aby uzyskać dostęp do publicznego punktu końcowego konta magazynu, podsieć sieci wirtualnej `Microsoft.Storage` musi mieć punkt końcowy usługi narażony. Następujące polecenia interfejsu wiersza `Microsoft.Storage` polecenia doda punktu końcowego usługi do podsieci, jeśli jeszcze go tam nie ma.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

Ostatnim krokiem w ograniczaniu ruchu do konta magazynu jest utworzenie reguły sieciowej i dodanie do zestawu reguł sieciowych konta magazynu.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Zobacz też
- [Zagadnienia dotyczące sieci usług Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie przekazywania usług DNS dla plików platformy Azure](storage-files-networking-dns.md)
- [Konfigurowanie sieci VPN S2S dla plików platformy Azure](storage-files-configure-s2s-vpn.md)