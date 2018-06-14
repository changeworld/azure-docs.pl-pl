---
title: Wdrażanie pochodzenia OpenShift na platformie Azure | Dokumentacja firmy Microsoft
description: Wdróż pochodzenia OpenShift na platformie Azure.
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
ms.openlocfilehash: f7a668f30d7acb1ea14fe9fd8921066d40a6669b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123123"
---
# <a name="deploy-openshift-origin-in-azure"></a>Wdrażanie pochodzenia OpenShift na platformie Azure

Można użyć jednej z dwóch sposobów wdrażania pochodzenia OpenShift na platformie Azure:

- Ręczne wdrażanie wszystkich składników niezbędnych infrastruktury platformy Azure i postępuj zgodnie ze źródłem OpenShift [dokumentacji](https://docs.openshift.org/3.6/welcome/index.html).
- Można również użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-origin) co upraszcza wdrażanie klastra OpenShift pochodzenia.

## <a name="deploy-by-using-the-openshift-origin-template"></a>Wdrażanie przy użyciu szablonu OpenShift źródła

Użyj `appId` wartości z nazwy głównej usługi, który utworzono wcześniej dla `aadClientId` parametru.

Poniższy przykład tworzy plik parametrów o nazwie azuredeploy.parameters.json z wszystkich wymaganych danych wejściowych.

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
> Polecenie wymaga interfejsu wiersza polecenia Azure 2.0.8 lub nowszym. Możesz sprawdzić wersję interfejsu wiersza polecenia z `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Poniższy przykład wdraża klaster OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie myResourceGroup, o nazwie myOpenShiftCluster wdrożenia. Szablon odwołuje się bezpośrednio z repozytorium GitHub przy użyciu pliku lokalnego parametrów o nazwie azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Wdrożenie ma co najmniej 25 minut, w zależności od całkowitej liczby węzłów wdrożone. Adres URL konsoli OpenShift i nazwa DNS odbitek wzorca OpenShift na terminalu po zakończeniu wdrożenia.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Połącz się z klastrem OpenShift

Po zakończeniu wdrożenia nawiązać konsoli OpenShift w przeglądarce, za pomocą `OpenShift Console Uri`. Można również łączyć się z główną OpenShift, za pomocą następującego polecenia:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenia, aby usunąć grupę zasobów, OpenShift klastra, a wszystkie powiązane zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- [Zadania po wdrożeniu](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift](./openshift-troubleshooting.md)
- [Wprowadzenie do platformy OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
