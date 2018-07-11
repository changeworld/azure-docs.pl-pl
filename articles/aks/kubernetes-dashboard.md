---
title: Zarządzanie klastrem Kubernetes na platformie Azure przy użyciu interfejsu użytkownika sieci web
description: Dowiedz się, jak używać wbudowany pulpit nawigacyjny interfejsu użytkownika sieci web rozwiązania Kubernetes za pomocą usługi Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e197a251df3f34e5416bafacfd54a3fc7f51d503
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928220"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Dostęp do pulpitu nawigacyjnego rozwiązania Kubernetes za pomocą usługi Azure Kubernetes Service (AKS)

Kubernetes obejmuje pulpit nawigacyjny sieci web, który może służyć do podstawowych operacji zarządzania. W tym artykule pokazano, jak dostęp do pulpitu nawigacyjnego rozwiązania Kubernetes za pomocą wiersza polecenia platformy Azure, a następnie przeprowadzi Cię przez niektóre operacje podstawowe pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web rozwiązania Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach szczegółowo opisanych w tym dokumencie przyjęto założenie, został utworzony klaster usługi AKS i ustalonymi `kubectl` połączenia z klastrem. Jeśli musisz utworzyć klaster usługi AKS, zobacz [szybkiego startu usługi AKS][aks-quickstart].

Musisz również mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Rozpocznij pulpit nawigacyjny platformy Kubernetes

Aby uruchomić Pulpit nawigacyjny platformy Kubernetes, należy użyć [Przeglądaj az aks] [ az-aks-browse] polecenia. W poniższym przykładzie otwierany pulpit nawigacyjny klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemu dla deweloperów i interfejsu API rozwiązania Kubernetes i otworzy w przeglądarce sieci web pulpitu nawigacyjnego platformy Kubernetes.

### <a name="for-rbac-enabled-clusters"></a>W przypadku klastrów z obsługą kontroli RBAC

Jeśli klaster AKS korzysta z funkcji RBAC, *ClusterRoleBinding* musi zostać utworzona zanim dostęp do pulpitu nawigacyjnego. Bez powiązania roli, wiersza polecenia platformy Azure zwraca błąd podobny do poniższego przykładu:

```
error: unable to forward port because pod is not running. Current status=Pending
```

Aby utworzyć powiązanie, Utwórz plik o nazwie *admin.yaml pulpit nawigacyjny* i wklej poniższy przykład. To przykładowe powiązanie nie ma zastosowania składników dodatkowego uwierzytelniania. Można użyć mechanizmów, takich jak tokenów elementu nośnego lub nazwy użytkownika/hasła do kontroli, kto może uzyskiwać dostęp do pulpitu nawigacyjnego i ich uprawnień. Aby uzyskać więcej informacji na temat metod uwierzytelniania, zobacz wiki pulpit nawigacyjny platformy Kubernetes w [kontrole dostępu][dashboard-authentication].

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

Zastosuj powiązanie o [zastosować kubectl] [ kubectl-apply] i określ swoje *admin.yaml pulpit nawigacyjny*, jak pokazano w poniższym przykładzie:

```
$ kubectl apply -f dashboard-admin.yaml

clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
```

Pulpit nawigacyjny rozwiązania Kubernetes można teraz uzyskać dostęp w klastrze z włączoną funkcją RBAC. Aby uruchomić Pulpit nawigacyjny platformy Kubernetes, należy użyć [Przeglądaj az aks] [ az-aks-browse] polecenia zgodnie z opisem w poprzednim kroku.


## <a name="run-an-application"></a>Uruchamianie aplikacji

Na pulpicie nawigacyjnym rozwiązania Kubernetes kliknij **Utwórz** przycisk w prawym górnym oknie po prawej stronie. Nadaj nazwę wdrożenia `nginx` i wprowadź `nginx:latest` nazwy obrazów kontenerów. W obszarze **usługi**, wybierz opcję **zewnętrznych** i wprowadź `80` port i port docelowy.

Gdy wszystko będzie gotowe, kliknij przycisk **Wdróż** utworzyć wdrożenie.

![Okno dialogowe Tworzenie usługi Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Wyświetlanie aplikacji

Stan aplikacji będą widoczne na pulpicie nawigacyjnym platformy Kubernetes. Gdy aplikacja jest uruchomiona, każdy składnik ma zielone pole wyboru obok niego.

![Zasobników Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Aby wyświetlić więcej informacji na temat zasobniki aplikacji, kliknij **zasobników** w menu po lewej stronie, a następnie wybierz **NGINX** zasobników. Tutaj można zobaczyć informacje specyficzne dla zasobników, takie jak zużycia zasobów.

![Zasoby platformy Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Aby znaleźć adres IP aplikacji, kliknij **usług** w menu po lewej stronie, a następnie wybierz **NGINX** usługi.

![Widok serwera nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Edytowanie aplikacji

Oprócz tworzenia i wyświetlania aplikacji, pulpit nawigacyjny platformy Kubernetes, może służyć do edycji i aktualizacji wdrożeń aplikacji.

Aby edytować wdrożenia, kliknij pozycję **wdrożeń** w menu po lewej stronie, a następnie wybierz **NGINX** wdrożenia. Na koniec wybierz pozycję **Edytuj** na pasku nawigacyjnym po prawej stronie górnej.

![Edytuj platformy Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Znajdź `spec.replica` wartość, która powinna wynosić 1, zmień tę wartość na 3. W ten sposób liczbę replik wdrożenia serwera NGINX zwiększa się z zakresu od 1 do 3.

Wybierz **aktualizacji** gdy będzie gotowe.

![Edytuj platformy Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes Zobacz dokumentacją rozwiązania Kubernetes.

> [!div class="nextstepaction"]
> [Pulpit nawigacyjny interfejsu użytkownika sieci Web rozwiązania Kubernetes][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
