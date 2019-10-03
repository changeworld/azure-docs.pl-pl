---
title: Instalowanie Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Istio do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9344d2832c37c34d5690dc8f3aae7394ca644276
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827325"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczenia, wymuszanie zasad oraz ich przestrzeganie. Aby uzyskać więcej informacji na temat Istio, zobacz oficjalny dokument dotyczący [Istio?][istio-docs-concepts] .

W tym artykule opisano sposób instalowania programu Istio. Plik binarny klienta Istio `istioctl` jest instalowany na komputerze klienckim, a składniki Istio są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Te instrukcje odnoszą się do Istio wersji `1.1.3`.
>
> Wersje Istio `1.1.x` zostały przetestowane przez zespół Istio w odniesieniu do wersji Kubernetes `1.11`, `1.12`, `1.13`. Dodatkowe wersje Istio można znaleźć w [artykułach][istio-release-notes]usługi [GitHub-Istio][istio-github-releases] i informacje o każdej z tych wersji w witrynie Istio.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz Istio
> * Zainstaluj plik binarny klienta Istio istioctl
> * Zainstaluj Istio CRDs na AKS
> * Zainstaluj składniki Istio na AKS
> * Weryfikowanie instalacji Istio
> * Uzyskiwanie dostępu do dodatków
> * Odinstaluj Istio z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster AKS (Kubernetes `1.11` lub nowszy z włączoną funkcją RBAC) i nastąpiło połączenie `kubectl` z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart].

Musisz [Helm][helm] , aby wykonać te instrukcje i zainstalować Istio. Zaleca się, aby w klastrze była zainstalowana i skonfigurowana wersja `2.12.2` lub nowsza. Jeśli potrzebujesz pomocy dotyczącej instalowania Helm, zobacz [wskazówki dotyczące instalacji programu AKS Helm][helm-install]. Wszystkie Istioowe zasobniki muszą być również zaplanowane do uruchomienia w węzłach systemu Linux.

Upewnij się, że zapoznaj się z dokumentacją dotyczącą [wydajności i skalowalności Istio](https://istio.io/docs/concepts/performance-and-scalability/) , aby poznać dodatkowe wymagania dotyczące zasobów związanych z uruchamianiem Istio w klastrze AKS. Wymagania podstawowe i dotyczące pamięci będą się różnić w zależności od konkretnego obciążenia. Wybierz odpowiednią liczbę węzłów i rozmiar maszyny wirtualnej, które mają być przeznaczone do instalacji.

Ten artykuł oddziela wskazówki dotyczące instalacji Istio do kilku dyskretnych kroków. Wynik końcowy ma taką samą strukturę jak oficjalne [wskazówki dotyczące][istio-install-helm]instalacji Istio.

## <a name="download-istio"></a>Pobierz Istio

Najpierw pobierz i Wyodrębnij najnowszą wersję Istio. Kroki są nieco inne dla powłoki bash w systemie MacOS, Linux lub Windows dla systemu Linux oraz dla powłoki programu PowerShell. Wybierz jeden z następujących kroków instalacji, które są zgodne z preferowanym środowiskiem:

* [Bash w systemie MacOS, Linux lub Windows podsystem dla systemu Linux](#bash)
* [Program PowerShell](#powershell)

### <a name="bash"></a>Bash

W systemie MacOS należy użyć `curl` do pobrania najnowszej wersji Istio, a następnie wyodrębnienia z `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

W podsystemie Linux lub Windows dla systemu Linux Użyj `curl`, aby pobrać najnowszą wersję Istio, a następnie wyodrębnij przy użyciu `tar` w następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Teraz przejdź do sekcji, aby [zainstalować dane binarne klienta Istio istioctl](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

W programie PowerShell Użyj `Invoke-WebRequest`, aby pobrać najnowszą wersję programu Istio, a następnie wyodrębnij ją z `Expand-Archive` w następujący sposób:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Teraz przejdź do sekcji, aby [zainstalować dane binarne klienta Istio istioctl](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Zainstaluj plik binarny klienta Istio istioctl

> [!IMPORTANT]
> Upewnij się, że wykonano kroki opisane w tej sekcji, z folderu najwyższego poziomu w wersji Istio, który został pobrany i wyodrębniony.

Dane binarne klienta `istioctl` są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z siatką usługi Istio. Kroki instalacji różnią się w zależności od systemów operacyjnych klienta. Wybierz jeden z następujących kroków instalacji, które są zgodne z preferowanym środowiskiem:

* [MacOS](#macos)
* [Podsystem Linux lub Windows dla systemu Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Aby zainstalować dane binarne klienta Istio `istioctl` w bash Shell na MacOS, użyj następujących poleceń. Te polecenia kopiują dane binarne klienta `istioctl` do lokalizacji standardowego programu użytkownika w `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Jeśli chcesz, aby wiersz polecenia był uzupełniany dla danych binarnych klienta Istio `istioctl`, skonfiguruj go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Teraz przejdź do następnej sekcji, aby [zainstalować Istio CRDs na AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Podsystem Linux lub Windows dla systemu Linux

Użyj następujących poleceń, aby zainstalować dane binarne klienta Istio `istioctl` w powłoce opartym na bash w [podsystemie Linux lub Windows dla systemu Linux][install-wsl]. Te polecenia kopiują dane binarne klienta `istioctl` do lokalizacji standardowego programu użytkownika w `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Jeśli chcesz, aby wiersz polecenia był uzupełniany dla danych binarnych klienta Istio `istioctl`, skonfiguruj go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Teraz przejdź do następnej sekcji, aby [zainstalować Istio CRDs na AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Aby zainstalować plik binarny klienta Istio `istioctl` w powłoce opartej na programie **PowerShell**w systemie Windows, użyj następujących poleceń. Te polecenia kopiują dane binarne klienta `istioctl` do folderu Istio, a następnie udostępniają je natychmiast (w bieżącej powłoce) i trwale (między ponownymi uruchomieniami powłoki) za pośrednictwem `PATH`. Nie musisz mieć podniesionych uprawnień (Administrator) do uruchamiania tych poleceń i nie musisz ponownie uruchamiać powłoki.

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

Teraz przejdź do następnej sekcji, aby [zainstalować Istio CRDs na AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Zainstaluj Istio CRDs na AKS

> [!IMPORTANT]
> Upewnij się, że wykonano kroki opisane w tej sekcji, z folderu najwyższego poziomu w wersji Istio, który został pobrany i wyodrębniony.

Istio używa [niestandardowych definicji zasobów (CRDs)][kubernetes-crd] do zarządzania konfiguracją środowiska uruchomieniowego. Musimy najpierw zainstalować Istio CRDs, ponieważ składniki Istio są zależne od nich. Użyj Helm i wykresu `istio-init`, aby zainstalować CRDs Istio w przestrzeni nazw `istio-system` w klastrze AKS:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Zadania][kubernetes-jobs] są wdrażane jako część wykresu Helm `istio-init`, aby zainstalować CRDs. Wykonanie tych zadań powinno zająć od 1 do 2 minut, w zależności od środowiska klastra. Można sprawdzić, czy zadania zostały pomyślnie wykonane w następujący sposób:

```azurecli
kubectl get jobs -n istio-system
```

Następujące przykładowe dane wyjściowe pokazują zadania zakończone pomyślnie.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Po potwierdzeniu pomyślnego zakończenia zadań sprawdźmy, czy zainstalowano poprawną liczbę Istio CRDs. Można sprawdzić, czy wszystkie 53 Istio CRDs zostały zainstalowane, uruchamiając odpowiednie polecenie dla danego środowiska. Polecenie powinno zwrócić liczbę `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Jeśli masz do tego momentu, oznacza to, że pomyślnie zainstalowano Istio CRDs. Teraz przejdź do następnej sekcji, aby [zainstalować składniki Istio na AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Zainstaluj składniki Istio na AKS

> [!IMPORTANT]
> Upewnij się, że wykonano kroki opisane w tej sekcji, z folderu najwyższego poziomu w wersji Istio, który został pobrany i wyodrębniony.

Będziemy instalować [Grafana][grafana] i [Kiali][kiali] w ramach naszej instalacji Istio. Usługa Grafana udostępnia pulpity nawigacyjne do analizy i monitorowania, a Kiali udostępnia pulpit nawigacyjny zauważalności siatki usług. W naszej instalacji każdy z tych składników wymaga poświadczeń, które muszą być podane jako [wpis tajny][kubernetes-secrets].

Przed zainstalowaniem składników Istio należy utworzyć klucze tajne zarówno dla Grafana, jak i Kiali. Utwórz te wpisy tajne, uruchamiając odpowiednie polecenia dla danego środowiska.

### <a name="add-grafana-secret"></a>Dodaj wpis tajny Grafana

Zastąp token `REPLACE_WITH_YOUR_SECURE_PASSWORD` hasłem i uruchom następujące polecenia:

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

### <a name="add-kiali-secret"></a>Dodaj wpis tajny Kiali

Zastąp token `REPLACE_WITH_YOUR_SECURE_PASSWORD` hasłem i uruchom następujące polecenia:

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

Teraz, po pomyślnym utworzeniu wpisów tajnych Grafana i Kiali w klastrze AKS, czas na zainstalowanie składników Istio. Użyj Helm i wykresu `istio`, aby zainstalować składniki Istio w przestrzeni nazw `istio-system` w klastrze AKS. Użyj odpowiednich poleceń dla danego środowiska.

> [!NOTE]
> W ramach naszej instalacji są używane następujące opcje:
> - `global.controlPlaneSecurityEnabled=true` — wzajemna obsługa protokołu TLS dla płaszczyzny kontroli
> - `mixer.adapters.useAdapterCRDs=false` — usuwanie czujek na adapterze miksera CRDs jako przestarzałe, co spowoduje zwiększenie wydajności
> - `grafana.enabled=true` — Włącz wdrażanie Grafana na potrzeby analiz i pulpitów nawigacyjnych monitorowania
> - `grafana.security.enabled=true` — Włącz uwierzytelnianie dla Grafana
> - `tracing.enabled=true` — włączenie wdrożenia Jaeger na potrzeby śledzenia
> - `kiali.enabled=true` — włączenie wdrożenia Kiali na pulpicie nawigacyjnym obserwowania sieci usług

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

Wykres Helm `istio` wdraża dużą liczbę obiektów. Można wyświetlić listę z danych wyjściowych powyższego polecenia `helm install`. Wdrożenie składników Istio może potrwać od 4 do 5 minut, w zależności od środowiska klastra.

> [!NOTE]
> Wszystkie Istioowe zasobniki muszą być zaplanowane do uruchomienia w węzłach systemu Linux. Jeśli w klastrze znajdują się pule węzłów systemu Windows Server oprócz pul węzłów Linux, sprawdź, czy wszystkie Istioy są zaplanowane do uruchomienia w węzłach systemu Linux.

W tym momencie wdrożono Istio w klastrze AKS. Aby upewnić się, że mamy pomyślne wdrożenie Istio, przejdź do następnej sekcji, aby [sprawdzić poprawność instalacji Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Weryfikowanie instalacji Istio

Najpierw upewnij się, że zostały utworzone oczekiwane usługi. Aby wyświetlić uruchomione usługi, użyj polecenia [polecenia kubectl Get SVC][kubectl-get] . Wykonaj zapytanie dotyczące przestrzeni nazw `istio-system`, gdzie składniki Istio i dodatków zostały zainstalowane @no__t przez wykres Helm-1.

```console
kubectl get svc --namespace istio-system --output wide
```

Następujące przykładowe dane wyjściowe przedstawiają usługi, które powinny być teraz uruchomione:

- usługi `istio-*`
- `jaeger-*`, `tracing` i usługi śledzenia dodatków `zipkin`
- Usługa metryk dodatku `prometheus`
- `grafana` Dodawanie i monitorowanie usług pulpitu nawigacyjnego
- `kiali` usługa pulpitu nawigacyjnego sieci usługi dodatku

Jeśli `istio-ingressgateway` pokazuje zewnętrzny adres IP `<pending>`, odczekaj kilka minut, aż adres IP nie zostanie przypisany przez sieć platformy Azure.

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

Następnie upewnij się, że zostały utworzone wymagane zasobniki. Użyj polecenia [polecenia kubectl Get][kubectl-get] -Binding i ponownie Zbadaj przestrzeń nazw `istio-system`:

```console
kubectl get pods --namespace istio-system
```

Następujące przykładowe dane wyjściowe pokazują, które z nich są uruchomione:

- zasobniki `istio-*`
- metryki dodatku `prometheus-*` pod
- Pulpit nawigacyjny dodatku `grafana-*` dla analiz i monitorowania pod
- Pulpit nawigacyjny usługi dodatku `kiali` w sieci

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

W przypadku stanu `Completed` powinny istnieć dwa zasobniki `istio-init-crd-*`. Te zasobniki były odpowiedzialne za uruchamianie zadań, które utworzyły CRDs w poprzednim kroku. Wszystkie pozostałe zasobniki powinny zawierać stan `Running`. Jeśli Twoje zasobniki nie mają tych stanów, Zaczekaj chwilę lub dwa, aż do ich wykonania. Jeśli którykolwiek z raportów zawiera raport o problemie, użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

Liczba dodatków została zainstalowana Istio w naszej konfiguracji powyżej, która zapewnia dodatkową funkcjonalność. Interfejsy użytkownika dla dodatków nie są udostępniane publicznie za pośrednictwem zewnętrznego adresu IP. Aby uzyskać dostęp do interfejsów użytkownika dodatku, użyj polecenia [polecenia kubectl port-forward][kubectl-port-forward] . To polecenie tworzy bezpieczne połączenie między komputerem klienckim i odpowiednim pod nim w klastrze AKS.

Dodaliśmy dodatkową warstwę zabezpieczeń dla Grafana i Kiali, określając poświadczenia dla nich wcześniej w tym artykule.

### <a name="grafana"></a>Grafana

Pulpity nawigacyjne analizy i monitorowania dla Istio są udostępniane przez [Grafana][grafana]. Prześlij dalej port lokalny `3000` na komputerze klienckim, aby port `3000` na stronie pod kontrolą Grafana w klastrze AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Następujące przykładowe dane wyjściowe pokazują, że port do przodu jest skonfigurowany dla Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Teraz możesz dotrzeć do Grafana o następującym adresie URL na komputerze klienckim- [http://localhost:3000](http://localhost:3000). Pamiętaj, aby przed wyświetleniem monitu użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Grafana.

### <a name="prometheus"></a>Prometheus

Metryki dla Istio są dostarczane przez [Prometheus][prometheus]. Prześlij dalej port lokalny `9090` na komputerze klienckim, aby port `9090` na stronie pod kontrolą Prometheus w klastrze AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Następujące przykładowe dane wyjściowe pokazują, że port do przodu jest skonfigurowany dla Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Teraz możesz uzyskać dostęp do przeglądarki wyrażeń Prometheus pod następującym adresem URL na komputerze klienckim- [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Śledzenie w ramach Istio jest dostarczane przez [Jaeger][jaeger]. Prześlij dalej port lokalny `16686` na komputerze klienckim, aby port `16686` na stronie pod kontrolą Jaeger w klastrze AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Następujące przykładowe dane wyjściowe pokazują, że port do przodu jest skonfigurowany dla Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Teraz możesz uzyskać dostęp do interfejsu użytkownika śledzenia Jaeger pod następującym adresem URL na komputerze klienckim- [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Pulpit nawigacyjny obserwowania sieci usługi jest udostępniany przez [Kiali][kiali]. Prześlij dalej port lokalny `20001` na komputerze klienckim, aby port `20001` na stronie pod kontrolą Kiali w klastrze AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Następujące przykładowe dane wyjściowe pokazują, że port do przodu jest skonfigurowany dla Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Teraz możesz uzyskać dostęp do pulpitu nawigacyjnego obserwowanie siatki usługi Kiali pod następującym adresem URL na komputerze klienckim- [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Pamiętaj, aby przed wyświetleniem monitu użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Kiali.

## <a name="uninstall-istio-from-aks"></a>Odinstaluj Istio z AKS

> [!WARNING]
> Usunięcie Istio z działającego systemu może skutkować problemami związanymi z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dotyczące systemu, aby nadal działały prawidłowo bez Istio przed kontynuowaniem.

### <a name="remove-istio-components-and-namespace"></a>Usuń składniki Istio i przestrzeń nazw

Aby usunąć Istio z klastra AKS, użyj następujących poleceń. Polecenia `helm delete` spowodują usunięcie wykresów `istio` i `istio-init`, a polecenie `kubectl delete ns` spowoduje usunięcie przestrzeni nazw `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Usuń Istio CRDs

Powyższe polecenia powodują usunięcie wszystkich składników Istio i przestrzeni nazw, ale nadal pozostawiamy CRDs Istio. Aby usunąć CRDs, można użyć jednej z poniższych metod.

Podejście #1 — to polecenie zakłada, że uruchamiasz ten krok z folderu najwyższego poziomu pobranej i wyodrębnionej wersji Istio, która została użyta do instalacji Istio za pomocą programu.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Podejście #2 — Użyj jednego z tych poleceń, jeśli nie masz już dostępu do pobranej i wyodrębnionej wersji Istio, która została użyta do instalacji Istio za pomocą programu. To polecenie potrwa nieco dłużej — może to potrwać kilka minut.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można użyć usługi Istio do udostępnienia inteligentnego routingu w celu przeprowadzenia wydania w wersji kanaryjskiej:

> [!div class="nextstepaction"]
> [Scenariusz inteligentnego routingu AKS Istio][istio-scenario-routing]

Aby poznać więcej opcji instalacji i konfiguracji dla usługi Istio, zobacz następujące oficjalne artykuły Istio:

- [Podręcznik instalacji Istio-Helm][istio-install-helm]
- [Opcje instalacji Istio-Helm][istio-install-helm-options]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu [przykładu aplikacji Istio Bookinfo][istio-bookinfo-example].

Aby dowiedzieć się, jak monitorować aplikację AKS przy użyciu Application Insights i Istio, zobacz następującą dokumentację Azure Monitor:
- [Zero monitorowanie aplikacji Instrumentacji dla aplikacji hostowanych Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
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
