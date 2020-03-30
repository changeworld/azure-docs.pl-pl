---
title: Infrastruktura sieci szkieletowej usług Azure jako najważniejsze wskazówki dotyczące kodu
description: Najważniejsze rozwiązania i zagadnienia dotyczące projektowania dotyczące zarządzania siecią szkieletową usług Azure jako infrastruktury jako kodu.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551832"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kod

W scenariuszu produkcyjnym utwórz klastry sieci szkieletowej usług Azure przy użyciu szablonów Menedżera zasobów. Szablony Menedżera zasobów zapewniają większą kontrolę nad właściwościami zasobów i zapewniają spójny model zasobów.

Przykładowe szablony Menedżera zasobów są dostępne dla systemów Windows i Linux w [przykładach platformy Azure w usłudze GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony mogą służyć jako punkt wyjścia dla szablonu klastra. `azuredeploy.json` Pobieraj `azuredeploy.parameters.json` i edytuj je, aby spełnić twoje wymagania niestandardowe.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby `azuredeploy.json` wdrożyć `azuredeploy.parameters.json` szablony pobrane powyżej, należy użyć następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Tworzenie zasobu przy użyciu programu Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Zasoby sieci szkieletowej usług Azure

Aplikacje i usługi można wdrażać w klastrze sieci szkieletowej usług za pośrednictwem usługi Azure Resource Manager. Szczegółowe informacje można znaleźć w obszarze [Zarządzanie aplikacjami i usługami jako zasoby usługi Azure Resource Manager.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) Poniżej przedstawiono najlepsze rozwiązania zasobów aplikacji sieci szkieletowej usługi do uwzględnienia w zasobach szablonu Menedżera zasobów Menedżera zasobów.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Aby wdrożyć aplikację przy użyciu usługi Azure Resource Manager, należy najpierw utworzyć pakiet aplikacji sieci szkieletowej usług [sfpkg.](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Poniższy skrypt python jest przykładem tworzenia sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfiguracja automatycznego uaktualniania systemu operacyjnego maszyny wirtualnej platformy Azure 
Uaktualnianie maszyn wirtualnych jest operacją inicjującą przez użytkownika i zaleca się użycie [automatycznego uaktualnienia systemu operacyjnego Virtual Machine Scale Set](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) dla klastrów usługi Azure Service Fabric hosta zarządzania poprawkami; Aplikacja aranżacji poprawek jest alternatywnym rozwiązaniem, które jest przeznaczone do hostowania poza platformą Azure, chociaż POA może być używany na platformie Azure, z obciążeniem hostingu POA na platformie Azure jest częstym powodem, aby preferować automatyczne uaktualnianie systemu operacyjnego maszyny wirtualnej niż POA. Poniżej przedstawiono właściwości szablonu Menedżera zasobów zestawu skalowania maszyny wirtualnej obliczeniowej, aby włączyć automatyczne uaktualnianie systemu operacyjnego:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Podczas korzystania z automatycznych uaktualnień systemu operacyjnego z sieci szkieletowej usług, nowy obraz systemu operacyjnego jest wdrażany jeden domeny aktualizacji w czasie, aby utrzymać wysoką dostępność usług uruchomionych w sieci szkieletowej usług. Aby korzystać z automatycznych uaktualnień systemu operacyjnego w sieci szkieletowej usług, klaster musi być skonfigurowany do używania warstwy Silver Durability lub nowszej.

Upewnij się, że następujący klucz rejestru jest ustawiony na false, aby uniemożliwić komputerom hosta systemu Windows inicjowanie nieskoordynowanych aktualizacji: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Poniżej przedstawiono właściwości szablonu Menedżera zasobów zestawu skalowania maszyny wirtualnej obliczeń, aby ustawić klucz rejestru WindowsUpdate na false:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfiguracja uaktualnienia klastra sieci szkieletowej usługi Azure
Poniżej znajduje się właściwość szablonu Menedżera zasobów klastra usług sieci szkieletowych, aby włączyć automatyczne uaktualnianie:
```json
"upgradeMode": "Automatic",
```
Aby ręcznie uaktualnić klaster, pobierz dystrybucję cab/deb na maszynę wirtualną klastra, a następnie wywołaj następujący program PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra sieci szkieletowej usług dla systemu Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [tworzenie klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)
