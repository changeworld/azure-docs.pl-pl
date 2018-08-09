---
title: Szybki start — klaster Azure Kubernetes
description: Szybka nauka tworzenia klastra Kubernetes dla kontenerów systemu Linux w usłudze AKS za pomocą witryny Azure Portal.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 07/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: aceddc2594065c9c36f8dbf63fce2ad03577a383
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443371"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster"></a>Szybki start: wdrażanie klastra usługi Azure Kubernetes Service (AKS)

W tym przewodniku Szybki start wdrażany jest klaster AKS przy użyciu witryny Azure Portal. Następnie w klastrze jest uruchamiana aplikacja obsługująca wiele kontenerów, która składa się z frontonu internetowego i wystąpienia pamięci podręcznej Redis. Po ukończeniu aplikacja będzie dostępna w Internecie.

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)

W tym przewodniku Szybki start założono, że masz podstawową wiedzę na temat pojęć związanych z rozwiązaniem Kubernetes. Aby uzyskać szczegółowe informacje na jego temat, zapoznaj się z [dokumentacją rozwiązania Kubernetes][kubernetes-documentation].

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Usługa Kubernetes**.

Aby utworzyć klaster usługi AKS, wykonaj następujące czynności:

1. **Podstawy** — skonfiguruj następujące opcje:
    - *SZCZEGÓŁY PROJEKTU*: wybierz subskrypcję platformy Azure, a następnie wybierz lub utwórz grupę zasobów platformy Azure, taką jak *myResourceGroup*. Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*.
    - *SZCZEGÓŁY KLASTRA*: wybierz region, wersję platformy Kubernetes i prefiks nazwy DNS dla klastra usługi AKS.
    - *SKALA*: wybierz rozmiar maszyny wirtualnej dla węzłów usługi AKS. Rozmiar maszyny wirtualnej **nie może** zostać zmieniony po wdrożeniu klastra AKS.
        - Wybierz liczbę węzłów do wdrożenia w klastrze. Na potrzeby tego przewodnika Szybki start ustaw pozycję **Liczba węzłów** na *1*. Liczbę węzłów **można** dostosować po wdrożeniu klastra.
    
    ![Tworzenie klastra AKS — podaj podstawowe informacje](media/kubernetes-walkthrough-portal/create-cluster-1.png)

    Wybierz pozycję **Dalej: uwierzytelnianie** po zakończeniu.

1. **Uwierzytelnianie**: skonfiguruj następujące opcje:
    - Utwórz nową główną nazwę usługi lub *skonfiguruj* użycie istniejącej. W przypadku korzystania z istniejącej głównej nazwy usługi należy podać identyfikator klienta i klucz tajny głównej nazwy usługi.
    - Włącz opcję dla funkcji kontroli dostępu opartej na rolach (RBAC) w rozwiązaniu Kubernetes. Te kontrolki zapewniają bardziej precyzyjną kontrolę nad dostępem do zasobów platformy Kubernetes wdrożonych w klastrze usługi AKS.

    ![Tworzenie klastra AKS — skonfiguruj uwierzytelnianie](media/kubernetes-walkthrough-portal/create-cluster-2.png)

    Po zakończeniu wybierz pozycję **Dalej: sieć**.

1. **Sieć**: skonfiguruj następujące opcje sieci, które należy ustawić jako domyślne:
    
    - **Routing aplikacji protokołu HTTP** — wybierz pozycję **Tak**, aby skonfigurować zintegrowanego kontrolera danych przychodzących z automatycznym tworzeniem publicznej nazwy DNS. Aby uzyskać więcej informacji na temat routingu Http, zobacz [Routing HTTP i DNS w usłudze AKS][http-routing].
    - **Konfiguracja sieci** — wybierz **podstawową** konfigurację sieci używającej wtyczki Kubernetes [kubenet][kubenet], zamiast zaawansowanej konfiguracji sieci używającej usługi [Azure CNI][azure-cni]. Aby uzyskać więcej informacji na temat opcji sieciowych, zobacz [Przegląd opcji sieciowych usługi AKS][aks-network].
    
    Po zakończeniu wybierz pozycję **Dalej: monitorowanie**.

1. W przypadku wdrażania klastra AKS można skonfigurować usługę Azure Container Insights do monitorowania kondycji klastra AKS i zasobników uruchomionych w klastrze. Aby uzyskać więcej informacji dotyczących monitorowania kondycji kontenera, zobacz [Monitorowanie kondycji usługi Azure Kubernetes Service][aks-monitor].

    Wybierz pozycję **Tak**, aby włączyć monitorowanie kontenera i wybierz istniejący obszar roboczy usługi Log Analytics lub utwórz nowy.
    
    Wybierz pozycje **Przegląd + utwórz**, a następnie, gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Utworzenie klastra usługi AKS i przygotowanie go do użycia trwa kilka minut. Przejdź do grupy zasobów klastra AKS, takiej jak *myResourceGroup*, i wybierz zasób usługi AKS, taki jak *myAKSCluster*. Zostanie wyświetlony pulpit nawigacyjny klastra usługi AKS, taki jak na poniższym przykładowym zrzucie ekranu:

![Przykładowy pulpit nawigacyjny usługi AKS w witrynie Azure Portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Klient `kubectl` jest instalowany wstępnie wraz z usługą Azure Cloud Shell.

Otwórz usługę Cloud Shell za pomocą przycisku w prawym górnym rogu witryny Azure Portal.

![Otwieranie usługi Azure Cloud Shell w portalu](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Użyj polecenia [az aks get-credentials][az-aks-get-credentials] w celu skonfigurowania narzędzia `kubectl` w celu nawiązania połączenia z klastrem Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby zweryfikować połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get] w celu zwrócenia listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach.

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     10m       v1.10.5
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Pliki manifestu rozwiązania Kubernetes definiują żądany stan klastra, w tym informacje o obrazach kontenera, które powinny zostać uruchomione. W tym przewodniku Szybki start manifest służy do tworzenia wszystkich obiektów potrzebnych do uruchomienia przykładowej aplikacji Azure Vote. Obiekty te obejmują dwa [wdrożenia rozwiązania Kubernetes][kubernetes-deployment] — jedno dla frontonu aplikacji Azure Vote i jedno dla wystąpienia usługi Redis. Tworzone są również dwie [usługi rozwiązania Kubernetes][kubernetes-service] — wewnętrzna usługa dla wystąpienia usługi Redis i zewnętrzna usługa służąca do uzyskiwania dostępu do aplikacji Azure Vote z Internetu.

Utwórz plik o nazwie `azure-vote.yaml` i skopiuj go do poniższego kodu YAML. Jeśli pracujesz w usłudze Azure Cloud Shell, utwórz ten plik przy użyciu serwera `vi` lub `Nano`, tak jak podczas pracy w systemie wirtualnym lub fizycznym.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Użyj polecenia [kubectl apply][kubectl-apply], aby uruchomić aplikację.

```azurecli-interactive
kubectl create -f azure-vote.yaml
```

Poniższe przykładowe dane wyjściowe zawierają zasoby platformy Kubernetes utworzone w klastrze usługi AKS:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji tworzona jest [usługa Kubernetes][kubernetes-service], która uwidacznia aplikację w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Początkowo adres *EXTERNAL-IP* dla usługi *azure-vote-front* pojawia się jako *oczekujący*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na *adres IP*, zatrzymaj proces śledzenia narzędzia kubectl za pomocą polecenia `CTRL-C`.

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

W przeglądarce internetowej otwórz zewnętrzny adres IP usługi, aby wyświetlić aplikację Azure Vote, jak pokazano w poniższym przykładzie:

![Obraz przedstawiający przechodzenie do przykładowej aplikacji do głosowania na platformie Azure](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="monitor-health-and-logs"></a>Monitorowanie kondycji i dzienników

Podczas tworzenia klastra włączono monitorowanie szczegółowych informacji o kontenerze. Ta funkcja monitorowania udostępnia metryki dotyczące kondycji zasobników i klastra usługi AKS działających w klastrze. Aby uzyskać więcej informacji dotyczących monitorowania kondycji kontenera, zobacz [Monitorowanie kondycji usługi Azure Kubernetes Service][aks-monitor].

Zebranie tych danych w witrynie Azure Portal może zająć kilka minut. Aby wyświetlić bieżący stan, czas pracy i użycie zasobów na potrzeby zasobników aplikacji Azure Vote, wróć do zasobu usługi AKS, takiego jak *myAKSCluster*, w witrynie Azure Portal. Wybierz pozycję **Monitorowanie kondycji kontenera** > wybierz **domyślną** przestrzeń nazw > wybierz pozycję **Kontenery**.  Zostaną wyświetlone kontenery *azure-vote-back* i *azure-vote-front*:

![Wyświetlanie kondycji uruchomionych kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Aby wyświetlić dzienniki dla zasobnika `azure-vote-front`, wybierz link **Wyświetl dzienniki** po prawej stronie listy kontenerów. Dzienniki te obejmują strumienie *stdout* i *stderr* z kontenera.

![Wyświetlanie dzienników kontenerów w usłudze AKS](media/kubernetes-walkthrough-portal/monitor-containers-logs.png)

## <a name="delete-cluster"></a>Usuwanie klastra

Gdy klaster nie będzie już potrzebny, usuń zasób klastra, co spowoduje usunięcie wszystkich skojarzonych zasobów. Tę operację można wykonać w witrynie Azure Portal, wybierając przycisk **Usuń** na pulpicie nawigacyjnym klastra usługi AKS. Ewentualnie można użyć polecenia [az aks delete][az-aks-delete] w usłudze Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku Szybki start jest tworzone wdrożenie rozwiązania Kubernetes za pomocą utworzonych wcześniej obrazów kontenerów. Powiązany kod aplikacji, plik Dockerfile i plik manifestu rozwiązania Kubernetes są dostępne w serwisie GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono klaster Kubernetes oraz wdrożono w nim aplikację obsługującą wiele kontenerów.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[kubernetes-deployment]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./networking-overview.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
