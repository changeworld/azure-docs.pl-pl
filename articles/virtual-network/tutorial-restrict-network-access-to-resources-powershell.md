---
title: Ograniczenie dostępu do sieci do zasobów PaaS - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak ograniczenia i ograniczenie dostępu do sieci do zasobów platformy Azure, takich jak usługi Azure Storage i bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej przy użyciu programu Azure PowerShell.
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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603763"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Ograniczenie dostępu do sieci do PaaS zasobów z punktów końcowych usługi sieci wirtualnej przy użyciu programu PowerShell

Punkty końcowe usługi sieci wirtualnej umożliwiają ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej. Możesz również uniemożliwić dostęp internetowy do zasobów. Punkty końcowe usługi zapewniają bezpośrednie połączenie z sieci wirtualnej z obsługiwanymi usługami platformy Azure, umożliwiając korzystanie z prywatnej przestrzeni adresowej sieci wirtualnej w celu uzyskiwania dostępu do usług platformy Azure. Ruch kierowany do zasobów platformy Azure za pośrednictwem punktów końcowych usługi zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieci wirtualnej z jedną podsiecią
* Dodawanie podsieci i włączanie punktu końcowego usługi
* Tworzenie zasobów platformy Azure i zezwalanie na dostęp sieciowy do nich tylko z podsieci
* Wdrażanie maszyny wirtualnej w każdej podsieci
* Potwierdzanie dostępu do zasobu z podsieci
* Potwierdzanie zablokowania dostępu do zasobu z podsieci i z Internetu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów dla sieci wirtualnej i innych zasobów utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup*: 

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

Utwórz konfigurację podsieci za pomocą polecenia [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Poniższy przykład tworzy konfiguracji podsieci dla podsieci o nazwie *publicznego*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Utwórz podsieć w sieci wirtualnej przez zapisywanie konfiguracji podsieci w sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi 

Można włączyć punkty końcowe usługi tylko dla usług, które obsługują punkty końcowe usługi. Wyświetl usług korzystających z punktu końcowego usługi dostępne w lokalizacji platformy Azure z [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). Poniższy przykład zwraca listę usług włączony punkt końcowy usługi dostępne w *eastus* regionu. Na liście usług zwrócił będzie rosnąć wraz z upływem czasu zgodnie z usług Azure więcej stają się włączony punkt końcowy usługi.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Utworzyć dodatkowe podsieci w sieci wirtualnej. W tym przykładzie podsieć o nazwie *prywatnej* jest tworzona z punktem końcowym usługi dla *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Tworzenie reguł zabezpieczeń grupy z zabezpieczenia sieci [AzureRmNetworkSecurityRuleConfig nowy](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Poniższa reguła umożliwia dostęp ruchu wychodzącego na publiczne adresy IP przypisane do usługi Azure Storage: 

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

Poniższa reguła nie zezwala na dostęp do wszystkich publicznych adresów IP. Poprzednie reguła zastępuje tej reguły, ze względu na wyższy priorytet, która zezwala na dostęp do publicznych adresów IP z usługi Azure Storage.

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

Poniższa reguła zezwala na ruch protokołu RDP (Remote Desktop) dla ruchu przychodzącego podsieci z dowolnego miejsca. Połączenia pulpitu zdalnego mogą podsieci, dzięki czemu można potwierdzić dostęp do zasobów w kolejnym kroku.

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

Sieciową grupę zabezpieczeń do skojarzenia *prywatnej* podsieć o [AzureRmVirtualNetworkSubnetConfig zestaw](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) , a następnie zapisać konfiguracji podsieci do sieci wirtualnej. W poniższym przykładzie *myNsgPrivate* sieciową grupę zabezpieczeń do *prywatnej* podsieci:

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

Kroki niezbędne do ograniczenia dostępu sieciowego do zasobów utworzonych za pomocą usług platformy Azure obsługujących punkty końcowe usługi różnią się w zależności od usługi. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. W dalszej części tego artykułu zawiera kroki, aby ograniczyć dostęp do konta usługi Azure Storage, na przykład.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu platformy Azure z [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Zastąp `<replace-with-your-unique-storage-account-name>` nazwę, która jest unikatowa dla wszystkich lokalizacji platformy Azure, w zakresie od 3 do 24 znaków długości, używając tylko cyfry i małe litery.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po utworzeniu konta magazynu, Pobierz klucz konta magazynu do zmiennej o [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Klucz jest używany do utworzenia udziału plików w kolejnym kroku. Wprowadź `$storageAcctKey` i zanotuj wartość, jak również należy ręcznie wprowadzić go w kolejnym kroku podczas mapowania udziału plików na dysku na maszynie wirtualnej.

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Tworzenie kontekstu konta magazynu i klucza z [AzureStorageContext nowy](/powershell/module/azure.storage/new-azurestoragecontext). W kontekście zawarta jest klucz nazwy i konta konta magazynu:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Utwórz udział plików z [AzureStorageShare nowy](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare Moje pliku share - kontekstu $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Odmowa dostępu do całej sieci na konto magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranej sieci, należy zmienić domyślną akcję do *Odmów* z [AzureRmStorageAccountNetworkRuleSet aktualizacji](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Po odmowa dostępu do sieci konto magazynu nie jest dostępny z żadną siecią.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Włączanie dostępu sieciowego z podsieci

Pobrać sieci wirtualnej utworzonej z [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) a następnie pobrać obiektu prywatne podsieci do zmiennej o [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Zezwalaj na dostęp sieciowy do konta magazynu z *prywatnej* podsieć o [AzureRmStorageAccountNetworkRule Dodaj](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Aby przetestować dostęp sieciowy do konta magazynu, należy wdrożyć maszynę wirtualną w każdej podsieci.

### <a name="create-the-first-virtual-machine"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną w *publicznego* podsieć o [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm). Podczas wykonywania poniższego polecenia jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej. Opcja `-AsJob` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Zwrócono dane wyjściowe podobne do następujących przykładowe dane wyjściowe:

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

Trwa kilka minut, aż Azure w celu utworzenia maszyny Wirtualnej. Nie należy kontynuować do następnego kroku, dopóki Azure zakończy tworzenie maszyny Wirtualnej i zwraca dane wyjściowe do programu PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

Użyj polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. Poniższy przykład zwraca publicznego adresu IP *myVmPrivate* maszyny Wirtualnej:

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

Na maszynie wirtualnej *myVmPrivate* mapuj udział plików platformy Azure na dysk Z przy użyciu programu PowerShell. Przed uruchomieniem polecenia, które należy wykonać, Zastąp `<storage-account-key>` i `<storage-account-name>` wartościami z dostarczonych lub pobrać w [Utwórz konto magazynu](#create-a-storage-account).

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

Upewnij się, że maszyna wirtualna nie ma wychodzącego łączności z innymi publicznych adresów IP:

```powershell
ping bing.com
```

Nie otrzymasz żadnych odpowiedzi, ponieważ sieciowa grupa zabezpieczeń skojarzona z podsiecią *Private* nie zezwala na dostęp ruchu wychodzącego do publicznych adresów IP innych niż adresy przypisane do usługi Azure Storage.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

Pobierz publiczny adres IP *myVmPublic* maszyny Wirtualnej:

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

Na *myVmPublic* maszyny Wirtualnej, próba mapowania na dysk Z udziału plików na platformę Azure. Przed uruchomieniem polecenia, które należy wykonać, Zastąp `<storage-account-key>` i `<storage-account-name>` wartościami z dostarczonych lub pobrać w [Utwórz konto magazynu](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Odmowa dostępu do udziału i pojawi się `New-PSDrive : Access is denied` błędu. Odmowa dostępu nastąpi, ponieważ maszyna wirtualna *myVmPublic* jest wdrożona w podsieci *Public*. Podsieć *Public* nie ma punktu końcowego usługi obsługującego usługę Azure Storage, a konto magazynu zezwala tylko na dostęp sieciowy z podsieci *Private*, a nie z podsieci *Public*.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*.

Z komputera Próba wyświetlenia udziałów plików w ramach konta magazynu przy użyciu następującego polecenia:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Odmowa dostępu i zostanie wyświetlony *polecenia Get-AzureStorageFile: serwer zdalny zwrócił błąd: (403) zabroniony. Kod stanu HTTP: 403 — komunikaty o błędach HTTP: tego żądania nie ma uprawnień do wykonania tej operacji* błąd, ponieważ komputer nie znajduje się w *prywatnej* podsieć *MyVirtualNetwork* sieć wirtualna.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule należy włączyć punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz teraz, że punkty końcowe usługi można włączyć dla zasobów wdrożonych w wielu usługach platformy Azure. Zostało utworzone konto usługi Azure Storage i dostęp sieciowy do konta magazynu został ograniczony tylko do zasobów w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Service endpoints overview (Omówienie punktów końcowych usługi)](virtual-network-service-endpoints-overview.md) i [Manage subnets (Zarządzanie podsieciami)](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz chcieć połączyć ze sobą dwie sieci wirtualne, aby zasoby w każdej sieci wirtualnej mogły komunikować się ze sobą. Aby dowiedzieć się więcej, zobacz temat [połączyć sieci wirtualnych](tutorial-connect-virtual-networks-powershell.md).
