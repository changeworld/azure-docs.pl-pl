---
title: Tworzenie kontrolera transferu danych przychodzących dla sieci wewnętrznej w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować międzyNGINXowy kontroler dla wewnętrznej, prywatnej sieci w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 8c3eeaf2f9a92f1be9c691091d8e33d09a60b22d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595658"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera transferu danych przychodzących w wewnętrznej sieci wirtualnej w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler Nginx Ingress][nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Kontroler transferu danych przychodzących jest konfigurowany w wewnętrznej, prywatnej sieci wirtualnej i adresie IP. Nie jest dozwolony dostęp zewnętrzny. W klastrze AKS są uruchamiane dwie aplikacje, z których każdy jest dostępny za pośrednictwem pojedynczego adresu IP.

Możesz także:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
- Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa się Helm do zainstalowania kontrolera transferu danych przychodzących, Menedżera certyfikatów i przykładowej aplikacji sieci Web. Konieczne jest zainicjowanie Helm w ramach klastra AKS i użycie konta usługi dla tego elementu. Aby uzyskać więcej informacji na temat konfigurowania i używania Helm, zobacz [Install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Ten artykuł wymaga również uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Domyślnie kontroler transferu danych przychodzących NGINX jest tworzony z dynamicznym przypisaniem publicznego adresu IP. Typowym wymaganiem konfiguracji jest użycie wewnętrznej, prywatnej sieci i adresu IP. Takie podejście umożliwia ograniczenie dostępu do usług użytkownikom wewnętrznym bez dostępu zewnętrznego.

Utwórz plik o nazwie *Internal-Ingres. YAML* przy użyciu następującego przykładowego pliku manifestu. Ten przykład przypisuje *10.240.0.42* do zasobu *loadBalancerIP* . Podaj własny wewnętrzny adres IP do użycia z kontrolerem transferu danych przychodzących. Upewnij się, że ten adres IP nie jest już używany w sieci wirtualnej.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Teraz Wdróż wykres *Nginx-* transferal z Helm. Aby użyć pliku manifestu utworzonego w poprzednim kroku, należy dodać parametr `-f internal-ingress.yaml`. W celu dodania nadmiarowości dwie repliki kontrolerów NGINX Ingress są wdrażane z parametrem `--set controller.replicaCount`. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze AKS znajduje się więcej niż jeden węzeł.

Kontroler transferu danych przychodzących należy również zaplanować w węźle systemu Linux. W węzłach systemu Windows Server (obecnie w wersji zapoznawczej w AKS) nie należy uruchamiać kontrolera transferu danych przychodzących. Selektor węzła jest określany za pomocą parametru `--set nodeSelector`, aby poinformować usługę Kubernetes Scheduler o uruchomieniu kontrolera usługi NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy przestrzeń nazw Kubernetes dla zasobów przychodzących o nazwie transfery *-Basic*. W razie potrzeby określ przestrzeń nazw dla własnego środowiska. Jeśli w klastrze AKS nie włączono kontroli RBAC, Dodaj `--set rbac.create=false` do poleceń Helm.

> [!TIP]
> Jeśli chcesz włączyć [zachowywanie źródłowych adresów IP klienta][client-source-ip] dla żądań do kontenerów w klastrze, Dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji Helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-forwardd-for*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączonym zachowywaniem źródłowych adresów IP klienta przekazywanie protokołu SSL nie będzie działało.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Gdy usługa równoważenia obciążenia Kubernetes jest tworzona dla kontrolera NGINX transferu danych przychodzących, zostanie przypisany wewnętrzny adres IP, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Nie utworzono jeszcze żadnych reguł dotyczących ruchu przychodzącego, więc w przypadku przechodzenia do wewnętrznego adresu IP zostanie wyświetlona domyślna strona 404 kontrolera NGINX. Reguły dotyczące transferu danych przychodzących są konfigurowane w poniższych krokach.

## <a name="run-demo-applications"></a>Uruchom aplikacje demonstracyjne

Aby wyświetlić kontroler transferu danych przychodzących w działaniu, Uruchom na klastrze AKS dwie aplikacje demonstracyjne. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji "Hello World".

Przed zainstalowaniem przykładowych wykresów Helm należy dodać repozytorium przykładów platformy Azure do środowiska Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz pierwszą aplikację demonstracyjną z wykresu Helm za pomocą następującego polecenia:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz Zainstaluj drugie wystąpienie aplikacji demonstracyjnej. Dla drugiego wystąpienia należy określić nowy tytuł, tak aby dwie aplikacje były wizualnie różne. Należy również określić unikatową nazwę usługi:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu danych przychodzących

Obie aplikacje działają teraz w klastrze Kubernetes. Aby skierować ruch do poszczególnych aplikacji, utwórz zasób Kubernetes. Zasób danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do `http://10.240.0.42/` adresu jest kierowany do usługi o nazwie `aks-helloworld`. Ruch do `http://10.240.0.42/hello-world-two` adresu jest kierowany do usługi `ingress-demo`.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj go w poniższym przykładzie YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Utwórz zasób transferu danych przychodzących przy użyciu polecenia `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testowanie kontrolera transferu danych przychodzących

Aby przetestować trasy dla kontrolera transferu danych przychodzących, przejdź do dwóch aplikacji za pomocą klienta sieci Web. W razie konieczności można szybko przetestować te funkcje wyłącznie wewnętrznie z poziomu klastra AKS. Utwórz test pod i Dołącz do niego sesję terminalu:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Zainstaluj `curl` w obszarze `apt-get`przy użyciu:

```console
apt-get update && apt-get install -y curl
```

Teraz Uzyskuj dostęp do adresu kontrolera Kubernetes Ingress za pomocą `curl`, takich jak *http://10.240.0.42* . Podaj własny wewnętrzny adres IP określony podczas wdrażania kontrolera transferu danych przychodzących w pierwszym kroku tego artykułu.

```console
curl -L http://10.240.0.42
```

Nie dostarczono żadnej dodatkowej ścieżki z adresem, dlatego kontroler transferu danych przychodzących jest domyślnie kierowany do */* . Zostanie zwrócona pierwsza aplikacja demonstracyjna, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Teraz dodaj ścieżkę */Hello-World-Two* do adresu, na przykład *http://10.240.0.42/hello-world-two* . Zostanie zwrócona druga aplikacja demonstracyjna z tytułem niestandardowym, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule użyto Helm do zainstalowania składników przychodzących i przykładowych aplikacji. Po wdrożeniu wykresu Helm są tworzone różne zasoby Kubernetes. Te zasoby obejmują między innymi te, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć całą przykładową przestrzeń nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuń przykładową przestrzeń nazw i wszystkie zasoby

Aby usunąć całą przykładową przestrzeń nazw, użyj polecenia `kubectl delete` i określ nazwę przestrzeni nazw. Wszystkie zasoby w przestrzeni nazw są usuwane.

```console
kubectl delete namespace ingress-basic
```

Następnie usuń repozytorium Helm dla aplikacji AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Usuń zasoby pojedynczo

Alternatywnie, bardziej szczegółowe podejście polega na usunięciu utworzonych poszczególnych zasobów. Utwórz listę wydań Helm za pomocą polecenia `helm list`. Poszukaj wykresów o nazwie *Nginx-Ingress* i *AKS-HelloWorld*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Usuń wydania za pomocą polecenia `helm delete`. Poniższy przykład usuwa wdrożenie NGINX, a dwa przykładowe aplikacje AKS Hello World.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Następnie usuń repozytorium Helm dla aplikacji AKS Hello World:

```console
helm repo remove azure-samples
```

Usuń trasę transferu danych przychodzących, która kieruje ruch do aplikacji przykładowych:

```console
kubectl delete -f hello-world-ingress.yaml
```

Na koniec można usunąć samą przestrzeń nazw. Użyj `kubectl delete` polecenia i określ nazwę przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Następne kroki

W tym artykule uwzględniono niektóre składniki zewnętrzne do AKS. Aby dowiedzieć się więcej o tych składnikach, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia Helm][helm-cli]
- [Kontroler transferu danych przychodzących NGINX][nginx-ingress]

Możesz także:

- [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
- [Włącz dodatek routingu aplikacji protokołu HTTP][aks-http-app-routing]
- [Utwórz kontroler transferu danych przychodzących z dynamicznym publicznym adresem IP, a następnie skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-tls]
- [Utwórz kontroler transferu danych przychodzących ze statycznym publicznym adresem IP i skonfiguruj szyfrowanie, aby automatycznie generować certyfikaty TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers