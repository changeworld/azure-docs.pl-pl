---
title: Ograniczanie dostępu sieciowego do zasobów PaaS — Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak ograniczyć i zablokować dostęp sieciowy do zasobów platformy Azure, takich jak Azure Storage i Azure SQL Database, za pomocą punktów końcowych usługi sieci wirtualnej przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b3977e045751165947243c67291e81b998b5fcb5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606118"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Ograniczanie dostępu sieciowego do zasobów PaaS za pomocą punktów końcowych usługi sieci wirtualnej przy użyciu programu PowerShell

Punkty końcowe usługi sieci wirtualnej umożliwiają ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej. Możesz również uniemożliwić dostęp internetowy do zasobów. Punkty końcowe usługi zapewniają bezpośrednie połączenie z sieci wirtualnej z obsługiwanymi usługami platformy Azure, umożliwiając korzystanie z prywatnej przestrzeni adresowej sieci wirtualnej w celu uzyskiwania dostępu do usług platformy Azure. Ruch kierowany do zasobów platformy Azure za pośrednictwem punktów końcowych usługi zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieci wirtualnej z jedną podsiecią
* Dodawanie podsieci i włączanie punktu końcowego usługi
* Tworzenie zasobów platformy Azure i zezwalanie na dostęp sieciowy do nich tylko z podsieci
* Wdrażanie maszyny wirtualnej w każdej podsieci
* Potwierdzanie dostępu do zasobu z podsieci
* Potwierdzanie zablokowania dostępu do zasobu z podsieci i z Internetu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup*: 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Utwórz konfigurację podsieci za pomocą polecenia [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Poniższy przykład tworzy konfigurację podsieci dla podsieci o nazwie *publicznych*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Utwórz podsieć w sieci wirtualnej, zapisując konfigurację podsieci sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi 

Można włączyć punkty końcowe usługi tylko w przypadku usług, które obsługują punkty końcowe usługi. Wyświetlanie usług korzystających z punktu końcowego usługi dostępne w lokalizacji platformy Azure za pomocą [Get AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). Poniższy przykład zwraca listę usług włączony punkt końcowy usługi dostępne w *eastus* regionu. Na liście usług, zwracana będzie rosnąć wraz z upływem czasu, zgodnie z innymi usługami platformy Azure stają się włączonego punktu końcowego usługi.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Utwórz dodatkowe podsieci w sieci wirtualnej. W tym przykładzie podsieć o nazwie *prywatnej* jest tworzona przy użyciu punktu końcowego usługi dla *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Utwórz bezpieczeństwo sieci reguł zabezpieczeń grupy za pomocą [polecenie New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Następująca reguła umożliwia dostęp ruchu wychodzącego do publicznych adresów IP przypisanych do usługi Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Poniższa reguła nie zezwala na dostęp do wszystkich publicznych adresów IP. Poprzednie reguła zastępuje tej reguły, ze względu na wyższy priorytet, która zezwala na dostęp do publicznych adresów IP usługi Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Następująca reguła zezwala na ruch protokołu RDP (Remote Desktop) przychodzący do podsieci z dowolnego miejsca. Podłączanie pulpitu zdalnego są dozwolone do podsieci, tak, aby potwierdzić dostęp sieciowy do zasobów w późniejszym kroku.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Kojarzenie sieciowej grupy zabezpieczeń do *prywatnej* podsieć o [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) , a następnie Zapisz konfigurację podsieci sieci wirtualnej. W poniższym przykładzie *myNsgPrivate* sieciowej grupy zabezpieczeń *prywatnej* podsieci:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Ograniczanie dostępu sieciowego do zasobu

Kroki niezbędne do ograniczenia dostępu sieciowego do zasobów utworzonych za pomocą usług platformy Azure obsługujących punkty końcowe usługi różnią się w zależności od usługi. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. W dalszej części tego artykułu obejmuje kroki pozwalające ograniczające dostęp do konta usługi Azure Storage jako przykład.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Tworzenie konta usługi Azure storage za pomocą [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Zastąp `<replace-with-your-unique-storage-account-name>` nazwą, która jest unikatowa dla wszystkich lokalizacji platformy Azure od 3 do 24 znaków długości, przy użyciu tylko cyfry i małe litery.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po utworzeniu konta magazynu, Pobierz klucz konta magazynu do zmiennej o [Get AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Klucz jest używany do utworzenia udziału plików w późniejszym kroku. Wprowadź `$storageAcctKey` i zwróć uwagę na wartość, jak również należy ręcznie wprowadzić ich w późniejszym kroku podczas mapowania udziału plików na dysku na maszynie wirtualnej.

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Utwórz kontekst konta magazynu i klucz za pomocą [poleceniem New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). W kontekście zawarta konta nazwy i klucza konta magazynu:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Tworzenie udziału plików za pomocą [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my-file-share - kontekstu $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Odmowa dostępu do całej sieci do konta magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, należy zmienić domyślną akcję na *Odmów* z [AzureRmStorageAccountNetworkRuleSet aktualizacji](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Po odmówiono dostępu do sieci, konto magazynu nie są dostępne z dowolnej sieci.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Włączanie dostępu sieciowego z podsieci

Pobieranie utworzonej sieci wirtualnej z [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) i następnie pobierać obiekt podsieci prywatnej do zmiennej za pomocą [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Zezwalanie na dostęp sieciowy do konta magazynu z *prywatnej* podsieć o [AzureRmStorageAccountNetworkRule Dodaj](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Aby przetestować dostęp sieciowy do konta magazynu, należy wdrożyć maszynę wirtualną w każdej podsieci.

### <a name="create-the-first-virtual-machine"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną w *publicznych* podsieć o [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Podczas wykonywania poniższego polecenia jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej. Opcja `-AsJob` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Dane wyjściowe podobne do następujących przykładowych danych wyjściowych jest zwracany:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>Tworzenie drugiej maszyny wirtualnej

Utwórz maszynę wirtualną w *prywatnej* podsieci:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Trwa kilka minut na utworzenie maszyny Wirtualnej na platformie Azure. Nie należy kontynuować do następnego kroku, dopóki nie zakończy tworzenia maszyny Wirtualnej platformy Azure i zwraca dane wyjściowe do programu PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

Użyj polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. Poniższy przykład zwraca publiczny adres IP *myVmPrivate* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

W poniższym poleceniu zastąp ciąg `<publicIpAddress>` publicznym adresem IP zwróconym w poprzednim poleceniu, a następnie wprowadź następujące polecenie: 

```powershell
mstsc /v:<publicIpAddress>
```

Zostanie utworzony i pobrany na komputer plik Remote Desktop Protocol (rdp). Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. Kliknij przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.

Na maszynie wirtualnej *myVmPrivate* mapuj udział plików platformy Azure na dysk Z przy użyciu programu PowerShell. Przed uruchomieniem poniższych poleceń, Zastąp `<storage-account-key>` i `<storage-account-name>` wartościami z dostarczonych lub pobranych w [Tworzenie konta magazynu](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
Program PowerShell zwraca dane wyjściowe podobne do następujących przykładowych danych wyjściowych:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Udział plików platformy Azure został pomyślnie mapowany na dysk Z.

Upewnij się, że maszyna wirtualna nie ma łączności wychodzącej do innych publicznych adresów IP:

```powershell
ping bing.com
```

Nie otrzymasz żadnych odpowiedzi, ponieważ sieciowa grupa zabezpieczeń skojarzona z podsiecią *Private* nie zezwala na dostęp ruchu wychodzącego do publicznych adresów IP innych niż adresy przypisane do usługi Azure Storage.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

Uzyskaj publiczny adres IP *myVmPublic* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

W poniższym poleceniu zastąp ciąg `<publicIpAddress>` publicznym adresem IP zwróconym w poprzednim poleceniu, a następnie wprowadź następujące polecenie: 

```powershell
mstsc /v:<publicIpAddress>
```

Na *myVmPublic* maszyny Wirtualnej, próba mapowania udziału plików platformy Azure na dysk Z. Przed uruchomieniem poniższych poleceń, Zastąp `<storage-account-key>` i `<storage-account-name>` wartościami z dostarczonych lub pobranych w [Tworzenie konta magazynu](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Odmowa dostępu do udziału, a otrzymasz `New-PSDrive : Access is denied` błędu. Odmowa dostępu nastąpi, ponieważ maszyna wirtualna *myVmPublic* jest wdrożona w podsieci *Public*. Podsieć *Public* nie ma punktu końcowego usługi obsługującego usługę Azure Storage, a konto magazynu zezwala tylko na dostęp sieciowy z podsieci *Private*, a nie z podsieci *Public*.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*.

Z tego komputera spróbować wyświetlić udziały plików na koncie magazynu za pomocą następującego polecenia:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Odmowa dostępu oraz otrzymasz *polecenia Get-AzureStorageFile: serwer zdalny zwrócił błąd: (403) zabroniony. Kod stanu HTTP: 403 — komunikat o błędzie HTTP: to żądanie nie ma autoryzacji do wykonania tej operacji* błąd, ponieważ komputer nie znajduje się w *prywatnej* podsieci *MyVirtualNetwork* sieć wirtualna.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule został włączony punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz teraz, że punkty końcowe usługi można włączyć dla zasobów wdrożonych w wielu usługach platformy Azure. Zostało utworzone konto usługi Azure Storage i dostęp sieciowy do konta magazynu został ograniczony tylko do zasobów w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Service endpoints overview (Omówienie punktów końcowych usługi)](virtual-network-service-endpoints-overview.md) i [Manage subnets (Zarządzanie podsieciami)](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz chcieć połączyć ze sobą dwie sieci wirtualne, aby zasoby w każdej sieci wirtualnej mogły komunikować się ze sobą. Aby dowiedzieć się więcej, zobacz temat [łączenie sieci wirtualnych](tutorial-connect-virtual-networks-powershell.md).
