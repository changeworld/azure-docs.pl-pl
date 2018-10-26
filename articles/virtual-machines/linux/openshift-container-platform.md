---
title: Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure.
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
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 21eebb6c27a83b939f321d38026da7d4c39b7071
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085890"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure

Do wdrożenia rozwiązania OpenShift Container Platform na platformie Azure, można użyć jednej z kilku metod:

- Można ręcznie wdrożyć składniki niezbędne infrastruktury platformy Azure, a następnie wykonaj [dokumentacja platformy OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-container-platform/) upraszczające proces wdrożenia klastra OpenShift Container Platform.
- Innym rozwiązaniem jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Wszystkie opcje wymagana jest subskrypcja firmy Red Hat. Podczas wdrażania wystąpienie Red Hat Enterprise Linux jest zarejestrowany do subskrypcji Red Hat i dołączony do identyfikator puli, który zawiera uprawnień dla rozwiązania OpenShift Container Platform.
Upewnij się, że masz prawidłowe Red Hat subskrypcji Menedżera (RHSM) nazwa użytkownika, hasło i identyfikator puli. Można użyć klucza aktywacji, Org ID i identyfikator puli. Możesz sprawdzić te informacje, logując się do https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Wdrażanie przy użyciu szablonu usługi Resource Manager platformy OpenShift Container Platform

Aby wdrożyć przy użyciu szablonu usługi Resource Manager, umożliwia pliku parametrów podać parametry wejściowe. Aby dostosować wdrożenia, rozwidlenie repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- Szczegóły klastra OpenShift, zmodyfikować za pomocą pliku hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurowanie pliku parametrów

[Szablonu OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) ma wiele gałęzi dostępne dla różnych wersji OpenShift Container Platform.  Zgodnie z potrzebami, można wdrażać bezpośrednio z repozytorium lub można utworzyć Rozgałęzienie repozytorium i zmiany niestandardowych szablonów lub skryptów przed wdrożeniem.

Użyj `appId` wartości z nazwy głównej usługi została wcześniej utworzona dla `aadClientId` parametru.

Poniższy przykład pokazuje plik parametrów o nazwie azuredeploy.parameters.json przy użyciu wszystkich wymaganych danych wejściowych.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
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
        "rhsmBrokerPoolId": {
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
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Zastąp parametry konkretnych informacji.

Różne wersje mogą mieć różnych parametrów, dlatego Sprawdź niezbędne parametry dla gałęzi, którego używasz.

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

> [!NOTE] 
> Poniższe polecenie wymaga interfejsu wiersza polecenia Azure 2.0.8 lub nowszej. Możesz sprawdzić wersję interfejsu wiersza polecenia przy użyciu `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Poniższy przykład służy do wdrażania klastra OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg, o nazwie wdrożenia myOpenShiftCluster. Szablon odwołuje się bezpośrednio z repozytorium GitHub i parametrów lokalnych, który jest używany plik o nazwie pliku azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Przeprowadzenie wdrożenia zajmie przynajmniej 30 minut, na podstawie całkowitej liczby wdrożonych węzłów i skonfigurowanych opcji. Bastionu FQDN DNS i adres URL konsoli usługi OpenShift drukuje do terminalu, po zakończeniu wdrożenia.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Jeśli nie chcesz blokowały wiersza polecenia, oczekiwanie na wdrożenie w celu ukończenia, Dodaj `--no-wait` jako jedną z opcji dla wdrożenia grupy. Dane wyjściowe z wdrożenia mogą być pobierane z witryny Azure portal w sekcji Wdrażanie dla grupy zasobów.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Wdrażanie przy użyciu oferty OpenShift Container Platform Azure Marketplace

Najprostszym sposobem wdrażania OpenShift Container Platform na platformie Azure jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Jest to najprostsza opcja, ale również ma ona ograniczone możliwości dostosowywania. Oferty w portalu Marketplace obejmuje następujące opcje konfiguracji:

- **Węzły główne**: wzorzec węzłów trzy (3) z możliwością konfiguracji typu wystąpienia.
- **Węzły infra**: trzy (3) Infra węzłów z typem wystąpienia można konfigurować.
- **Węzły**: liczba węzłów jest konfigurowany (zakresu od 2 do 9) oraz typu wystąpienia.
- **Typ dysku**: dyski Managed Disks są używane.
- **Sieć**: Obsługa nowej lub istniejącej sieci, a także niestandardowy zakres CIDR.
- **CNS**: CNS można ją włączyć.
- **Metryki**: metryki można ją włączyć.
- **Rejestrowanie**: Rejestrowanie można włączyć.
- **Dostawca usług w chmurze Azure**: można ją włączyć.

## <a name="connect-to-the-openshift-cluster"></a>Nawiąż połączenie z klastrem platformy OpenShift

Po zakończeniu wdrożenia, należy pobrać połączenie z sekcji wyjściowej wdrożenia. Łączenie z konsolą OpenShift za pomocą przeglądarki przy użyciu `OpenShift Console URL`. Możesz też SSH z hostem bastionu. Poniżej znajduje się przykład, w którym nazwa użytkownika administratora jest clusteradmin i publiczny adres IP bastionu pełni kwalifikowaną nazwę domeny DNS jest bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenia, aby usunąć grupę zasobów, klaster OpenShift i wszystkie pokrewne zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure](./openshift-troubleshooting.md)
- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Współautorów dokumentacji

Dziękujemy za Vincent zasilania (vincepower) i Alfred Sin (asinn826) ich wkładu do niniejszej dokumentacji aktualizowanie!