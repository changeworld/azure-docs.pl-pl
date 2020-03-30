---
title: Zasady sieciowe usługi Azure Kubernetes | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach sieciowych kubernetes w celu zabezpieczenia klastra kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159681"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Zasady sieciowe platformy Azure Kubernetes — przegląd

Zasady sieci zapewniają mikrosegmentacji zasobników, podobnie jak sieciowe grupy zabezpieczeń (NSG) zapewniają mikrosegmentacji dla maszyn wirtualnych. Implementacja zasad sieciowej platformy Azure obsługuje standardową specyfikację zasad sieciowych kubernetes. Za pomocą etykiet można wybrać grupę zasobników i zdefiniować listę reguł ruchu przychodzącego i wychodzącego, które określają rodzaj ruchu, który jest dozwolony do i z tych zasobników. Dowiedz się więcej o zasadach sieci kubernetes w [dokumentacji kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Omówienie zasad sieciowych usługi Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Zasady sieci platformy Azure działają w połączeniu z usługą Azure CNI, która zapewnia integrację sieci wirtualnej dla kontenerów. Jest obsługiwany tylko w węzłach Linuksa dzisiaj. Implementacje konfigurują reguły tabeli IP systemu Linux na podstawie zdefiniowanych zasad w celu wymuszenia filtrowania ruchu.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planowanie zabezpieczeń klastra kubernetes
Podczas wdrażania zabezpieczeń klastra należy używać sieciowych grup zabezpieczeń (NSG) do filtrowania ruchu północ-południe, czyli ruchu wchodzącego i wychodzącego z podsieci klastra oraz używać zasad sieciowych Kubernetes dla ruchu wschód-zachód, czyli ruchu między zasobnikami w klastra.

## <a name="using-azure-kubernetes-network-policies"></a>Korzystanie z zasad sieciowych usługi Azure Kubernetes
Zasady sieci azure można używać w następujących sposobów, aby zapewnić mikrosegmentacji zasobników.

### <a name="acs-engine"></a>Silnik ACS
ACS-Engine to narzędzie, które generuje szablon usługi Azure Resource Manager do wdrażania klastra Kubernetes na platformie Azure. Konfiguracja klastra jest określona w pliku JSON, który jest przekazywany do narzędzia podczas generowania szablonu. Aby uzyskać więcej informacji na temat wszystkich obsługiwanych ustawień klastra, zobacz Microsoft Azure Container Service Engine — Cluster Definition (Usługa Microsoft Azure Container Service Engine — definicja klastra).

Aby włączyć zasady dotyczące klastrów wdrożonych przy użyciu acs-engine, określ wartość ustawienia networkPolicy w pliku definicji klastra jako "azure".

#### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniższa przykładowa konfiguracja JSON tworzy nową sieć wirtualną i podsieć i wdraża w nim klaster Kubernetes za pomocą usługi Azure CNI. Zalecamy użycie "Notatnika" do edycji pliku JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Tworzenie własnego klastra kubernetes na platformie Azure
Implementacja może służyć do zapewnienia zasad sieciowych dla zasobników w klastrach Kubernetes, które można wdrożyć samodzielnie, bez polegania na narzędziach, takich jak aparat ACS. W takim przypadku należy najpierw zainstalować wtyczkę CNI i włączyć ją na każdej maszynie wirtualnej w klastrze. Aby uzyskać szczegółowe instrukcje, zobacz [Wdrażanie wtyczki dla samodzielnie wdrażanego klastra Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Po wdrożeniu klastra `kubectl` uruchom następujące polecenie, aby pobrać i zastosować *demon zasad sieciowej* platformy Azure do klastra.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Rozwiązanie jest również open source i kod jest dostępny w [repozytorium Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Kubernetes](../aks/intro-kubernetes.md).
-  Dowiedz się więcej o [sieci kontenerowej](container-networking-overview.md).
- [Wdrażanie dodatku plug-in](deploy-container-networking.md) dla klastrów usługi Kubernetes lub kontenerów platformy Docker.
