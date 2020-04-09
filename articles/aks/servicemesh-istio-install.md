---
title: Instalowanie programu Istio w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak zainstalować i używać programu Istio do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877698"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie programu Istio w usłudze Azure Kubernetes (AKS)

[Istio][istio-github] to siatka usługi typu open source, która zapewnia kluczowy zestaw funkcji w mikrousługach w klastrze Kubernetes. Funkcje te obejmują zarządzanie ruchem, tożsamość usługi i bezpieczeństwo, wymuszanie zasad i obserwowalność. Aby uzyskać więcej informacji na temat Istio, zobacz [oficjalnej Dokumentacji Istio?][istio-docs-concepts]

W tym artykule pokazano, jak zainstalować Istio. Binarny `istioctl` klienta Istio jest zainstalowany na komputerze klienckim, a składniki Istio są instalowane w klastrze Kubernetes w u usługi AKS.

> [!NOTE]
> Poniższe instrukcje odwołują się do wersji `1.4.0`Istio .
>
> Wersje `1.4.x` Istio zostały przetestowane przez zespół Istio przeciwko `1.13`wersji `1.14` `1.15`Kubernetes , , . Dodatkowe wersje Istio można znaleźć w [GitHub - Istio Releases][istio-github-releases], informacje o każdej z wersji w [Istio News][istio-release-notes] i obsługiwane wersje Kubernetes w [Istio General FAQ][istio-faq].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz i zainstaluj binarny klienta Istio istioctl
> * Zainstaluj Istio na AKS
> * Sprawdzanie poprawności instalacji Istio
> * Dostęp do dodatków
> * Odinstaluj Istio z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

Kroki opisane w tym artykule założono, że utworzono klaster AKS (Kubernetes `1.13` i `kubectl` powyżej, z włączoną funkcją RBAC) i nawiązałeś połączenie z klastrem. Jeśli potrzebujesz pomocy w przypadku któregokolwiek z tych elementów, zobacz [przewodnik Szybki start usługi AKS][aks-quickstart].

Upewnij się, że zostały przeczytane [istio wydajność i skalowalność](https://istio.io/docs/concepts/performance-and-scalability/) dokumentacji, aby zrozumieć dodatkowe wymagania dotyczące zasobów dla uruchamiania programu Istio w klastrze AKS. Wymagania dotyczące rdzenia i pamięci będą się różnić w zależności od określonego obciążenia. Wybierz odpowiednią liczbę węzłów i rozmiar maszyny Wirtualnej, aby zapewnić konfigurację.

W tym artykule oddzieli wskazówki dotyczące instalacji istio na kilka dyskretnych kroków. Efekt końcowy jest taki sam w strukturze jak oficjalne [wytyczne][istio-install-istioctl]instalacji Istio .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Zainstaluj komponenty Istio na AKS

Będziemy instalować [Grafana][grafana] i [Kiali][kiali] w ramach naszej instalacji Istio. Grafana zapewnia pulpity analityczne i monitorujące, a Kiali zapewnia pulpit nawigacyjny obserwacji siatki usług. W naszej konfiguracji każdy z tych składników wymaga poświadczeń, które muszą być dostarczone jako [klucz tajny.][kubernetes-secrets]

Zanim będziemy mogli zainstalować komponenty Istio, musimy stworzyć sekrety zarówno dla Grafany, jak i Kiali. Te wpisy tajne muszą `istio-system` być zainstalowane w przestrzeni nazw, które będą używane przez Istio, więc musimy utworzyć obszar nazw też. Musimy użyć `--save-config` tej opcji podczas tworzenia obszaru `kubectl create` nazw za pośrednictwem, aby `kubectl apply` instalator Istio mógł działać na tym obiekcie w przyszłości.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Instalowanie komponentów Istio

Teraz, gdy udało nam się utworzyć tajemnice Grafany i Kiali w naszym klastrze AKS, nadszedł czas, aby zainstalować składniki Istio. 

Podejście do instalacji [Helm][helm] dla Istio zostanie przestarzałe w przyszłości. Nowe podejście instalacyjne dla Istio wykorzystuje binarny `istioctl` klienta, profile [konfiguracyjne Istio][istio-configuration-profiles]oraz nową [specyfikację płaszczyzny sterowania Istio i api.][istio-control-plane] To nowe podejście jest to, co będziemy używać do zainstalowania Istio.

> [!NOTE]
> Istio obecnie musi być zaplanowane do uruchomienia w węzłach Systemu Linux. Jeśli masz węzły systemu Windows Server w klastrze, należy upewnić się, że zasobniki Istio są zaplanowane tylko do uruchomienia w węzłach systemu Linux. Użyjemy [selektorów węzłów,][kubernetes-node-selectors] aby upewnić się, że zasobniki są zaplanowane do odpowiednich węzłów.

> [!CAUTION]
> Funkcje [SDS (secret discovery service)][istio-feature-sds] i [Istio CNI][istio-feature-cni] Istio są obecnie w [Alfa][istio-feature-stages], więc należy pomyśleć, zanim je włączą. 
>
> Należy zauważyć, że funkcja Kubernetes [projekcji woluminu tokenu usługi][kubernetes-feature-sa-projected-volume] (wymóg dla SDS) jest teraz **włączona** dla wszystkich wersji Kubernetes 1.13 i nowszych w usłudze AKS.

Utwórz plik `istio.aks.yaml` wywoływany z następującą zawartością. Ten plik będzie zawierać szczegóły [specyfikacji płaszczyzny sterowania Istio][istio-control-plane] do konfigurowania Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Zainstaluj istio `istioctl apply` za pomocą `istio.aks.yaml` polecenia i powyższego pliku specyfikacji płaszczyzny sterowania Istio w następujący sposób:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Instalator wdroży szereg [crd,][kubernetes-crd] a następnie zarządzać zależności zainstalować wszystkie odpowiednie obiekty zdefiniowane dla tej konfiguracji Istio. Powinieneś zobaczyć coś takiego jak poniższy fragment kodu wyjściowego.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

W tym momencie wdrożono istio do klastra AKS. Aby upewnić się, że mamy udane wdrożenie Istio, przejdźmy do następnej sekcji, aby [zweryfikować instalację Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Sprawdzanie poprawności instalacji Istio

Najpierw upewnij się, że oczekiwane usługi zostały utworzone. Użyj [polecenia kubectl get svc,][kubectl-get] aby wyświetlić uruchomione usługi. Zapytanie `istio-system` o obszar nazw, w którym składniki Istio `istio` i add-on zostały zainstalowane przez wykres Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Poniższe przykładowe dane wyjściowe pokazują usługi, które powinny być teraz uruchomione:

- `istio-*`Usług
- `jaeger-*`, `tracing`i `zipkin` dodatkowe usługi śledzenia
- `prometheus`usługa metryk dodatku
- `grafana`dodatkowa usługa pulpitu nawigacyjnego i analizy
- `kiali`usługa sieci owejnych dodatków

Jeśli `istio-ingressgateway` pokazuje zewnętrznego adresu `<pending>`IP , poczekaj kilka minut, aż adres IP został przypisany przez sieć Platformy Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Następnie upewnij się, że utworzono wymagane zasobniki. Użyj [polecenia kubectl get strąki][kubectl-get] `istio-system` i ponownie kwerenda obszaru nazw:

```console
kubectl get pods --namespace istio-system
```

Poniższe przykładowe dane wyjściowe pokazują zasobników, które są uruchomione:

- strąki `istio-*`
- zasobnika `prometheus-*` metryk dodatku
- dodatkowa `grafana-*` podszycie analityczne i monitorujące pulpit nawigacyjny
- zasobnik `kiali` siatki usługi dodatku

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Wszystkie strąki powinny wykazywać `Running`status . Jeśli zasobniki nie mają tych stanów, poczekaj minutę lub dwie, aż to zrobią. Jeśli jakiekolwiek zasobników zgłosić problem, użyj [kubectl opisać pod][kubectl-describe] polecenia, aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

W powyższej konfiguracji istio zainstalowało kilka dodatków, które zapewniają dodatkową funkcjonalność. Aplikacje internetowe dla dodatków **nie** są udostępniane publicznie za pośrednictwem zewnętrznego adresu IP. 

Aby uzyskać dostęp do dodatkowych interfejsów `istioctl dashboard` użytkownika, użyj polecenia. To polecenie wykorzystuje [port kubectl do przodu][kubectl-port-forward] i losowy port, aby utworzyć bezpieczne połączenie między komputerem klienckim a odpowiednim zasobnikiem w klastrze AKS. Następnie automatycznie otworzy aplikację sieci web dodatku w domyślnej przeglądarce.

Dodaliśmy dodatkową warstwę zabezpieczeń dla grawany i Kiali, określając poświadczenia dla nich wcześniej w tym artykule.

### <a name="grafana"></a>Grafana

Pulpity analityczne i monitorujące dla Istio są dostarczane przez [Grafana][grafana]. Pamiętaj, aby użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Grafana wcześniej po wyświetleniu monitu. Otwórz bezpiecznie pulpit nawigacyjny Grafana w następujący sposób:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metryki dla Istio są dostarczane przez [Prometheus][prometheus]. Otwórz bezpiecznie pulpit nawigacyjny Prometheus w następujący sposób:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Śledzenie w Istio jest świadczone przez [Jaeger][jaeger]. Otwórz prawidłowo otwarty pulpit nawigacyjny Jaegera w następujący sposób:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali (Kiali)

Deska rozdzielcza do obserwacji siatki usługowej jest dostarczana przez [Kiali][kiali]. Pamiętaj, aby użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Kiali wcześniej po wyświetleniu monitu. Otwórz panel Kiali bezpiecznie w następujący sposób:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Dostępny jest prosty interfejs do serwerów proxy [Envoy.][envoy] Zawiera informacje o konfiguracji i metryki dla serwera proxy wysłannika działającego w określonym zasobniku. Otwórz interfejs Envoy bezpiecznie w następujący sposób:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Odinstaluj Istio z AKS

> [!WARNING]
> Usunięcie istio z uruchomionego systemu może spowodować problemy związane z ruchem między usługami. Upewnij się, że wprowadzono przepisy dotyczące systemu nadal działać poprawnie bez Istio przed kontynuowaniem.

### <a name="remove-istio-components-and-namespace"></a>Usuwanie składników i przestrzeni nazw Istio

Aby usunąć istio z klastra AKS, użyj `istioctl manifest generate` polecenia z plikiem `istio.aks.yaml` specyfikacji płaszczyzny sterowania Istio. Spowoduje to wygenerowanie wdrożonego manifestu, `kubectl delete` do którego będziemy potokować `istio-system` w celu usunięcia wszystkich zainstalowanych składników i obszaru nazw.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Usuń istio CRDs i tajemnice

Powyższe polecenia usuwają wszystkie składniki Istio i obszar nazw, ale nadal pozostaje nam wygenerowane wpisy tajne Istio. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można użyć istio do zapewnienia inteligentnego routingu do wdrożenia kanarek wersji:

> [!div class="nextstepaction"]
> [Inteligentny scenariusz routingu AKS Istio][istio-scenario-routing]

Aby zapoznać się z większą licześcią opcji instalacji i konfiguracji programu Istio, zobacz następujące oficjalne wskazówki dotyczące programu Istio:

- [Istio - prowadnice instalacyjne][istio-installation-guides]

Można również wykonać dodatkowe scenariusze za pomocą:

- [Przykład aplikacji Istio Bookinfo][istio-bookinfo-example]

Aby dowiedzieć się, jak monitorować aplikację AKS przy użyciu usługi Application Insights i Istio, zobacz następującą dokumentację usługi Azure Monitor:

- [Zero monitorowania aplikacji oprzyrządowania dla aplikacji hostowanych kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
