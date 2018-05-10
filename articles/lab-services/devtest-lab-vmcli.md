---
title: Tworzenie i zarządzanie maszynami wirtualnymi w usłudze DevTest Labs z wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Azure DevTest Labs umożliwia tworzenie i zarządzanie maszynami wirtualnymi Azure CLI 2.0
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0f6713b9b8704e813ab1fd77ab1cf4e71e7f6670
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Tworzenie i zarządzanie maszynami wirtualnymi z DevTest Labs przy użyciu wiersza polecenia platformy Azure
To szybki start przeprowadzi użytkownika przez proces tworzenia, uruchamiania, połączenie, aktualizowanie i czyszczenia komputera Programowanie w laboratorium. 

Przed rozpoczęciem:

* Jeśli laboratorium nie został utworzony, można znaleźć instrukcje [tutaj](devtest-lab-create-lab.md).

* [Zainstaluj interfejs wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby rozpocząć, uruchom az logowania, aby utworzyć połączenie z platformą Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Tworzenie i sprawdź maszyny wirtualnej 
Utwórz maszynę Wirtualną z obrazu z witryny marketplace z ssh uwierzytelniania.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Umieść **grupy zasobów w laboratorium** nazwę w parametrze--grupy zasobów.
>

Jeśli chcesz utworzyć Maszynę wirtualną przy użyciu formuły, użyj formuły parametru w [tworzenia maszyny wirtualnej laboratorium az](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).


Sprawdź, czy maszyna wirtualna jest dostępna.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Rozpoczęcie i połączenie z maszyną wirtualną
Uruchom Maszynę wirtualną.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Umieść **grupy zasobów w laboratorium** nazwę w parametrze--grupy zasobów.
>

Połączenie z maszyną wirtualną: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) lub [pulpitu zdalnego](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Zaktualizuj maszynę wirtualną
Zastosuj artefakty do maszyny Wirtualnej.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Na liście artefaktów związanych dostępne w środowisku laboratoryjnym.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Zatrzymaj i Usuń maszynę wirtualną    
Zatrzymaj Maszynę wirtualną.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Usuń Maszynę wirtualną.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]