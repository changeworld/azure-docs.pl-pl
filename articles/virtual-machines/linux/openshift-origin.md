---
title: Wdrażanie OKD na platformie Azure | Dokumentacja firmy Microsoft
description: Wdróż OKD na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 0d3a9f05802bef7d6dfc99fcfae6668044f214c8
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190308"
---
# <a name="deploy-okd-in-azure"></a>Wdrażanie OKD na platformie Azure

Można użyć jednej z dwóch sposobów, aby wdrożyć OKD (dawniej platformy OpenShift Origin) na platformie Azure:

- Można ręcznie wdrożyć wszystkie składniki niezbędne infrastruktury platformy Azure, a następnie wykonaj OKD [dokumentacji](https://docs.okd.io/3.10/welcome/index.html).
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-origin) upraszczające proces wdrażania OKD klastra.

## <a name="deploy-by-using-the-okd-template"></a>Wdrażanie przy użyciu szablonu OKD

Użyj `appId` wartości z nazwy głównej usługi, która została wcześniej utworzona dla `aadClientId` parametru.

Poniższy przykład tworzy plik parametrów o nazwie azuredeploy.parameters.json przy użyciu wszystkich wymaganych danych wejściowych.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure


> [!NOTE] 
> Poniższe polecenie wymaga interfejsu wiersza polecenia Azure 2.0.8 lub nowszej. Możesz sprawdzić wersję interfejsu wiersza polecenia przy użyciu `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Poniższy przykład służy do wdrażania klastra OKD i wszystkie powiązane zasoby w grupie zasobów o nazwie myResourceGroup, o nazwie wdrożenia myOpenShiftCluster. Szablon odwołuje się bezpośrednio z repozytorium GitHub przy użyciu pliku parametrów lokalnych o nazwie azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Przeprowadzenie wdrożenia zajmuje co najmniej 25 minut, w zależności od całkowitej liczby wdrożonych węzłów. Adres URL konsoli OKD i nazwa DNS drukuje wzorca OpenShift na terminalu, po zakończeniu wdrożenia.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Nawiąż połączenie z klastrem OKD

Po zakończeniu wdrożenia, połączyć się z konsoli OKD za pomocą przeglądarki przy użyciu `OpenShift Console Uri`. Można też połączyć do poziomu głównego OKD, za pomocą następującego polecenia:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenia, aby usunąć grupę zasobów, klaster OpenShift i wszystkie pokrewne zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia platformy OpenShift](./openshift-troubleshooting.md)
- [Wprowadzenie do OKD](https://docs.okd.io/latest/getting_started/index.html)
