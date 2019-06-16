---
title: Zasad sieciowych w usłudze Azure Kubernetes | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o o usłudze Kubernetes zasad sieciowych do zabezpieczania klastra Kubernetes.
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
ms.openlocfilehash: a5c367402bd1e61485095fd1d565a8582acc3a9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824907"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Omówienie zasad sieci w usłudze Azure Kubernetes

Zasady sieciowe Obejmij mikrosegmentację zasobników tak samo, jak sieciowe grupy zabezpieczeń (NSG) zapewnia mikrosegmentację w przypadku maszyn wirtualnych. Implementacja zasad sieci platformy Azure obsługuje standardowych specyfikacji zasad sieciowych usługi Kubernetes. Etykiety można użyć, aby wybrać grupę zasobników oraz zdefiniować listę reguł ruchu przychodzącego i wychodzącego, określające typ ruchu, który jest dozwolony do i z tych zasobników. Dowiedz się więcej na temat zasad sieciowych platformy Kubernetes w [dokumentacją rozwiązania Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Omówienie zasad sieciowych usługi Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Zasady sieci platformy Azure działają w połączeniu z wtyczki Azure CNI, który zapewnia integrację sieci wirtualnej dla kontenerów. Możliwe jest tylko w przypadku węzłów systemu Linux już dziś. Implementacje skonfigurować reguły tabeli IP systemu Linux, na podstawie zdefiniowanych zasad do wymuszania filtrowanie ruchu.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planowanie zabezpieczeń dla klastra Kubernetes
Podczas implementowania zabezpieczeń dla klastra, używać sieciowych grup zabezpieczeń (NSG) do filtrowania ruchu północ-południe, oznacza to, że ruch wprowadzania i pozostawienie niezmienionej podsieci środowiska klastra i zasad sieci Kubernetes wschód-zachód ruchu, który jest. ruch między zasobników w klastrze.

## <a name="using-azure-kubernetes-network-policies"></a>Przy użyciu zasad sieci Azure Kubernetes
Zasady sieci platformy Azure może służyć w następujący sposób w celu zapewnienia mikrosegmentację zasobników.

### <a name="acs-engine"></a>ACS-engine
Aparat ACS jest narzędziem, które generuje szablonu usługi Azure Resource Manager w przypadku wdrażania klastra Kubernetes na platformie Azure. Konfiguracja klastra jest określona w pliku JSON, który jest przekazywany do narzędzia podczas generowania szablonu. Aby dowiedzieć się więcej na temat całą listę ustawień klastra obsługiwane i ich opisów, zobacz aparat Microsoft Azure Container Service — definicja klastra.

Aby włączyć zasady w klastrach wdrożonych za pomocą usługi acs-engine, należy określić wartość ustawienia networkPolicy w pliku definicji klastra jako "azure".

#### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniżej JSON przykładową konfigurację tworzy nową sieć wirtualną i podsieć i wdraża klaster Kubernetes w nim za pomocą wtyczki Azure CNI. Zalecamy użycie "Notatnik" do edycji pliku JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
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
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Tworzenie klastra Kubernetes na platformie Azure
Implementacja może służyć do zapewnienia zasad sieciowych zasobników w klastrów Kubernetes wdrażanie samodzielnie, bez konieczności polegania na narzędzi, takich jak aparat ACS. W tym przypadku najpierw zainstalować dodatek typu plug-in CNI i włączenie go dla każdej maszyny wirtualnej w klastrze. Aby uzyskać szczegółowe instrukcje, zobacz [Wdrażanie wtyczki dla samodzielnie wdrażanego klastra Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Gdy jest wdrażany klaster, uruchom następujące polecenie `kubectl` polecenie pobrania i zastosowania zasad sieci platformy Azure *daemonset* do klastra.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
To rozwiązanie jest również "open source", a kod jest dostępna w [repozytorium Azure kontenera sieci](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Dowiedz się więcej o [kontenerowa](container-networking-overview.md).
- [Wdrażanie dodatku typu plug-in](deploy-container-networking.md) dla klastrów Kubernetes lub kontenerów platformy Docker.