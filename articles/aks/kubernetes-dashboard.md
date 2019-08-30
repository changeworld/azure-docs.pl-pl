---
title: Zarządzanie klastrem usługi Azure Kubernetes Service przy użyciu pulpitu nawigacyjnego sieci Web
description: Dowiedz się, jak za pomocą wbudowanego pulpitu nawigacyjnego interfejsu użytkownika sieci Web Kubernetes zarządzać klastrem usługi Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126877"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Dostęp do pulpitu nawigacyjnego sieci Web Kubernetes w usłudze Azure Kubernetes Service (AKS)

Kubernetes zawiera internetowy pulpit nawigacyjny, który może być używany do podstawowych operacji zarządzania. Ten pulpit nawigacyjny pozwala wyświetlić podstawowy stan kondycji i metryki dla aplikacji, utworzyć i wdrożyć usługi oraz edytować istniejące aplikacje. W tym artykule pokazano, jak uzyskać dostęp do pulpitu nawigacyjnego Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie przeprowadzi Cię przez niektóre podstawowe operacje pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach przedstawionych w tym dokumencie przyjęto założenie, że utworzono klaster AKS i `kubectl` nawiązano połączenie z klastrem. Jeśli musisz utworzyć klaster AKS, zapoznaj się z [przewodnikiem Szybki Start][aks-quickstart]dotyczącym AKS.

Musisz również mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie  `az --version` , aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Uruchamianie pulpitu nawigacyjnego Kubernetes

Aby uruchomić Pulpit nawigacyjny Kubernetes, użyj polecenia [AZ AKS Browse][az-aks-browse] . W poniższym przykładzie zostanie otwarty pulpit nawigacyjny dla klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemem deweloperskim i interfejsem API Kubernetes, a następnie otwiera przeglądarkę internetową na pulpicie nawigacyjnym Kubernetes. Jeśli przeglądarka sieci Web nie jest otwarta na pulpicie nawigacyjnym Kubernetes, skopiuj i wklej adres URL zanotowany w interfejsie `http://127.0.0.1:8001`wiersza polecenia platformy Azure, zazwyczaj.

![Strona logowania na pulpicie nawigacyjnym sieci Web Kubernetes](./media/kubernetes-dashboard/dashboard-login.png)

Dostępne są następujące opcje logowania do pulpitu nawigacyjnego klastra:

* [Plik kubeconfig][kubeconfig-file]. Plik kubeconfig można wygenerować za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials].
* Token, taki jak [token konta usługi][aks-service-accounts] lub token użytkownika. W klastrach z obsługą usługi [AAD][aad-cluster]token ten będzie tokenem usługi AAD. Możesz użyć `kubectl config view` , aby wyświetlić listę tokenów w pliku kubeconfig. Aby uzyskać więcej informacji na temat tworzenia tokenu usługi AAD do użycia z klastrem AKS [, zobacz integrowanie Azure Active Directory z usługą Azure Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure][aad-cluster].
* Domyślne konto usługi pulpitu nawigacyjnego, które jest używane po kliknięciu przycisku *Pomiń*.

> [!WARNING]
> Nigdy nie ujawniaj publicznie pulpitu nawigacyjnego Kubernetes niezależnie od używanej metody uwierzytelniania.
> 
> W przypadku konfigurowania uwierzytelniania dla pulpitu nawigacyjnego Kubernetes zaleca się użycie tokenu przez domyślne konto usługi pulpitu nawigacyjnego. Token umożliwia każdemu użytkownikowi korzystanie z własnych uprawnień. Użycie domyślnego konta usługi pulpitu nawigacyjnego może pozwolić użytkownikowi na pominięcie własnych uprawnień i użycie zamiast tego konta usługi.
> 
> W przypadku wybrania opcji używania domyślnego konta usługi pulpitu nawigacyjnego, gdy klaster AKS używa kontroli RBAC, należy utworzyć *ClusterRoleBinding* , aby można było prawidłowo uzyskać dostęp do pulpitu nawigacyjnego. Domyślnie pulpit nawigacyjny Kubernetes jest wdrażany z minimalnym dostępem do odczytu i wyświetla błędy dostępu RBAC. Administrator klastra może zdecydować się na przyznanie dodatkowego dostępu do konta usługi *Kubernetes-pulpitu nawigacyjnego* , jednak może to być wektor dla eskalacji uprawnień. Możesz także zintegrować Azure Active Directory uwierzytelnianie, aby zapewnić bardziej szczegółowy poziom dostępu.
>
> Aby utworzyć powiązanie, użyj polecenia [polecenia kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] , jak pokazano w poniższym przykładzie. **To przykładowe powiązanie nie dotyczy żadnych dodatkowych składników uwierzytelniania i może prowadzić do niebezpiecznego użycia.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Teraz możesz uzyskać dostęp do pulpitu nawigacyjnego Kubernetes w klastrze z obsługą kontroli RBAC. Aby uruchomić Pulpit nawigacyjny Kubernetes, użyj polecenia [AZ AKS Browse][az-aks-browse] , zgodnie z opisem w poprzednim kroku.
>
> Jeśli klaster nie używa RBAC, nie zaleca się tworzenia *ClusterRoleBinding*.
> 
> Aby uzyskać więcej informacji na temat korzystania z różnych metod uwierzytelniania, zobacz wiki Kubernetes pulpitu nawigacyjnego na stronie [kontroli dostępu][dashboard-authentication].

Po wybraniu metody do zalogowania zostanie wyświetlony pulpit nawigacyjny Kubernetes. W przypadku wybrania opcji Użyj *tokenu* lub *Pomiń*pulpit nawigacyjny Kubernetes będzie używać uprawnień aktualnie zalogowanego użytkownika w celu uzyskania dostępu do klastra.

![Strona przegląd pulpitu nawigacyjnego sieci Web Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby zobaczyć, jak pulpit nawigacyjny Kubernetes może zmniejszyć złożoność zadań związanych z zarządzaniem, Utwórz aplikację. Możesz utworzyć aplikację z poziomu pulpitu nawigacyjnego Kubernetes, dostarczając tekst wejściowy, plik YAML lub za pośrednictwem graficznego kreatora.

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Wybierz przycisk **Utwórz** w prawym górnym oknie.
1. Aby użyć Kreatora graficznego, wybierz opcję **utworzenia aplikacji**.
1. Podaj nazwę wdrożenia, na przykład *Nginx*
1. Wprowadź nazwę obrazu kontenera, który ma być używany, na przykład *Nginx: 1.15.5*
1. Aby udostępnić port 80 dla ruchu w sieci Web, należy utworzyć usługę Kubernetes. W obszarze **Usługa**wybierz opcję **zewnętrzny**, a następnie wprowadź **80** dla portu i portu docelowego.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Wdróż** , aby utworzyć aplikację.

![Wdrażanie aplikacji na pulpicie nawigacyjnym sieci Web Kubernetes](./media/kubernetes-dashboard/create-app.png)

Aby można było przypisać publiczny zewnętrzny adres IP do usługi Kubernetes, trwa minutę lub dwie. W obszarze rozmiar po lewej stronie w obszarze **odnajdywanie i równoważenie obciążenia** wybierz pozycję **usługi**. Zostanie wyświetlona Usługa aplikacji, łącznie z *zewnętrznymi punktami końcowymi*, jak pokazano w następującym przykładzie:

![Wyświetl listę usług i punktów końcowych](./media/kubernetes-dashboard/view-services.png)

Wybierz adres punktu końcowego, aby otworzyć okno przeglądarki sieci Web na domyślnej stronie NGINX:

![Wyświetlanie domyślnej strony NGINX wdrożonej aplikacji](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Wyświetl informacje pod

Pulpit nawigacyjny Kubernetes może zapewniać Podstawowe metryki monitorowania i informacje dotyczące rozwiązywania problemów, takie jak dzienniki.

Aby wyświetlić więcej informacji o zasobnikach aplikacji, wybierz pozycję zasobniki w menu po lewej stronie. Zostanie wyświetlona lista dostępnych zasobników. Wybierz *Nginx* pod, aby wyświetlić informacje, takie jak użycie zasobów:

![Wyświetl informacje pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Edytowanie aplikacji

Oprócz tworzenia i wyświetlania aplikacji Pulpit nawigacyjny Kubernetes może służyć do edytowania i aktualizowania wdrożeń aplikacji. Aby zapewnić dodatkową nadmiarowość aplikacji, Zwiększmy liczbę replik NGINX.

Aby edytować wdrożenie:

1. Wybierz pozycję **wdrożenia** w menu po lewej stronie, a następnie wybierz wdrożenie *Nginx* .
1. Wybierz pozycję **Edytuj** w prawym górnym pasku nawigacyjnym.
1. `spec.replica` Znajdź wartość w około 20 wierszach. Aby zwiększyć liczbę replik dla aplikacji, należy zmienić tę wartość z *1* na *3*.
1. Wybierz pozycję **Aktualizuj** , gdy wszystko będzie gotowe.

![Edytowanie wdrożenia w celu zaktualizowania liczby replik](./media/kubernetes-dashboard/edit-deployment.png)

Utworzenie nowych wartości w zestawie replik trwa kilka minut. W menu po lewej stronie wybierz pozycję **zestawy replik**, a następnie wybierz zestaw replik *Nginx* . Lista zasobników odzwierciedla teraz zaktualizowaną liczbę replik, jak pokazano w następujących przykładowych danych wyjściowych:

![Wyświetl informacje o zestawie replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web Kubernetes][kubernetes-dashboard].

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
