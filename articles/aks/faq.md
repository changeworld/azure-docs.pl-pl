---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/09/2018
ms.author: nepeters
ms.openlocfilehash: ca8d883add04d623c17390a0b5031aaf08be0429
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596501"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Service Kubernetes (AKS)

Ten artykuł adresy częste pytania dotyczące usługi Azure Service Kubernetes (AKS).

> [!IMPORTANT]
> Usługa Azure Kubernetes Service (AKS) jest obecnie w **wersji zapoznawczej**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Regiony platformy Azure zapewnia dzisiaj usługi Kubernetes Azure (AKS)?

- Kanada Środkowa
- Kanada Wschodnia
- Środkowe stany USA
- Wschodnie stany USA
- Europa Zachodnia

## <a name="when-will-additional-regions-be-added"></a>Gdy zostaną dodane dodatkowe regionów?

Dodatkowe regiony są dodawane wraz ze wzrostem wymagań.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Aktualizacje zabezpieczeń są stosowane do węzłów agenta AKS?

Azure automatycznie stosuje poprawki zabezpieczeń do węzłów w klastrze zgodnie z harmonogramem co noc. Jednak ponosisz odpowiedzialność za zapewnienie, że węzły są ponownie uruchamiane zgodnie z wymaganiami. Masz kilka opcji umożliwiających wykonywanie ponowne uruchomienie węzła:

- Ręcznie za pośrednictwem portalu Azure lub interfejsu wiersza polecenia Azure.
- Uaktualniając AKS klastra. Uaktualnienia klastrowania automatycznie [cordon i opróżnienia węzłów](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), dostosowania ich kopię zapasową przy użyciu najnowszych Ubuntu obrazu. Aktualizuj obraz systemu operacyjnego na węzły bez zmiany wersji Kubernetes, określając w bieżącej wersji klastra `az aks upgrade`.
- Przy użyciu [Kured](https://github.com/weaveworks/kured), demon Kubernetes open source ponowne uruchomienie komputera. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł obecność pliku wskazujący, że wymagane jest ponowne uruchomienie. Organizuje następnie te ponowne uruchomienia w ramach klastra zgodnie z tej samej cordon i opróżniania proces opisany wcześniej.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>Polecisz klientom użycie usługi ACS lub AKS?

Podczas AKS pozostaje w wersji zapoznawczej, zaleca się utworzenie klastrów produkcyjnych przy użyciu usługi ACS Kubernetes lub [aparat acs](https://github.com/azure/acs-engine). Użyj AKS Weryfikacja koncepcji wdrożeń i środowisk i testowania.

## <a name="when-will-acs-be-deprecated"></a>Gdy zostaną wycofane ACS?

Usługi ACS zostaną wycofane wokół AKS staje się po chwili Konieczne będzie 12 miesięcy po tej dacie migrację klastrów do AKS. W okresie 12-miesięczny możesz uruchomić wszystkie operacje usług ACS.

## <a name="does-aks-support-node-autoscaling"></a>AKS obsługuje funkcję skalowania automatycznego węzeł?

Skalowanie automatyczne węzeł nie jest obsługiwane, ale znajduje się na plan. Można przyjrzeć to powierzając jej ich konserwację Otwórz [implementacji Skalowanie automatyczne][auto-scaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS obsługuje Kubernetes kontroli dostępu opartej na rolach (RBAC)?

Nie, RBAC nie jest obecnie obsługiwany w AKS, ale będzie wkrótce dostępna.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć AKS w mojej istniejącej sieci wirtualnej

Tak, ta funkcja jest obsługiwana przez [zaawansowanych funkcji sieci](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Usługa Azure Key Vault jest zintegrowany z AKS?

Nie, nie jest, ale jest planowane tej integracji. W międzyczasie, wypróbuj następujące rozwiązania z [Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kontenery systemu Windows Server można uruchomić na AKS?

Aby uruchomić kontenery systemu Windows Server, należy uruchomić węzłów z systemem Windows Server. Węzły na serwerze z systemem Windows są obecnie w [prywatnej wersji zapoznawczej](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/). Jeśli musisz uruchomić kontenery systemu Windows Server na Kubernetes na platformie Azure poza w wersji zapoznawczej, zobacz [dokumentacji dla aparatu acs](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego są tworzone dwie grupy zasobów z AKS?

Dla każdego wdrożenia AKS obejmuje dwie grupy zasobów. Pierwszy jest tworzone przez użytkownika i zawiera tylko zasób AKS. Dostawca zasobów AKS automatycznie tworzy drugi podczas wdrażania o nazwie, jak *MC_myResourceGroup_myAKSCluster_eastus*. Drugi grupa zasobów zawiera wszystkie zasoby infrastruktury skojarzony z klastrem, takich jak maszyn wirtualnych, sieci i magazynu. Aby uprościć Oczyszczanie zasobów zostanie utworzony.

W przypadku tworzenia zasobów, które będą używane z klastra AKS, takich jak konta magazynu lub zastrzeżone publicznego adresu IP, należy umieścić je w grupie zasobów wygenerowanej automatycznie.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
