---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: f365fcd61944fbae131ab79a1c3660aaf02fa8d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073930"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

Ten artykuł adresy częste pytania dotyczące usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Azure Kubernetes Service (AKS) jest świadczona w których regionach platformy Azure już dzisiaj?

Aby uzyskać pełną listę dostępnych regionów, zobacz [AKS regiony i dostępność][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Usługa AKS obsługuje automatyczne skalowanie węzłów?

Tak, automatycznego skalowania jest dostępna za pośrednictwem [skalowania automatycznego platformy Kubernetes] [ auto-scaler] od Kubernetes 1.10. Aby uzyskać więcej informacji na temat ręcznego konfigurowania i używania skalowanie klastra, zobacz [klastra funkcja automatycznego skalowania w usłudze AKS][aks-cluster-autoscale].

Umożliwia także skalowanie klastra wbudowane (obecnie dostępna w wersji zapoznawczej w usłudze AKS) można zarządzać skalowaniem węzłów. Aby uzyskać więcej informacji, zobacz [automatyczne skalowanie klastra, aby spełniać wymagania aplikacji w usłudze AKS][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Usługa AKS obsługuje kontroli dostępu opartej na rolach Kubernetes (RBAC)?

Tak, Kubernetes RBAC jest domyślnie włączona, gdy klastry są tworzone przy użyciu wiersza polecenia platformy Azure. RBAC można włączyć w przypadku klastrów utworzonych za pomocą witryny Azure portal lub szablonów.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć usługi AKS w mojej istniejącej sieci wirtualnej?

Tak, można wdrożyć klaster usługi AKS do istniejącej sieci wirtualnej przy użyciu [zaawansowanych funkcji sieciowych][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Można ograniczyć serwera interfejsu API rozwiązania Kubernetes, aby tylko był dostępny w ramach sieci wirtualnej?

Nie w tej chwili. Serwera interfejsu API rozwiązania Kubernetes jest udostępniany jako publiczny w pełni kwalifikowana nazwa domeny (FQDN). Możesz kontrolować dostęp do klastra za pomocą [Kubernetes opartej na rolach kontrola dostępu (RBAC) i Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Aktualizacje zabezpieczeń są stosowane do węzłów agentów AKS?

Azure automatycznie stosuje poprawki zabezpieczeń do węzłów systemu Linux w klastrze nocne zgodnie z harmonogramem. Jesteś odpowiedzialny za zapewnienie, że Linux, te węzły są ponownie uruchamiane jako wymagane. Masz kilka opcji umożliwiających wykonywanie ponownego uruchamiania węzła:

- Ręcznie za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.
- Po uaktualnieniu klastra usługi AKS. Klaster automatycznie uaktualnienia [odizolowywanie i opróżnianie węzłów][cordon-drain], następnie przełączyć każdy węzeł kopii zapasowej przy użyciu najnowszego obrazu systemu Ubuntu i nowa wersja poprawki lub pomocniczej wersji platformy Kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade].
- Za pomocą [Kured](https://github.com/weaveworks/kured), demon ponowny rozruch typu open source dla platformy Kubernetes. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł na obecność pliku wskazujący, że wymagane jest ponowne uruchomienie komputera. Ponowne uruchamianie systemu operacyjnego są zarządzane w klastrze, korzystając z tych samych [odizolowywanie i opróżnianie procesu] [ cordon-drain] jako uaktualniania klastra.

Aby uzyskać więcej informacji na temat używania kured zobacz [stosowania aktualizacji zabezpieczeń i jądra dla węzłów w usłudze AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS) aktualizacja Windows automatycznie uruchamiania i Zastosuj najnowsze aktualizacje. Zgodnie z ustalonym harmonogramem w całym cyklu tworzenia wydań Windows Update i procesu sprawdzania poprawności należy wykonać uaktualnienie na pule węzłów systemu Windows Server, w klastrze AKS. Ten proces uaktualnienia tworzy węzły, które Uruchom najnowszego obrazu systemu Windows Server i poprawkami, a następnie usuwa starszą węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego dwie grupy zasobów są tworzone za pomocą usługi AKS

Każde wdrożenie usługi AKS obejmuje dwie grupy zasobów:

- Pierwszej grupy zasobów jest tworzony przez użytkownika i zawiera zasób usługi Kubernetes. Dostawca zasobów usługi AKS automatycznie tworzy drugi podczas wdrażania, takie jak *MC_myResourceGroup_myAKSCluster_eastus*. Instrukcje dotyczące określania nazwy tej drugiej grupy zasobów Zobacz następną sekcję.
- Ten zasób drugiej grupy, takie jak *MC_myResourceGroup_myAKSCluster_eastus*, zawiera wszystkie zasoby infrastruktury skojarzonego z klastrem. Te zasoby obejmują maszyny wirtualne z węzła usługi Kubernetes, sieci wirtualnych i magazynu. Ta grupa osobny zasób jest tworzony uprościć czyszczenie zasobów.

Jeśli tworzysz zasoby przeznaczone do użycia z klastrem usługi AKS, takich jak konta magazynu lub zastrzeżone publiczne adresy IP, należy je umieścić w grupie zasobów automatycznie wygenerowany.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Czy mogę przekazać własną nazwę grupy zasobów infrastruktury usługi AKS

Tak. Domyślnie dostawca zasobów usługi AKS automatycznie tworzy grupę zasobów pomocniczych podczas wdrażania, takie jak *MC_myResourceGroup_myAKSCluster_eastus*. Aby zapewnić zgodność z zasadami firmowymi, zapewniają swoją własną nazwę dla tego klastra zarządzanego (*MC_*) grupy zasobów.

Aby określić własne nazwy grupy zasobów, należy zainstalować [podglądu usługi aks] [ aks-preview-cli] wiersza polecenia platformy Azure w wersji rozszerzenia *wersji 0.3.2* lub nowszej. Podczas tworzenia klastra usługi AKS przy użyciu [tworzenie az aks] [ az-aks-create] poleceń, użyj *— węzeł resource-group* parametru i określ nazwę grupy zasobów. Jeśli użytkownik [używania szablonu usługi Azure Resource Manager] [ aks-rm-template] wdrożyć klaster usługi AKS, można zdefiniować, używając nazwy grupy zasobów *nodeResourceGroup* właściwości.

* Ta grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure w ramach własnej subskrypcji.
* Nazwa grupy zasobów niestandardowych można określić tylko wtedy, gdy został utworzony klaster.

Nie są obsługiwane następujące scenariusze:

* Nie można określić istniejącą grupę zasobów dla *MC_* grupy.
* Nie można określić inną subskrypcję dla *MC_* grupy zasobów.
* Nie można zmienić *MC_* nazwy grupy zasobów po utworzeniu klastra.
* Nie można określić nazwy zasobów zarządzanych w ramach *MC_* grupy zasobów.
* Nie można zmodyfikować lub usunąć tagów zasobów zarządzanych w ramach *MC_* grupy zasobów (Zobacz dodatkowe informacje w następnej sekcji).

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

Tak, kontenery systemu Windows Server są dostępne w wersji zapoznawczej. Do uruchamiania kontenerów systemu Windows Server w usłudze AKS, możesz utworzyć pulę węzłów z systemem Windows Server jako system operacyjny gościa. Kontenery systemu Windows Server można używać tylko systemu Windows Server w 2019 r. Aby rozpocząć pracę, [Tworzenie klastra AKS z pulą węzłów systemu Windows Server][aks-windows-cli].

Obsługa puli węzeł serwera okna obejmuje pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie platformy Kubernetes. Aby uzyskać więcej informacji na temat tych ograniczeń, zobacz [kontenery systemu Windows Server w usłudze AKS ograniczenia][aks-windows-limitations].

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
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
