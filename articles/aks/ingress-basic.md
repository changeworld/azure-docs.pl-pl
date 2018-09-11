---
title: Tworzenie kontrolera podstawowego ruch przychodzący w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować podstawowy kontroler danych przychodzących NGINX w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 3ae7a3193e0a4bacc64524f477b6c179ead20b6b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357258"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera danych przychodzących w usłudze Azure Kubernetes Service (AKS)

Kontroler danych przychodzących jest to oprogramowanie, które zapewnia zwrotny serwer proxy, routing ruchu można konfigurować i zakończenie protokołu TLS dla usługi Kubernetes. Zasoby danych przychodzących rozwiązania Kubernetes są używane do konfigurowania reguł ruchu przychodzącego i trasy dla poszczególnych usług Kubernetes. Korzystanie z reguł ruchu przychodzącego i kontroler danych przychodzących, pojedynczy adres IP może służyć do kierowania ruchu do wielu usług w klastrze Kubernetes.

W tym artykule przedstawiono sposób wdrażania [kontrolera danych przychodzących NGINX] [ nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Dwie aplikacje są następnie uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- [Tworzenie kontrolera danych przychodzących z dynamicznym publicznym adresem IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-tls]
- [Tworzenie kontrolera danych przychodzących za pomocą statyczny publiczny adres IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa narzędzia Helm do zainstalowania serwera NGINX kontrolera danych przychodzących, Menedżer certyfikatów i przykładową aplikację sieci web. Musisz mieć narzędzia Helm inicjowane w obrębie klastra usługi AKS i przy użyciu konta usługi dla Tiller. Aby uzyskać więcej informacji na temat konfigurowania i używania narzędzia Helm, zobacz [instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera danych przychodzących

Aby utworzyć kontroler danych przychodzących, użyj `Helm` zainstalował *ruch przychodzący serwera nginx*.

> [!TIP]
> W poniższym przykładzie instalowana kontroler danych przychodzących w `kube-system` przestrzeni nazw. Jeśli to konieczne, można określić innej przestrzeni nazw dla Twojego środowiska. Jeśli klaster AKS nie jest włączone RBAC, Dodaj `--set rbac.create=false` poleceń.

```console
helm install stable/nginx-ingress --namespace kube-system
```

Po utworzeniu rozwiązania Kubernetes usługi równoważenia obciążenia dla kontrolera danych przychodzących NGINX jest przypisany dynamicznego publicznego adresu IP, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
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

W poniższym przykładzie ruch do adresu `http://40.117.74.8/` jest kierowany do usługi o nazwie `aks-helloworld`. Ruch do adresu `http://40.117.74.8/hello-world-two` jest kierowany do `ingress-demo` usługi.

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

Aby przetestować trasy dla kontrolera danych przychodzących, przejdź do dwóch aplikacji. Otwórz przeglądarkę internetową na adres IP serwera NGINX kontrolera danych przychodzących, takich jak *http://40.117.74.8*. Pierwszy aplikacji pokazowej zostanie wyświetlona w przeglądarce sieci web, jak pokazano w przykładzie poniżej:

![Pierwsza aplikacja działające poza usługą kontrolera danych przychodzących](media/ingress-basic/app-one.png)

Teraz Dodaj */hello-world-two* ścieżki do adresu IP adres, takie jak *http://40.117.74.8/hello-world-two*. Zostanie wyświetlony drugi aplikacji demonstracyjnej z tytułem niestandardowe:

![Drugiej aplikacji działające poza usługą kontrolera danych przychodzących](media/ingress-basic/app-two.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule uwzględnione niektóre składniki zewnętrzne w usłudze AKS. Aby dowiedzieć się więcej na temat tych składników, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia narzędzia Helm][helm-cli]
- [Kontroler danych przychodzących serwera NGINX][nginx-ingress]

Możesz również wykonać następujące czynności:

- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- [Tworzenie kontrolera danych przychodzących z dynamicznym publicznym adresem IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-tls]
- [Tworzenie kontrolera danych przychodzących za pomocą statyczny publiczny adres IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
