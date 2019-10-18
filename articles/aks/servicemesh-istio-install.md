---
title: Instalowanie Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Istio do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 9c9dcd567b8632626bf4b1f0bf2ef6b5e69b8a9d
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530448"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczenia, wymuszanie zasad oraz ich przestrzeganie. Aby uzyskać więcej informacji na temat Istio, zobacz oficjalny dokument dotyczący [Istio?][istio-docs-concepts] .

W tym artykule opisano sposób instalowania programu Istio. Plik binarny klienta Istio `istioctl` jest instalowany na komputerze klienckim, a składniki Istio są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Te instrukcje odwołują się do wersji Istio `1.3.2` i używają co najmniej Helm wersji `2.14.2`.
>
> Wersje `1.3.x` Istio zostały przetestowane przez zespół Istio w odniesieniu do wersji Kubernetes `1.13`, `1.14` `1.15`. Dodatkowe wersje Istio można znaleźć w artykułach usługi [GitHub-Istio][istio-github-releases], informacje o każdej z tych wersji w usłudze [Istio News][istio-release-notes] i obsługiwane wersje Kubernetes na [ogół często zadawane pytania][istio-faq]na temat Istio.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz i zainstaluj dane binarne klienta Istio istioctl
> * Zainstaluj Istio na AKS
> * Weryfikowanie instalacji Istio
> * Dostęp do dodatków
> * Odinstaluj Istio z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

Kroki opisane w tym artykule założono, że utworzono klaster AKS (Kubernetes `1.13` lub nowszy z włączoną funkcją RBAC) i nastąpiło połączenie `kubectl` z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart].

Musisz [Helm][helm] , aby wykonać te instrukcje i zainstalować Istio. Zalecane jest, aby Najnowsza stabilna wersja była zainstalowana i skonfigurowana w klastrze. Jeśli potrzebujesz pomocy dotyczącej instalowania Helm, zobacz [wskazówki dotyczące instalacji programu AKS Helm][helm-install]. Wszystkie Istioowe zasobniki muszą być również zaplanowane do uruchomienia w węzłach systemu Linux.

Upewnij się, że zapoznaj się z dokumentacją dotyczącą [wydajności i skalowalności Istio](https://istio.io/docs/concepts/performance-and-scalability/) , aby poznać dodatkowe wymagania dotyczące zasobów związanych z uruchamianiem Istio w klastrze AKS. Wymagania podstawowe i dotyczące pamięci będą się różnić w zależności od konkretnego obciążenia. Wybierz odpowiednią liczbę węzłów i rozmiar maszyny wirtualnej, które mają być przeznaczone do instalacji.

Ten artykuł oddziela wskazówki dotyczące instalacji Istio do kilku dyskretnych kroków. Wynik końcowy ma taką samą strukturę jak oficjalne [wskazówki dotyczące][istio-install-helm]instalacji Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Dodawanie repozytorium wykresu Istio Helm

Dodaj repozytorium Istio Helm Chart dla wydania Istio. Upewnij się, że uruchomiono `helm repo update`, aby zaktualizować informacje lokalne dla repozytorium wykresu.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Zainstaluj Istio CRDs na AKS

Istio używa [niestandardowych definicji zasobów (CRDs)][kubernetes-crd] do zarządzania konfiguracją środowiska uruchomieniowego. Musimy najpierw zainstalować Istio CRDs, ponieważ składniki Istio są zależne od nich. Użyj Helm i wykresu `istio-init`, aby zainstalować CRDs Istio w przestrzeni nazw `istio-system` w klastrze AKS:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[Zadania][kubernetes-jobs] są wdrażane jako część wykresu Helm `istio-init`, aby zainstalować CRDs. Te zadania powinny trwać krócej niż 20s, w zależności od środowiska klastra. Można sprawdzić, czy zadania zostały pomyślnie wykonane w następujący sposób:

```azurecli
kubectl get jobs -n istio-system
```

Następujące przykładowe dane wyjściowe pokazują zadania zakończone pomyślnie.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Po potwierdzeniu pomyślnego zakończenia zadań sprawdźmy, czy zainstalowano poprawną liczbę Istio CRDs. Możesz sprawdzić, czy wszystkie 23 Istio CRDs zostały zainstalowane, uruchamiając następujące polecenie. Polecenie powinno zwrócić numer `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Jeśli masz do tego momentu, oznacza to, że pomyślnie zainstalowano Istio CRDs. Teraz przejdź do następnej sekcji, aby [zainstalować składniki Istio na AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Zainstaluj składniki Istio na AKS

Będziemy instalować [Grafana][grafana] i [Kiali][kiali] w ramach naszej instalacji Istio. Usługa Grafana udostępnia pulpity nawigacyjne do analizy i monitorowania, a Kiali udostępnia pulpit nawigacyjny zauważalności siatki usług. W naszej instalacji każdy z tych składników wymaga poświadczeń, które muszą być podane jako [wpis tajny][kubernetes-secrets].

Przed zainstalowaniem składników Istio należy utworzyć klucze tajne zarówno dla Grafana, jak i Kiali. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Zainstaluj składniki Istio

Teraz, po pomyślnym utworzeniu wpisów tajnych Grafana i Kiali w klastrze AKS, czas na zainstalowanie składników Istio. Użyj Helm i wykresu `istio`, aby zainstalować składniki Istio w przestrzeni nazw `istio-system` w klastrze AKS. 

> [!NOTE]
> **Opcje instalacji**
> 
> W ramach naszej instalacji są używane następujące opcje:
> - `global.controlPlaneSecurityEnabled=true` — wzajemna obsługa protokołu TLS dla płaszczyzny kontroli
> - `global.mtls.enabled=true` — Wymagaj komunikacji wszystkich usług z usługą MTLS
> - `grafana.enabled=true` — Włącz wdrażanie Grafana na potrzeby analiz i pulpitów nawigacyjnych monitorowania
> - `grafana.security.enabled=true` — Włącz uwierzytelnianie dla Grafana
> - `tracing.enabled=true` — Włącz wdrożenie Jaeger na potrzeby śledzenia
> - `kiali.enabled=true` — Włącz wdrożenie Kiali na pulpicie nawigacyjnym obserwowania sieci usług
>
> **Selektory węzłów**
>
> Istio obecnie muszą być zaplanowane do uruchomienia w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Istio są zaplanowane do uruchomienia tylko w węzłach z systemem Linux. Użyjemy [selektorów węzłów][kubernetes-node-selectors] , aby upewnić się, że zasobniki są zaplanowane do poprawnego węzła.

> [!CAUTION]
> Funkcje [usługi biodiscovery (poufnego odnajdowania)][istio-feature-sds] i [Istio CNI][istio-feature-cni] Istio są obecnie w [technologii alfa][istio-feature-stages], dlatego należy je zapewnić przed ich włączeniem. Ponadto funkcja Kubernetes [projekcji woluminu tokenów konta usługi][kubernetes-feature-sa-projected-volume] nie jest włączona w bieżących wersjach AKS.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

Wykres `istio` Helm służy do wdrażania dużej liczby obiektów. Można wyświetlić listę z danych wyjściowych powyższego polecenia `helm install`. Wdrożenie składników Istio powinno zająć mniej niż 2 minuty, w zależności od środowiska klastra.

W tym momencie wdrożono Istio w klastrze AKS. Aby upewnić się, że mamy pomyślne wdrożenie Istio, przejdź do następnej sekcji, aby [sprawdzić poprawność instalacji Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Weryfikowanie instalacji Istio

Najpierw upewnij się, że zostały utworzone oczekiwane usługi. Aby wyświetlić uruchomione usługi, użyj polecenia [polecenia kubectl Get SVC][kubectl-get] . Wykonaj zapytanie dotyczące przestrzeni nazw `istio-system`, gdzie składniki Istio i dodatków zostały zainstalowane przez wykres `istio` Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Następujące przykładowe dane wyjściowe przedstawiają usługi, które powinny być teraz uruchomione:

- usługi `istio-*`
- usługi śledzenia dodatków `jaeger-*`, `tracing` i `zipkin`
- Usługa metryk `prometheus` dodatek
- `grafana` dodatku do analizy i monitorowania pulpitu nawigacyjnego
- `kiali` dodatku pulpitu nawigacyjnego usługi Service oczka

Jeśli `istio-ingressgateway` pokazuje zewnętrzny adres IP `<pending>`, odczekaj kilka minut, aż adres IP nie zostanie przypisany przez sieć platformy Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
```

Następnie upewnij się, że zostały utworzone wymagane zasobniki. Użyj polecenia [polecenia kubectl Get][kubectl-get] -Binding i ponownie wykonaj zapytanie dotyczące przestrzeni nazw `istio-system`:

```console
kubectl get pods --namespace istio-system
```

Następujące przykładowe dane wyjściowe pokazują, które z nich są uruchomione:

- `istio-*` — zasobniki
- metryki dodatku `prometheus-*` pod
- Pulpit nawigacyjny `grafana-*` dodatku i monitorowanie pod
- Pulpit nawigacyjny sieci usług dodatków `kiali` w obszarze

```console
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Powinien istnieć trzy `istio-init-crd-*`owe zasobniki ze stanem `Completed`. Te zasobniki były odpowiedzialne za uruchamianie zadań, które utworzyły CRDs w poprzednim kroku. Wszystkie pozostałe zasobniki powinny zawierać stan `Running`. Jeśli Twoje zasobniki nie mają tych stanów, Zaczekaj chwilę lub dwa, aż do ich wykonania. Jeśli którykolwiek z raportów zawiera raport o problemie, użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

Wiele dodatków zostało zainstalowanych przez Istio w naszych konfiguracjach, które zapewniają dodatkową funkcjonalność. Aplikacje sieci Web dla dodatków **nie** są udostępniane publicznie za pośrednictwem zewnętrznego adresu IP. 

Aby uzyskać dostęp do interfejsów użytkownika dodatku, użyj polecenia `istioctl dashboard`. To polecenie wykorzystuje port [polecenia kubectl do przodu][kubectl-port-forward] i losowo, aby utworzyć bezpieczne połączenie między komputerem klienckim i odpowiednim pod nim w klastrze AKS. Następnie aplikacja sieci Web dodatku zostanie automatycznie otwarta w domyślnej przeglądarce.

Dodaliśmy dodatkową warstwę zabezpieczeń dla Grafana i Kiali, określając poświadczenia dla nich wcześniej w tym artykule.

### <a name="grafana"></a>Grafana

Pulpity nawigacyjne analizy i monitorowania dla Istio są udostępniane przez [Grafana][grafana]. Pamiętaj, aby przed wyświetleniem monitu użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Grafana. Otwórz pulpit nawigacyjny Grafana w następujący sposób:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metryki dla Istio są dostarczane przez [Prometheus][prometheus]. Otwórz pulpit nawigacyjny Prometheus w następujący sposób:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Śledzenie w ramach Istio jest dostarczane przez [Jaeger][jaeger]. Otwórz pulpit nawigacyjny Jaeger w następujący sposób:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Pulpit nawigacyjny obserwowania sieci usługi jest udostępniany przez [Kiali][kiali]. Pamiętaj, aby przed wyświetleniem monitu użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Kiali. Otwórz pulpit nawigacyjny Kiali w następujący sposób:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Dostępny jest prosty interfejs do serwerów proxy [wysłannika][envoy] . Zawiera informacje o konfiguracji i metryki dla serwera proxy wysłannika uruchomionego w określonym pod. Należy bezpiecznie otworzyć interfejs wysłannika w następujący sposób:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Odinstaluj Istio z AKS

> [!WARNING]
> Usunięcie Istio z działającego systemu może skutkować problemami związanymi z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dotyczące systemu, aby nadal działały prawidłowo bez Istio przed kontynuowaniem.

### <a name="remove-istio-components-and-namespace"></a>Usuń składniki Istio i przestrzeń nazw

Aby usunąć Istio z klastra AKS, użyj następujących poleceń. @No__t_0 polecenia spowodują usunięcie wykresów `istio` i `istio-init`, a polecenie `kubectl delete namespace` spowoduje usunięcie `istio-system` przestrzeni nazw.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Usuń Istio CRDs i wpisy tajne

Powyższe polecenia powodują usunięcie wszystkich składników Istio i przestrzeni nazw, ale nadal pozostawiamy CRDsy Istio i wpisy tajne. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można użyć usługi Istio do udostępnienia inteligentnego routingu w celu przeprowadzenia wydania w wersji kanaryjskiej:

> [!div class="nextstepaction"]
> [Scenariusz inteligentnego routingu AKS Istio][istio-scenario-routing]

Aby poznać więcej opcji instalacji i konfiguracji dla usługi Istio, zobacz następujące oficjalne artykuły Istio:

- [Podręcznik instalacji Istio-Helm][istio-install-helm]
- [Opcje instalacji Istio-Helm][istio-install-helm-options]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu:

- [Przykład aplikacji Istio Bookinfo][istio-bookinfo-example]

Aby dowiedzieć się, jak monitorować aplikację AKS przy użyciu Application Insights i Istio, zobacz następującą dokumentację Azure Monitor:

- [Zero monitorowanie aplikacji Instrumentacji dla aplikacji hostowanych Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/en-us/azure/aks/concepts-clusters-workloads#node-selectors
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
[helm-install]: ./kubernetes-helm.md
