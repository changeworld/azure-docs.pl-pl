---
title: Infrastruktury jako kodu najlepsze rozwiązania platformy Azure Service Fabric | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące zarządzania usługi Service Fabric jako infrastruktura jako kod.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237433"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kod

W scenariuszu produkcyjnym Tworzenie klastrów usługi Azure Service Fabric za pomocą szablonów usługi Resource Manager. Szablony usługi Resource Manager zapewnia lepszą kontrolę nad właściwości zasobów i upewnij się, że model zasobu spójne.

Szablony usługi Resource Manager próbki są dostępne dla Windows i Linux w [przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony może służyć jako punkt wyjścia do szablonu klastra. Pobierz `azuredeploy.json` i `azuredeploy.parameters.json` i edytować je zgodnie z wymaganiami niestandardowego.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wdrożyć `azuredeploy.json` i `azuredeploy.parameters.json` szablony pobrane powyżej, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Tworzenie zasobu za pomocą programu Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Zasoby platformy Azure Service Fabric

Możesz wdrażać aplikacje i usługi w klastrze usługi Service Fabric za pośrednictwem usługi Azure Resource Manager. Zobacz [zarządzania aplikacjami i usługami jako zasoby usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) Aby uzyskać szczegółowe informacje. Poniżej przedstawiono najlepsze praktyki usługi Service Fabric aplikacji określone zasoby do uwzględnienia w swoich zasobach szablonu usługi Resource Manager.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Aby wdrożyć aplikację za pomocą usługi Azure Resource Manager, najpierw musisz [tworzenie sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) pakietu aplikacji usługi Service Fabric. Poniższy skrypt języka python jest przykładem sposobu tworzenia sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfiguracja automatycznego uaktualnienia systemu operacyjnego maszyny wirtualnej platformy Azure 
Uaktualnianie maszyny wirtualnej jest operacją zainicjowanej przez użytkownika i zaleca się, że używasz [maszyn wirtualnych skalowania Ustaw automatyczne uaktualnienie systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) dla usługi Azure Service Fabric clusters zarządzania poprawkami hosta; Patch Orchestration Application to alternatywne rozwiązanie, które jest przeznaczony dla w przypadku hostowania poza platformą Azure, mimo że POA mogą być używane na platformie Azure z koszty hostingu POA na platformie Azure jest typową przyczyną preferowanie automatycznego uaktualniania systemu operacyjnego maszyny wirtualnej za pośrednictwem POA. Właściwości szablonu obliczeniowych maszyn wirtualnych skalowania Ustaw usługi Resource Manager, aby umożliwić uaktualnienie systemu operacyjnego automatycznie są następujące:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Korzystając z automatycznych uaktualnień systemu operacyjnego za pomocą usługi Service Fabric, nowy obraz systemu operacyjnego jest udostępniona jednej domeny aktualizacji jednocześnie, aby zapewnić wysoką dostępność usługi działające w usłudze Service Fabric. Korzystanie z automatycznego uaktualniania systemu operacyjnego w usłudze Service Fabric klaster musi być skonfigurowana pod kątem użycia warstwy trwałości Silver lub nowszej.

Upewnij się, że następujący klucz rejestru jest ustawiona na wartość FAŁSZ aby zapobiec inicjowaniu nieskoordynowane aktualizacje w maszynach hostów systemu windows: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Poniżej przedstawiono właściwości szablonu obliczeniowych maszyn wirtualnych skalowania Ustaw usługi Resource Manager, aby ustawić klucz rejestru Windows Update na wartość false:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfiguracja klastra uaktualnienia sieci szkieletowej usługi platformy Azure
Właściwości szablonu usługi Resource Manager umożliwia automatyczne uaktualnianie klastra usługi Service Fabric jest następująca:
```json
"upgradeMode": "Automatic",
```
Aby ręcznie uaktualnić klaster, Pobierz dystrybucji pliku cab/deb do maszyny wirtualnej klastra, a następnie wywołaj następujące polecenie programu PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)
