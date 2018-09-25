---
title: Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure.
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
ms.openlocfilehash: 48b6287fef673c5f335531b6f230993969fc9e1c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996336"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure

Do wdrożenia rozwiązania OpenShift Container Platform na platformie Azure, można użyć jednej z kilku metod:

- Można ręcznie wdrożyć składniki niezbędne infrastruktury platformy Azure, a następnie wykonaj OpenShift Container Platform [dokumentacji](https://docs.openshift.com/container-platform/3.10/welcome/index.html).
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-container-platform/) upraszczające proces wdrożenia klastra OpenShift Container Platform.
- Innym rozwiązaniem jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Wszystkie opcje wymagana jest subskrypcja firmy Red Hat. Podczas wdrażania wystąpienie Red Hat Enterprise Linux jest zarejestrowany do subskrypcji Red Hat i dołączony do identyfikator puli, który zawiera uprawnień dla rozwiązania OpenShift Container Platform.
Upewnij się, że masz prawidłową nazwę użytkownika w systemie Red Hat subskrypcji Menedżera (RHSM), hasło i identyfikator puli. Możesz sprawdzić te informacje, logując się do https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Wdrażanie przy użyciu szablonu usługi Resource Manager platformy OpenShift Container Platform

Aby wdrożyć przy użyciu szablonu usługi Resource Manager, należy użyć pliku parametrów umożliwiają określanie wartości parametrów wejściowych. Dostosuj którekolwiek z elementów wdrożenia, które nie są objęte przy użyciu parametrów wejściowych, Utwórz rozwidlenie repozytorium GitHub i zmienić odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Sieć wirtualna CIDR (zmienna w azuredeploy.json)
- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- Szczegóły klastra OpenShift, zmodyfikować za pomocą pliku hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurowanie pliku parametrów

Użyj `appId` wartości z nazwy głównej usługi została wcześniej utworzona dla `aadClientId` parametru. 

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

Zastąp elementy w nawiasach przy użyciu konkretnych informacji.

### <a name="deploy-by-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

> [!NOTE] 
> Poniższe polecenie wymaga CLI.8 platformy Azure lub nowszej. Możesz sprawdzić wersję interfejsu wiersza polecenia przy użyciu `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Poniższy przykład służy do wdrażania klastra OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie myResourceGroup, o nazwie wdrożenia myOpenShiftCluster. Szablon odwołuje się bezpośrednio z repozytorium GitHub i parametrów lokalnych, który jest używany plik o nazwie pliku azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Przeprowadzenie wdrożenia zajmie przynajmniej 30 minut, w zależności od całkowitej liczby wdrożonych węzłów. Adres URL konsoli usługi OpenShift i nazwa DNS drukuje wzorca OpenShift na terminalu, po zakończeniu wdrożenia.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Wdrażanie przy użyciu oferty OpenShift Container Platform Azure Marketplace

Najprostszym sposobem wdrażania OpenShift Container Platform na platformie Azure jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Jest to najprostsza opcja, ale również ma ona ograniczone możliwości dostosowywania. Oferta obejmuje trzy opcje konfiguracji:

- **Małe**: wdraża klaster bez wysokiej dostępności (HA) przy użyciu jednego węzła głównego, węzła jedna infrastruktura, z uwzględnieniem dwóch węzłów aplikacji i bastionu jeden węzeł. Wszystkie węzły są standardowych rozmiarów maszyn wirtualnych DS2v2. Ten klaster wymaga 10 łączna liczba rdzeni i doskonale nadaje się do testowania na niewielką skalę.
- **Średnia**: wdraża klaster o wysokiej dostępności przy użyciu trzech węzłów głównych, z uwzględnieniem dwóch węzłów infrastruktury, cztery węzły aplikacji i bastionu jeden węzeł. Wszystkie węzły oprócz węzła bastionu są standardowych rozmiarów maszyn wirtualnych DS3v2. Ten węzeł bastionu jest standardowa DS2v2. Ten klaster wymaga 38 rdzeni.
- **Duże**: wdraża klaster o wysokiej dostępności przy użyciu trzech węzłów głównych, z uwzględnieniem dwóch węzłów infrastruktury, sześciu węzłów aplikacji i bastionu jeden węzeł. Węzły główne i infrastruktury są standardowych rozmiarów maszyn wirtualnych DS3v2. Węzły aplikacji są standardowych rozmiarów maszyn wirtualnych DS4v2, a węzeł bastionu jest standardowa DS2v2. Ten klaster wymaga 70 rdzeni.

Konfiguracja dostawcy rozwiązań w chmurze platformy Azure jest opcjonalny w przypadku klastrów w średnich i dużych rozmiarów. Rozmiar klastra małych nie daje opcję konfigurowania dostawcy rozwiązań w chmurze platformy Azure.

## <a name="connect-to-the-openshift-cluster"></a>Nawiąż połączenie z klastrem platformy OpenShift

Po zakończeniu wdrożenia, połączyć się z konsoli platformy OpenShift za pomocą przeglądarki przy użyciu `OpenShift Console Uri`. Można też połączyć do poziomu głównego OpenShift, za pomocą następującego polecenia:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenia, aby usunąć grupę zasobów, klaster OpenShift i wszystkie pokrewne zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure](./openshift-troubleshooting.md)
- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
