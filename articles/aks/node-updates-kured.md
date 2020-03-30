---
title: Aktualizowanie i ponowne uruchamianie węzłów systemu Linux za pomocą usługi Kubernetes platformy Azure (AKS)
description: Dowiedz się, jak zaktualizować węzły systemu Linux i automatycznie ponownie uruchomić je za pomocą usługi Kured w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 74b12c1bc6e2a88582cc357c8091b5590e6bf3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191286"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Stosowanie aktualizacji zabezpieczeń i jąder do węzłów systemu Linux w usłudze Azure Kubernetes Service (AKS)

Aby chronić klastry, aktualizacje zabezpieczeń są automatycznie stosowane do węzłów systemu Linux w uktory AKS. Te aktualizacje obejmują poprawki zabezpieczeń systemu operacyjnego lub aktualizacje jądra. Niektóre z tych aktualizacji wymagają ponownego uruchomienia węzła, aby zakończyć proces. AKS nie automatycznie ponownie uruchamia te węzły systemu Linux, aby zakończyć proces aktualizacji.

Proces przechowywania aktualnego daty węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS) jest nieco inny. Węzły systemu Windows Server nie otrzymują codziennych aktualizacji. Zamiast tego należy wykonać uaktualnienie usługi AKS, które wdraża nowe węzły z najnowszym obrazem i poprawkami serwera okna podstawowego. W przypadku klastrów AKS korzystających z węzłów systemu Windows Server zobacz [Uaktualnianie puli węzłów w programie AKS][nodepool-upgrade].

W tym artykule pokazano, jak używać kured open source [(KUbernetes REboot Daemon)][kured] do oglądania węzłów Linuksa, które wymagają ponownego uruchomienia, a następnie automatycznie obsługiwać ponowne harmonogramy uruchamiania zasobników i proces ponownego uruchamiania węzła.

> [!NOTE]
> `Kured`jest projektem open-source realizowanym przez Weaveworks. Wsparcie dla tego projektu w AKS jest udzielane na zasadzie najlepszego wysiłku. Dodatkowe wsparcie można znaleźć w #weave-społecznościowym kanale Slack.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Opis środowiska aktualizacji węzła AKS

W klastrze AKS węzły kubernetes działają jako maszyny wirtualne platformy Azure (maszyny wirtualne). Te maszyny wirtualne oparte na systemie Linux używają obrazu Ubuntu, z systemem operacyjnym skonfigurowanym do automatycznego sprawdzania dostępności aktualizacji każdej nocy. Jeśli dostępne są aktualizacje zabezpieczeń lub jądra, są one automatycznie pobierane i instalowane.

![Proces aktualizacji i ponownego uruchamiania węzła AKS z](media/node-updates-kured/node-reboot-process.png)

Niektóre aktualizacje zabezpieczeń, takie jak aktualizacje jądra, wymagają ponownego uruchomienia węzła w celu sfinalizowania procesu. Węzeł systemu Linux, który wymaga ponownego uruchomienia, tworzy plik o nazwie */var/run/reboot-required*. Ten proces ponownego uruchamiania nie odbywa się automatycznie.

Można użyć własnych przepływów pracy i procesów do `kured` obsługi ponownego uruchamiania węzłów lub użyć do zorganizowania procesu. Z `kured`, [DaemonSet][DaemonSet] jest wdrażany, który uruchamia zasobnika na każdym węźle systemu Linux w klastrze. Te zasobników w DaemonSet oglądać istnienie */var/run/reboot-required* pliku, a następnie zainicjować proces ponownego uruchomienia węzłów.

### <a name="node-upgrades"></a>Uaktualnienia węzłów

Istnieje dodatkowy proces w uzywniu usługi AKS, który umożliwia *uaktualnienie* klastra. Uaktualnienie jest zazwyczaj, aby przejść do nowszej wersji kubernetes, a nie tylko zastosować aktualizacje zabezpieczeń węzła. Uaktualnienie AKS wykonuje następujące akcje:

* Nowy węzeł jest wdrażany z najnowszymi aktualizacjami zabezpieczeń i zastosowaną wersją kubernetes.
* Stary węzeł jest kordonowany i opróżniany.
* Zasobniki są zaplanowane w nowym węźle.
* Stary węzeł zostanie usunięty.

Nie można pozostać w tej samej wersji kubernetes podczas zdarzenia uaktualniania. Należy określić nowszą wersję kubernetes. Aby uaktualnić do najnowszej wersji programu Kubernetes, można [uaktualnić klaster AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Wdrażanie w klastrze AKS

Aby wdrożyć `kured` Zestaw Demonów, zainstaluj następujący oficjalny wykres Steru Kureda. Spowoduje to utworzenie roli i roli klastra, powiązań i konta usługi, `kured`a następnie wdraża zestaw demonów przy użyciu programu .

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Można również skonfigurować dodatkowe `kured`parametry dla , takie jak integracja z Prometheus lub Slack. Aby uzyskać więcej informacji na temat dodatkowych parametrów konfiguracji, zobacz [wykres steru kured][kured-install].

## <a name="update-cluster-nodes"></a>Aktualizowanie węzłów klastra

Domyślnie węzły systemu Linux w AKS sprawdzają dostępność aktualizacji każdego wieczoru. Jeśli nie chcesz czekać, możesz ręcznie przeprowadzić aktualizację, `kured` aby sprawdzić, czy działa poprawnie. Najpierw wykonaj kroki, aby [SSH do jednego z węzłów AKS][aks-ssh]. Po połączeniu SSH z węzłem Systemu Linux sprawdź dostępność aktualizacji i zastosuj je w następujący sposób:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Jeśli zastosowano aktualizacje wymagające ponownego uruchomienia węzła, plik jest zapisywany na */var/run/reboot-required*. `Kured`sprawdza, czy węzły, które wymagają ponownego uruchomienia co 60 minut domyślnie.

## <a name="monitor-and-review-reboot-process"></a>Monitorowanie i przeglądanie procesu ponownego uruchamiania

Gdy jedna z replik w DaemonSet wykryje, że wymagany jest ponowny rozruch węzła, blokada jest umieszczana w węźle za pośrednictwem interfejsu API Kubernetes. Ta blokada zapobiega planowaniu dodatkowych zasobników w węźle. Blokada wskazuje również, że tylko jeden węzeł powinien zostać ponownie uruchomiony w czasie. Po wyłączeniu węzła uruchomione zasobniki są opróżniane z węzła, a węzeł jest ponownie uruchamiany.

Stan węzłów można monitorować za pomocą polecenia [kubectl get nodes.][kubectl-get-nodes] Poniższe przykładowe dane wyjściowe pokazuje węzeł o stanie *SchedulingDisabled* jak węzeł przygotowuje się do procesu ponownego uruchamiania:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Po zakończeniu procesu aktualizacji można wyświetlić stan węzłów za pomocą [polecenia kubectl get nodes][kubectl-get-nodes] z parametrem. `--output wide` To dodatkowe dane wyjściowe pozwala zobaczyć różnicę w *kernel-wersja* węzłów źródłowych, jak pokazano w poniższym przykładzie danych wyjściowych. *Aks-nodepool1-28993262-0* został zaktualizowany w poprzednim kroku i pokazuje jądro w wersji *4.15.0-1039-azure*. Węzeł *aks-nodepool1-28993262-1,* który nie został zaktualizowany, pokazuje jądro w wersji *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Następne kroki

W tym artykule `kured` opisano, jak używać do automatycznego ponownego uruchamiania węzłów systemu Linux w ramach procesu aktualizacji zabezpieczeń. Aby uaktualnić do najnowszej wersji programu Kubernetes, można [uaktualnić klaster AKS][aks-upgrade].

W przypadku klastrów AKS korzystających z węzłów systemu Windows Server zobacz [Uaktualnianie puli węzłów w programie AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
