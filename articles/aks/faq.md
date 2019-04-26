---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: ae92a5c894b186a1c8b471c1b446a88299742aec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466379"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

Ten artykuł adresy częste pytania dotyczące usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Azure Kubernetes Service (AKS) jest świadczona w których regionach platformy Azure już dzisiaj?

Aby uzyskać pełną listę dostępnych regionów, zobacz [AKS regiony i dostępność][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Usługa AKS obsługuje automatyczne skalowanie węzłów?

Tak, automatycznego skalowania jest dostępna za pośrednictwem [skalowania automatycznego platformy Kubernetes] [ auto-scaler] od Kubernetes 1.10. Aby uzyskać więcej informacji na temat sposobu konfigurowania i używania skalowanie klastra, zobacz [klastra funkcja automatycznego skalowania w usłudze AKS][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Usługa AKS obsługuje kontroli dostępu opartej na rolach Kubernetes (RBAC)?

Tak, Kubernetes RBAC jest domyślnie włączona, gdy klastry są tworzone przy użyciu wiersza polecenia platformy Azure. RBAC można włączyć w przypadku klastrów utworzonych za pomocą witryny Azure portal lub szablonów.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć usługi AKS w mojej istniejącej sieci wirtualnej?

Tak, można wdrożyć klaster usługi AKS do istniejącej sieci wirtualnej przy użyciu [zaawansowanych funkcji sieciowych][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Można ograniczyć serwera interfejsu API rozwiązania Kubernetes, aby tylko był dostępny w ramach sieci wirtualnej?

Nie w tej chwili. Serwera interfejsu API rozwiązania Kubernetes jest udostępniany jako publiczny w pełni kwalifikowana nazwa domeny (FQDN). Możesz kontrolować dostęp do klastra za pomocą [Kubernetes opartej na rolach kontrola dostępu (RBAC) i Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Aktualizacje zabezpieczeń są stosowane do węzłów agentów AKS?

Tak, Azure automatycznie stosuje poprawki zabezpieczeń do węzłów w klastrze nocne zgodnie z harmonogramem. Jednak odpowiedzialność za zapewnienie, że węzły są ponownie uruchamiane zgodnie z wymaganiami. Masz kilka opcji umożliwiających wykonywanie ponownego uruchamiania węzła:

- Ręcznie za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.
- Po uaktualnieniu klastra usługi AKS. Klaster automatycznie uaktualnienia [odizolowywanie i opróżnianie węzłów][cordon-drain], następnie przełączyć każdy węzeł kopii zapasowej przy użyciu najnowszego obrazu systemu Ubuntu i nowa wersja poprawki lub pomocniczej wersji platformy Kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade].
- Za pomocą [Kured](https://github.com/weaveworks/kured), demon ponowny rozruch typu open source dla platformy Kubernetes. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł na obecność pliku wskazujący, że wymagane jest ponowne uruchomienie komputera. Ponowne uruchamianie systemu operacyjnego są zarządzane w klastrze, korzystając z tych samych [odizolowywanie i opróżnianie procesu] [ cordon-drain] jako uaktualniania klastra.

Aby uzyskać więcej informacji na temat używania kured zobacz [stosowania aktualizacji zabezpieczeń i jądra dla węzłów w usłudze AKS][node-updates-kured].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego dwie grupy zasobów są tworzone za pomocą usługi AKS

Każde wdrożenie usługi AKS obejmuje dwie grupy zasobów:

- Pierwszej grupy zasobów jest tworzony przez użytkownika i zawiera zasób usługi Kubernetes. Dostawca zasobów usługi AKS automatycznie tworzy drugi podczas wdrażania, takie jak *MC_myResourceGroup_myAKSCluster_eastus*.
- Ten zasób drugiej grupy, takie jak *MC_myResourceGroup_myAKSCluster_eastus*, zawiera wszystkie zasoby infrastruktury skojarzonego z klastrem. Te zasoby obejmują maszyny wirtualne z węzła usługi Kubernetes, sieci wirtualnych i magazynu. Ta grupa osobny zasób jest tworzony uprościć czyszczenie zasobów.

Jeśli tworzysz zasoby przeznaczone do użycia z klastrem usługi AKS, takich jak konta magazynu lub zastrzeżone publiczne adresy IP, należy je umieścić w grupie zasobów automatycznie wygenerowany.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Można zmodyfikować tagów i innych właściwości zasobów usługi AKS w grupie zasobów MC_ *?

Modyfikowanie i usuwanie tagi utworzone przez platformę Azure i innych właściwości zasobów w *MC_** grupy zasobów może prowadzić do nieoczekiwanych wyników, takich jak skalowanie i uaktualnianie błędy. Możliwe jest tworzenie i modyfikowanie dodatkowe znaczniki niestandardowe, takie jak przypisać centrum kosztu lub jednostki biznesowej. Modyfikowanie zasobów w ramach *MC_** w usłudze AKS klastra przerywa cel poziomu usług (SLO). Aby uzyskać więcej informacji, zobacz [AKS jest oferują umowy dotyczącej poziomu usług?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jakie kontrolery dopuszczenie Kubernetes obsługuje AKS? Można kontrolerów dopuszczenie dodane lub usunięte?

AKS obsługuje następujące [kontrolerów dopuszczenie][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Nie jest obecnie możliwe zmodyfikować listę kontrolerów przyjęcia w usłudze AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy usługa Azure Key Vault jest zintegrowana z usługą AKS?

Obecnie natywnie AKS nie jest zintegrowany z usługą Azure Key Vault. Jednak [Azure klucza magazynu FlexVolume dla projektu Kubernetes] [ keyvault-flexvolume] umożliwia bezpośrednią integrację z zasobników Kubernetes do wpisów tajnych usługi KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy można uruchamiać kontenery systemu Windows Server w usłudze AKS

Do uruchamiania kontenerów systemu Windows Server, musisz uruchomić węzłów z systemem Windows Server. Węzły na serwerze z systemem Windows nie są dostępne w usłudze AKS w tej chwili. Jednak umożliwia rozwiązania Virtual Kubelet planowanie kontenerów Windows w usłudze Azure Container Instances i zarządzać nimi w ramach klastra usługi AKS. Aby uzyskać więcej informacji, zobacz [rozwiązania Virtual Kubelet użycia za pomocą usługi AKS][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS oferuje Umowa dotycząca poziomu usług?

Umowa dotycząca poziomu usług (SLA) dostawca zgadza się, zwrócić klienta kosztu usługi, jeśli poziom opublikowanej usługi nie jest spełniony. Ponieważ AKS, sama jest bezpłatna, ma żadnych dostępnych do zwrotu kosztów i dlatego formalne umowa SLA nie. Jednak AKS dąży do zapewnienia dostępności co najmniej 99,5% do serwera interfejsu API rozwiązania Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol

