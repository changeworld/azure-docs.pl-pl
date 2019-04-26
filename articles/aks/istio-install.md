---
title: Zainstaluj Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Istio utworzyć siatki usługi klastra Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465346"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Istio w usłudze Azure Kubernetes Service (AKS)

[Istio] [ istio-github] jest siatki usługi typu open source, zapewniająca klucza zestaw funkcji w mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczeń, wymuszanie zasad i observability. Aby uzyskać więcej informacji na temat Istio można znaleźć w oficjalnej [co to jest Istio?] [ istio-docs-concepts] dokumentacji.

W tym artykule pokazano, jak zainstalować Istio. Istio `istioctl` binarnych klienta zostanie zainstalowana na komputerze klienckim, a następnie składniki Istio są zainstalowane w klastrze Kubernetes w usłudze AKS. Te instrukcje odwoływać się do wersji Istio *1.0.4*. Możesz znaleźć dodatkowe wersje Istio na [GitHub — wersje Istio][istio-github-releases].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz Istio
> * Zainstaluj klienta Istio binarne
> * Zainstaluj składniki Istio Kubernetes
> * Sprawdzanie poprawności instalacji

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach szczegółowo opisanych w tym artykule przyjęto założenie, został utworzony klaster AKS (Kubernetes 1.10 i nowszych, przy użyciu RBAC włączone), a `kubectl` połączenia z klastrem. Jeśli potrzebujesz pomocy przy użyciu dowolnego z tych elementów, zobacz [szybkiego startu usługi AKS][aks-quickstart].

Aby zainstalować Istio, musisz mieć [Helm] [ helm] wersji *2.10.0* lub później poprawnie zainstalowany i skonfigurowany w klastrze. Jeśli potrzebujesz pomocy przy instalowaniu narzędzia Helm, zobacz [wskazówki dotyczące instalacji narzędzia Helm AKS][helm-install]. Jeśli nie masz co najmniej wersji *2.10.0* Helm zainstalowane, uaktualnienia lub zobacz [Istio - instalacji za pomocą narzędzia Helm przewodnik] [ istio-install-helm] inne opcje instalacji.

W tym artykule oddziela wskazówki dotyczące instalacji Istio na kilka kroków dyskretnych. Każdy z tych kroków jest opisane, Dowiedz się, jak zainstalować Istio i Dowiedz się, jak działa Istio przy użyciu rozwiązania Kubernetes. Efekt jest taki sam sposób, w strukturze jako oficjalna instalację Istio [wskazówki][istio-install-k8s-quickstart].

## <a name="download-istio"></a>Pobierz Istio

Najpierw należy pobrać i wyodrębnić najnowsze wydanie Istio. Kroki różnią się nieco dla powłoki bash w systemie MacOS, Linux lub podsystemu Windows dla systemu Linux oraz dla powłoki PowerShell. Wybierz jeden z następujących instalacji kroków dla preferowanego środowiska:

* [Powłoka bash w systemie MacOS, Linux lub podsystemu Windows dla systemu Linux](#bash)
* [Program PowerShell](#powershell)

### <a name="bash"></a>Bash

W systemie MacOS, należy użyć `curl` Aby pobrać najnowszą wersję Istio, a następnie wyodrębnij z `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

W systemie Linux lub podsystem Windows dla systemu Linux, należy użyć `curl` Aby pobrać najnowszą wersję Istio, a następnie wyodrębnij z `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

W programie PowerShell użyj [Invoke-WebRequest] [ Invoke-WebRequest] Aby pobrać najnowszą wersję Istio, a następnie wyodrębnij z [rozwiń archiwum] [ Expand-Archive] jako następujące:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Zainstaluj klienta Istio binarne

`istioctl` Binarnych klienta jest uruchamiane na komputerze klienckim i umożliwia zarządzanie Istio reguł routingu i zasad. Ponownie kroki instalacji różnią się nieco między systemami operacyjnymi klienta. Wybierz jeden z następujących instalacji kroków dla swoim preferowanym środowisku.

> [!IMPORTANT]
> Upewnij się, wykonaj kroki w tej sekcji z folderu najwyższego poziomu z wersji Istio pobrane i wyodrębnione.

### <a name="macos"></a>MacOS

Aby zainstalować Istio `istioctl` binarnych klienta w powłoce oparte na funkcji bash w systemie MacOS, należy użyć następujących poleceń. Skopiuj te polecenia `istioctl` klienta binarne do lokalizacji programu użytkownika standardowego w Twojej `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Jeśli chcesz zakończenia wiersza polecenia dla Istio `istioctl` klienta binarny, następnie skonfigurować go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Teraz przejść do sekcji, aby [Zainstaluj składniki usługi Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux lub podsystem Windows dla systemu Linux

Użyj następujących poleceń, aby zainstalować Istio `istioctl` klienta binarne w powłoce bash, oparte na systemie Linux lub [podsystem Windows dla systemu Linux][install-wsl]. Skopiuj te polecenia `istioctl` klienta binarne do lokalizacji programu użytkownika standardowego w Twojej `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Jeśli chcesz zakończenia wiersza polecenia dla Istio `istioctl` klienta binarny, następnie skonfigurować go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Teraz przejść do sekcji, aby [Zainstaluj składniki usługi Istio Kubernetes](#install-the-istio-kubernetes-components).

### <a name="windows"></a>Windows

Aby zainstalować Istio `istioctl` binarnych klienta w powłoce oparte na programie Powershell na Windows, użyj następujących poleceń. Skopiuj te polecenia `istioctl` binarnych klienta z nowym użytkownikiem programu lokalizacji i był dostępny za pośrednictwem usługi `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Zainstaluj składniki Istio Kubernetes

> [!IMPORTANT]
> Upewnij się, wykonaj kroki w tej sekcji z folderu najwyższego poziomu z wersji Istio pobrane i wyodrębnione.

> [!NOTE]
> Wersja `1.0.6` i nowszej wykresu Istio Helm ma istotne zmiany. Jeśli wybierzesz, aby zainstalować tę wersję, teraz należy ręcznie utworzyć wpis tajny dla Kiali. Należy również ręcznie utworzyć wpis tajny do narzędzia Grafana, jeśli zostały ustawione `grafana.security.enabled=true`. Zobacz wykresu Helm Istio [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) więcej szczegółowych informacji dotyczących sposobu tworzenia tych kluczy tajnych.

Aby zainstalować składniki Istio do klastra usługi AKS, użyj narzędzia Helm. Instalowanie zasobów Istio do `istio-system` przestrzeni nazw i Włącz dodatkowe opcje zabezpieczeń i monitorowania w następujący sposób:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Wykresu Helm wdraża dużą liczbę obiektów. Wdrożenie może potrwać 2 – 3 minut, w zależności od środowiska klastra.

## <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji

Aby upewnić się, że masz pomyślne wdrożenie Istio, możemy sprawdzić poprawność instalacji.

Najpierw upewnij się, czy oczekiwany usługi zostały utworzone. Użyj [kubectl get-svc] [ kubectl-get] polecenie, aby wyświetlić uruchomionych usług. Zapytanie *istio systemu* przestrzeni nazw, w którym Istio i dodatek składniki zostały zainstalowane przez wykresu Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Następujące przykładowe dane wyjściowe pokazuje usług, które powinna teraz być uruchomiona:

- *istio -** usług
- * jaeger-**, *śledzenia*, i *zipkin* dodatkowych śledzenia usług
- *prometheus* usługa metryk dodatku
- *grafana* dodatek analizy i monitorowania usługi Pulpit nawigacyjny
- *kiali* dodatek usługi Pulpit nawigacyjny siatki

Jeśli `istio-ingressgateway` pokazuje zewnętrzny adres ip, z `<pending>`, poczekaj kilka minut, zanim został przypisany adres IP, sieci platformy Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Następnie upewnij się, czy zostały utworzone wymagane zasobników. Użyj [kubectl get pods-] [ kubectl-get] poleceń i ponownie zapytanie *istio systemu* przestrzeni nazw:

```console
kubectl get pods --namespace istio-system
```

Następujące przykładowe dane wyjściowe pokazuje zasobników, które są uruchomione:

- *istio -** zasobników
- *prometheus -** zasobnika metryki dodatku
- *grafana -** dodatek analizy i monitorowania zasobnika pulpitu nawigacyjnego
- *kiali* zasobnika pulpitu nawigacyjnego siatki usługi dodatku

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Pokaż wszystkie zasobników stan `Running`. Jeśli zasobników nie mają tych stanów, poczekaj minutę lub dwie robią. Jeśli wszystkie zasobników zgłosić problem, użyj [kubectl opisują zasobnika] [ kubectl-describe] polecenie, aby przejrzeć swoje dane wyjściowe i stanu.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

Liczba dodatków zostały zainstalowane Istio w naszej konfiguracji powyżej zapewniające dodatkowe funkcje. Interfejsy użytkownika dla dodatków nie są widoczne publicznie za pośrednictwem adresu ip zewnętrznej. Aby uzyskać dostęp do interfejsów użytkownika dodatku, należy użyć [kubectl portu na następny okres] [ kubectl-port-forward] polecenia. To polecenie tworzy bezpiecznego połączenia między portu lokalnego na komputerze klienckim i odpowiednie zasobnik w klastrze AKS.

### <a name="grafana"></a>Grafana

Analizy i pulpity nawigacyjne monitorowania dla Istio [Grafana][grafana]. Przekazuj port lokalny *3000* na komputerze klienckim do portu *3000* na zasobnik uruchomioną Grafana klastra usługi AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Następujące przykładowe dane wyjściowe pokazuje konfigurowanych Grafana przekazywanie portu:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Możesz teraz uzyskiwać dostęp Grafana następującego adresu URL na komputerze klienckim - [ http://localhost:3000 ](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

Wyrażenie przeglądarki pod kątem metryki są dostarczane przez [Prometheus][prometheus]. Przekazuj port lokalny *9090* na komputerze klienckim do portu *9090* na zasobnik uruchomioną Prometheus klastra usługi AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Następujące przykładowe dane wyjściowe pokazuje konfigurowanych Prometheus przekazywanie portu:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Możesz teraz uzyskiwać dostęp przeglądarki wyrażenie Prometheus następującego adresu URL na komputerze klienckim - [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Śledzenie interfejsu użytkownika są dostarczane przez [Jaeger][jaeger]. Przekazuj port lokalny *16686* na komputerze klienckim do portu *16686* na zasobnik uruchomioną Jaeger klastra usługi AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Następujące przykładowe dane wyjściowe pokazuje konfigurowanych Jaeger przekazywanie portu:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Możesz teraz uzyskiwać dostęp interfejsu użytkownika śledzenia Jaeger następującego adresu URL na komputerze klienckim - [ http://localhost:16686 ](http://localhost:16686).

### <a name="kiali"></a>Kiali

Pulpit nawigacyjny usług siatki observability są dostarczane przez [Kiali][kiali]. Przekazuj port lokalny *20001* na komputerze klienckim do portu *20001* na zasobnik uruchomioną Kiali klastra usługi AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Następujące przykładowe dane wyjściowe pokazuje konfigurowanych Kiali przekazywanie portu:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Teraz możesz połączyć Kiali siatki observability pulpit nawigacyjny usługi pod adresem URL, na komputerze klienckim - [ http://localhost:20001 ](http://localhost:20001).

Domyślna nazwa użytkownika i hasło dla pulpitu nawigacyjnego Kiali *username:admin / hasło: admin*. Te poświadczenia można skonfigurować za pomocą *kiali.dashboard.username* i *kiali.dashboard.passphrase* Helm wartości.

## <a name="next-steps"></a>Kolejne kroki

Aby zobaczyć, jak używać Istio inteligentnego routingu między wiele wersji aplikacji i wdrażanie wydania canary, dokumentacji:

> [!div class="nextstepaction"]
> [Inteligentne scenariusz routingu AKS Istio][istio-scenario-routing]

Aby poznać więcej opcji instalacji i konfiguracji Istio, zobacz następujące artykuły Istio oficjalne:

- [Istio — instalacja Kubernetes — Szybki Start][istio-install-k8s-quickstart]
- [Istio — Przewodnik instalacji narzędzia Helm][istio-install-helm]
- [Istio — opcje instalacji narzędzia Helm][istio-install-helm-options]

Można również wykonać dodatkowe scenariusze za pomocą [Przykładowa aplikacja Bookinfo Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
