---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 1cc03cbcffc5253e8b357b6702cd21c45740ff81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514491"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

Ten artykuł adresy częste pytania dotyczące usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Które regiony platformy Azure jest obecnie świadczona AKS?

Aby uzyskać pełną listę dostępnych regionów, zobacz [AKS regiony i dostępność][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Usługa AKS obsługuje automatyczne skalowanie węzłów?

Tak, automatycznego skalowania jest dostępna za pośrednictwem [skalowania automatycznego platformy Kubernetes] [ auto-scaler] od Kubernetes 1.10. Aby uzyskać informacje na temat ręcznego konfigurowania i używania skalowanie klastra, zobacz [klastra funkcja automatycznego skalowania w usłudze AKS][aks-cluster-autoscale].

Umożliwia także skalowanie klastra wbudowane (obecnie dostępna w wersji zapoznawczej w usłudze AKS) można zarządzać skalowaniem węzłów. Aby uzyskać więcej informacji, zobacz [automatyczne skalowanie klastra, aby spełniać wymagania aplikacji w usłudze AKS][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-rbac"></a>Usługa AKS obsługuje RBAC platformy Kubernetes?

Tak, kontrola dostępu oparta na rolach Kubernetes (RBAC) jest domyślnie włączona, gdy klastry są tworzone przy użyciu wiersza polecenia platformy Azure. W przypadku klastrów, które zostały utworzone przy użyciu witryny Azure portal lub szablonów, można włączyć funkcji RBAC.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć usługi AKS w mojej istniejącej sieci wirtualnej?

Tak, można wdrożyć klaster usługi AKS w istniejącej sieci wirtualnej za pomocą [zaawansowanych funkcji sieciowych][aks-advanced-networking].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Można utworzyć serwera interfejsu API rozwiązania Kubernetes dostępne tylko w obrębie sieci wirtualnej?

Nie w tej chwili. Serwera interfejsu API rozwiązania Kubernetes jest udostępniany jako publiczny w pełni kwalifikowana nazwa domeny (FQDN). Dostęp do klastra można kontrolować przy użyciu [RBAC platformy Kubernetes i Azure Active Directory (Azure AD)][aks-rbac-aad].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Aktualizacje zabezpieczeń są stosowane do węzłów agentów AKS?

Azure automatycznie stosuje poprawki zabezpieczeń do węzłów systemu Linux w klastrze nocne zgodnie z harmonogramem. Jesteś odpowiedzialny za zapewnienie, że Linux, te węzły są ponownie uruchamiane jako wymagane. Masz kilka opcji ponownego uruchomienia węzłów:

- Ręcznie za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.
- Po uaktualnieniu klastra usługi AKS. Uaktualnianie klastra [odizolowywanie i opróżnianie węzłów] [ cordon-drain] automatycznie, a następnie przełączyć nowy węzeł online przy użyciu najnowszego obrazu systemu Ubuntu i nowa wersja poprawki lub wersja pomocnicza Kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade].
- Za pomocą [Kured](https://github.com/weaveworks/kured), demon ponowny rozruch typu open source dla platformy Kubernetes. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł na obecność pliku, która wskazuje, że wymagane jest ponowne uruchomienie komputera. W klastrze, ponownego uruchamiania systemu operacyjnego są zarządzane przez ten sam [odizolowywanie i opróżnianie procesu] [ cordon-drain] jako uaktualniania klastra.

Aby uzyskać więcej informacji na temat używania kured zobacz [stosowania aktualizacji zabezpieczeń i jądra dla węzłów w usłudze AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS) aktualizacja Windows automatycznie uruchamiania i Zastosuj najnowsze aktualizacje. Zgodnie z ustalonym harmonogramem w całym cyklu tworzenia wydań Windows Update i procesu sprawdzania poprawności należy wykonać uaktualnienie na pule węzłów systemu Windows Server, w klastrze AKS. Ten proces uaktualnienia tworzy węzły, które Uruchom najnowszego obrazu systemu Windows Server i poprawkami, a następnie usuwa starszą węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego dwie grupy zasobów są tworzone za pomocą usługi AKS

Każde wdrożenie usługi AKS obejmuje dwie grupy zasobów:

1. Utworzysz pierwszej grupy zasobów. Ta grupa zawiera zasób usługi Kubernetes. Dostawca zasobów usługi AKS automatycznie tworzy drugi grupy zasobów podczas wdrażania. Na przykład drugiej grupy zasobów *MC_myResourceGroup_myAKSCluster_eastus*. Aby uzyskać informacje dotyczące sposobu określania nazwy tej drugiej grupy zasobów, zobacz następną sekcję.
1. Grupa zasobów drugi, takich jak *MC_myResourceGroup_myAKSCluster_eastus*, zawiera wszystkie zasoby infrastruktury skojarzonego z klastrem. Te zasoby obejmują maszyny wirtualne z węzła usługi Kubernetes, sieci wirtualnych i magazynu. Ta grupa zasobów ma na celu uproszczenie czyszczenie zasobów.

Jeśli tworzysz zasoby do korzystania z klastra usługi AKS, takich jak konta magazynu lub zastrzeżone publiczne adresy IP, należy je umieścić w grupie zasobów automatycznie generowanych.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Czy mogę przekazać własną nazwę grupy zasobów infrastruktury usługi AKS

Tak. Domyślnie dostawca zasobów usługi AKS automatycznie tworzy grupę zasobów pomocniczych (takich jak *MC_myResourceGroup_myAKSCluster_eastus*) podczas wdrażania. Aby zapewnić zgodność z zasadami firmowymi, zapewniają swoją własną nazwę dla tego klastra zarządzanego (*MC_* ) grupy zasobów.

Aby określić własne nazwy grupy zasobów, należy zainstalować [podglądu usługi aks] [ aks-preview-cli] wiersza polecenia platformy Azure w wersji rozszerzenia *wersji 0.3.2* lub nowszej. Podczas tworzenia klastra usługi AKS przy użyciu [tworzenie az aks] [ az-aks-create] poleceń, użyj *— węzeł resource-group* parametru i określ nazwę grupy zasobów. Jeśli użytkownik [używania szablonu usługi Azure Resource Manager] [ aks-rm-template] wdrożyć klaster usługi AKS, można zdefiniować nazwę grupy zasobów za pomocą *nodeResourceGroup* właściwości.

* Grupa zasobów dodatkowych jest automatycznie tworzona przez dostawcę zasobów platformy Azure w ramach własnej subskrypcji.
* Można określić nazwę grupy zasobów niestandardowych, tylko wtedy, gdy tworzysz klaster.

Podczas pracy nad *MC_* grupy zasobów, należy pamiętać, że nie możesz:

* Określ istniejącą grupę zasobów dla *MC_* grupy.
* Określ inną subskrypcję dla *MC_* grupy zasobów.
* Zmiana *MC_* nazwy grupy zasobów po utworzeniu klastra.
* Określanie nazw zarządzanych zasobów w ramach *MC_* grupy zasobów.
* Modyfikowanie lub usuwanie tagów zasobów zarządzanych w ramach *MC_* grupy zasobów. (Zobacz dodatkowe informacje w następnej sekcji).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Można zmodyfikować tagów i innych właściwości zasobów usługi AKS w grupie zasobów MC_?

Jeśli zmodyfikujesz lub usuniesz, tagi utworzone przez platformę Azure i inne właściwości zasobu w *MC_* grupy zasobów mogą wystąpić nieoczekiwane wyniki, takie jak skalowanie i uaktualnianie błędy. AKS umożliwia tworzenie i modyfikowanie tagów niestandardowych. Możesz chcieć, aby utworzyć lub zmodyfikować znaczniki niestandardowe, na przykład przypisać centrum kosztu lub jednostki biznesowej. Modyfikując zasobów w ramach *MC_* w klastrze AKS możesz przerwać cel poziomu usług (SLO). Aby uzyskać więcej informacji, zobacz [AKS jest oferty z umową dotyczącą poziomu usług?](#does-aks-offer-a-service-level-agreement)

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

Obecnie nie można zmodyfikować listę kontrolerów przyjęcia w usłudze AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy usługa Azure Key Vault jest zintegrowana z usługą AKS?

AKS obecnie natywnie nie jest zintegrowana z usługą Azure Key Vault. Jednak [Azure klucza magazynu FlexVolume dla projektu Kubernetes] [ keyvault-flexvolume] umożliwia bezpośrednią integrację z zasobników Kubernetes do wpisów tajnych usługi Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy można uruchamiać kontenery systemu Windows Server w usłudze AKS

Tak, kontenery systemu Windows Server są dostępne w wersji zapoznawczej. Do uruchamiania kontenerów systemu Windows Server w usłudze AKS, możesz utworzyć pulę węzłów z systemem Windows Server jako system operacyjny gościa. Kontenery systemu Windows Server można użyć tylko 2019 serwera systemu Windows. Aby rozpocząć pracę, zobacz [Tworzenie klastra AKS z pulą węzłów systemu Windows Server][aks-windows-cli].

Okno obsługi serwera dla puli węzłów obejmuje pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie platformy Kubernetes. Aby uzyskać więcej informacji na temat tych ograniczeń, zobacz [kontenery systemu Windows Server w usłudze AKS ograniczenia][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS oferuje umowę dotyczącą poziomu usług?

Umowy poziomu usług (SLA) dostawca zgadza się zwrotu klienta kosztu usługi, jeśli poziom opublikowanej usługi nie zostanie spełniony. Ponieważ AKS jest bezpłatna, bez kosztów dostępnej do zwrotu, więc nie AKS umowa SLA nie formalny. Jednak AKS dąży do zapewnienia dostępności co najmniej 99,5% do serwera interfejsu API rozwiązania Kubernetes.

## <a name="why-cant-i-set-maxpods-below-30"></a>Dlaczego nie można ustawić maxPods poniżej 30

W usłudze AKS, można ustawić `maxPods` wartości podczas tworzenia klastra za korzystanie z szablonów interfejsu wiersza polecenia platformy Azure i usługi Azure Resource Manager. Jednak wymaga wtyczki Kubenet i wtyczki Azure CNI *minimalną wartość* (zweryfikowany w czasie tworzenia):

| Networking | Minimalne | Maksimum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

AKS to zarządzana usługa, możemy wdrożyć i zarządzać nią dodatki i zasobników w ramach klastra. W przeszłości, użytkownicy mogą definiować `maxPods` wartości mniejszej niż wartość, która zarządzanych zasobników wymagane do uruchomienia (na przykład, 30). Teraz AKS oblicza minimalną liczbę zasobników za pomocą następującej formuły: ((maxPods lub (maxPods * vm_count)) > minimum zasobników zarządzanych dodatku.

Użytkownicy nie mogą przesłaniać minimum `maxPods` sprawdzania poprawności.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Czy mogę zastosować rabaty rezerwacji platformy Azure do mojego węzłów agenta usługi AKS

Węzły agenta AKS są rozliczane jako standardowe maszyny wirtualne platformy Azure, więc jeśli została zakupiona [Azure rezerwacje] [ reservation-discounts] dla rozmiaru maszyny Wirtualnej, którego używasz w usłudze AKS, których rabaty są automatycznie stosowane.

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
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
