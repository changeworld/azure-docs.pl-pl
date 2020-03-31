---
title: Eksplorowanie zasobów platformy Azure
description: Dowiedz się, jak używać języka zapytań wykresu zasobów do eksplorowania zasobów i odkrywania, w jaki sposób są one połączone.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 0c191915b8c558d80ffef554ef758a35157e035c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156985"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Badanie zasobów platformy Azure przy użyciu usługi Resources Graph

Usługa Azure Resource Graph umożliwia szybkie i wynajęcie zasobów platformy Azure i odkrywanie zasobów platformy Azure. Zaprojektowany z myślą o szybkich odpowiedziach, to świetny sposób, aby dowiedzieć się o środowisku, a także o właściwościach, które składają się na zasoby platformy Azure.

## <a name="explore-virtual-machines"></a>Poznaj maszyny wirtualne

Typowym zasobem na platformie Azure jest maszyna wirtualna. Jako typ zasobu maszyny wirtualne mają wiele właściwości, które można zbadać. Każda właściwość udostępnia opcję filtrowania lub znajdowania dokładnie tego zasobu, którego szukasz.

### <a name="virtual-machine-discovery"></a>Odnajdowanie maszyn wirtualnych

Zacznijmy od prostej kwerendy, aby uzyskać pojedynczą maszynę wirtualną z naszego środowiska i spójrz na właściwości zwrócone.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Polecenie cmdlet `Search-AzGraph` programu Azure PowerShell zwraca **PSCustomObject** domyślnie. Aby dane wyjściowe wyglądały tak samo, jak `ConvertTo-Json` to, co jest zwracane przez platformę Azure CLI, używane jest polecenie cmdlet. Wartość domyślna dla **głębokość** wynosi _2_. Ustawienie go na _100_ należy przekonwertować wszystkie zwrócone poziomy.

Wyniki JSON są skonstruowane podobnie do następującego przykładu:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Właściwości informują nas o dodatkowych informacjach o samym zasobie maszyny wirtualnej, z jednostki SKU, systemu operacyjnego, dysków, tagów oraz grupy zasobów i subskrypcji, do czego jest członkiem.

### <a name="virtual-machines-by-location"></a>Maszyny wirtualne według lokalizacji

Biorąc to, czego dowiedzieliśmy się o zasobie maszyn wirtualnych, użyjmy właściwości **lokalizacji,** aby policzyć wszystkie maszyny wirtualne według lokalizacji. Aby zaktualizować kwerendę, usuniemy limit i podsumujemy liczbę wartości lokalizacji.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Wyniki JSON są skonstruowane podobnie do następującego przykładu:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Teraz możemy zobaczyć, ile maszyn wirtualnych mamy w każdym regionie platformy Azure.

### <a name="virtual-machines-by-sku"></a>Maszyny wirtualne według jednostki SKU

Wracając do oryginalnych właściwości maszyny wirtualnej, spróbujmy znaleźć wszystkie maszyny wirtualne o rozmiarze jednostki SKU **Standard_B2s**. Patrząc na JSON zwrócony, widzimy, że jest przechowywany w **properties.hardwareprofile.vmsize**. Zaktualizujemy kwerendę, aby znaleźć wszystkie maszyny wirtualne, które pasują do tego rozmiaru i zwrócić tylko nazwę maszyny Wirtualnej i regionu.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Maszyny wirtualne połączone z dyskami zarządzanymi w wersji premium

Jeśli chcemy uzyskać szczegółowe informacje o dyskach zarządzanych w wersji premium, które są dołączone do tych **Standard_B2s** maszyn wirtualnych, możemy rozszerzyć kwerendę, aby nadać nam identyfikator zasobu tych dysków zarządzanych.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Innym sposobem uzyskania jednostki SKU byłoby przy użyciu właściwości **aliasów** **Microsoft.Compute/virtualMachines/sku.name**. Zobacz [przykłady Pokaż aliasy](../samples/starter.md#show-aliases) i [Pokaż różne wartości aliasów.](../samples/starter.md#distinct-alias-values)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Wynikiem jest lista identyfikatorów dysków.

### <a name="managed-disk-discovery"></a>Odnajdowanie dysku zarządzanego

Za pomocą pierwszego rekordu z poprzedniej kwerendy będziemy eksplorować właściwości, które istnieją na dysku zarządzanym, który został dołączony do pierwszej maszyny wirtualnej. Zaktualizowana kwerenda używa identyfikatora dysku i zmienia typ.

Przykładowy wynik z poprzedniej kwerendy na przykład:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Przed uruchomieniem kwerendy, skąd wiemy, **że typem** powinien być teraz **Microsoft.Compute/disks?**
Jeśli spojrzysz na pełny identyfikator, zobaczysz **/providers/Microsoft.Compute/disks/** jako część ciągu. Ten fragment ciągu daje wskazówkę, jaki typ wyszukać. Alternatywną metodą byłoby usunięcie limitu według typu i zamiast tego przeszukiwanie tylko przez pole identyfikator. Ponieważ identyfikator jest unikatowy, tylko jeden rekord zostanie zwrócony, a właściwość **typu** na nim zapewnia te szczegóły.

> [!NOTE]
> W tym przykładzie do pracy należy zastąpić pole identyfikatora wynikiem z własnego środowiska.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Wyniki JSON są skonstruowane podobnie do następującego przykładu:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Eksploruj maszyny wirtualne, aby znaleźć publiczne adresy IP

Ten zestaw zapytań najpierw wyszukuje i przechowuje wszystkie zasoby interfejsów sieciowych (NIC) podłączone do maszyn wirtualnych. Następnie kwerendy używają listy kart sieciowych, aby znaleźć każdy zasób adresu IP, który jest publicznym adresem IP i przechowywać te wartości. Na koniec kwerendy zawierają listę publicznych adresów IP.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Użyj pliku (Azure CLI) lub zmiennej (Azure PowerShell) w następnej kwerendzie, aby uzyskać szczegóły powiązanych zasobów interfejsu sieciowego, gdzie istnieje publiczny adres IP dołączony do karty sieciowej.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Na koniec użyj listy publicznych zasobów adresów IP przechowywanych w pliku (Azure CLI) lub zmiennej (Azure PowerShell), aby uzyskać rzeczywisty publiczny adres IP z powiązanego obiektu i wyświetlania.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Aby zobaczyć, jak wykonać te kroki `join` w jednej kwerendzie z operatorem, zobacz [Lista maszyn wirtualnych z ich interfejs sieciowy i przykład publiczny adres IP.](../samples/advanced.md#join-vmpip)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku zapytań](query-language.md).
- Zobacz język używany w [kwerendach startowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [kwerendach zaawansowanych](../samples/advanced.md).
