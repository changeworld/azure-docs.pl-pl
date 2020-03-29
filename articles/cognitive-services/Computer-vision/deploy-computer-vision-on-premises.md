---
title: Użyj kontenera Przetwarzania Obrazów komputerowych z kubernetami i helmem
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera przetwarzania w usłudze Azure Container Instance i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458022"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Użyj kontenera Przetwarzania Obrazów komputerowych z kubernetami i helmem

Jedną z opcji zarządzania kontenerami przetwarzania obrazów w środowisku lokalnym jest użycie aplikacji Kubernetes i Helm. Za pomocą Kubernetes i Helm do definiowania obrazu kontenera przetwarzania obrazów, utworzymy pakiet Kubernetes. Ten pakiet zostanie wdrożony w lokalnym klastrze kubernetes. Na koniec zbadamy, jak przetestować wdrożone usługi. Aby uzyskać więcej informacji na temat uruchamiania kontenerów platformy Docker bez aranżacji kubernetes, zobacz [instalowanie i uruchamianie kontenerów przetwarzania.](computer-vision-how-to-install-containers.md)

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne przed użyciem kontenerów usługi Computer Vision lokalnie:

| Wymagany | Przeznaczenie |
|----------|---------|
| Konto platformy Azure | Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][free-azure-account] przed rozpoczęciem. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli] jest wymagane do zarządzania poświadczeniami udostępnionymi z rejestru kontenerów. Kubernetes jest również potrzebne przed Helm, który jest menedżerem pakietów Kubernetes. |
| Helm CLI | Zainstaluj [interfejsu wiersza polecenia helm][helm-install], który jest używany do instalowania wykresu helm (definicja pakietu kontenera). |
| Zasób przetwarzania obrazów komputerowych |Aby korzystać z kontenera, musisz mieć:<br><br>Zasób usługi Azure **Computer Vision** i skojarzony klucz interfejsu API identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach Przegląd i Klucze dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie **Klucze**<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie **Przegląd**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Łączenie się z klastrem Kubernetes

Oczekuje się, że komputer-host będzie miał dostępny klaster Kubernetes. Zobacz ten samouczek dotyczący [wdrażania klastra Kubernetes,](../../aks/tutorial-kubernetes-deploy-cluster.md) aby zapoznać się z koncepcyjnie, jak wdrożyć klaster Kubernetes na komputerze-hoście.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Udostępnianie poświadczeń platformy Docker za pomocą klastra Kubernetes

Aby zezwolić klastrowi Kubernetes na `docker pull` skonfigurowane `containerpreview.azurecr.io` obrazy z rejestru kontenerów, należy przenieść poświadczenia platformy docker do klastra. Wykonaj [`kubectl create`][kubectl-create] poniższe polecenie, aby utworzyć *klucz tajny rejestru platformy docker* na podstawie poświadczeń podanych z wymaganego dostępu do rejestru kontenera.

Z wybranego interfejsu wiersza polecenia uruchom następujące polecenie. Pamiętaj, aby `<username>`zastąpić `<password>`, `<email-address>` i poświadczenia rejestru kontenera.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Jeśli masz już dostęp `containerpreview.azurecr.io` do rejestru kontenerów, można utworzyć klucz tajny Kubernetes przy użyciu flagi ogólnej zamiast. Należy wziąć pod uwagę następujące polecenie, które wykonuje przeciwko konfiguracji platformy Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Następujące dane wyjściowe są drukowane na konsoli po pomyślnym utworzeniu klucza tajnego.

```console
secret "containerpreview" created
```

Aby sprawdzić, czy klucz tajny [`kubectl get`][kubectl-get] został `secrets` utworzony, wykonaj z flagą.

```console
kubectl get secrets
```

Wykonywanie `kubectl get secrets` wydruków wszystkie skonfigurowane wpisy tajne.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurowanie wartości wykresu helm dla wdrożenia

Rozpocznij od utworzenia folderu o nazwie *odczyt,* a następnie wklej następującą zawartość YAML do nowego pliku o nazwie *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Aby skonfigurować wartości domyślne wykresu Helm, skopiuj `values.yaml`i wklej następujący yaml do pliku o nazwie . Zastąp `# {ENDPOINT_URI}` i `# {API_KEY}` komentarze własnymi wartościami.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Jeśli `billing` wartości `apikey` i wartości nie zostaną dostarczone, usługi wygasną po 15 minutach. Podobnie weryfikacja zakończy się niepowodzeniem, ponieważ usługi nie będą dostępne.

Utwórz folder *szablonów* w katalogu *odczytu.* Skopiuj i wklej następujący `deployment.yaml`PLIK YAML do pliku o nazwie . Plik `deployment.yaml` będzie służyć jako szablon Helm.

> Szablony generują pliki manifestów, które są opisami zasobów w formacie YAML, które mogą zrozumieć firmy Kubernetes. [- Przewodnik po szablonach wykresów helm][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Szablon określa usługę równoważenia obciążenia i wdrożenie kontenera/obrazu dla odczytu.

### <a name="the-kubernetes-package-helm-chart"></a>Pakiet Kubernetes (wykres Helm)

*Wykres Helm* zawiera konfigurację obrazów dokowych do ścinania do ściągnięcia z rejestru `containerpreview.azurecr.io` kontenerów.

> [Wykres helm][helm-charts] jest zbiorem plików, które opisują powiązany zestaw zasobów Kubernetes. Jeden wykres może służyć do wdrażania coś prostego, jak memcached zasobnika lub coś złożonego, jak pełny stos aplikacji sieci web z serwerami HTTP, baz danych, pamięci podręcznych i tak dalej.

Podane *wykresy Helm* wyciągnąć obrazy platformy docker usługi przetwarzania obrazu `containerpreview.azurecr.io` i odpowiedniej usługi z rejestru kontenerów.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalowanie wykresu Helm w klastrze Kubernetes

Aby zainstalować *wykres steru,* musimy wykonać [`helm install`][helm-install-cmd] polecenie. Upewnij się, że polecenie install wykona `read` polecenie z katalogu nad folderem.

```console
helm install read ./read
```

Oto przykładowe dane wyjściowe, których można oczekiwać po pomyślnym wykonaniu instalacji:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Wdrożenie usługi Kubernetes może potrwać kilka minut. Aby potwierdzić, że zarówno zasobników, jak i usług są poprawnie wdrożone i dostępne, wykonaj następujące polecenie:

```console
kubectl get all
```

Należy się spodziewać, aby zobaczyć coś podobnego do następującego wyjścia:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat instalowania aplikacji z helmem w usłudze Azure Kubernetes Service (AKS), [odwiedź tutaj][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
