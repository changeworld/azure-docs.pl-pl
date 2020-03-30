---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)
description: Znajdź odpowiedzi na niektóre z typowych pytań dotyczących usługi Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497757"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

Ten artykuł dotyczy częstych pytań dotyczących usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Które regiony platformy Azure obecnie zapewniają usługę AKS?

Aby uzyskać pełną listę dostępnych regionów, zobacz [Regiony i dostępność AKS][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Czy AKS obsługuje automatyczne skalowanie węzłów?

Tak, możliwość automatycznego skalowania węzłów agenta w poziomie w uzywniu AKS jest obecnie dostępna w wersji zapoznawczej. Zobacz [Automatyczne skalowanie klastra w celu spełnienia wymagań aplikacji w uzyklu w uzykluckim systemie AKS][aks-cluster-autoscaler] w celu uzyskania instrukcji. Skalowanie automatyczne usługi AKS jest oparte na [skalowaniu automatycznym Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć usługi AKS w istniejącej sieci wirtualnej?

Tak, klaster AKS można wdrożyć w istniejącej sieci wirtualnej przy użyciu [zaawansowanej funkcji sieciowej][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Czy mogę ograniczyć dostęp do serwera interfejsu API kubernetes?

Tak, można ograniczyć dostęp do serwera api Kubernetes przy użyciu [autoryzowanych zakresów ip serwera INTERFEJSU API][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Czy serwer interfejsu API Kubernetes może być dostępny tylko w mojej sieci wirtualnej?

Nie w tej chwili, ale to jest planowane. Można śledzić postęp w [repozytorium AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Czy mogę mieć różne rozmiary maszyn wirtualnych w jednym klastrze?

Tak, można użyć różnych rozmiarów maszyn wirtualnych w klastrze AKS, tworząc [wiele pul węzłów][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Czy aktualizacje zabezpieczeń są stosowane do węzłów agenta AKS?

Platforma Azure automatycznie stosuje poprawki zabezpieczeń do węzłów systemu Linux w klastrze zgodnie z harmonogramem nocnym. Jednak jesteś odpowiedzialny za zapewnienie, że te węzły systemu Linux są ponownie uruchamiane zgodnie z wymaganiami. Dostępnych jest kilka opcji ponownego uruchamiania węzłów:

- Ręcznie za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.
- Uaktualniając klaster AKS. Klaster automatycznie uaktualnia [węzły kordonu i opróżniania,][cordon-drain] a następnie wprowadza nowy węzeł do trybu online z najnowszym obrazem Ubuntu i nową wersją poprawki lub niewielką wersją kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra AKS][aks-upgrade].
- Za pomocą [Kured](https://github.com/weaveworks/kured), demona restartu open source dla Kubernetes. Kured działa jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł pod kątem obecności pliku, który wskazuje, że wymagane jest ponowne uruchomienie. W całym klastrze ponowne uruchomienie systemu operacyjnego są zarządzane przez ten sam [proces kordonu i drenażu][cordon-drain] jako uaktualnienie klastra.

Aby uzyskać więcej informacji na temat używania kured, zobacz [Stosowanie zabezpieczeń i aktualizacji jądra do węzłów w AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS) usługa Windows Update nie uruchamia się automatycznie i nie stosuje najnowszych aktualizacji. Zgodnie z regularnym harmonogramem wokół cyklu wersji usługi Windows Update i własnego procesu sprawdzania poprawności należy przeprowadzić uaktualnienie w klastrze i pulach węzłów systemu Windows Server w klastrze AKS. Ten proces uaktualniania tworzy węzły, które uruchamiają najnowszy obraz i poprawki systemu Windows Server, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [Uaktualnianie puli węzłów w programie AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego dwie grupy zasobów są tworzone za pomocą aks?

Usługa AKS opiera się na wielu zasobach infrastruktury platformy Azure, w tym zestawach skalowania maszyn wirtualnych, sieciach wirtualnych i dyskach zarządzanych. Dzięki temu można wykorzystać wiele podstawowych możliwości platformy Azure w zarządzanym środowisku Kubernetes dostarczonych przez usługę AKS. Na przykład większość typów maszyn wirtualnych platformy Azure może być używana bezpośrednio z usługą AKS, a usługa Azure Reservations może służyć do automatycznego otrzymywania rabatów na te zasoby.

Aby włączyć tę architekturę, każde wdrożenie usługi AKS obejmuje dwie grupy zasobów:

1. Utwórz pierwszą grupę zasobów. Ta grupa zawiera tylko zasób usługi Kubernetes. Dostawca zasobów usługi AKS automatycznie tworzy drugą grupę zasobów podczas wdrażania. Przykładem drugiej grupy zasobów jest *MC_myResourceGroup_myAKSCluster_eastus*. Aby uzyskać informacje na temat określania nazwy tej drugiej grupy zasobów, zobacz następną sekcję.
1. Druga grupa zasobów, znana jako *grupa zasobów węzła,* zawiera wszystkie zasoby infrastruktury skojarzone z klastrem. Zasoby te obejmują maszyny wirtualne węzła Kubernetes, sieci wirtualne i magazyn. Domyślnie grupa zasobów węzła ma nazwę taką jak *MC_myResourceGroup_myAKSCluster_eastus*. Usługa AKS automatycznie usuwa zasób węzła za każdym razem, gdy klaster jest usuwany, więc powinien być używany tylko dla zasobów współużytkuje cykl życia klastra.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Czy mogę podać własną nazwę grupy zasobów węzła AKS?

Tak. Domyślnie program AKS będzie nazywał grupę zasobów węzłów *MC_resourcegroupname_clustername_location*, ale można również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji *0.3.2* lub nowszej w [wersji aks-preview][aks-preview-cli] Azure. Podczas tworzenia klastra AKS za pomocą polecenia [az aks create][az-aks-create] należy użyć parametru *--node-resource-group* i określić nazwę grupy zasobów. Jeśli [używasz szablonu usługi Azure Resource Manager][aks-rm-template] do wdrażania klastra AKS, można zdefiniować nazwę grupy zasobów przy użyciu właściwości *nodeResourceGroup.*

* Pomocnicza grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure w ramach własnej subskrypcji.
* Niestandardową nazwę grupy zasobów można określić tylko podczas tworzenia klastra.

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

* Określ istniejącą grupę zasobów dla grupy zasobów węzła.
* Określ inną subskrypcję dla grupy zasobów węzła.
* Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
* Określ nazwy zasobów zarządzanych w grupie zasobów węzła.
* Modyfikowanie lub usuwanie znaczników zasobów zarządzanych w grupie zasobów węzła. (Zobacz dodatkowe informacje w następnej sekcji).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Czy można modyfikować znaczniki i inne właściwości zasobów AKS w grupie zasobów węzła?

Jeśli zmodyfikujesz lub usuniesz tagi utworzone przez platformę Azure i inne właściwości zasobów w grupie zasobów węzła, możesz uzyskać nieoczekiwane wyniki, takie jak błędy skalowania i uaktualniania. Usługa AKS umożliwia tworzenie i modyfikowanie tagów niestandardowych. Można utworzyć lub zmodyfikować tagi niestandardowe, na przykład przypisać jednostkę biznesową lub centrum kosztów. Modyfikując zasoby w ramach grupy zasobów węzła w klastrze AKS, można podzielić cel na poziomie usługi (SLO). Aby uzyskać więcej informacji, zobacz [Czy AKS oferuje umowę dotyczącą poziomu usług?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jakie kontrolery wstępu Kubernetes obsługuje AKS? Czy kontrolery przyjęć mogą zostać dodane lub usunięte?

AKS obsługuje następujące [kontrolery przyjęć:][admission-controllers]

- *Przestrzeń nazwŻytniakl*
- *LimitRanger (rozerż.*
- *Serviceaccount*
- *DefaultStorageClass*
- *DomyślneToleracjaSekundy*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota (Quota zasobów)*

Obecnie nie można zmodyfikować listy kontrolerów dostępu w aks.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy usługa Azure Key Vault jest zintegrowana z usługą AKS?

Usługa AKS nie jest obecnie natywnie zintegrowana z usługą Azure Key Vault. Jednak [projekt Azure Key Vault FlexVolume for Kubernetes][keyvault-flexvolume] umożliwia bezpośrednią integrację z zasobnikami kubernetes do wpisów tajnych usługi Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy mogę uruchamiać kontenery systemu Windows Server w usłudze AKS?

Tak, kontenery systemu Windows Server są dostępne w wersji zapoznawczej. Aby uruchomić kontenery systemu Windows Server w systemie AKS, należy utworzyć pulę węzłów, która działa w systemie Windows Server jako system operacyjny gościa. Kontenery systemu Windows Server mogą używać tylko systemu Windows Server 2019. Aby rozpocząć, zobacz [Tworzenie klastra AKS z pulą węzłów systemu Windows Server][aks-windows-cli].

Obsługa puli węzłów w systemie Windows Server zawiera pewne ograniczenia, które są częścią nadrzędnego systemu Windows Server w projekcie kubernetes. Aby uzyskać więcej informacji na temat tych ograniczeń, zobacz [Kontenery systemu Windows Server w ograniczeniach usługi AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Czy AKS oferuje umowę dotyczącą poziomu usług?

W umowie dotyczącej poziomu usług (SLA) dostawca wyraża zgodę na zwrot klientowi kosztów usługi, jeśli opublikowany poziom usług nie jest spełniony. Ponieważ AKS jest bezpłatny, zwrot kosztów nie jest dostępny, więc AKS nie ma formalnej umowy SLA. Jednak usługa AKS stara się zachować dostępność co najmniej 99,5 procent dla serwera interfejsu API usługi Kubernetes.

Ważne jest, aby rozpoznać rozróżnienie między dostępnością usługi AKS, która odnosi się do czasu dostępności płaszczyzny sterowania usługi Kubernetes i dostępności określonego obciążenia, które jest uruchomione na maszynach wirtualnych platformy Azure. Chociaż płaszczyzna sterowania może być niedostępna, jeśli płaszczyzna sterowania nie jest gotowa, obciążenia klastra uruchomione na maszynach wirtualnych platformy Azure mogą nadal działać. Biorąc pod uwagę, że maszyny wirtualne platformy Azure są płatnymi zasobami, są one wspierane przez finansową umowy SLA. Przeczytaj [tutaj,](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) aby uzyskać więcej informacji na temat umowy SLA platformy Azure i jak zwiększyć tę dostępność za pomocą funkcji, takich jak [strefy dostępności.][availability-zones]

## <a name="why-cant-i-set-maxpods-below-30"></a>Dlaczego nie mogę ustawić maxPods poniżej 30?

W usłudze AKS `maxPods` można ustawić wartość podczas tworzenia klastra przy użyciu szablonów interfejsu wiersza polecenia platformy Azure i usługi Azure Resource Manager. Jednak zarówno Kubenet, jak i azure CNI wymagają *minimalnej wartości* (zweryfikowanej w czasie tworzenia):

| Obsługa sieci | Minimalne | Maksimum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Okręg wyborczy Kubenet | 30 | 110 |

Ponieważ usługa AKS jest usługą zarządzaną, wdrażamy dodatki i zasobników i zarządzamy nimi jako część klastra. W przeszłości użytkownicy mogli `maxPods` zdefiniować wartość niższą niż wartość, którą zarządzane zasobniki wymagane do uruchomienia (na przykład 30). AKS oblicza teraz minimalną liczbę zasobników przy użyciu tej formuły: ((maxPods lub (maxPods * vm_count)) > zarządzanych zasobników dodatków minimum.

Użytkownicy nie mogą zastąpić `maxPods` minimalnej weryfikacji.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Czy mogę zastosować rabaty rezerwacji platformy Azure do węzłów agenta AKS?

Węzły agenta usługi AKS są rozliczane jako standardowe maszyny wirtualne platformy Azure, więc jeśli zakupiono [rezerwacje platformy Azure][reservation-discounts] dla rozmiaru maszyny Wirtualnej, którego używasz w usłudze AKS, rabaty te są automatycznie stosowane.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Czy mogę przenieść/przeprowadzić migrację klastra między dzierżawami platformy Azure?

Polecenie `az aks update-credentials` może służyć do przenoszenia klastra AKS między dzierżawami platformy Azure. Postępuj zgodnie z instrukcjami w [wybierz, aby zaktualizować lub utworzyć jednostkę usługi,](https://docs.microsoft.com/azure/aks/update-credentials) a następnie [zaktualizować klaster AKS za pomocą nowych poświadczeń](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Czy mogę przenieść/przeprowadzić migrację klastra między subskrypcjami?

Przenoszenie klastrów między subskrypcjami jest obecnie nieobsługiwał.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Czy mogę przenieść klastry AKS z bieżącej subskrypcji platformy Azure do innej? 

Przenoszenie klastra AKS i skojarzonych zasobów między subskrypcjami platformy Azure nie jest obsługiwane.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Dlaczego usuwanie klastra trwa tak długo? 

Większość klastrów są usuwane na żądanie użytkownika; w niektórych przypadkach, zwłaszcza gdy klienci przynoszą własną grupę zasobów lub wykonywanie zadań cross-RG może zająć dodatkowy czas lub zakończyć się niepowodzeniem. Jeśli masz problem z usuwaniem, sprawdź, czy nie masz blokad na RG, że wszelkie zasoby poza RG są odłączone od RG itp.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Jeśli mam zasobników / wdrożeń w stanie "NodeLost" lub "Nieznany" mogę jeszcze uaktualnić mój klaster?

Możesz, ale AKS tego nie zaleca. Uaktualnienia powinny być wykonywane, gdy stan klastra jest znany i w dobrej kondycji.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Jeśli mam klaster z co najmniej jednym węzłem w stanie złej kondycji lub zamykany, czy mogę przeprowadzić uaktualnienie?

Nie, usuń/usuń wszystkie węzły w stanie awarii lub w inny sposób usunięte z klastra przed uaktualnieniem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Uruchomiono usunięcie klastra, ale widzę błąd`[Errno 11001] getaddrinfo failed` 

Najczęściej jest to spowodowane przez użytkowników, którzy nadal są używane i są skojarzone z klastrem.  Usuń je i spróbuj usunąć ponownie.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Uruchomiłem uaktualnienie, ale teraz moje strąki są w pętli awaryjnych, a sondy gotowości nie?

Upewnij się, że usługodawca nie wygasł.  Zobacz: [Podmiot usługi AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) i [poświadczenia aktualizacji usługi AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mój klaster działał, ale nagle nie można aprowizować LoadBalancers, mount PVC, itp.? 

Upewnij się, że usługodawca nie wygasł.  Zobacz: [Podmiot usługi AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) i [poświadczenia aktualizacji usługi AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Czy można użyć zestawu interfejsów API zestawu skalowania maszyny wirtualnej do ręcznego skalowania?

Nie, operacje skalowania przy użyciu zestawów API zestawu skalowania maszyny wirtualnej nie są obsługiwane. Użyj interfejsów API AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Czy można używać zestawów skalowania maszyny wirtualnej do ręcznego skalowania do 0 węzłów?

Nie, operacje skalowania przy użyciu zestawów API zestawu skalowania maszyny wirtualnej nie są obsługiwane.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Czy mogę zatrzymać lub oddzielić wszystkie moje maszyny wirtualne?

Chociaż AKS ma mechanizmy odporności, aby wytrzymać taki config i odzyskać od niego, nie jest to zalecana konfiguracja.

## <a name="can-i-use-custom-vm-extensions"></a>Czy mogę używać niestandardowych rozszerzeń maszyn wirtualnych?

Żaden usługa AKS nie jest usługą zarządzaną, a manipulowanie zasobami usługi IaaS nie jest obsługiwane. Aby zainstalować składniki niestandardowe itp. należy wykorzystać interfejsy API i mechanizmy Kubernetes. Na przykład wykorzystanie DaemonSets zainstalować wymagane składniki.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
