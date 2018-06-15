---
title: Wdrażanie OpenShift platformy kontenera na platformie Azure | Dokumentacja firmy Microsoft
description: Wdróż OpenShift platformy kontenera na platformie Azure.
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
ms.openlocfilehash: f1ba6a3d3b9e576d513b55beac4e9365102433e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125745"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Wdrażanie OpenShift platformy kontenera na platformie Azure

Jedną z kilku metod umożliwia wdrażanie OpenShift platformy kontenera platformy Azure:

- Można ręcznie wdrożyć składniki niezbędne infrastruktury platformy Azure, a następnie postępuj zgodnie z OpenShift kontenera platformy [dokumentacji](https://docs.openshift.com/container-platform/3.6/welcome/index.html).
- Można również użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-container-platform/) co upraszcza wdrażanie klastra OpenShift kontenera platformy.
- Inną opcją jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Wszystkie opcje wymagana jest subskrypcja Red Hat. Podczas wdrażania wystąpienia Red Hat Enterprise Linux jest zarejestrowany do subskrypcji Red Hat i powiązany identyfikator puli, zawierający uprawnień OpenShift kontenera platformy.
Upewnij się, że masz prawidłową Red Hat subskrypcji Menedżera (RHSM) nazwę użytkownika, hasło i identyfikator puli. Po zarejestrowaniu się w https://access.redhat.com można sprawdzić te informacje.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Wdrażanie przy użyciu szablonu usługi Resource Manager OpenShift kontenera platformy

Aby wdrożyć przy użyciu szablonu usługi Resource Manager, należy użyć pliku parametrów umożliwiają określanie wartości parametrów wejściowych. Aby dostosować żadnego z elementów wdrożenia, które nie są objęte przy użyciu parametrów wejściowych, rozwidlania repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowania obejmują, ale nie są ograniczone do:

- Sieć wirtualna CIDR (zmiennej w azuredeploy.json)
- Rozmiar maszyny Wirtualnej bastionu (zmiennej w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- Szczegóły klastra OpenShift zmodyfikowany za pomocą pliku hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurowanie pliku parametrów

Użyj `appId` wartości z nazwy głównej usługi utworzony wcześniej dla `aadClientId` parametru. 

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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
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

Zastąp w nawiasach z informacjami o określonych elementów.

### <a name="deploy-by-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

> [!NOTE] 
> Polecenie wymaga interfejsu wiersza polecenia Azure 2.0.8 lub nowszym. Możesz sprawdzić wersję interfejsu wiersza polecenia z `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [zainstalować Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Poniższy przykład wdraża klaster OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie myResourceGroup, o nazwie myOpenShiftCluster wdrożenia. Szablon odwołuje się bezpośrednio z repozytorium GitHub i lokalne parametry, których jest używany plik o nazwie pliku azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Wdrożenie ma co najmniej 30 minut, w zależności od całkowitej liczby węzłów wdrożone. Adres URL konsoli OpenShift i nazwa DNS odbitek wzorca OpenShift na terminalu po zakończeniu wdrożenia.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Wdrażanie przy użyciu oferty OpenShift kontenera platformy Azure Marketplace

Najprostszym sposobem, aby wdrożyć OpenShift kontenera platformy na platformie Azure jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Jest to najprostsza opcja, ale również ma ono ograniczone możliwości dostosowywania. Oferta obejmuje trzy opcje konfiguracji:

- **Mała**: wdraża z jednego węzła głównego, infrastruktury jeden węzeł, dwa węzły aplikacji i bastionu jednego węzła klastra z systemem innym niż wysokiej dostępności (HA). Wszystkie węzły są standardowe rozmiary DS2v2 maszyny Wirtualnej. Ten klaster wymaga 10 całkowita liczba rdzeni i doskonale testowania na niewielką skalę.
- **Średnia liczba godzin**: wdraża klastra HA z trzech węzłów głównych, dwóch węzłów infrastruktury cztery węzły aplikacji i bastionu jednego węzła. Wszystkie węzły z wyjątkiem węzła bastionu są standardowe rozmiarów maszyn wirtualnych DS3v2. Węzeł bastionu jest DS2v2 standardowa. Ten klaster wymaga 38 rdzeni.
- **Duże**: wdraża klastra HA z trzech węzłów głównych, dwóch węzłów infrastruktury sześciu węzłów aplikacji i bastionu jednego węzła. Węzły master i infrastruktury są standardowe rozmiary DS3v2 maszyny Wirtualnej. Węzły aplikacji są standardowe rozmiarów maszyn wirtualnych DS4v2, a węzeł bastionu jest standardowe DS2v2. Ten klaster wymaga 70 rdzeni.

Konfiguracja Azure Cloud Solution Provider jest opcjonalny w przypadku klastrów średnich i dużych rozmiarów. Rozmiar klastra małych nie daje opcja konfigurowania Azure Cloud Solution Provider.

## <a name="connect-to-the-openshift-cluster"></a>Połącz się z klastrem OpenShift

Po zakończeniu wdrożenia nawiązać konsoli OpenShift w przeglądarce, za pomocą `OpenShift Console Uri`. Można również łączyć się z główną OpenShift, za pomocą następującego polecenia:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenia, aby usunąć grupę zasobów, OpenShift klastra, a wszystkie powiązane zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- [Zadania po wdrożeniu](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift na platformie Azure](./openshift-troubleshooting.md)
- [Rozpoczynanie pracy z platformą kontenera OpenShift](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
