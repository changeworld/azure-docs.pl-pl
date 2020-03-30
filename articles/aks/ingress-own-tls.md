---
title: Używanie własnych certyfikatów TLS dla transferu przychodzącego za pomocą klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować kontroler transferu danych przychodzących NGINX, który używa własnych certyfikatów w klastrze usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: e567f5384cdd1e40ea67284713a29a92ee87af7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595505"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera ruchu przychodzącego HTTPS i używanie własnych certyfikatów protokołu TLS w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler transferu danych przychodzących NGINX][nginx-ingress] w klastrze usługi Azure Kubernetes (AKS). Generujesz własne certyfikaty i tworzysz klucz tajny Kubernetes do użycia z marszrutą transferu danych przychodzących. Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każda jest dostępna za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub ze [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa helm zainstalować kontroler transferu danych przychodzących NGINX i przykładowej aplikacji sieci web. Musisz mieć Helm zainicjowane w klastrze AKS i przy użyciu konta usługi dla Tiller. Upewnij się, że używasz najnowszej wersji helm. Aby uzyskać instrukcje uaktualniania, zobacz [dokumenty instalacji helm][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania helmu, zobacz [Instalowanie aplikacji z helmem w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, że są uruchomione interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Aby utworzyć kontroler transferu danych `Helm` przychodzących, należy użyć do *zainstalowania nginx-ingress*. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Podczas instalacji publiczny adres IP platformy Azure jest tworzony dla kontrolera transferu danych przychodzących. Ten publiczny adres IP jest statyczny dla trwałości kontrolera transferu danych przychodzących. Jeśli usuniesz kontroler transferu danych przychodzących, przypisanie publicznego adresu IP zostanie utracone. Jeśli następnie utworzysz dodatkowy kontroler transferu danych przychodzących, zostanie przypisany nowy publiczny adres IP. Jeśli chcesz zachować użycie publicznego adresu IP, możesz zamiast [tego utworzyć kontroler transferu danych przychodzących ze statycznym publicznym adresem IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia. Trwa kilka minut, aby adres IP został przypisany do usługi.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Zanotuj ten publiczny adres IP, ponieważ jest on używany w ostatnim kroku do testowania wdrożenia.

Nie utworzono jeszcze żadnych reguł ruchu przychodzącego. Jeśli przejdziesz do publicznego adresu IP, zostanie wyświetlona domyślna strona 404 kontrolera ruchu przychodzącego NGINX.

## <a name="generate-tls-certificates"></a>Generowanie certyfikatów TLS

W tym artykule wygenerujmy `openssl`certyfikat z podpisem własnym za pomocą pliku . Do użytku w produkcji należy zażądać zaufanego, podpisanego certyfikatu za pośrednictwem dostawcy lub własnego urzędu certyfikacji.For production use, you should request a trusted, signed certificate through a provider or your own certificate authority (CA). W następnym kroku wygenerujesz *klucz tajny* Kubernetes przy użyciu certyfikatu TLS i klucza prywatnego wygenerowanego przez OpenSSL.

Poniższy przykład generuje 2048-bitowy certyfikat RSA X509 ważny przez 365 dni o nazwie *aks-ingress-tls.crt*. Plik klucza prywatnego nosi nazwę *aks-ingress-tls.key*. Klucz tajny Protokołu TLS kubernetes wymaga obu tych plików.

Ten artykuł działa z nazwą pospolitą *podmiotu demo.azure.com* i nie wymaga zmiany. W przypadku zastosowań produkcyjnych określ własne `-subj` wartości organizacyjne dla tego parametru:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Tworzenie wpisu tajnego platformy Kubernetes dla certyfikatu protokołu TLS

Aby umożliwić kubernetes używać certyfikatu TLS i klucza prywatnego dla kontrolera transferu danych przychodzących, należy utworzyć i użyć klucza tajnego. Klucz tajny jest zdefiniowany raz i używa certyfikatu i pliku klucza utworzonego w poprzednim kroku. Następnie odwołać się do tego klucza tajnego podczas definiowania tras przychodzących.

Poniższy przykład tworzy *tajną nazwę aks-ingress-tls:*

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Kontroler transferu danych przychodzących i klucz tajny z certyfikatem zostały skonfigurowane. Teraz uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji "Hello world".

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

Obie aplikacje są teraz uruchomione w klastrze kubernetes, jednak `ClusterIP`są one skonfigurowane z usługą typu . W związku z tym aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, należy utworzyć zasób transferu danych przychodzących Kubernetes. Zasób transferu danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch `https://demo.azure.com/` na adres jest `aks-helloworld`kierowany do usługi o nazwie . Ruch na `https://demo.azure.com/hello-world-two` adres jest kierowany `ingress-demo` do usługi. W tym artykule nie trzeba zmieniać tych nazw hostów demonstracyjnych. Do użytku w produkcji podaj nazwy określone jako część żądania certyfikatu i procesu generowania.

> [!TIP]
> Jeśli nazwa hosta określona podczas procesu żądania certyfikatu, nazwa CN, nie jest zgodna z hostem zdefiniowanym w marszruty transferu danych przychodzących, kontroler transferu danych przychodzących jest wyświetlany ostrzeżenie o *fałszywym poście kontrolera transferu danych przychodzących Kubernetes.* Upewnij się, że nazwy hosta routicy i transferu przychodzącego są zgodne.

Sekcja *tls* informuje trasę przychodzącą o użyciu wpisu Secret o nazwie *aks-ingress-tls* dla *demo.azure.com*hosta . Ponownie do użytku w produkcji określ własny adres hosta.

Utwórz plik `hello-world-ingress.yaml` o nazwie i skopiuj w poniższym przykładzie YAML.

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

Utwórz zasób transferu `kubectl apply -f hello-world-ingress.yaml` ruchu przychodzącego za pomocą polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Aby przetestować certyfikaty *demo.azure.com* przy użyciu naszego `curl` fałszywego demo.azure.com hosta, użyj i określ parametr *--resolve.* Ten parametr umożliwia mapowanie nazwy *demo.azure.com* na publiczny adres IP kontrolera transferu danych przychodzących. Określ publiczny adres IP własnego kontrolera transferu danych przychodzących, jak pokazano w poniższym przykładzie:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nie podano dodatkowej ścieżki z adresem, więc kontroler ruchu */* przychodzącego domyślnie do trasy. Zwracana jest pierwsza aplikacja demonstracyjna, jak pokazano na poniższym skróconym przykładzie:

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

Parametr *-v* w `curl` naszym poleceniu wyprowadza pełne informacje, w tym odebrany certyfikat TLS. W połowie drogi wyjściowej curl można sprawdzić, czy użyto własnego certyfikatu TLS. Parametr *-k* kontynuuje ładowanie strony, nawet jeśli używamy certyfikatu z podpisem własnym. Poniższy przykład pokazuje, że *wystawca: CN=demo.azure.com; Użyto certyfikatu O=aks-ingress-tls:*

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

Teraz dodaj *ścieżkę /hello-world-two* do `https://demo.azure.com/hello-world-two`adresu, na przykład . Zwracana jest druga aplikacja demonstracyjna z tytułem niestandardowym, jak pokazano na poniższym skróconym przykładzie:

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

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Usuń zwolnienia za `helm delete` pomocą polecenia. Poniższy przykład usuwa wdrożenia transferu ruchu przychodzącego NGINX i dwóch przykładowych aplikacji AKS hello world.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Następnie usuń repozytorium Helm dla aplikacji AKS hello world:

```console
helm repo remove azure-samples
```

Usuń trasę transferu danych przychodzących, która kierowała ruch do przykładowych aplikacji:

```console
kubectl delete -f hello-world-ingress.yaml
```

Usuń certyfikat tajny:

```console
kubectl delete secret aks-ingress-tls
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
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub ze [statycznym publicznym adresem IP][aks-ingress-static-tls]

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
[client-source-ip]: concepts-network.md#ingress-controllers
