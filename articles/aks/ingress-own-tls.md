---
title: Certyfikaty protokołu TLS na użytek ruch przychodzący z klastra Azure Kubernetes Service (AKS)
description: Informacje o sposobie instalowania i konfigurowania kontrolera danych przychodzących NGINX, który używa własnych certyfikatów w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 4ba38ee0a4c26a99b7cbddc46eef35cfc39a511d
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392566"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera danych przychodzących HTTPS i używanie certyfikatów protokołu TLS w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule przedstawiono sposób wdrażania [kontrolera danych przychodzących NGINX] [ nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Generowanie własne certyfikaty i tworzyć rozwiązania Kubernetes wpisu tajnego do użytku z trasą transferu danych przychodzących. Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- Utwórz kontroler danych przychodzących, który używa umożliwia szyfrowanie, aby automatycznie wygenerować certyfikaty protokołu TLS [za pomocą dynamicznego publicznego adresu IP] [ aks-ingress-tls] lub [ze statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa narzędzia Helm do zainstalowania serwera NGINX kontrolera danych przychodzących i przykładową aplikację sieci web. Musisz mieć narzędzia Helm inicjowane w obrębie klastra usługi AKS i przy użyciu konta usługi dla Tiller. Upewnij się, że używasz najnowszej wersji narzędzia Helm. Aby uzyskać instrukcje uaktualniania, zobacz [Helm zainstalować docs][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania narzędzia Helm, zobacz [instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera danych przychodzących

Aby utworzyć kontroler danych przychodzących, użyj `Helm` zainstalował *ruch przychodzący serwera nginx*. Dodano nadmiarowość dwóch replik kontrolerów ruch przychodzący serwera NGINX są wdrażane przy użyciu `--set controller.replicaCount` parametru. Aby w pełni korzystać z systemem replik kontrolera danych przychodzących, upewnij się, że istnieje więcej niż jeden węzeł w klastrze AKS.

Kontroler danych przychodzących musi odbywać się w węźle systemu Linux. Węzły systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS) nie należy uruchamiać kontrolera danych przychodzących. Selektor węzła jest określony, przy użyciu `--set nodeSelector` parametru, aby poinformować harmonogram Kubernetes, aby uruchomić kontroler danych przychodzących NGINX w węźle opartych na systemie Linux.

> [!TIP]
> Poniższy przykład obejmuje tworzenie przestrzeni nazw Kubernetes, transferu danych przychodzących zasobów o nazwie *basic ruch przychodzący*. Określ obszar nazw dla Twojego środowiska, zgodnie z potrzebami. Jeśli klaster AKS nie jest włączone RBAC, Dodaj `--set rbac.create=false` polecenia narzędzia Helm.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Podczas instalacji Azure publiczny adres IP jest tworzona dla kontrolera danych przychodzących. Ten publiczny adres IP to statyczny dla żywotności kontrolera danych przychodzących. Jeśli usuniesz kontrolera danych przychodzących, przypisanie publicznego adresu IP zostanie utracony. Jeśli następnie utworzysz kontroler dodatkowy ruch przychodzący, nowy publiczny adres IP zostanie przypisany. Jeśli chcesz zachować użycie publicznego adresu IP, możesz zamiast tego [utworzyć kontroler danych przychodzących z statyczny publiczny adres IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia. Trwa kilka minut, aż adres IP, który można przypisać do usługi.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Zanotuj ten publiczny adres IP, ponieważ jest używany w ostatnim kroku do przetestowania wdrożenia.

Utworzono jeszcze żadnych reguł ruchu przychodzącego. Po przejściu do publicznego adresu IP, zostanie wyświetlona strona 404 domyślna serwera NGINX kontrolera danych przychodzących.

## <a name="generate-tls-certificates"></a>Generowanie certyfikatów protokołu TLS

W tym artykule teraz wygenerować certyfikat z podpisem własnym za pomocą `openssl`. Do użytku produkcyjnego należy zażądać certyfikatu zaufanych za pośrednictwem dostawcy lub własnego urzędu certyfikacji (CA). W następnym kroku Generowanie Kubernetes *klucz tajny* przy użyciu certyfikatu TLS i klucz prywatny, generowane przez OpenSSL.

Poniższy przykład generuje 2048-bitowy klucz RSA X 509 certyfikatu, odpowiedniego do 365 dni o nazwie *aks — ruch przychodzący tls.crt*. Nosi nazwę pliku klucza prywatnego *aks — ruch przychodzący tls.key*. Klucz tajny protokołu TLS Kubernetes wymaga obu tych plików.

W tym artykule w programach *demo.azure.com* nazwa pospolita podmiotu i nie muszą zostać zmienione. Do użytku produkcyjnego określić własne wartości organizacji `-subj` parametru:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Tworzenie wpisu tajnego dla certyfikatu TLS rozwiązania Kubernetes

Aby umożliwić Kubernetes, należy użyć protokołu TLS, certyfikat i klucz prywatny dla kontrolera danych przychodzących, utworzyć i używać hasła. Klucz tajny jest zdefiniowana raz i używa certyfikatu i klucza pliku utworzonego w poprzednim kroku. Podczas definiowania ruch przychodzący trasy, następnie odwołać ten klucz tajny.

Poniższy przykład tworzy Nazwa wpisu tajnego *aks — ruch przychodzący tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler danych przychodzących i wpisu tajnego za pomocą certyfikatu. Teraz sklonujemy wykonywania dwóch pokaz aplikacji w klastrze AKS. W tym przykładzie Helm służy do wdrażania dwa wystąpienia aplikacji proste "Hello world".

Przed zainstalowaniem wykresów rozwiązania Helm przykładowe Dodaj repozytorium przykładów dla platformy Azure w środowisku Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Tworzenie pierwszej aplikacji demonstracyjnej z planu narzędzia Helm, za pomocą następującego polecenia:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz zainstalować drugie wystąpienie aplikacji pokazowej. Dla drugiego wystąpienia należy określić nowy tytuł, aby wizualnie różniące się od dwóch aplikacji. Możesz również określić unikatową nazwę usługi:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Utwórz trasę w protokole transferu danych przychodzących

Obie aplikacje są uruchomione w klastrze Kubernetes, ale są one konfigurowane przy użyciu usługi typu `ClusterIP`. Jako takie aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób transferu danych przychodzących rozwiązania Kubernetes. Zasób ruch przychodzący konfiguruje zasady, które kierują ruch do jednego z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `https://demo.azure.com/` jest kierowany do usługi o nazwie `aks-helloworld`. Ruch do adresu `https://demo.azure.com/hello-world-two` jest kierowany do `ingress-demo` usługi. W tym artykule nie trzeba zmieniać tych nazw hostów pokaz. Do użytku produkcyjnego należy podać nazwy określone jako część procesu żądania i generowania certyfikatów.

> [!TIP]
> Jeśli nazwa hosta określona w procesie żądania certyfikatu, Nazwa CN nie być dopasowana do hosta, zdefiniowane w trasy ruch przychodzący, możesz transferu danych przychodzących kontroler Wyświetla *certyfikatu fałszywe kontrolera transferu danych przychodzących rozwiązania Kubernetes*. Upewnić się, że certyfikat i ruch przychodzący trasy dopasowania nazwy hosta.

*Tls* sekcji informuje trasy ruch przychodzący do użycia klucza tajnego, o nazwie *aks — ruch przychodzący tls* dla hosta *demo.azure.com*. Ponownie w przypadku użycia w środowisku produkcyjnym należy określić adres hosta.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj poniższy przykład kodu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

Tworzenie przy użyciu zasobów ruch przychodzący `kubectl apply -f hello-world-ingress.yaml` polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Do przetestowania certyfikaty z naszych sfałszowana *demo.azure.com* hosta, użyj `curl` i określ *— rozwiązania* parametru. Ten parametr umożliwia mapowanie *demo.azure.com* nazwa publiczny adres IP kontrolera danych przychodzących. Określ publiczny adres IP swojego własnego kontrolera danych przychodzących, jak pokazano w poniższym przykładzie:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nie dodatkowe ścieżki został podany adres, więc kontrolera danych przychodzących wartość domyślna to */* trasy. Pierwszy aplikacji pokazowej zostaną zwrócone, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

*- V* parametru w naszym `curl` polecenie wyświetla pełne informacje, w tym certyfikat TLS odebrany. W połowie za pomocą programu curl danych wyjściowych, można sprawdzić, użyto certyfikatu TLS. *-K* parametr nadal ładowania strony, mimo że firma Microsoft korzysta z certyfikatu z podpisem własnym. Poniższy przykład pokazuje, że *wystawcy: CN=Demo.Azure.com; O = aks — ruch przychodzący tls* użyto certyfikatu:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Teraz Dodaj */hello-world-two* ścieżkę na adres, takie jak `https://demo.azure.com/hello-world-two`. Zwracana jest drugiej aplikacji demonstracyjnej z tytułem niestandardowych, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule używane narzędzia Helm do zainstalowania składników transferu danych przychodzących i przykładowe aplikacje. Podczas wdrażania wykresu Helm tworzonych wiele zasobów Kubernetes. Te zasoby obejmują zasobników, wdrożenia i usług. Aby wyczyścić te zasoby, albo można usunąć przestrzeni nazw cały przykładowy lub poszczególnych zasobów.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuń przestrzeń nazw przykładowy i wszystkie zasoby

Aby usunąć cały przykładowy przestrzeni nazw, należy użyć `kubectl delete` polecenia i podaj nazwę swojej przestrzeni nazw. Zostaną usunięte wszystkie zasoby w przestrzeni nazw.

```console
kubectl delete namespace ingress-basic
```

Następnie usuń repozytorium narzędzia Helm dla usługi AKS hello world aplikacji:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Aby usunąć zasoby pojedynczo

Alternatywnie bardziej szczegółowego podejścia polega na usunięciu poszczególnych zasobów, które są tworzone. Lista Helm zwalnia z `helm list` polecenia. Wyszukaj wykresy o nazwie *ruch przychodzący serwera nginx* i *aks-helloworld*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Usuń wersjach z `helm delete` polecenia. Poniższy przykład usuwa wdrożenia ruch przychodzący serwera NGINX i dwie przykładowe AKS Witaj świecie aplikacje.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Następnie usuń repozytorium narzędzia Helm dla usługi AKS hello world aplikacji:

```console
helm repo remove azure-samples
```

Usuwanie trasy transferu danych przychodzących, który kierowany ruch do aplikacji przykładowej:

```console
kubectl delete -f hello-world-ingress.yaml
```

Usuń certyfikat klucza tajnego:

```console
kubectl delete secret aks-ingress-tls
```

Na koniec można usunąć samego obszaru nazw. Użyj `kubectl delete` polecenia i podaj nazwę swojej przestrzeni nazw:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule uwzględnione niektóre składniki zewnętrzne w usłudze AKS. Aby dowiedzieć się więcej na temat tych składników, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia narzędzia Helm][helm-cli]
- [Kontroler danych przychodzących serwera NGINX][nginx-ingress]

Możesz również wykonać następujące czynności:

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- Utwórz kontroler danych przychodzących, który używa umożliwia szyfrowanie, aby automatycznie wygenerować certyfikaty protokołu TLS [za pomocą dynamicznego publicznego adresu IP] [ aks-ingress-tls] lub [ze statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
