---
title: Zestawy skalowania maszyn wirtualnych platformy Azure — dołączone dyski danych | Microsoft Docs
description: Informacje o używaniu dołączonych dysków danych z zestawami skalowania maszyn wirtualnych
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Zestawy skalowania i dołączone dyski danych maszyn wirtualnych platformy Azure
Aby umożliwić rozbudowanie dostępnego magazynu, [zestawy skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/) platformy Azure obsługują wystąpienia maszyn wirtualnych przy użyciu dołączonych dysków z danymi. Dyski z danymi można dołączać do istniejącego zestawu skalowania lub podczas tworzenia takiego zestawu.

> [!NOTE]
>  Podczas tworzenia zestawu skalowania z dołączonymi dyskami z danymi konieczne jest zainstalowanie i sformatowanie dysków z poziomu maszyny wirtualnej w celu ich użycia (podobnie jak w przypadku autonomicznych maszyn wirtualnych platformy Azure). Wygodnym sposobem wykonania tych czynności jest użycie rozszerzenia niestandardowego skryptu, które wywołuje skrypt w celu podzielenia na partycje i sformatowania wszystkich dysków z danymi na maszynie wirtualnej. Aby zapoznać się z odpowiednimi przykładami, zobacz [Interfejs wiersza polecenia platformy Azure 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) i [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Tworzenie dysków i zarządzanie nimi w zestawie skalowania
Aby uzyskać szczegółowe informacje na temat sposobu tworzenia zestawu skalowania z dołączonymi dyskami z danymi, zobacz jeden z następujących samouczków:

- [Interfejs wiersza polecenia platformy Azure 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

W pozostałej części tego artykułu przedstawiono wybrane przypadki użycia, takie jak klastry usługi Service Fabric wymagające dysków z danymi i dołączanie istniejących dysków z danymi z zawartością do zestawu skalowania.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Tworzenie klastra usługi Service Fabric z dołączonymi dyskami danych
Każdy [typ węzła](../service-fabric/service-fabric-cluster-nodetypes.md) w klastrze usługi [Service Fabric](/azure/service-fabric) uruchomiony na platformie Azure jest obsługiwany przez zestaw skalowania maszyn wirtualnych.  Za pomocą szablonu usługi Azure Resource Manager możesz dołączać dyski z danymi do zestawów skalowania tworzących klaster usługi Service Fabric. Jako punktu startowego możesz użyć [istniejącego szablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates). W szablonie dołącz sekcję _dataDisks_ w obszarze _storageProfile_ zasobów _Microsoft.Compute/virtualMachineScaleSets_, a następnie wdróż szablon. Poniższy przykład służy do dołączania dysku z danymi o rozmiarze 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Po wdrożeniu klastra możesz automatycznie tworzyć partycje na dyskach danych, a także formatować je i instalować.  Dodaj rozszerzenie niestandardowego skryptu do sekcji _extensionProfile_ obszaru _virtualMachineProfile_ zestawów skalowania.

Aby automatycznie przygotować dyski danych w klastrze systemu Windows, dodaj następujący fragment kodu:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Aby automatycznie przygotować dyski danych w klastrze systemu Linux, dodaj następujący fragment kodu:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Dodawanie wstępnie wypełnionych dysków danych do istniejącego zestawu skalowania 
> Podczas dodawania dysków do istniejącego modelu zestawu skalowania dysk zawsze jest tworzony jako pusty — jest to celowe. Ten scenariusz obejmuje także nowe wystąpienia utworzone przez zestaw skalowania. To zachowanie jest spowodowane obecnością pustego dysku danych w definicji zestawu skalowania. Aby utworzyć wstępnie wypełnione dyski danych dla istniejącego modelu zestawu skalowania, możesz wybrać dowolną z następujących dwóch opcji:

* Skopiowanie danych z maszyny wirtualnej wystąpienia 0 na dyski danych w innych maszynach wirtualnych przy użyciu skryptu niestandardowego.
* Utworzenie obrazu zarządzanego z dyskiem systemu operacyjnego oraz dyskiem danych (z wymaganymi danymi) i utworzenie nowego zestawu skalowania przy użyciu tego obrazu. W ten sposób każda nowa utworzona maszyna wirtualna ma dysk danych podany w definicji zestawu skalowania. Ponieważ ta definicja odwołuje się do obrazu z dyskiem danych zawierającym dostosowane dane, te zmiany są uwzględniane na każdej maszynie wirtualnej w zestawie skalowania.

> Sposób tworzenia obrazu niestandardowego można znaleźć tutaj: [Tworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure](/azure/virtual-machines/windows/capture-image-resource/) 

> Użytkownik musi przechwycić maszynę wirtualną wystąpienia 0 zawierającą wymagane dane, a następnie utworzyć definicję obrazu przy użyciu tego wirtualnego dysku twardego.


## <a name="additional-notes"></a>Uwagi dodatkowe
Obsługa usługi Azure Managed Disks i dołączonych dysków danych w zestawach skalowania jest dostępna w wersji [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) interfejsu API Microsoft.Compute i w nowszych wersjach.

We wstępnej implementacji obsługi dysków dołączonych dla zestawów skalowania nie można dołączać ani odłączać dysków danych w przypadku pojedynczych maszyn wirtualnych należących do zestawu skalowania.

W witrynie Azure Portal obsługa dołączonych dysków danych w zestawach skalowania jest wstępnie ograniczona. W zależności od wymagań można zarządzać dyskami dołączonymi przy użyciu szablonów platformy Azure, interfejsu wiersza polecenia, programu PowerShell, zestawów SDK oraz interfejsu API REST.


