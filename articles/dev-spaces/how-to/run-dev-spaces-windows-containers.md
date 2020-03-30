---
title: Interakcja z kontenerami systemu Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Dowiedz się, jak uruchomić usługi Azure Dev Spaces w istniejącym klastrze z kontenerami systemu Windows
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery, kontenery systemu Windows
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240484"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interakcja z kontenerami systemu Windows przy użyciu usługi Azure Dev Spaces

Można włączyć usługi Azure Dev Spaces zarówno w nowych, jak i istniejących obszarach nazw kubernetes. Usługa Azure Dev Spaces będzie działać i instruować usługi, które są uruchamiane na kontenerach systemu Linux. Te usługi mogą również wchodzić w interakcje z aplikacjami, które są uruchamiane w kontenerach systemu Windows w tym samym obszarze nazw. W tym artykule pokazano, jak używać przestrzeni deweloperskich do uruchamiania usług w obszarze nazw z istniejącymi kontenerami systemu Windows. W tej chwili nie można debugować ani dołączać do kontenerów systemu Windows za pomocą usługi Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Konfigurowanie klastra

W tym artykule założono, że masz już klaster z pulami węzłów systemu Linux i Windows. Jeśli chcesz utworzyć klaster z pulami węzłów Linux i Windows, możesz postępować zgodnie z instrukcjami [tutaj][windows-container-cli].

Połącz się z klastrem za pomocą [kubectl][kubectl], klienta wiersza polecenia Kubernetes. Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe przedstawia klaster z węzłem Systemu Windows i Linux. Upewnij się, że stan jest *gotowy* dla każdego węzła przed kontynuowaniem.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Zastosuj [skazę][using-taints] do węzłów systemu Windows. Skażenie w węzłach systemu Windows uniemożliwia tworzenie przestrzeni deweloperskich z planowania kontenerów systemu Linux do uruchomienia w węzłach systemu Windows. Poniższe polecenie przykładowe polecenie stosuje skazę do węzła systemu Windows *aksnpwin987654* z poprzedniego przykładu.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Po zastosowaniu skazy do węzła, należy skonfigurować pasujące tolerancji w szablonie wdrażania usługi, aby uruchomić usługę w tym węźle. Przykładowa aplikacja jest już skonfigurowana z [dopasowującą tolerancją][sample-application-toleration-example] do taint skonfigurowanego w poprzednim poleceniu.

## <a name="run-your-windows-service"></a>Uruchamianie usługi systemu Windows

Uruchom usługę systemu Windows w klastrze AKS i sprawdź, czy jest w stanie *uruchomionym.* W tym artykule użyto [przykładowej aplikacji][sample-application] do zademonstrowania usługi Windows i Linux uruchomionej w klastrze.

Sklonuj przykładową aplikację z gitHub i przejdź do `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` katalogu:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Przykładowa aplikacja używa [helm 3][helm-installed] do uruchomienia usługi Windows w klastrze. Przejdź do `charts` katalogu i użyj Helm uruchom usługę Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Powyższe polecenie używa helm do uruchomienia usługi systemu Windows w obszarze nazw *deweloperów.* Jeśli nie masz obszaru nazw o nazwie *dev,* zostanie ona utworzona.

Użyj `kubectl get pods` polecenia, aby sprawdzić, czy usługa systemu Windows jest uruchomiona w klastrze. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Włączanie pomieszczeń deweloperskich platformy Azure

Włącz miejsca dewelopera w tym samym obszarze nazw, który był używany do uruchamiania usługi systemu Windows. Następujące polecenie włącza miejsca programu Dev Spaces w obszarze nazw *deweloperów:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aktualizowanie usługi systemu Windows dla miejsc deweloperskich

Po włączeniu obszarów deweloperskich na istniejącym obszarze nazw z kontenerami, które są już uruchomione, domyślnie dev spaces spróbuje przywzwiczają wszystkie nowe kontenery uruchamiane w tym obszarze nazw. Dev Spaces spróbuje również przywdzwać wszystkie nowe kontenery utworzone dla usługi już uruchomionej w obszarze nazw. Aby zapobiec instrumentacji kontenera działającego w obszarze nazw w obszarze `deployment.yaml`nazw, dodaj nagłówek bez serwera *proxy* do pliku .

Dodaj `azds.io/no-proxy: "true"` do `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` pliku:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Służy `helm list` do wyliowania listy wdrożenia usługi systemu Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

W powyższym przykładzie nazwa wdrożenia to *usługa windows.* Zaktualizuj usługę systemu `helm upgrade`Windows o nową konfigurację za pomocą :

```cmd
helm upgrade windows-service . --namespace dev
```

Od czasu `deployment.yaml`zaktualizowania usługi Dev Spaces nie będzie próbował aranżować usługi.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Uruchamianie aplikacji systemu Linux za pomocą usługi Azure Dev Spaces

Przejdź do `webfrontend` katalogu i `azds prep` użyj `azds up` i poleceń, aby uruchomić aplikację Systemu Linux w klastrze.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Polecenie `azds prep --enable-ingress` generuje wykres Helm i pliki dockerfiles dla aplikacji.

> [!TIP]
> [Wykres Dockerfile i Helm](../how-dev-spaces-works-prep.md#prepare-your-code) dla projektu jest używany przez usługę Azure Dev Spaces do tworzenia i uruchamiania kodu, ale można zmodyfikować te pliki, jeśli chcesz zmienić sposób tworzenia i uruchamiania projektu.

Polecenie `azds up` uruchamia usługę w obszarze nazw.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Możesz zobaczyć usługę uruchomiona przez otwarcie publicznego adresu URL, który jest wyświetlany w danych wyjściowych z polecenia azds up. W tym przykładzie publiczny `http://dev.webfrontend.abcdef0123.eus.azds.io/`adres URL to . Przejdź do usługi w przeglądarce i kliknij przycisk *Informacje* u góry. Sprawdź, czy zostanie wyświetlony komunikat z usługi *mywebapi* zawierający wersję systemu Windows, z której korzysta kontener.

![Przykładowa aplikacja przedstawiająca wersję systemu Windows z mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
