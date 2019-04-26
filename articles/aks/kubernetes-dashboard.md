---
title: Zarządzanie klastrem usługi Azure Kubernetes Service, korzystając z pulpitu nawigacyjnego sieci web
description: Dowiedz się, jak wbudowany pulpit nawigacyjny interfejsu użytkownika sieci web rozwiązania Kubernetes umożliwiają zarządzanie klastrem usługi Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: e831979e0a9f9c8b4d812f682403ecf466abbd13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465500"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Dostęp do pulpitu nawigacyjnego sieci web rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

Kubernetes obejmuje pulpit nawigacyjny sieci web, który może służyć do podstawowych operacji zarządzania. Ten pulpit nawigacyjny pozwala wyświetlić stan kondycji podstawowych i metryk dla aplikacji, tworzenia i wdrażania usług i edytować istniejące aplikacje. W tym artykule pokazano, jak dostęp do pulpitu nawigacyjnego rozwiązania Kubernetes za pomocą wiersza polecenia platformy Azure, a następnie przeprowadzi Cię przez niektóre operacje podstawowe pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web rozwiązania Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach szczegółowo opisanych w tym dokumencie przyjęto założenie, został utworzony klaster usługi AKS i ustalonymi `kubectl` połączenia z klastrem. Jeśli musisz utworzyć klaster usługi AKS, zobacz [szybkiego startu usługi AKS][aks-quickstart].

Musisz również mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie  `az --version` , aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Rozpocznij pulpit nawigacyjny platformy Kubernetes

Aby uruchomić Pulpit nawigacyjny platformy Kubernetes, należy użyć [Przeglądaj az aks] [ az-aks-browse] polecenia. W poniższym przykładzie otwierany pulpit nawigacyjny klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemu dla deweloperów i interfejsu API rozwiązania Kubernetes i otworzy w przeglądarce sieci web pulpitu nawigacyjnego platformy Kubernetes. Przeglądarki sieci web nie zostanie otwarta do pulpitu nawigacyjnego rozwiązania Kubernetes, skopiuj i wklej adres URL zanotowanym w wiersza polecenia platformy Azure, zwykle `http://127.0.0.1:8001`.

![Na stronie Przegląd pulpitu nawigacyjnego sieci web rozwiązania Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

### <a name="for-rbac-enabled-clusters"></a>W przypadku klastrów z obsługą kontroli RBAC

Jeśli klaster AKS korzysta z funkcji RBAC, *ClusterRoleBinding* musi zostać utworzona zanim poprawnie możesz uzyskać dostęp do pulpitu nawigacyjnego. Domyślnie Pulpit nawigacyjny platformy Kubernetes jest wdrażany z minimalnym dostęp do odczytu i wyświetla błędy dostęp RBAC. Pulpit nawigacyjny platformy Kubernetes, nie obsługuje obecnie poświadczenia dostarczone przez użytkownika, aby określić poziom dostępu, a nie używa ról przypisywane do konta usługi. Administrator klastra może wybrać przyznanie dodatkowe prawa dostępu do *pulpit nawigacyjny platformy kubernetes* do konta usługi, jednak może to być wektor dla podwyższenie poziomu uprawnień. Można również zintegrować uwierzytelniania usługi Azure Active Directory, aby zapewnić bardziej szczegółowy poziom dostępu.

Aby utworzyć powiązanie, użyj [kubectl tworzenie clusterrolebinding] [ kubectl-create-clusterrolebinding] polecenia, jak pokazano w poniższym przykładzie. 

> [!WARNING]
> To powiązanie przykładowych nie obejmuje wszystkie składniki dodatkowego uwierzytelniania i może prowadzić do metody use niezabezpieczone. Pulpit nawigacyjny platformy Kubernetes jest otwarta dla każda osoba z dostępem do adresu URL. Nie ujawniaj pulpit nawigacyjny platformy Kubernetes publicznie.
>
> Aby uzyskać więcej informacji na temat korzystania z różnych metod uwierzytelniania, zobacz wiki pulpitu nawigacyjnego rozwiązania Kubernetes na [kontrole dostępu][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Pulpit nawigacyjny rozwiązania Kubernetes można teraz uzyskać dostęp w klastrze z włączoną funkcją RBAC. Aby uruchomić Pulpit nawigacyjny platformy Kubernetes, należy użyć [Przeglądaj az aks] [ az-aks-browse] polecenia zgodnie z opisem w poprzednim kroku.

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby zobaczyć, jak zmniejszyć złożoność zadania związane z zarządzaniem pulpit nawigacyjny platformy Kubernetes, Utwórzmy aplikację. Można utworzyć aplikację z poziomu pulpitu nawigacyjnego rozwiązania Kubernetes, zapewniając tekstowego, pliku YAML, lub za pomocą Kreatora graficznego.

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Wybierz **Utwórz** przycisk w prawym górnym oknie po prawej stronie.
1. Aby użyć graficznego kreatora, należy wybrać **tworzenie aplikacji**.
1. Podaj nazwę dla wdrożenia, takich jak *serwera nginx*
1. Wprowadź nazwę obrazu kontenera do użycia, taki jak *nginx:1.15.5*
1. Aby uwidocznić port 80 dla ruchu w sieci web, należy utworzyć usługi Kubernetes. W obszarze **usługi**, wybierz opcję **zewnętrznych**, wprowadź **80** port i port docelowy.
1. Gdy wszystko będzie gotowe, wybierz **Wdróż** do tworzenia aplikacji.

![Wdrażanie aplikacji na pulpicie nawigacyjnym w sieci web rozwiązania Kubernetes](./media/kubernetes-dashboard/create-app.png)

Trwa minutę lub dwie publiczny, zewnętrzny adres IP ma być przypisane do usługi Kubernetes. Rozmiaru po lewej stronie w obszarze **odnajdywania i równoważenie obciążenia** wybierz **usług**. Usługa aplikacji jest wyświetlane, w tym *zewnętrzne punkty końcowe*, jak pokazano w poniższym przykładzie:

![Wyświetl listę usług i punktów końcowych](./media/kubernetes-dashboard/view-services.png)

Wybierz adres punktu końcowego, aby otworzyć okno przeglądarki sieci web, aby domyślna strona serwera NGINX:

![Wyświetlić domyślną stronę serwera NGINX wdrożonej aplikacji](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Wyświetlanie informacji pod

Pulpit nawigacyjny platformy Kubernetes, może zapewnić podstawowe metryki monitorowania i informacje, takie jak dzienniki dotyczące rozwiązywania problemów.

Aby wyświetlić więcej informacji na temat zasobników aplikacji, wybierz **zasobników** w menu po lewej stronie. Wyświetlana jest lista dostępnych zasobników. Wybierz swoje *nginx* zasobników, aby wyświetlić informacje, takie jak użycie zasobów:

![Wyświetlanie informacji pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Edytowanie aplikacji

Oprócz tworzenia i wyświetlania aplikacji, pulpit nawigacyjny platformy Kubernetes, może służyć do edycji i aktualizacji wdrożeń aplikacji. Aby zapewnić dodatkowe nadmiarowość dla aplikacji, możemy zwiększyć liczbę replik NGINX.

Aby edytować wdrożenia:

1. Wybierz **wdrożeń** w menu po lewej stronie, a następnie wybierz swoje *nginx* wdrożenia.
1. Wybierz **Edytuj** na pasku nawigacyjnym po prawej stronie górnej.
1. Znajdź `spec.replica` wartość, w całym wierszu 20. Aby zwiększyć liczbę replik dla aplikacji, należy zmienić wartość tego parametru *1* do *3*.
1. Wybierz **aktualizacji** gdy będzie gotowe.

![Edytuj wdrożenia w celu zaktualizowania liczba replik](./media/kubernetes-dashboard/edit-deployment.png)

Może potrwać kilka minut w przypadku nowych zasobników, która ma zostać utworzony wewnątrz zestawu replik. W menu po lewej stronie wybierz **zestawów replik**, a następnie wybierz swoje *nginx* zestawu replik. Lista zasobników teraz odzwierciedla liczbę replik zaktualizowane, jak pokazano w następujących przykładowych danych wyjściowych:

![Wyświetlanie informacji na temat zestawu replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web rozwiązania Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
