---
title: Używanie kontenerów usługi mowy z kubernetami i helmem
titleSuffix: Azure Cognitive Services
description: Za pomocą Kubernetes i Helm do definiowania obrazów kontenerów zamiany mowy na tekst i zamiany tekstu na mowę utworzymy pakiet Kubernetes. Ten pakiet zostanie wdrożony w lokalnym klastrze kubernetes.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 6ad5a843c8cc287834305e09b48cd3fafe09ca51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474768"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Używanie kontenerów usługi mowy z kubernetami i helmem

Jedną z opcji zarządzania kontenerami mowy lokalnie jest użycie kubernetes i helm. Za pomocą Kubernetes i Helm do definiowania obrazów kontenerów zamiany mowy na tekst i zamiany tekstu na mowę utworzymy pakiet Kubernetes. Ten pakiet zostanie wdrożony w lokalnym klastrze kubernetes. Na koniec zbadamy, jak przetestować wdrożone usługi i różne opcje konfiguracji. Aby uzyskać więcej informacji na temat uruchamiania kontenerów platformy Docker bez aranżacji kubernetes, zobacz [instalowanie i uruchamianie kontenerów usługi mowy](speech-container-howto.md).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne przed użyciem kontenerów mowy w środowisku lokalnym:

| Wymagany | Przeznaczenie |
|----------|---------|
| Konto platformy Azure | Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][free-azure-account] przed rozpoczęciem. |
| Dostęp do rejestru kontenerów | Aby kubernetes wyciągnąć obrazy platformy docker do klastra, będzie potrzebował dostępu do rejestru kontenerów. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli] jest wymagane do zarządzania poświadczeniami udostępnionymi z rejestru kontenerów. Kubernetes jest również potrzebne przed Helm, który jest menedżerem pakietów Kubernetes. |
| Helm CLI | Zainstaluj [interfejsu wiersza polecenia helm][helm-install], który jest używany do instalowania wykresu helm (definicja pakietu kontenera). |
|Zasób mowy |Aby korzystać z tych pojemników, musisz mieć:<br><br>Zasób _platformy Speech_ Azure w celu uzyskania skojarzonego klucza rozliczeniowego i identyfikatora URI punktu końcowego rozliczeń. Obie wartości są dostępne na stronach **Omówienie mowy** i klucze witryny azure portal i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: klucz zasobu<br><br>**{ENDPOINT_URI}**: przykład identyfikatora URI punktu końcowego to:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Zalecana konfiguracja komputera-hosta

Odnoszą się do szczegółów [komputera hosta kontenera usługi mowy][speech-container-host-computer] jako odwołanie. Ten *wykres helm* automatycznie oblicza wymagania procesora CPU i pamięci na podstawie liczby dekodów (równoczesnych żądań), które określa użytkownik. Ponadto dostosuje się do tego, czy optymalizacje dla wprowadzania audio/tekstowego są skonfigurowane jako `enabled`. Domyślnie wykres helm, dwa równoczesne żądania i wyłączenie optymalizacji.

| Usługa | Procesor / kontener | Pamięć / Kontener |
|--|--|--|
| **Zamiana mowy na tekst** | jeden dekoder wymaga minimum 1150 milikores. Jeśli `optimizedForAudioFile` jest włączona, wymagane jest 1950 milikores. (domyślnie: dwa dekodery) | Wymagane: 2 GB<br>Limitowane: 4 GB |
| **Text-to-speech** | jednocześnie wymaga co najmniej 500 milikores. Jeśli `optimizeForTurboMode` jest włączona, wymagane jest 1000 milikores. (domyślnie: dwa równoczesne żądania) | Wymagane: 1 GB<br> Limitowane: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Łączenie się z klastrem Kubernetes

Oczekuje się, że komputer-host będzie miał dostępny klaster Kubernetes. Zobacz ten samouczek dotyczący [wdrażania klastra Kubernetes,](../../aks/tutorial-kubernetes-deploy-cluster.md) aby zapoznać się z koncepcyjnie, jak wdrożyć klaster Kubernetes na komputerze-hoście.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Udostępnianie poświadczeń platformy Docker za pomocą klastra Kubernetes

Aby zezwolić klastrowi Kubernetes na `docker pull` skonfigurowane `containerpreview.azurecr.io` obrazy z rejestru kontenerów, należy przenieść poświadczenia platformy docker do klastra. Wykonaj [`kubectl create`][kubectl-create] poniższe polecenie, aby utworzyć *klucz tajny rejestru platformy docker* na podstawie poświadczeń podanych z wymaganego dostępu do rejestru kontenera.

Z wybranego interfejsu wiersza polecenia uruchom następujące polecenie. Pamiętaj, aby `<username>`zastąpić `<password>`, `<email-address>` i poświadczenia rejestru kontenera.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Jeśli masz już dostęp `containerpreview.azurecr.io` do rejestru kontenerów, można utworzyć klucz tajny Kubernetes przy użyciu flagi ogólnej zamiast. Należy wziąć pod uwagę następujące polecenie, które wykonuje przeciwko konfiguracji platformy Docker JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Następujące dane wyjściowe są drukowane na konsoli po pomyślnym utworzeniu klucza tajnego.

```console
secret "mcr" created
```

Aby sprawdzić, czy klucz tajny [`kubectl get`][kubectl-get] został `secrets` utworzony, wykonaj z flagą.

```console
kubectl get secrets
```

Wykonywanie `kubectl get secrets` wydruków wszystkie skonfigurowane wpisy tajne.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurowanie wartości wykresu helm dla wdrożenia

Odwiedź [centrum Microsoft Helm Hub, aby][ms-helm-hub] uzyskać wszystkie publicznie dostępne wykresy helm oferowane przez firmę Microsoft. W centrum Microsoft Helm Hub znajdziesz **lokalny wykres mowy usług Cognitive Services**. **Program Cognitive Services Speech On-Premises** jest wykresem, który zainstalujemy, ale najpierw musimy utworzyć plik z jawnymi `config-values.yaml` konfiguracjami. Zacznijmy od dodania repozytorium firmy Microsoft do naszego wystąpienia Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Następnie skonfigurujemy nasze wartości wykresu Helm. Skopiuj i wklej następujący `config-values.yaml`PLIK YAML do pliku o nazwie . Aby uzyskać więcej informacji na temat dostosowywania **lokalnego wykresu helmu mowy usług Cognitive Services,** zobacz [dostosowywanie wykresów helm .](#customize-helm-charts) Zastąp `# {ENDPOINT_URI}` i `# {API_KEY}` komentarze własnymi wartościami.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Jeśli `billing` wartości `apikey` i wartości nie zostaną dostarczone, usługi wygasną po 15 minutach. Podobnie weryfikacja zakończy się niepowodzeniem, ponieważ usługi nie będą dostępne.

### <a name="the-kubernetes-package-helm-chart"></a>Pakiet Kubernetes (wykres Helm)

*Wykres Helm* zawiera konfigurację obrazów dokowych do ścinania do ściągnięcia z rejestru `containerpreview.azurecr.io` kontenerów.

> [Wykres helm][helm-charts] jest zbiorem plików, które opisują powiązany zestaw zasobów Kubernetes. Jeden wykres może służyć do wdrażania coś prostego, jak memcached zasobnika lub coś złożonego, jak pełny stos aplikacji sieci web z serwerami HTTP, baz danych, pamięci podręcznych i tak dalej.

Dostarczone *wykresy Helm* wyciągnąć obrazy platformy docker usługi mowy, zarówno tekst do mowy i `containerpreview.azurecr.io` usług zamiany mowy na tekst z rejestru kontenerów.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalowanie wykresu Helm w klastrze Kubernetes

Aby zainstalować *wykres helm* musimy wykonać [`helm install`][helm-install-cmd] polecenie, zastępując `<config-values.yaml>` odpowiednią ścieżkę i argument nazwy pliku. Wykres `microsoft/cognitive-services-speech-onpremise` Helm, do którego odwołuje się poniższy, jest dostępny w [Microsoft Helm Hub tutaj][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Oto przykładowe dane wyjściowe, których można oczekiwać po pomyślnym wykonaniu instalacji:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Wdrożenie usługi Kubernetes może potrwać kilka minut. Aby potwierdzić, że zarówno zasobników, jak i usług są poprawnie wdrożone i dostępne, wykonaj następujące polecenie:

```console
kubectl get all
```

Należy się spodziewać, aby zobaczyć coś podobnego do następującego wyjścia:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Weryfikowanie wdrażania helmu za pomocą testów Helm

Zainstalowane wykresy Helm definiują *testy Helm,* które służą jako wygoda weryfikacji. Te testy sprawdzają gotowość usługi. Aby zweryfikować zarówno **zamiany mowy na tekst,** jak i usługi **zamiany tekstu na mowę,** wykonamy polecenie [testu Helm.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Te testy zakończy się niepowodzeniem, jeśli stan POD `Running` nie `AVAILABLE` jest lub jeśli wdrożenie nie jest wymienione w kolumnie. Bądź cierpliwy, ponieważ może to potrwać ponad dziesięć minut.

Testy te będą wyprowadzać różne wyniki stanu:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Jako alternatywę dla wykonywania *testów steru*można zebrać zewnętrzne adresy *IP* i odpowiednie porty z `kubectl get all` polecenia. Korzystając z adresu IP i portu, `http://<external-ip>:<port>:/swagger/index.html` otwórz przeglądarkę internetową i przejdź do, aby wyświetlić strony swagger interfejsu API.

## <a name="customize-helm-charts"></a>Dostosowywanie wykresów helm

Wykresy helm są hierarchiczne. Hierarchiczne umożliwia dziedziczenie wykresu, ale także uwzględnia pojęcie specyfiki, gdzie ustawienia, które są bardziej szczegółowe zastępowania reguł dziedziczonych.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
