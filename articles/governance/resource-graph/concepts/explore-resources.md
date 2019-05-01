---
title: Zapoznaj się z zasobami platformy Azure
description: Dowiedz się więcej, zapoznaj się z zasobami i Odkryj, jak są one połączone przy użyciu języka zapytań wykres zasobów.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0b4a75558f5e82b707ae5d012acef4d2c5c4b7a0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723807"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Badanie zasobów platformy Azure przy użyciu usługi Resources Graph

Wykres zasobów platformy Azure umożliwia Eksplorowanie i odnajdywanie zasobów platformy Azure, szybko i w odpowiedniej skali. Zaprojektowany z myślą o szybkie uzyskiwanie odpowiedzi, jest doskonały sposób, aby dowiedzieć się więcej o środowisku, a także informacje o właściwościach, składających się z zasobami platformy Azure.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>Poznaj usługę virtual machines

Typowe zasobów na platformie Azure jest maszyną wirtualną. Jako typ zasobu maszyny wirtualne mają wiele właściwości, które mogą być wyszukiwane. Każda właściwość udostępnia opcję filtrowania lub znajdowania dokładnie zasób, którego szukasz.

### <a name="virtual-machine-discovery"></a>Odnajdywanie maszyny wirtualnej

Zacznijmy od prostego zapytania do uzyskać pojedyncza maszyna wirtualna z naszego środowiska i przyjrzyj się zwrócono żadnych wartości.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` polecenie cmdlet zwraca **PSCustomObject** domyślnie. Aby dane wyjściowe wyglądają tak samo jak dane zwracane przez interfejs wiersza polecenia platformy Azure, `ConvertTo-Json` służy polecenie cmdlet. Wartością domyślną dla **głębokość** jest _2_. Ustawienie _100_ należy przekonwertować wszystkie zwrócone poziomów.

Wyniki JSON są strukturę podobną do poniższego przykładu:

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

Właściwości Powiedz nam, dodatkowe informacje na temat zasobów maszyny wirtualnej, wszystko — od jednostek SKU, systemu operacyjnego, dysków, tagi, i grupy zasobów i subskrypcji jest członkiem.

### <a name="virtual-machines-by-location"></a>Maszyny wirtualne według lokalizacji

Biorąc dowiedzieliśmy dotyczące zasobów maszyny wirtualnej, użyjemy **lokalizacji** właściwości do obliczenia wszystkich maszyn wirtualnych według lokalizacji. Aby zaktualizować zapytanie, możemy usunąć limit i podsumowanie łącznej liczby wartości lokalizacji.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Wyniki JSON są strukturę podobną do poniższego przykładu:

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

Teraz widać liczbę maszyn wirtualnych, które mamy w każdym regionie platformy Azure.

### <a name="virtual-machines-by-sku"></a>Maszyn wirtualnych za pomocą jednostki SKU

Wypróbujmy wracając do oryginalnego właściwości maszyny wirtualnej znaleźć wszystkie maszyny wirtualne, które mają rozmiar jednostki SKU **Standard_B2s**. Spojrzenie na dane JSON zwracane, widzimy, że jest on przechowywany w **properties.hardwareprofile.vmsize**. Zaktualizujemy zapytanie w celu znalezienia wszystkich maszyn wirtualnych, która pasuje do tego rozmiaru i zwracać tylko nazwę maszyny Wirtualnej i regionu.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Maszyny wirtualne podłączone do dysków zarządzanych w warstwie premium

Jeśli chcemy uzyskać szczegółowe informacje na dyski zarządzane w warstwie premium, które są dołączone do tych **Standard_B2s** maszyn wirtualnych, możemy rozszerzyć zapytania, aby przesłać nam identyfikator zasobu tych dysków zarządzanych.

```kusto
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Inny sposób pobrania jednostki SKU byłby przy użyciu **aliasy** właściwość **Microsoft.Compute/virtualMachines/sku.name**. Zobacz [Pokaż aliasy](../samples/starter.md#show-aliases) i [Pokaż alias odrębne wartości](../samples/starter.md#distinct-alias-values) przykłady.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Wynik jest listę identyfikatorów dysku.

### <a name="managed-disk-discovery"></a>Odnajdowanie dysku zarządzanego

Za pomocą pierwszy rekord z poprzedniej kwerendy pokażemy, właściwości, które istnieją na dysku zarządzanego, który został dołączony do pierwszej maszyny wirtualnej. Zaktualizowano zapytanie używa identyfikator dysku i zmienia typ.

Przykład danych wyjściowych z poprzedniej kwerendy, na przykład:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Przed uruchomieniem kwerendy, jak wiemy **typu** powinno być teraz **Microsoft.Compute/disks**?
Jeśli przyjrzymy się pełny identyfikator, zobaczysz **/providers/Microsoft.Compute/disks/** jako część ciągu. Ten fragment ciągu zapewnia wskazówkę, jaki typ do wyszukania. Alternatywną metodą jest zamiast tylko wyszukiwanie pole Identyfikatora i usunąć limit według typu. Jako identyfikator jest unikatowy, zostaną zwrócone tylko do jednego rekordu a **typu** właściwości na nim zawiera szczegółowe informacje na ten.

> [!NOTE]
> W tym przykładzie do pracy można zastąpić pole Identyfikatora z wynikiem z własnego środowiska.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Wyniki JSON są strukturę podobną do poniższego przykładu:

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

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Poznaj usługę virtual machines, aby znaleźć publiczne adresy IP

Ten zestaw wiersza polecenia platformy Azure zapytań najpierw znajduje i przechowująca wszystkie interfejsy sieciowe (NIC) zasobów podłączonych do maszyn wirtualnych. Następnie używa listy kart sieciowych można znaleźć każdego zasobu adresu IP, który jest publiczny adres IP i przechowywania tych wartości. Na koniec zawiera listę publicznych adresów IP.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

Użyj `nics.txt` pliku w następnym zapytaniu, aby dowiedzieć się interfejs sieciowy powiązane zasoby w przypadku, gdy ma publiczny adres IP dołączony do karty sieciowej.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Ostatnie, użyj listy zasobów publicznych adresów IP, przechowywane w `ips.txt` uzyskać rzeczywiste publiczny adres IP z nich i wyświetlania.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [języku zapytań](query-language.md)
- Zobacz język używany w [początkowego zapytania](../samples/starter.md)
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md)