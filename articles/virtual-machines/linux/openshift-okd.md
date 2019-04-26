---
title: Wdrażanie OKD na platformie Azure | Dokumentacja firmy Microsoft
description: Wdróż OKD na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: 7db50007dd32c84a360eaec25bf860709272437b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542513"
---
# <a name="deploy-okd-in-azure"></a>Wdrażanie OKD na platformie Azure

Można użyć jednej z dwóch sposobów, aby wdrożyć OKD (dawniej platformy OpenShift Origin) na platformie Azure:

- Ręczne wdrażanie wszystkich składników niezbędnych infrastruktury platformy Azure, a następnie wykonaj [dokumentacji OKD](https://docs.okd.io).
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-origin) upraszczające proces wdrażania OKD klastra.

## <a name="deploy-using-the-okd-template"></a>Wdrażanie przy użyciu szablonu OKD

Aby wdrożyć przy użyciu szablonu usługi Resource Manager, umożliwia pliku parametrów podać parametry wejściowe. Aby dostosować wdrożenia, rozwidlenie repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- Szczegóły klastra OpenShift, zmodyfikować za pomocą pliku hosts (deployOpenShift.sh)

[Szablonu OKD](https://github.com/Microsoft/openshift-origin) ma dostępne dla różnych wersji OKD wielu gałęzi.  Zgodnie z potrzebami, można wdrażać bezpośrednio z repozytorium lub można utworzyć Rozgałęzienie repozytorium i zmiany niestandardowych przed wdrożeniem.

Użyj `appId` wartości z nazwy głównej usługi, która została wcześniej utworzona dla `aadClientId` parametru.

Oto przykładowy plik parametrów o nazwie azuredeploy.parameters.json przy użyciu wszystkich wymaganych danych wejściowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
        "storageKind": {
            "value": "managed"
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
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
        "enableAzure": {
            "value": "true"
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

Zastąp parametry konkretnych informacji.

Różne wersje może mieć różne parametry dlatego Sprawdź, czy niezbędne parametry dla gałęzi, możesz użyć.

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure


> [!NOTE] 
> Poniższe polecenie wymaga interfejsu wiersza polecenia Azure 2.0.8 lub nowszej. Możesz sprawdzić wersję interfejsu wiersza polecenia przy użyciu `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Poniższy przykład służy do wdrażania klastra OKD i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg, o nazwie wdrożenia myOpenShiftCluster. Szablon odwołuje się bezpośrednio z repozytorium GitHub, aby podczas korzystania z pliku lokalnego parametrów o nazwie azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Przeprowadzenie wdrożenia zajmie przynajmniej 30 minut, aby zakończyć, na podstawie całkowitej liczby wdrożonych węzłów. Adres URL konsoli usługi OpenShift i nazwa DNS drukuje wzorca OpenShift na terminalu, po zakończeniu wdrożenia. Alternatywnie można wyświetlić sekcję danych wyjściowych wdrożenia w witrynie Azure portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Jeśli nie chcesz blokowały wiersza polecenia, oczekiwanie na wdrożenie w celu ukończenia, Dodaj `--no-wait` jako jedną z opcji dla wdrożenia grupy. Dane wyjściowe z wdrożenia mogą być pobierane z witryny Azure portal w sekcji Wdrażanie dla grupy zasobów.

## <a name="connect-to-the-okd-cluster"></a>Nawiąż połączenie z klastrem OKD

Po zakończeniu wdrożenia, łączenie z konsolą OpenShift z sieci za pomocą przeglądarki `OpenShift Console Url`. Możesz też SSH z wzorcem OKD. Poniżej przedstawiono przykład, który korzysta z danych wyjściowych z wdrożenia:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group) polecenia, aby usunąć grupę zasobów, klaster OpenShift i wszystkie pokrewne zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia platformy OpenShift](./openshift-troubleshooting.md)
- [Wprowadzenie do OKD](https://docs.okd.io)
