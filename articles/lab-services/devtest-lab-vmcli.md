---
title: Tworzenie i zarządzanie maszynami wirtualnymi w usłudze DevTest Labs przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i zarządzać maszynami wirtualnymi przy użyciu wiersza polecenia platformy Azure za pomocą usługi Azure DevTest Labs
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
ms.openlocfilehash: ccf9b08856fcc652e3ad4b2b31587d43d7ef9cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995958"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Tworzenie i zarządzanie maszynami wirtualnymi za pomocą usługi DevTest Labs przy użyciu wiersza polecenia platformy Azure
Ten przewodnik Szybki start przeprowadzi Cię przez tworzenie, uruchamianie, łączenie, aktualizowanie i czyszczenie komputera deweloperskiego z systemem w środowisku laboratoryjnym. 

Przed rozpoczęciem:

* Jeśli nie utworzono laboratorium, instrukcje można znaleźć [tutaj](devtest-lab-create-lab.md).

* [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Aby rozpocząć, uruchom logowania az, aby utworzyć połączenie z platformą Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Utwórz i Zweryfikuj maszynę wirtualną 
Utwórz Maszynę wirtualną z obrazu z witryny marketplace za pomocą protokołu ssh uwierzytelniania.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Umieść **grupy zasobów laboratorium** nazwy parametru--grupy zasobów.
>

Jeśli chcesz utworzyć Maszynę wirtualną przy użyciu formuły, użyj formuły parametru--w [tworzenie az lab vm](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).


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
Uruchamianie maszyny Wirtualnej.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Umieść **grupy zasobów laboratorium** nazwy parametru--grupy zasobów.
>

Łączenie z maszyną Wirtualną: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) lub [pulpitu zdalnego](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizowanie maszyny wirtualnej
Zastosuj artefaktów do maszyny Wirtualnej.
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

Wyświetl listę artefaktów dostępnych w środowisku laboratoryjnym.
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
Zatrzymywanie maszyny Wirtualnej.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Usuń Maszynę wirtualną.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
