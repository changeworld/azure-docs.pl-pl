---
title: Zaktualizuj i uruchom ponownie węzły z kured w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zaktualizować węzłów i Automatyczny ponowny rozruch je za pomocą kured w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 11/06/2018
ms.author: iainfou
ms.openlocfilehash: 0bcc49df6540b73b8feb5bb1ec4312e680572797
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617874"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>Zastosowanie aktualizacji zabezpieczeń i aktualizacji jądra dla węzłów w usłudze Azure Kubernetes Service (AKS)

Aby chronić klastrów, aktualizacje zabezpieczeń są automatycznie stosowane do węzłów w usłudze AKS. Te aktualizacje obejmują poprawki zabezpieczeń systemu operacyjnego lub aktualizacji jądra. Niektóre z tych aktualizacji konieczne jest ponowne uruchomienie węzła, aby ukończyć proces. AKS nie automatycznie uruchom ponownie węzły w celu ukończenia procesu aktualizacji.

W tym artykule dowiesz się, jak używać typu open-source [kured (KUbernetes ponownie uruchomić demona)] [ kured] obejrzeć dla węzłów, które wymagają ponownego uruchomienia, następnie automatycznie obsługiwać ponowne uruchomienia zasobników i ponowne uruchomienie węzła proces.

> [!NOTE]
> `Kured` to projekt typu open source przez Weaveworks. Pomoc techniczna dla tego projektu w usłudze AKS jest świadczona na zasadzie największej staranności. Dodatkowa pomoc techniczna znajdują się w kanale usługi slack #weave społeczności instytucji rządowych

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.49 lub później zainstalowane i skonfigurowane. Uruchom `az --version` Aby znaleźć wersję. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Zrozumieć działanie aktualizacji węzłów AKS

W klastrze AKS węzły rozwiązania Kubernetes, Uruchom jako maszyn wirtualnych (VM). Te maszyny wirtualne z opartych na systemie Linux Użyj obrazu systemu Ubuntu w systemie operacyjnym skonfigurowany tak, aby automatycznie sprawdzać dostępność aktualizacji każdej nocy. Jeśli są dostępne, zabezpieczeń lub aktualizacji jądra, są one automatyczne pobranie i zainstalowanie.

![Węzeł AKS aktualizacji i ponownym uruchomieniu przetwarzanie za pomocą kured](media/node-updates-kured/node-reboot-process.png)

Niektóre aktualizacje zabezpieczeń, takich jak aktualizacji jądra wymaga ponownego uruchomienia węzła, aby zakończyć proces. Węzeł, który wymaga ponownego uruchomienia, tworzy plik o nazwie */var/run/reboot-required*. Ten proces ponownego rozruchu nie jest realizowane automatycznie.

Można użyć własnych przepływów pracy i procesów do obsługi ponownego uruchomienia węzła, lub użyj `kured` do organizowania procesu. Za pomocą `kured`, [DaemonSet] [ DaemonSet] wdrożeniu, które jest uruchamiane zasobnik w każdym węźle w klastrze. Te zasobniki w element DaemonSet poszukaj istnienie */var/run/reboot-required* pliku, a następnie inicjuje proces ponownego uruchomienia w węzłach.

### <a name="node-upgrades"></a>Uaktualnienia węzła

Brak dodatkowych procesów w usłudze AKS, która umożliwia *uaktualnienia* klastra. Uaktualnienie jest zwykle przejść do nowszej wersji platformy Kubernetes, nie tylko zastosować aktualizacje zabezpieczeń węzła. Uaktualnienie AKS wykonuje następujące czynności:

* Nowy węzeł jest wdrażana przy użyciu najnowszych aktualizacji zabezpieczeń i wersję rozwiązania Kubernetes stosowane.
* Węzła starego odizolowywane i opróżniane.
* Zasobniki są planowane w nowym węźle.
* Węzła starego jest usuwany.

Nie można nadal korzystać z tej samej wersji platformy Kubernetes, podczas uaktualniania zdarzeń. Należy określić nowszą wersję rozwiązania Kubernetes. Aby przeprowadzić uaktualnienie do najnowszej wersji platformy Kubernetes, możesz [Uaktualnianie klastra usługi AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Wdrażanie kured w klastrze AKS

Aby wdrożyć `kured` DaemonSet, Zastosuj poniższy przykład kodu YAML manifestu ze strony projektu usługi GitHub. Tego manifestu tworzy rolę i rolę klastra, powiązania i konto usługi, a następnie wdraża przy użyciu DaemonSet `kured` wersji 1.1.0 obsługuje klastry usługi AKS 1.9 lub nowsze.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

Można również skonfigurować dodatkowe parametry `kured`, takich jak integracja z Prometheus lub Slack. Aby uzyskać więcej informacji na temat dodatkowe parametry konfiguracyjne, zobacz [docs instalacji kured][kured-install].

## <a name="update-cluster-nodes"></a>Aktualizowanie węzłów klastra

Domyślnie węzłów AKS sprawdzał dostępność aktualizacji co wieczór. Jeśli nie chcesz czekać, należy ręcznie wykonać aktualizację, aby sprawdzić, czy `kured` działa poprawnie. Najpierw wykonaj kroki, aby [SSH do jednego z węzłów AKS][aks-ssh]. Gdy masz połączenie SSH z węzłem, sprawdź dostępność aktualizacji i zastosować je w następujący sposób:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Jeśli aktualizacje zostały zastosowane, które wymagają ponownego uruchomienia węzła, plik jest zapisywany do */var/run/reboot-required*. `Kured` sprawdza, czy węzły, które wymagają ponownego uruchomienia co 60 minut domyślnie.

## <a name="monitor-and-review-reboot-process"></a>Monitoruj i Przeglądaj ponownego uruchomienia procesu

Po jednej z replik w element DaemonSet wykrył, że wymagany jest ponowny rozruch węzła, blokady jest umieszczany w węźle za pomocą interfejsu API rozwiązania Kubernetes. Ta blokada uniemożliwia dodatkowe zasobniki zaplanowane w węźle. Blokada wskazuje również, że tylko jeden węzeł powinien zostać przeprowadzony ponowny rozruch w danym momencie. Z węzłem odizolowywane zasobników uruchomione są opróżniane z węzła, a węzeł został ponownie uruchomiony.

Możesz monitorować stan węzłów przy użyciu [kubectl get-węzły] [ kubectl-get-nodes] polecenia. Następujące przykładowe dane wyjściowe pokazuje węzła ze stanem *SchedulingDisabled* jako węzeł przygotowuje się do procesu rozruchu:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-79590246-2   Ready,SchedulingDisabled   agent     1h        v1.9.11
```

Po zakończeniu procesu aktualizacji można wyświetlić stan węzłów przy użyciu [kubectl get-węzły] [ kubectl-get-nodes] polecenia `--output wide` parametru. Te dodatkowe dane wyjściowe pozwala zobaczyć różnicę w *wersja jądra* z jego węzłów podrzędnych, jak pokazano w następujących przykładowych danych wyjściowych. *Aks-nodepool1-79590246-2* został zaktualizowany w poprzednim kroku oraz wersja jądra pokazuje *4.15.0-1025-azure*. Węzeł *aks-nodepool1-79590246-1* który nie został zaktualizowany przedstawiono wersja jądra *4.15.0-1023-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.11   10.240.0.6    <none>        Ubuntu 16.04.5 LTS   4.15.0-1023-azure   docker://1.13.1
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.11   10.240.0.4    <none>        Ubuntu 16.04.5 LTS   4.15.0-1025-azure   docker://1.13.1
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule szczegółowo sposób użycia `kured` ponownego uruchomienia węzłów automatycznie jako część procesu aktualizacji zabezpieczeń. Aby przeprowadzić uaktualnienie do najnowszej wersji platformy Kubernetes, możesz [Uaktualnianie klastra usługi AKS][aks-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
