---
title: Zainstaluj Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Istio utworzyć siatki usługi klastra Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9d973cb2ac210e912d93941a2f81889557379f43
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625975"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] is an open-source service mesh that provides a key set of functionality across the microservices in a Kubernetes cluster. These features include traffic management, service identity and security, policy enforcement, and observability. For more information about Istio, see the official [What is Istio?][istio-docs-concepts] dokumentacji.

W tym artykule pokazano, jak zainstalować Istio. Istio `istioctl` binarnych klienta zostanie zainstalowana na komputerze klienckim i składniki Istio są zainstalowane w klastrze Kubernetes w usłudze AKS.

> [!NOTE]
> Te instrukcje odwoływać się do wersji Istio `1.1.3`.
>
> Istio `1.1.x` wersje zostały przetestowane przez zespół Istio względem wersji rozwiązania Kubernetes `1.11`, `1.12`, `1.13`. Możesz znaleźć dodatkowe wersje Istio na [GitHub — wersje Istio][istio-github-releases] and information about each of the releases at [Istio - Release Notes][istio-release-notes].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz Istio
> * Zainstaluj klienta istioctl Istio binarne
> * Zainstaluj Słowniki CRD Istio w usłudze AKS
> * Zainstaluj składniki Istio w usłudze AKS
> * Zweryfikuj instalację Istio
> * Uzyskiwanie dostępu do dodatków
> * Odinstaluj Istio z usługi AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach szczegółowo opisanych w tym artykule przyjęto założenie, że utworzono klaster usługi AKS (Kubernetes `1.11` i nowszych, przy użyciu RBAC włączone), a `kubectl` połączenia z klastrem. Jeśli potrzebujesz pomocy przy użyciu dowolnego z tych elementów, zobacz [szybkiego startu usługi AKS][aks-quickstart].

Będziesz potrzebować [Helm][helm] wykonaj te instrukcje i zainstalować Istio. Zaleca się, że została zainstalowana wersja `2.12.2` lub później poprawnie zainstalowany i skonfigurowany w klastrze. Jeśli potrzebujesz pomocy przy instalowaniu narzędzia Helm, zobacz [wskazówki dotyczące instalacji narzędzia Helm AKS][helm-install]. Wszystkie zasobników Istio również musi być zaplanowane do uruchomienia w węzłach systemu Linux.

W tym artykule oddziela wskazówki dotyczące instalacji Istio na kilka kroków dyskretnych. Efekt jest taki sam sposób, w strukturze jako oficjalna instalację Istio [wskazówki][istio-install-helm].

## <a name="download-istio"></a>Pobierz Istio

Najpierw należy pobrać i wyodrębnić najnowsze wydanie Istio. Kroki różnią się nieco dla powłoki bash w systemie MacOS, Linux lub podsystemu Windows dla systemu Linux oraz dla powłoki PowerShell. Wybierz jedną z poniższych kroków instalacji Twojego preferowanego środowiska:

* [Powłoka bash w systemie MacOS, Linux lub podsystemu Windows dla systemu Linux](#bash)
* [Program PowerShell](#powershell)

### <a name="bash"></a>Bash

W systemie MacOS, należy użyć `curl` Aby pobrać najnowszą wersję Istio, a następnie wyodrębnij z `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

W systemie Linux lub podsystem Windows dla systemu Linux, należy użyć `curl` Aby pobrać najnowszą wersję Istio, a następnie wyodrębnij z `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Teraz przejść do sekcji, aby [zainstalować klienta istioctl Istio binarne](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

W programie PowerShell użyj `Invoke-WebRequest` Aby pobrać najnowszą wersję Istio, a następnie wyodrębnij z `Expand-Archive` w następujący sposób:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Teraz przejść do sekcji, aby [zainstalować klienta istioctl Istio binarne](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Zainstaluj klienta istioctl Istio binarne

> [!IMPORTANT]
> Upewnij się, wykonaj kroki w tej sekcji z folderu najwyższego poziomu z wersji Istio pobrane i wyodrębnione.

`istioctl` Binarnych klienta jest uruchamiana na komputerze klienckim i umożliwia użytkownikowi interakcję z siatki usługi Istio. Kroki instalacji różnią się nieco między systemami operacyjnymi klienta. Wybierz jedną z poniższych kroków instalacji Twojego preferowanego środowiska:

* [MacOS](#macos)
* [Linux lub podsystem Windows dla systemu Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Aby zainstalować Istio `istioctl` binarnych klienta w powłoce oparte na funkcji bash w systemie MacOS, należy użyć następujących poleceń. Skopiuj te polecenia `istioctl` klienta binarne do lokalizacji programu użytkownika standardowego w Twojej `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Teraz przejść do następnej sekcji, aby [zainstalować Słowniki CRD Istio w usłudze AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux lub podsystem Windows dla systemu Linux

Użyj następujących poleceń, aby zainstalować Istio `istioctl` klienta binarne w powłoce bash, oparte na systemie Linux lub [podsystem Windows dla systemu Linux][install-wsl]. Skopiuj te polecenia `istioctl` klienta binarne do lokalizacji programu użytkownika standardowego w Twojej `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

Teraz przejść do następnej sekcji, aby [zainstalować Słowniki CRD Istio w usłudze AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Do zainstalowania Istio `istioctl` klienta binarne w **Powershell**-shell oparte na Windows, użyj następujących poleceń. Skopiuj te polecenia `istioctl` binarne do folderu Istio klienta, a następnie dostępna natychmiast (w bieżącej powłoce) i trwałe (za pośrednictwem powłoki ponownego uruchomienia) za pośrednictwem usługi `PATH`. Nie ma potrzeby podwyższonym poziomem uprawnień (Administrator), aby wykonać te polecenia, a nie jest konieczne ponowne uruchomienie powłoki.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Teraz przejść do następnej sekcji, aby [zainstalować Słowniki CRD Istio w usłudze AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Zainstaluj Słowniki CRD Istio w usłudze AKS

> [!IMPORTANT]
> Upewnij się, wykonaj kroki w tej sekcji z folderu najwyższego poziomu z wersji Istio pobrane i wyodrębnione.

Używa Istio [definicji zasobów niestandardowych (Słowniki CRD)][kubernetes-crd] zarządzać jego konfigurację środowiska uruchomieniowego. Należy najpierw zainstalować Słowniki CRD Istio, ponieważ składniki Istio mają zależności na nich. Użyj narzędzia Helm i `istio-init` wykres, aby zainstalować Słowniki CRD Istio do `istio-system` przestrzeni nazw w klastrze AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Zadania][kubernetes-jobs] są wdrażane jako część `istio-init` narzędzia Helm do zainstalowania Słowniki CRD. Te zadania powinno zająć od 1 do 2 minut, w zależności od środowiska klastra. Aby sprawdzić, czy zadania zostały pomyślnie ukończone w następujący sposób:

```azurecli
kubectl get jobs -n istio-system
```

Następujące przykładowe dane wyjściowe pokazuje pomyślnie zakończonych zadań.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Teraz, gdy firma Microsoft zostało potwierdzone pomyślne zakończenie zadania, Przyjrzyjmy Sprawdź, że mamy poprawną liczbę Słowniki CRD Istio zainstalowane. Aby sprawdzić, czy wszystkie 53 Słowniki CRD Istio zostały zainstalowane, uruchamiając polecenie odpowiednie dla danego środowiska. Polecenie powinna zwracać numer `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Jeśli masz z tym punktem, następnie oznacza to, że został pomyślnie zainstalowany Słowniki CRD Istio. Teraz przejść do następnej sekcji, aby [zainstalować składniki Istio w usłudze AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Zainstaluj składniki Istio w usłudze AKS

> [!IMPORTANT]
> Upewnij się, wykonaj kroki w tej sekcji z folderu najwyższego poziomu z wersji Istio pobrane i wyodrębnione.

Firma Microsoft będzie instalował [Grafana][grafana] and [Kiali][kiali] jako część naszego Istio instalacji. Grafana zapewnia analizy i pulpity nawigacyjne monitorowania i Kiali udostępnia pulpit nawigacyjny observability siatki usług. W naszym konfiguracji każdego z tych składników wymaga poświadczeń, które musi zostać podana jako [klucz tajny][wpisy tajne usługi kubernetes].

Firma Microsoft może zainstalować składniki Istio, możemy utworzyć wpisy tajne, zarówno dla platformy Grafana i Kiali. Tworzenie tych kluczy tajnych za pomocą odpowiednich poleceń w danym środowisku.

### <a name="add-grafana-secret"></a>Dodawanie wpisu tajnego z narzędzia Grafana

Zastąp `REPLACE_WITH_YOUR_SECURE_PASSWORD` tokenu przy użyciu hasła, a następnie uruchom następujące polecenia:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Dodaj klucz tajny Kiali

Zastąp `REPLACE_WITH_YOUR_SECURE_PASSWORD` tokenu przy użyciu hasła, a następnie uruchom następujące polecenia:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Zainstaluj składniki Istio

Teraz, gdy firma Microsoft została pomyślnie utworzona Grafana oraz Kiali wpisów tajnych w klastrze AKS, nadszedł czas na zainstalowanie składników Istio. Użyj narzędzia Helm i `istio` wykres, aby zainstalować składniki Istio do `istio-system` przestrzeni nazw w klastrze AKS. Polecenia odpowiednie dla danego środowiska.

> [!NOTE]
> Użyto następujących opcji w ramach naszej instalacji:
> - `global.controlPlaneSecurityEnabled=true` -wzajemne TLS dla na płaszczyźnie kontroli włączone
> - `mixer.adapters.useAdapterCRDs=false` -Usuń zegarki karty mixer Słowniki CRD zgodnie z ich do staną się przestarzałe i to poprawi wydajność
> - `grafana.enabled=true` -Włącz wdrożenie Grafana analizy i pulpity nawigacyjne monitorowania
> - `grafana.security.enabled=true` — Włączanie uwierzytelniania dla narzędzia Grafana
> - `tracing.enabled=true` -Włącz wdrożenie Jaeger śledzenia
> - `kiali.enabled=true` -Włącz wdrożenie Kiali dla pulpitu nawigacyjnego usług siatki observability

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio` Narzędzia Helm wdraża dużą liczbę obiektów. Zobaczysz listę z danych wyjściowych usługi `helm install` polecenia powyżej. Wdrażanie składników Istio może potrwać od 4 do 5 minut, w zależności od środowiska klastra.

> [!NOTE]
> Wszystkie zasobników Istio musi być zaplanowane do uruchomienia w węzłach systemu Linux. Oprócz pule węzłów systemu Linux w klastrze istnieją pule węzłów systemu Windows Server, sprawdź, czy wszystkie zasobników Istio zostały zaplanowane do uruchomienia w węzłach systemu Linux.

W tym momencie wdrożono Istio do klastra usługi AKS. Aby upewnić się, że mamy pomyślne wdrożenie Istio, przejdźmy do następnej sekcji, aby [sprawdzić poprawność instalacji Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Zweryfikuj instalację Istio

Najpierw upewnij się, czy oczekiwany usługi zostały utworzone. Użyj [kubectl get-svc][kubectl-get] polecenie, aby wyświetlić uruchomionych usług. Zapytanie `istio-system` przestrzeni nazw, w której Istio i dodatek składniki zostały zainstalowane przez `istio` narzędzia Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Następujące przykładowe dane wyjściowe pokazuje usług, które powinna teraz być uruchomiona:

- `istio-*` Usługi
- `jaeger-*`, `tracing`, i `zipkin` dodatkowych śledzenia usług
- `prometheus` Usługa metryk dodatku
- `grafana` Dodatek analizy i monitorowania usługi Pulpit nawigacyjny
- `kiali` Dodatek usługi Pulpit nawigacyjny siatki

Jeśli `istio-ingressgateway` pokazuje zewnętrzny adres ip, z `<pending>`, poczekaj kilka minut, zanim został przypisany adres IP, sieci platformy Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Następnie upewnij się, czy zostały utworzone wymagane zasobników. Użyj [kubectl get pods-][kubectl-get] poleceń i ponownie zapytanie `istio-system` przestrzeni nazw:

```console
kubectl get pods --namespace istio-system
```

Następujące przykładowe dane wyjściowe pokazuje zasobników, które są uruchomione:

- `istio-*` zasobników
- `prometheus-*` zasobnika metryki dodatku
- `grafana-*` dodatek analizy i monitorowania zasobnika pulpitu nawigacyjnego
- `kiali` zasobnika pulpitu nawigacyjnego siatki usługi dodatku

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Powinny istnieć dwie `istio-init-crd-*` zasobników z `Completed` stanu. Te zasobniki zostały odpowiedzialny za działanie zadania, które tworzone Słowniki CRD we wcześniejszym kroku. Wszystkie inne zasobników powinien być wyświetlony stan `Running`. Jeśli zasobników nie mają tych stanów, poczekaj minutę lub dwie robią. Jeśli wszystkie zasobników zgłosić problem, użyj [kubectl opisują zasobnika][kubectl-describe] polecenie, aby przejrzeć swoje dane wyjściowe i stanu.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

Liczba dodatków zostały zainstalowane Istio w naszej konfiguracji powyżej zapewniające dodatkowe funkcje. Interfejsy użytkownika dla dodatków nie są widoczne publicznie za pośrednictwem adresu ip zewnętrznej. Aby uzyskać dostęp do interfejsów użytkownika dodatku, należy użyć [kubectl portu na następny okres][kubectl-port-forward] polecenia. To polecenie tworzy bezpiecznego połączenia między komputerze klienckim i odpowiednie zasobnik w klastrze AKS.

Dodano dodatkową warstwę zabezpieczeń dla narzędzia Grafana oraz Kiali określając poświadczeń we wcześniejszej części tego artykułu.

### <a name="grafana"></a>Grafana

Analiza i monitorowanie pulpitów nawigacyjnych dla Istio są dostarczane przez [Grafana][grafana]. Przekazuj port lokalny `3000` na komputerze klienckim z portem `3000` na zasobnik uruchomioną Grafana klastra usługi AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Następujące przykładowe dane wyjściowe pokazuje konfigurowanych Grafana przekazywanie portu:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Możesz teraz uzyskiwać dostęp Grafana następującego adresu URL na komputerze klienckim - [ http://localhost:3000 ](http://localhost:3000). Pamiętaj, aby użyć poświadczeń, która została utworzona za pomocą platformy Grafana wpisu tajnego wcześniej po wyświetleniu monitu.

### <a name="prometheus"></a>Prometheus

Metryki dla Istio są dostarczane przez [Prometheus][prometheus]. Przekazuj port lokalny `9090` na komputerze klienckim z portem `9090` na zasobnik uruchomioną Prometheus klastra usługi AKS:

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

Śledzenie w obrębie Istio są dostarczane przez [Jaeger][jaeger]. Przekazuj port lokalny `16686` na komputerze klienckim z portem `16686` na zasobnik uruchomioną Jaeger klastra usługi AKS:

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

Pulpit nawigacyjny usług siatki observability są dostarczane przez [Kiali][kiali]. Przekazuj port lokalny `20001` na komputerze klienckim z portem `20001` na zasobnik uruchomioną Kiali klastra usługi AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Następujące przykładowe dane wyjściowe pokazuje konfigurowanych Kiali przekazywanie portu:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Teraz możesz połączyć Kiali siatki observability pulpit nawigacyjny usługi pod adresem URL, na komputerze klienckim - [ http://localhost:20001/kiali/console/ ](http://localhost:20001/kiali/console/). Pamiętaj, aby użyć poświadczeń, która została utworzona za pomocą Kiali wpisu tajnego wcześniej po wyświetleniu monitu.

## <a name="uninstall-istio-from-aks"></a>Odinstaluj Istio z usługi AKS

> [!WARNING]
> Usuwanie Istio z uruchomionym systemie może spowodować ruchu kwestiach między usługami. Upewnij się, wprowadzono przepisów dla używanego systemu, aby nadal działać poprawnie bez Istio przed kontynuowaniem.

### <a name="remove-istio-components-and-namespace"></a>Usuń Istio składniki i przestrzeni nazw

Aby usunąć Istio z klastra usługi AKS, użyj następujących poleceń. `helm delete` Polecenia spowoduje usunięcie `istio` i `istio-init` wykresów i `kubectl delete ns` polecenie spowoduje usunięcie `istio-system` przestrzeni nazw.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Usuń Słowniki CRD Istio

Powyższe polecenia Usuń wszystkie składniki Istio i przestrzeni nazw, ale firma Microsoft wciąż pozostają Słowniki CRD Istio. Aby usunąć Słowniki CRD, umożliwia jeden poniższych metod.

Podejście #1 — to polecenie przyjęto założenie, że używasz tego kroku folder najwyższego poziomu w wersji pobrane i wyodrębnione Istio użytym do zainstalowania Istio za pomocą.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Podejście #2 — użyj jednej z tych poleceń, jeśli nie masz już dostępu do wersji pobrane i wyodrębnione Istio użytym do zainstalowania Istio za pomocą. To polecenie będzie trwać nieco dłużej — powinien zająć kilka minut.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Następne kroki

Poniższa dokumentacja w tym artykule opisano sposób korzystania Istio do dostarczania routingu inteligentne to dystrybucję wydania canary:

> [!div class="nextstepaction"]
> [Inteligentne scenariusz routingu AKS Istio][istio-scenario-routing]

Aby poznać więcej opcji instalacji i konfiguracji Istio, zobacz następujące artykuły Istio oficjalne:

- [Istio — Przewodnik instalacji narzędzia Helm][istio-install-helm]
- [Istio — opcje instalacji narzędzia Helm][istio-install-helm-options]

Można również wykonać dodatkowe scenariusze za pomocą [Przykładowa aplikacja Bookinfo Istio][istio-bookinfo-example].

Aby dowiedzieć się, jak monitorować aplikację usługi AKS przy użyciu usługi Application Insights i Istio, znajduje się poniższej dokumentacji usługi Azure Monitor:
- [Monitorowanie zero instrumentacji aplikacji dla rozwiązania Kubernetes obsługiwanych aplikacji][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
