---
title: Zarządzanie klastrem usługi Azure Kubernetes za pomocą pulpitu nawigacyjnego sieci Web
description: Dowiedz się, jak zarządzać klastrem usługi Kubernetes (AKS) za pomocą wbudowanego pulpitu użytkownika sieci Web firmy Kubernetes
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595352"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Dostęp do pulpitu nawigacyjnego sieci Web usługi Kubernetes w usłudze Azure Kubernetes (AKS)

Kubernetes zawiera pulpit nawigacyjny sieci Web, który może służyć do podstawowych operacji zarządzania. Ten pulpit nawigacyjny umożliwia wyświetlanie podstawowego stanu kondycji i metryki aplikacji, tworzenie i wdrażanie usług oraz edytowanie istniejących aplikacji. W tym artykule pokazano, jak uzyskać dostęp do pulpitu nawigacyjnego usługi Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie prowadzi użytkownika przez niektóre podstawowe operacje pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na pulpicie nawigacyjnym aplikacji [Kubernetes, zobacz Pulpit nawigacyjny interfejsu użytkownika sieci Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Kroki opisane w tym dokumencie zakładają, że utworzono `kubectl` klaster AKS i nawiązałeś połączenie z klastrem. Jeśli chcesz utworzyć klaster AKS, zobacz [szybki start usługi AKS][aks-quickstart].

Musisz również mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie  `az --version` , aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Uruchamianie pulpitu nawigacyjnego kubernetes

Aby uruchomić pulpit nawigacyjny kubernetes, użyj polecenia [az aks browse.][az-aks-browse] Poniższy przykład otwiera pulpit nawigacyjny dla klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemem deweloperów a interfejsem API kubernetes i otwiera przeglądarkę sieci Web na pulpicie nawigacyjnym aplikacji Kubernetes. Jeśli przeglądarka internetowa nie otwiera się na pulpicie nawigacyjnym narzędzia Kubernetes, zazwyczaj `http://127.0.0.1:8001`skopiuj i wklej adres URL odnotowany w interfejsie wiersza polecenia platformy Azure.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Jeśli klaster AKS używa funkcji RBAC, przed prawidłowym dostępem do pulpitu nawigacyjnego należy utworzyć *narzędzie ClusterRoleBinding.* Domyślnie pulpit nawigacyjny kubernetes jest wdrażany z minimalnym dostępem do odczytu i wyświetla błędy dostępu RBAC. Pulpit nawigacyjny usługi Kubernetes nie obsługuje obecnie poświadczeń dostarczonych przez użytkownika w celu określenia poziomu dostępu, a raczej używa ról przyznanych kontu usługi. Administrator klastra może udzielić dodatkowego dostępu do konta usługi *kubernetes-dashboard,* jednak może to być wektor eskalacji uprawnień. Można również zintegrować uwierzytelnianie usługi Azure Active Directory, aby zapewnić bardziej szczegółowy poziom dostępu.
> 
> Aby utworzyć powiązanie, należy użyć polecenia [kubectl create clusterrolebinding.][kubectl-create-clusterrolebinding] W poniższym przykładzie pokazano, jak utworzyć wiązanie próbki, jednak to wiązanie próbki nie stosuje żadnych dodatkowych składników uwierzytelniania i może prowadzić do niezabezpieczonego użycia. Pulpit nawigacyjny kubernetes jest otwarty dla każdego, kto ma dostęp do adresu URL. Nie należy udostępniać pulpitu nawigacyjnego kubernetes publicznie.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Aby uzyskać więcej informacji na temat korzystania z różnych metod uwierzytelniania, zobacz wiki pulpitu nawigacyjnego Kubernetes na [temat kontroli dostępu][dashboard-authentication].

![Strona przeglądu pulpitu nawigacyjnego sieci Web kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby zobaczyć, jak pulpit nawigacyjny kubernetes może zmniejszyć złożoność zadań zarządzania, utwórzmy aplikację. Aplikację można utworzyć na pulpicie nawigacyjnym narzędzia Kubernetes, udostępniając wprowadzanie tekstu, plik YAML lub za pomocą kreatora graficznego.

Aby utworzyć aplikację, wykonaj następujące kroki:

1. Wybierz przycisk **Utwórz** w prawym górnym oknie.
1. Aby użyć kreatora graficznego, wybierz pozycję **Utwórz aplikację**.
1. Podaj nazwę wdrożenia, na przykład *nginx*
1. Wprowadź nazwę obrazu kontenera, który ma być używany, na przykład *nginx:1.15.5*
1. Aby udostępnić port 80 dla ruchu sieciowego, należy utworzyć usługę Kubernetes. W obszarze **Usługa**wybierz pozycję **Zewnętrzna**, a następnie wprowadź **80** dla portu i portu docelowego.
1. Gdy będzie gotowy, wybierz **pozycję Wdrażanie,** aby utworzyć aplikację.

![Wdrażanie aplikacji na pulpicie nawigacyjnym aplikacji Kubernetes](./media/kubernetes-dashboard/create-app.png)

Trwa minuta lub dwie dla publicznego zewnętrznego adresu IP, które mają być przypisane do usługi Kubernetes. W rozmiarze po lewej stronie w obszarze **Odnajdowanie i równoważenie obciążenia** wybierz pozycję **Usługi**. Usługa aplikacji znajduje się na liście, w tym *zewnętrzne punkty końcowe,* jak pokazano w poniższym przykładzie:

![Wyświetlanie listy usług i punktów końcowych](./media/kubernetes-dashboard/view-services.png)

Wybierz adres punktu końcowego, aby otworzyć okno przeglądarki sieci Web na domyślnej stronie NGINX:

![Wyświetlanie domyślnej strony NGINX wdrożonej aplikacji](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Wyświetlanie informacji o zasobniku

Pulpit nawigacyjny usługi Kubernetes może dostarczyć podstawowych metryk monitorowania i informacji o rozwiązywaniu problemów, takich jak dzienniki.

Aby wyświetlić więcej informacji o zasobnikach aplikacji, wybierz **zasobniki** w menu po lewej stronie. Zostanie wyświetlona lista dostępnych zasobników. Wybierz *zasobnik nginx,* aby wyświetlić informacje, takie jak zużycie zasobów:

![Wyświetlanie informacji o zasobniku](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Edytowanie aplikacji

Oprócz tworzenia i wyświetlania aplikacji pulpit nawigacyjny usługi Kubernetes może służyć do edytowania i aktualizowania wdrożeń aplikacji. Aby zapewnić dodatkową nadmiarowość dla aplikacji, zwiększmy liczbę replik NGINX.

Aby edytować wdrożenie:

1. Wybierz **wdrożeń** w menu po lewej stronie, a następnie wybierz wdrożenie *nginx.*
1. Wybierz **pozycję Edytuj** na prawym górnym pasku nawigacyjnym.
1. Zlokalizuj `spec.replica` wartość wokół linii 20. Aby zwiększyć liczbę replik dla aplikacji, zmień tę wartość z *1* na *3*.
1. Wybierz **pozycję Aktualizuj,** gdy będzie gotowy.

![Edytuj wdrożenie, aby zaktualizować liczbę replik](./media/kubernetes-dashboard/edit-deployment.png)

Tworzenie nowych zasobników w zestawie replik zajmuje kilka chwil. W menu po lewej stronie wybierz polecenie **Zestawy replik**, a następnie wybierz zestaw replik *nginx.* Lista zasobników odzwierciedla teraz zaktualizowaną liczbę replik, jak pokazano w poniższym przykładzie danych wyjściowych:

![Wyświetlanie informacji o zestawie replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego usługi Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
