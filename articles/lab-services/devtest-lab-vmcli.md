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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622324"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Tworzenie i zarządzanie maszynami wirtualnymi za pomocą usługi DevTest Labs przy użyciu wiersza polecenia platformy Azure
Ten przewodnik Szybki Start przeprowadzi Cię przez tworzenie, uruchamianie, łączenie, aktualizowanie i czyszczenie komputera deweloperskiego z systemem w środowisku laboratoryjnym. 

Przed rozpoczęciem:

* Jeśli nie utworzono laboratorium, instrukcje można znaleźć [tutaj](devtest-lab-create-lab.md).

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby rozpocząć, uruchom logowania az, aby utworzyć połączenie z platformą Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Utwórz i Zweryfikuj maszynę wirtualną 
Przed wykonaniem poleceń powiązanych DevTest Labs, ustaw odpowiedni kontekst platformy Azure przy użyciu `az account set` polecenia:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

To polecenie, aby utworzyć maszynę wirtualną: `az lab vm create`. Grupa zasobów dla laboratorium, Nazwa laboratorium i nazwę maszyny wirtualnej są wszystkie wymagane. Pozostałe argumenty zmianie w zależności od typu maszyny wirtualnej.

Następujące polecenie tworzy obraz z systemem Windows z usługi Azure Marketplace. Nazwa obrazu jest taka sama, jak podczas tworzenia maszyny wirtualnej przy użyciu witryny Azure portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

Następujące polecenie tworzy maszynę wirtualną, opartych na obrazach niestandardowych dostępnych w środowisku laboratoryjnym:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**Typ obrazu** argument został zmieniony z **galerii** do **niestandardowe**. Nazwa obrazu jest taka sama, zobacz były do utworzenia maszyny wirtualnej w witrynie Azure portal.

Następujące polecenie tworzy Maszynę wirtualną z obrazu z witryny marketplace za pomocą protokołu ssh uwierzytelniania:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Można również tworzyć maszyny wirtualne oparte na formułach, ustawiając **typ obrazu** parametr **formuły**. Jeśli musisz wybrać określone sieci wirtualnej dla maszyny wirtualnej, użyj **nazwa sieci wirtualnej** i **podsieci** parametrów. Aby uzyskać więcej informacji, zobacz [tworzenie az lab vm](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Sprawdź, czy maszyna wirtualna jest dostępna.
Użyj `az lab vm show` polecenie, aby sprawdzić, czy maszyna wirtualna jest dostępna, przed start i nawiązać z nim. 

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
Następujące przykładowe polecenie uruchamia Maszynę wirtualną:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Łączenie z maszyną Wirtualną: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) lub [pulpitu zdalnego](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizowanie maszyny wirtualnej
Następujące przykładowe polecenie dotyczy artefaktów maszyny Wirtualnej:

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
Następujące przykładowe polecenie powoduje zatrzymanie maszyny Wirtualnej.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Usuń Maszynę wirtualną.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następującą zawartość: [Dokumentacja interfejsu wiersza polecenia platformy Azure dla usługi Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 