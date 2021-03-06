---
title: Tworzenie kontrolera transferu danych przychodzących
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zainstalować i skonfigurować podstawowy kontroler transferu danych przychodzących NGINX w klastrze usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: 8b9e4537d6dd771697accc1f9fbdc3b6e2584863
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668535"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera transferu danych przychodzących w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler transferu danych przychodzących NGINX][nginx-ingress] w klastrze usługi Azure Kubernetes (AKS). Dwie aplikacje są następnie uruchamiane w klastrze AKS, z których każda jest dostępna za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub ze [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa helm zainstalować kontroler transferu danych przychodzących NGINX i przykładowej aplikacji sieci web.

W tym artykule wymaga również, że są uruchomione interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Aby utworzyć kontroler transferu danych przychodzących, użyj funkcji Helm, aby zainstalować *nginx-ingress*. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Węzły systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS) nie powinny uruchamiać kontrolera transferu danych przychodzących. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy obszar nazw Kubernetes dla zasobów przychodzących o nazwie *ingress-basic*. W razie potrzeby określ obszar nazw dla własnego środowiska.

> [!TIP]
> Jeśli chcesz włączyć [zachowanie adresu IP źródła klienta][client-source-ip] dla żądań do kontenerów w klastrze, dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-Forwarded-For*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączoną obsługą ochrony adresów IP źródła klienta przekazywanie protokołu SSL nie będzie działać.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Po utworzeniu usługi modułu równoważenia obciążenia Kubernetes dla kontrolera transferu danych przychodzących NGINX jest przypisywany dynamiczny publiczny adres IP, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Nie utworzono jeszcze żadnych reguł ruchu przychodzącego, więc domyślna strona kontrolera 404 kontrolera transferu danych przychodzących NGINX jest wyświetlana po przejmuje się do wewnętrznego adresu IP. Reguły transferu danych przychodzących są konfigurowane w następujących krokach.

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Aby wyświetlić kontroler transferu danych przychodzących w akcji, uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji *Hello world.*

Przed zainstalowaniem przykładowych wykresów Helm należy dodać repozytorium przykładów platformy Azure do środowiska Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz pierwszą aplikację demonstracyjną z wykresu Helm za pomocą następującego polecenia:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz zainstaluj drugie wystąpienie aplikacji demonstracyjnej. W drugim wystąpieniu należy określić nowy tytuł, tak aby dwie aplikacje były wizualnie różne. Można również określić unikatową nazwę usługi:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu ruchu przychodzącego

Obie aplikacje są teraz uruchomione w klastrze kubernetes. Aby kierować ruch do każdej aplikacji, należy utworzyć zasób transferu danych przychodzących Kubernetes. Zasób transferu danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do *EXTERNAL_IP* jest kierowany `aks-helloworld`do usługi o nazwie . Ruch do *EXTERNAL_IP/hello-world-two* jest kierowany do `aks-helloworld-two` usługi. Ruch do *EXTERNAL_IP/statyczny* jest kierowany do usługi `aks-helloworld` o nazwie dla zasobów statycznych.

Utwórz plik `hello-world-ingress.yaml` o nazwie i skopiuj w poniższym przykładzie YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Utwórz zasób transferu `kubectl apply -f hello-world-ingress.yaml` ruchu przychodzącego za pomocą polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Testowanie kontrolera transferu danych przychodzących

Aby przetestować trasy dla kontrolera transferu danych przychodzących, przejdź do dwóch aplikacji. Otwórz przeglądarkę internetową na adres IP kontrolera ruchu przychodzącego NGINX, na przykład *EXTERNAL_IP*. Pierwsza aplikacja demonstracyjna jest wyświetlana w przeglądarce internetowej, jak pokazano w poniższym przykładzie:

![Pierwsza aplikacja działająca za kontrolerem transferu danych przychodzących](media/ingress-basic/app-one.png)

Teraz dodaj ścieżkę */hello-world-two* do adresu IP, na przykład *EXTERNAL_IP/hello-world-two*. Wyświetlana jest druga aplikacja demonstracyjna o niestandardowym tytule:

![Druga aplikacja działająca za kontrolerem transferu danych przychodzących](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule użyto helm do zainstalowania składników transferu przychodzącego i przykładowych aplikacji. Podczas wdrażania wykresu Helm tworzone są wiele zasobów Kubernetes. Zasoby te obejmują zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć cały przykładowy obszar nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuwanie przykładowego obszaru nazw i wszystkich zasobów

Aby usunąć całą przykładową przestrzeń `kubectl delete` nazw, użyj polecenia i określ nazwę obszaru nazw. Wszystkie zasoby w obszarze nazw są usuwane.

```console
kubectl delete namespace ingress-basic
```

Następnie usuń repozytorium Helm dla aplikacji AKS hello world:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Usuwanie zasobów pojedynczo

Alternatywnie bardziej szczegółowe podejście jest usunięcie poszczególnych zasobów utworzonych. Lista komunikatów helm `helm list` z poleceniem. Poszukaj wykresów o nazwach *nginx-ingress* i *aks-helloworld*, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Usuń zwolnienia za `helm delete` pomocą polecenia. Poniższy przykład usuwa wdrożenia transferu ruchu przychodzącego NGINX i dwóch przykładowych aplikacji AKS hello world.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Następnie usuń repozytorium Helm dla aplikacji AKS hello world:

```console
helm repo remove azure-samples
```

Usuń trasę transferu danych przychodzących, która kierowała ruch do przykładowych aplikacji:

```console
kubectl delete -f hello-world-ingress.yaml
```

Na koniec można usunąć sam obszar nazw. Użyj `kubectl delete` polecenia i określ nazwę obszaru nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawierał niektóre zewnętrzne składniki usługi AKS. Aby dowiedzieć się więcej o tych składnikach, zobacz następujące strony projektu:

- [Helm CLI][helm-cli]
- [Kontroler transferu danych przychodzących NGINX][nginx-ingress]

Możesz również wykonać następujące czynności:

- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub ze [statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
