---
title: Tworzenie kontrolera ruch przychodzący w sieci wewnętrznej w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować kontrolera danych przychodzących NGINX w ramach sieci prywatne, wewnętrzne w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 76ad9d21f7b328e7f201d227cdd9ace51c62a3fd
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357276"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Utworzyć kontroler ruch przychodzący z siecią wirtualną wewnętrzny w usłudze Azure Kubernetes Service (AKS)

Kontroler danych przychodzących jest to oprogramowanie, które zapewnia zwrotny serwer proxy, routing ruchu można konfigurować i zakończenie protokołu TLS dla usługi Kubernetes. Zasoby danych przychodzących rozwiązania Kubernetes są używane do konfigurowania reguł ruchu przychodzącego i trasy dla poszczególnych usług Kubernetes. Korzystanie z reguł ruchu przychodzącego i kontroler danych przychodzących, pojedynczy adres IP może służyć do kierowania ruchu do wielu usług w klastrze Kubernetes.

W tym artykule przedstawiono sposób wdrażania [kontrolera danych przychodzących NGINX] [ nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Kontroler danych przychodzących jest skonfigurowany na wirtualnej sieci prywatnej, wewnętrzne i adres IP. Nie zewnętrznych dostęp jest dozwolony. Dwie aplikacje są następnie uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących z dynamicznym publicznym adresem IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-tls]
- [Tworzenie kontrolera danych przychodzących za pomocą statyczny publiczny adres IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa narzędzia Helm do zainstalowania serwera NGINX kontrolera danych przychodzących, Menedżer certyfikatów i przykładową aplikację sieci web. Musisz mieć narzędzia Helm inicjowane w obrębie klastra usługi AKS i przy użyciu konta usługi dla Tiller. Aby uzyskać więcej informacji na temat konfigurowania i używania narzędzia Helm, zobacz [instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera danych przychodzących

Domyślnie serwer NGINX kontrolera danych przychodzących jest tworzony z dynamicznego przypisywania publicznych adresów IP. Jest typowym wymogiem konfiguracji do użycia sieci prywatne, wewnętrzne i adresu IP. To podejście pozwala ograniczyć dostęp do usług dla użytkowników wewnętrznych, bez dostępu zewnętrznego.

Utwórz plik o nazwie *wewnętrzny ingress.yaml* przy użyciu poniższej przykładowy plik manifestu. W tym przykładzie przypisuje *10.240.0.42* do *loadBalancerIP* zasobów. Podaj własny wewnętrzny adres IP do użycia z usługą kontrolera danych przychodzących. Upewnij się, że ten adres IP nie jest już używana w ramach sieci wirtualnej.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Teraz wdrożyć *ruch przychodzący serwera nginx* wykresu za pomocą narzędzia Helm. Aby użyć pliku manifestu utworzonego w poprzednim kroku, należy dodać `-f internal-ingress.yaml` parametru:

> [!TIP]
> W poniższym przykładzie instalowana kontroler danych przychodzących w `kube-system` przestrzeni nazw. Jeśli to konieczne, można określić innej przestrzeni nazw dla Twojego środowiska. Jeśli klaster AKS nie jest włączone RBAC, Dodaj `--set rbac.create=false` poleceń.

```console
helm install stable/nginx-ingress --namespace kube-system -f internal-ingress.yaml
```

Po utworzeniu rozwiązania Kubernetes usługi równoważenia obciążenia dla kontrolera danych przychodzących NGINX swoje wewnętrzny adres IP jest przypisywany, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Żadnych reguł ruchu przychodzącego zostały utworzone, aby strona 404 domyślna serwera NGINX kontrolera danych przychodzących jest wyświetlana, jeśli przejdziesz do wewnętrznego adresu IP. Reguły ruchu przychodzącego są konfigurowane w poniższych krokach.

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Aby wyświetlić kontroler danych przychodzących w akcji, uruchom dwóch wersji demonstracyjnej aplikacji w klastrze AKS. W tym przykładzie Helm służy do wdrażania dwa wystąpienia aplikacji proste "Hello world".

Przed zainstalowaniem wykresów rozwiązania Helm przykładowe Dodaj repozytorium przykładów dla platformy Azure w środowisku Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Tworzenie pierwszej aplikacji demonstracyjnej z planu narzędzia Helm, za pomocą następującego polecenia:

```console
helm install azure-samples/aks-helloworld
```

Teraz zainstalować drugie wystąpienie aplikacji pokazowej. Dla drugiego wystąpienia należy określić nowy tytuł, aby wizualnie różniące się od dwóch aplikacji. Możesz również określić unikatową nazwę usługi:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Utwórz trasę w protokole transferu danych przychodzących

Obie aplikacje są uruchomione w klastrze Kubernetes. Do kierowania ruchu do każdej aplikacji, utwórz zasób transferu danych przychodzących rozwiązania Kubernetes. Zasób ruch przychodzący konfiguruje zasady, które kierują ruch do jednego z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `http://10.240.0.42/` jest kierowany do usługi o nazwie `aks-helloworld`. Ruch do adresu `http://10.240.0.42/hello-world-two` jest kierowany do `ingress-demo` usługi.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj poniższy przykład kodu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Tworzenie przy użyciu zasobów ruch przychodzący `kubectl apply -f hello-world-ingress.yaml` polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testowanie kontrolera danych przychodzących

Aby przetestować trasy dla kontrolera danych przychodzących, przejdź do dwóch aplikacji za pomocą klienta sieci web. Jeśli to konieczne, możesz szybko przetestować tę funkcję tylko wewnętrzne z zasobników w klastrze AKS. Utwórz zasobnik testu i do niej dołączyć sesję terminalu:

```console
kubectl run -it --rm aks-ingress-test --image=debian
```

Zainstaluj `curl` w zasobnik przy użyciu `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Teraz uzyskiwać dostęp do usługi Kubernetes transferu danych przychodzących kontroler przy użyciu adresu `curl`, takich jak *http://10.240.0.42*. Podaj własny wewnętrzny adres IP określony podczas wdrażania kontrolera danych przychodzących w pierwszym kroku w tym artykule.

```console
curl -L http://10.240.0.42
```

Nie dodatkowe ścieżki został podany adres, więc kontrolera danych przychodzących wartość domyślna to */* trasy. Pierwszy aplikacji pokazowej zostaną zwrócone, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Teraz Dodaj */hello-world-two* ścieżkę na adres, takie jak *http://10.240.0.42/hello-world-two*. Zwracana jest drugiej aplikacji demonstracyjnej z tytułem niestandardowych, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule uwzględnione niektóre składniki zewnętrzne w usłudze AKS. Aby dowiedzieć się więcej na temat tych składników, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia narzędzia Helm][helm-cli]
- [Kontroler danych przychodzących serwera NGINX][nginx-ingress]

Możesz również wykonać następujące czynności:

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących z dynamicznym publicznym adresem IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-tls]
- [Tworzenie kontrolera danych przychodzących za pomocą statyczny publiczny adres IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
