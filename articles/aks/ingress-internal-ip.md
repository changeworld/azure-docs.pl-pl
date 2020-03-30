---
title: Tworzenie kontrolera transferu danych przychodzących dla sieci wewnętrznej w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować kontroler transferu danych przychodzących NGINX dla wewnętrznej sieci prywatnej w klastrze usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 8c3eeaf2f9a92f1be9c691091d8e33d09a60b22d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595658"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera transferu danych przychodzących do wewnętrznej sieci wirtualnej w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler transferu danych przychodzących NGINX][nginx-ingress] w klastrze usługi Azure Kubernetes (AKS). Kontroler transferu danych przychodzących jest skonfigurowany w wewnętrznej, prywatnej sieci wirtualnej i adresie IP. Dostęp zewnętrzny nie jest dozwolony. Dwie aplikacje są następnie uruchamiane w klastrze AKS, z których każda jest dostępna za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub ze [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa helm zainstalować kontroler transferu danych przychodzących NGINX, cert-manager i przykładowej aplikacji sieci web. Musisz mieć Helm zainicjowane w klastrze AKS i przy użyciu konta usługi dla Tiller. Aby uzyskać więcej informacji na temat konfigurowania i używania helmu, zobacz [Instalowanie aplikacji z helmem w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, że są uruchomione interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Domyślnie kontroler przystawek NGINX jest tworzony z dynamicznym przypisaniem publicznego adresu IP. Typowym wymaganiem konfiguracji jest użycie wewnętrznej, prywatnej sieci i adresu IP. Takie podejście umożliwia ograniczenie dostępu do usług do użytkowników wewnętrznych, bez dostępu zewnętrznego.

Utwórz plik o nazwie *internal-ingress.yaml* przy użyciu następującego przykładowego pliku manifestu. W tym przykładzie przypisuje *10.240.0.42* do *zasobu loadBalancerIP.* Podaj swój wewnętrzny adres IP do użytku z kontrolerem transferu danych przychodzących. Upewnij się, że ten adres IP nie jest jeszcze używany w sieci wirtualnej.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Teraz wdrożyć *wykres nginx-ingress* z Helm. Aby użyć pliku manifestu utworzonego w `-f internal-ingress.yaml` poprzednim kroku, dodaj parametr. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Węzły systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS) nie powinny uruchamiać kontrolera transferu danych przychodzących. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy obszar nazw Kubernetes dla zasobów przychodzących o nazwie *ingress-basic*. W razie potrzeby określ obszar nazw dla własnego środowiska. Jeśli klaster AKS nie jest włączony `--set rbac.create=false` RBAC, dodaj do poleceń Helm.

> [!TIP]
> Jeśli chcesz włączyć [zachowanie adresu IP źródła klienta][client-source-ip] dla żądań do kontenerów w klastrze, dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-Forwarded-For*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączoną obsługą ochrony adresów IP źródła klienta przekazywanie protokołu SSL nie będzie działać.

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

Po utworzeniu usługi modułu równoważenia obciążenia Kubernetes dla kontrolera transferu danych przychodzących NGINX wewnętrzny adres IP jest przypisywany, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Nie utworzono jeszcze żadnych reguł ruchu przychodzącego, więc domyślna strona kontrolera 404 kontrolera transferu danych przychodzących NGINX jest wyświetlana po przejmuje się do wewnętrznego adresu IP. Reguły transferu danych przychodzących są konfigurowane w następujących krokach.

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Aby wyświetlić kontroler transferu danych przychodzących w akcji, uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji "Hello world".

Przed zainstalowaniem przykładowych wykresów Helm należy dodać repozytorium przykładów platformy Azure do środowiska Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz pierwszą aplikację demonstracyjną z wykresu Helm za pomocą następującego polecenia:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz zainstaluj drugie wystąpienie aplikacji demonstracyjnej. W drugim wystąpieniu należy określić nowy tytuł, tak aby dwie aplikacje były wizualnie różne. Można również określić unikatową nazwę usługi:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu ruchu przychodzącego

Obie aplikacje są teraz uruchomione w klastrze kubernetes. Aby kierować ruch do każdej aplikacji, należy utworzyć zasób transferu danych przychodzących Kubernetes. Zasób transferu danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch `http://10.240.0.42/` na adres jest `aks-helloworld`kierowany do usługi o nazwie . Ruch na `http://10.240.0.42/hello-world-two` adres jest kierowany `ingress-demo` do usługi.

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

Utwórz zasób transferu `kubectl apply -f hello-world-ingress.yaml` ruchu przychodzącego za pomocą polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testowanie kontrolera transferu danych przychodzących

Aby przetestować trasy dla kontrolera transferu danych przychodzących, przejdź do dwóch aplikacji z klientem sieci web. W razie potrzeby można szybko przetestować tę funkcję tylko wewnętrzną z zasobnika w klastrze AKS. Utwórz zasobnik testowy i dołącz do niej sesję terminala:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Zainstaluj `curl` w zasobniku za pomocą: `apt-get`

```console
apt-get update && apt-get install -y curl
```

Teraz dostęp do adresu kontrolera transferu danych `curl`przychodzących *http://10.240.0.42*Kubernetes za pomocą , takich jak . Podaj swój własny wewnętrzny adres IP określony podczas wdrażania kontrolera transferu danych przychodzących w pierwszym kroku tego artykułu.

```console
curl -L http://10.240.0.42
```

Nie podano dodatkowej ścieżki z adresem, więc kontroler ruchu */* przychodzącego domyślnie do trasy. Zwracana jest pierwsza aplikacja demonstracyjna, jak pokazano na poniższym skróconym przykładzie:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Teraz dodaj *ścieżkę /hello-world-two* do *http://10.240.0.42/hello-world-two*adresu, na przykład . Zwracana jest druga aplikacja demonstracyjna z tytułem niestandardowym, jak pokazano na poniższym skróconym przykładzie:

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
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Usuń zwolnienia za `helm delete` pomocą polecenia. Poniższy przykład usuwa wdrożenia transferu ruchu przychodzącego NGINX i dwóch przykładowych aplikacji AKS hello world.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
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

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących z dynamicznym publicznym adresem IP i konfigurowanie szyfrowania let's do automatycznego generowania certyfikatów TLS][aks-ingress-tls]
- [Tworzenie kontrolera transferu danych przychodzących ze statycznym publicznym adresem IP i konfigurowanie szyfrowania let's do automatycznego generowania certyfikatów TLS][aks-ingress-static-tls]

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