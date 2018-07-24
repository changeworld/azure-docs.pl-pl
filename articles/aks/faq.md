---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: ea22b33233f85da117de54829e5a16bd7dcab36a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205252"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

Ten artykuł adresy częste pytania dotyczące usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Azure Kubernetes Service (AKS) jest świadczona w których regionach platformy Azure już dzisiaj?

Zobacz usługi Azure Kubernetes Service [regiony i dostępność] [ aks-regions] dokumentacji, aby uzyskać pełną listę.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Aktualizacje zabezpieczeń są stosowane do węzłów agentów AKS?

Azure automatycznie stosuje poprawki zabezpieczeń do węzłów w klastrze nocne zgodnie z harmonogramem. Jednak odpowiedzialność za zapewnienie, że węzły są ponownie uruchamiane zgodnie z wymaganiami. Masz kilka opcji umożliwiających wykonywanie ponownego uruchamiania węzła:

- Ręcznie za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.
- Po uaktualnieniu klastra usługi AKS. Uaktualnienia klastrowania automatycznie [odizolowywanie i opróżnianie węzłów](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), przenieś je wykonać kopię zapasową przy użyciu najnowszego obrazu systemu Ubuntu. Aktualizacja obrazu systemu operacyjnego w węzłach usługi bez wprowadzania zmian w wersji rozwiązania Kubernetes, określając bieżącą wersję klastra `az aks upgrade`.
- Za pomocą [Kured](https://github.com/weaveworks/kured), demon ponowny rozruch typu open source dla platformy Kubernetes. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł na obecność pliku wskazujący, że wymagane jest ponowne uruchomienie komputera. Organizuje ona następnie ponownego uruchamiania w klastrze, korzystania z tego samego cordon i opróżniania proces opisany wcześniej.

## <a name="does-aks-support-node-autoscaling"></a>Usługa AKS obsługuje automatyczne skalowanie węzłów?

Tak, automatycznego skalowania jest dostępna za pośrednictwem [skalowania automatycznego platformy Kubernetes] [ auto-scaler] od Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Usługa AKS obsługuje kontroli dostępu opartej na rolach Kubernetes (RBAC)?

Tak, może być RBAC włączone, gdy [wdrażanie klastra usługi AKS z wiersza polecenia platformy Azure lub usługi Azure Resource Manager szablon](https://docs.microsoft.com/en-us/azure/aks/aad-integration). Ta funkcja niebawem do witryny Azure portal.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Jakie kontrolery dopuszczenie Kubernetes obsługuje AKS? Można to skonfigurować?

AKS obsługuje następujące [kontrolerów dopuszczenie][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Nie jest obecnie możliwe zmodyfikować listę kontrolerów przyjęcia w usłudze AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć usługi AKS w mojej istniejącej sieci wirtualnej?

Tak, można wdrożyć klaster usługi AKS do istniejącej sieci wirtualnej przy użyciu [zaawansowanych funkcji sieciowych](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Można ograniczyć serwera interfejsu API rozwiązania Kubernetes, aby tylko był dostępny w ramach sieci wirtualnej?

Nie w tej chwili. Serwera interfejsu API rozwiązania Kubernetes jest udostępniany jako publiczny pełni kwalifikowana nazwa domeny (FQDN). Należy kontrolować dostęp do klastra za pomocą [Kubernetes opartej na rolach kontrola dostępu (RBAC) i Azure Active Directory (AAD)](https://docs.microsoft.com/en-us/azure/aks/aad-integration).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy usługa Azure Key Vault jest zintegrowana z usługą AKS?

AKS nie jest natywnie zintegrowany z usługą Azure Key Vault w tej chwili. Istnieją jednak rozwiązania społeczności, takich jak [acs agenta magazynu kluczy z Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy można uruchamiać kontenery systemu Windows Server w usłudze AKS

Do uruchamiania kontenerów systemu Windows Server, musisz uruchomić węzłów z systemem Windows Server. Węzły na serwerze z systemem Windows nie są dostępne w usłudze AKS w tej chwili. Jednak umożliwia rozwiązania Virtual Kubelet planowanie kontenerów Windows w usłudze Azure Container Instances i zarządzać nimi w ramach klastra usługi AKS. Aby uzyskać więcej informacji, zobacz [rozwiązania Virtual Kubelet użycia za pomocą usługi AKS][virtual-kubelet].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego dwie grupy zasobów są tworzone za pomocą usługi AKS

Każde wdrożenie usługi AKS obejmuje dwie grupy zasobów. Pierwszy jest tworzony przez użytkownika i zawiera zasób usługi Kubernetes. Dostawca zasobów usługi AKS automatycznie tworzy drugi podczas wdrażania przy użyciu nazwy, takie jak *MC_myResourceGroup_myAKSCluster_eastus*. Druga grupa zasobów zawiera wszystkie zasoby infrastruktury skojarzonego z klastrem, takie jak maszyny wirtualne, sieci i magazynu. Aby uprościć czyszczenie zasobów zostanie utworzony.

Jeśli tworzysz zasoby, które będą używane na potrzeby klastra AKS, takich jak konta magazynu lub zastrzeżony publiczny adres IP, należy umieścić je w grupie zasobów automatycznie generowanych.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS oferuje Umowa dotycząca poziomu usług?

Umowa dotycząca poziomu usług (SLA) dostawca zgadza się, zwrócić klienta kosztu usługi powinien poziom opublikowanej usługi nie będzie spełniony. Ponieważ AKS, sama jest bezpłatna, ma żadnych dostępnych do zwrotu kosztów i dlatego formalne umowa SLA nie. Jednak firma Microsoft stara się zapewnić dostępność co najmniej 99,5% do serwera interfejsu API rozwiązania Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
