---
title: Tworzenie maszyn wirtualnych w laboratoriach DevTest i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać laboratoriów DevTest Labs do tworzenia maszyn wirtualnych i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167053"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Tworzenie maszyn wirtualnych i zarządzanie nimi za pomocą laboratorium DevTest labs przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przewodnik Szybki start poprowadzi Cię przez tworzenie, uruchamianie, łączenie, aktualizowanie i czyszczenie maszyny deweloperskiej w laboratorium. 

Przed rozpoczęciem:

* Jeśli laboratorium nie zostało utworzone, instrukcje można znaleźć [tutaj](devtest-lab-create-lab.md).

* [Zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby rozpocząć, uruchom az login, aby utworzyć połączenie z platformą Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Tworzenie i weryfikowanie maszyny wirtualnej 
Przed wykonaniem poleceń związanych z DevTest Labs `az account set` należy ustawić odpowiedni kontekst platformy Azure przy użyciu polecenia:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Polecenie tworzenia maszyny wirtualnej `az lab vm create`to: . Wymagana jest grupa zasobów dla laboratorium, nazwy laboratorium i nazwy maszyny wirtualnej. Pozostałe argumenty zmieniają się w zależności od typu maszyny wirtualnej.

Następujące polecenie tworzy obraz oparty na systemie Windows z usługi Azure Market Place. Nazwa obrazu jest taka sama, jak podczas tworzenia maszyny wirtualnej przy użyciu witryny Azure portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Następujące polecenie tworzy maszynę wirtualną na podstawie niestandardowego obrazu dostępnego w laboratorium:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argument **typu obrazu** został zmieniony z **galerii** na **niestandardowy**. Nazwa obrazu jest zgodna z tym, co widzisz, jeśli chcesz utworzyć maszynę wirtualną w witrynie Azure portal.

Następujące polecenie tworzy maszynę wirtualną z obrazu portalu marketplace z uwierzytelnianiem ssh:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Maszyn wirtualnych można również tworzyć na podstawie formuł, ustawiając parametr **typu obrazu** na **formułę**. Jeśli chcesz wybrać określoną sieć wirtualną dla maszyny wirtualnej, użyj **nazwy sieci wirtualnej** i parametrów **podsieci.** Aby uzyskać więcej informacji, zobacz [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Sprawdź, czy maszyna wirtualna jest dostępna.
Użyj `az lab vm show` polecenia, aby sprawdzić, czy maszyna wirtualna jest dostępna przed rozpoczęciem i połączyć się z nim. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>Uruchamianie i łączenie się z maszyną wirtualną
Następujące przykładowe polecenie uruchamia maszynę wirtualną:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Łączenie się z maszyną wirtualną: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) lub [pulpitem zdalnym](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizowanie maszyny wirtualnej
Następujące przykładowe polecenie stosuje artefakty do maszyny Wirtualnej:

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

### <a name="list-artifacts-available-in-the-lab"></a>Lista artefaktów dostępnych w laboratorium

Aby wyświetlić listę artefaktów dostępnych w maszynie wirtualnej w laboratorium, uruchom następujące polecenia.

**Cloud Shell - PowerShell**: zwróć uwagę\`na użycie backtick ( ) przed $ w $expand (tj. '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: zwróć\\uwagę na użycie ukośnika ( ) znak przed $ w poleceniu. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Przykładowe dane wyjściowe: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Zatrzymywać i usuwać maszynę wirtualną    
Następujące przykładowe polecenie zatrzymuje maszynę wirtualną.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Usuwanie maszyny Wirtualnej.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Następne kroki
Zobacz następującą zawartość: [Dokumentacja interfejsu wiersza polecenia platformy Azure dla laboratoriów DevTest Platformy Azure](/cli/azure/lab?view=azure-cli-latest). 
