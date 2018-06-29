---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: iainfou
ms.openlocfilehash: ffd81835de82cc5a00b3f6705a7607a51bb3bfa0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096455"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Service Kubernetes (AKS)

Ten artykuł adresy częste pytania dotyczące usługi Azure Service Kubernetes (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Regiony platformy Azure zapewnia dzisiaj usługi Kubernetes Azure (AKS)?

- Australia Wschodnia
- Kanada Środkowa
- Kanada Wschodnia
- Środkowe stany USA
- Wschodnie stany USA
- Wschodnie US2
- Europa Północna
- Południowe Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Aktualizacje zabezpieczeń są stosowane do węzłów agenta AKS?

Azure automatycznie stosuje poprawki zabezpieczeń do węzłów w klastrze zgodnie z harmonogramem co noc. Jednak ponosisz odpowiedzialność za zapewnienie, że węzły są ponownie uruchamiane zgodnie z wymaganiami. Masz kilka opcji umożliwiających wykonywanie ponowne uruchomienie węzła:

- Ręcznie za pośrednictwem portalu Azure lub interfejsu wiersza polecenia Azure.
- Uaktualniając AKS klastra. Uaktualnienia klastrowania automatycznie [cordon i opróżnienia węzłów](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), dostosowania ich kopię zapasową przy użyciu najnowszych Ubuntu obrazu. Aktualizuj obraz systemu operacyjnego na węzły bez zmiany wersji Kubernetes, określając w bieżącej wersji klastra `az aks upgrade`.
- Przy użyciu [Kured](https://github.com/weaveworks/kured), demon Kubernetes open source ponowne uruchomienie komputera. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł obecność pliku wskazujący, że wymagane jest ponowne uruchomienie. Organizuje następnie ponowne uruchomienia w ramach klastra zgodnie z tej samej cordon i opróżniania proces opisany wcześniej.

## <a name="does-aks-support-node-autoscaling"></a>AKS obsługuje funkcję skalowania automatycznego węzeł?

Tak, funkcję skalowania automatycznego jest dostępny za pośrednictwem [Kubernetes autoscaler] [ auto-scaler] od Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS obsługuje Kubernetes kontroli dostępu opartej na rolach (RBAC)?

Tak, RBAC można włączyć w przypadku wdrażania klastra AKS z wiersza polecenia platformy Azure lub usługi Azure Resource Manager szablonu. Ta funkcja wkrótce rozpocznie się do portalu Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Jakie Kubernetes wprowadzenia kontrolerów obsługuje AKS? Można to skonfigurować?

AKS obsługuje następujące [wprowadzenia kontrolerów][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* Konto_usługi
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Nie jest obecnie możliwe zmodyfikowanie listy kontrolerów wprowadzenia w AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć AKS w mojej istniejącej sieci wirtualnej

Tak, można wdrożyć klaster AKS do istniejącej sieci wirtualnej przy użyciu [zaawansowanych funkcji sieci](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Usługa Azure Key Vault jest zintegrowany z AKS?

AKS nie jest natywnie zintegrowany z usługą Azure Key Vault w tej chwili. Istnieją jednak rozwiązania społeczności, takich jak [acs agenta keyvault z Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kontenery systemu Windows Server można uruchomić na AKS?

Aby uruchomić kontenery systemu Windows Server, należy uruchomić węzłów z systemem Windows Server. W tej chwili nie są dostępne w AKS węzły na serwerze z systemem Windows. Jeśli musisz uruchomić kontenery systemu Windows Server na Kubernetes na platformie Azure, zobacz [dokumentacji dla aparatu acs](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego są tworzone dwie grupy zasobów z AKS?

Dla każdego wdrożenia AKS obejmuje dwie grupy zasobów. Pierwszy jest tworzone przez użytkownika i zawiera zasób usługi Kubernetes. Dostawca zasobów AKS automatycznie tworzy drugi podczas wdrażania o nazwie, jak *MC_myResourceGroup_myAKSCluster_eastus*. Drugi grupa zasobów zawiera wszystkie zasoby infrastruktury skojarzony z klastrem, takich jak maszyn wirtualnych, sieci i magazynu. Aby uprościć Oczyszczanie zasobów zostanie utworzony.

W przypadku tworzenia zasobów, które będą używane z klastra AKS, takich jak konta magazynu lub zastrzeżone publicznego adresu IP, należy umieścić je w grupie zasobów wygenerowanej automatycznie.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS oferuje umowę dotyczącą poziomu usług?

W Umowa dotycząca poziomu usług (SLA) dostawca zgadza się zwrócić klienta do kosztu usługi powinny poziomu opublikowane usługi nie można spełnić. Ponieważ AKS sam jest bezpłatne, jest bez dostępne zwrotu kosztów i w związku z tym nie formalnych umowy SLA. Jednak firma Microsoft starać się zachować dostępność co najmniej 99,5% do serwera interfejsu API Kubernetes.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
