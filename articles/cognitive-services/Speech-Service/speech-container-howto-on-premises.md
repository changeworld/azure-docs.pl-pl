---
title: Użyj rozwiązania Kubernetes w środowisku lokalnym
titleSuffix: Azure Cognitive Services
description: Za pomocą platformy Kubernetes i Helm do definiowania obrazów kontenerów mowy na tekst i zamiany tekstu na mowę, utworzymy pakiet dla platformy Kubernetes. Ten pakiet zostanie wdrożony do rozwiązania Kubernetes klastra w środowisku lokalnym.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786256"
---
# <a name="use-kubernetes-on-premises"></a>Użyj rozwiązania Kubernetes w środowisku lokalnym

Za pomocą platformy Kubernetes i Helm do definiowania obrazów kontenerów mowy na tekst i zamiany tekstu na mowę, utworzymy pakiet dla platformy Kubernetes. Ten pakiet zostanie wdrożony do rozwiązania Kubernetes klastra w środowisku lokalnym. Ponadto pokażemy, jak przetestować różne opcje konfiguracji i wdrożonych usług.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem mowy kontenerów w środowisku lokalnym, musi spełniać następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
| Azure Account | Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][free-azure-account]. |
| Dostęp do rejestru kontenerów | Aby Kubernetes do ściągania obrazów platformy docker do klastra będzie musiała dostępu do rejestru kontenerów. Musisz [żądanie dostępu do rejestru kontenerów][speech-preview-access] pierwszy. |
| Interfejs wiersza polecenia usługi Kubernetes | [Interfejsu wiersza polecenia Kubernetes][kubernetes-cli] jest wymagana do zarządzania poświadczeń udostępnionych z rejestru kontenerów. Kubernetes jest również potrzebne przed Helm, czyli Menedżer pakietów platformy Kubernetes. |
| Interfejs wiersza polecenia narzędzia Helm | Jako część [interfejsu wiersza polecenia narzędzia Helm][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]. |
|Zasób mowy |Aby można było używać tych kontenerów, musisz mieć:<br><br>A _mowy_ zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne w witrynie Azure portal **mowy** strony Przegląd i klucze i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : klucz zasobu<br><br>**{ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Konfiguracja komputera zalecany host

Zapoznaj się [komputer-host kontenera usługi mowy][speech-container-host-computer] szczegóły jako odwołanie. To *narzędzia Helm* automatycznie oblicza zapotrzebowanie Procesora i pamięci, w oparciu o ile dekoduje (żądań) podaną przez użytkownika. Ponadto skoryguje zależnie od tego, czy optymalizacje audio/tekstowych danych wejściowych są skonfigurowane jako `enabled`. Domyślnie wykresu helm do dwóch jednoczesnych żądań i wyłączenie optymalizacji.

| Usługa | Procesor CPU / kontenera | Pamięć / kontenera |
|--|--|--|
| **Zamiany mowy na tekst** | jeden dekoder wymaga co najmniej 1,150 millicores. Jeśli `optimizedForAudioFile` jest włączona, a następnie 1,950 millicores są wymagane. (domyślne: dwa dekodery) | Wymagane: 2 GB<br>Ograniczone:  4 GB |
| **Zamiana tekstu na mowę** | jedno żądanie współbieżnych wymaga co najmniej 500 millicores. Jeśli `optimizeForTurboMode` jest włączona, 1000 millicores są wymagane. (domyślne: dwóch jednoczesnych żądań) | Wymagane: 1 GB<br> Ograniczone: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Nawiąż połączenie z klastrem Kubernetes

Komputer-host jest powinny mieć dostępne klastra Kubernetes. Zobacz w tym samouczku na [wdrażaniu klastra Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) dla pojęć dotyczących wdrażania klastra Kubernetes do komputera-hosta.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Udostępnianie poświadczenia platformy Docker z klastrem Kubernetes

Aby umożliwić klastra Kubernetes do `docker pull` skonfigurowane obrazy z `containerpreview.azurecr.io` rejestru kontenerów, musisz przenieść poświadczenia platformy docker do klastra. Wykonaj [ `kubectl create` ][kubectl-create] poniższe polecenie, aby utworzyć *klucz tajny rejestru platformy docker* oparte na poświadczeniach dostarczane z wstępnie wymaganego składnika dostępu do rejestru kontenerów.

Z poziomu interfejsu wiersza polecenia, wyboru, uruchom następujące polecenie. Koniecznie Zastąp `<username>`, `<password>`, i `<email-address>` przy użyciu poświadczeń rejestru kontenerów.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Jeśli masz już dostęp do `containerpreview.azurecr.io` rejestru kontenerów, można utworzyć przy użyciu flagi ogólny, zamiast tego wpisu tajnego rozwiązania Kubernetes. Należy wziąć pod uwagę następujące polecenie, który jest wykonywany przed JSON konfiguracji platformy Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Następujące dane wyjściowe są drukowane w konsoli po pomyślnym utworzeniu wpisu tajnego.

```console
secret "containerpreview" created
```

Aby sprawdzić, czy został utworzony klucz tajny, wykonaj [ `kubectl get` ][kubectl-get] z `secrets` flagi.

```console
kuberctl get secrets
```

Wykonywanie `kubectl get secrets` drukuje wszystkich skonfigurowanych wpisów tajnych.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurowanie wartości wykresu Helm do wdrożenia

Odwiedź stronę [Microsoft Helm Hub][ms-helm-hub] dla wszystkich wykresów publicznie dostępne narzędzia helm, oferowane przez firmę Microsoft. Z poziomu Centrum Helm firmy Microsoft można znaleźć **Cognitive Services mowy w środowisku lokalnym wykres**. **Cognitive Services mowy On-Premises** wykres zostanie przedstawiona instalacja, ale należy najpierw utworzyć `config-values.yaml` plików za pomocą jawnej konfiguracji. Najpierw Dodajmy repozytorium firmy Microsoft do naszych wystąpienia narzędzia Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Następnie skonfigurujemy naszych wartości wykresu Helm. Skopiuj i Wklej poniższego kodu YAML w pliku o nazwie `config-values.yaml`. Aby uzyskać więcej informacji na temat dostosowywania **Cognitive Services mowy lokalne narzędzia Helm**, zobacz [Dostosowywanie wykresów rozwiązania helm](#customize-helm-charts). Zastąp `billing` i `apikey` wartości swoimi własnymi.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Jeśli `billing` i `apikey` wartości nie są dostarczane, usługi wygaśnie po 15 minut. Podobnie weryfikacja zakończy się niepowodzeniem, ponieważ usługi nie będą dostępne.

### <a name="the-kubernetes-package-helm-chart"></a>Pakiet rozwiązania Kubernetes (narzędzia Helm)

*Narzędzia Helm* konfigurację, które obrazów platformy docker, aby ściągnąć z `containerpreview.azurecr.io` rejestru kontenerów.

> A [narzędzia Helm][helm-charts] to zbiór plików, które opisują zestaw powiązanych zasobów platformy Kubernetes. Pojedynczy wykres mogą być używane do wdrażania coś prostego, takie jak zasobnik memcached lub złożonej, takich jak web pełną stosu aplikacji za pomocą protokołu HTTP serwerów, baz danych, pamięci podręcznych i tak dalej.

Podany *Helm wykresy* ściąganie obrazów platformy docker z usługi rozpoznawania mowy, zamiana tekstu na mowę i usług mowy na tekst z `containerpreview.azurecr.io` rejestru kontenerów.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Zainstaluj pakiet Helm w klastrze usługi Kubernetes

Aby zainstalować *narzędzia Helm* musimy wykonać [ `helm install` ][helm-install-cmd] polecenia, zastępując `<config-values.yaml>` przy użyciu odpowiedniego argumentu ścieżkę i nazwę. `microsoft/cognitive-services-speech-onpremise` Narzędzia Helm poniżej znajduje się na [Microsoft Helm tutaj Hub][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Poniżej przedstawiono przykładowe dane wyjściowe oczekiwanych stwierdzić na podstawie wykonywania pomyślnej instalacji:

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

Wdrażanie rozwiązania Kubernetes można zająć w ciągu kilku minut. Aby upewnić się, że zarówno zasobników, jak i usługi są poprawnie wdrożone i dostępne, wykonaj następujące polecenie:

```console
kubectl get all
```

Należy się spodziewać wyświetlone informacje podobne do następujących danych wyjściowych:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Sprawdź wdrażania narzędzia Helm, za pomocą narzędzia Helm testów

Zdefiniuj zainstalowanych wykresów rozwiązania Helm *Helm testy*, które służą jako udogodnienie weryfikacji. Te testy sprawdzania poprawności gotowości usługi. Aby sprawdzić, zarówno **mowy na tekst** i **zamiany tekstu na mowę** usług, firma Microsoft będzie wykonać [testu Helm][helm-test] polecenia.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Te testy zakończy się niepowodzeniem, jeśli nie jest w stanie ZASOBNIKA `Running` lub jeśli wdrożenia nie znajduje się w obszarze `AVAILABLE` kolumny. Cierpliwość, ponieważ może to potrwać ponad dziesięć minut, aby zakończyć.

Testy te będą dane wyjściowe różnych stanu:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Jako alternatywa dla wykonywania *helm testy*, można zbierać *zewnętrzny adres IP* adresy i porty odpowiedniego z `kubectl get all` polecenia. Korzystając z adresu IP i portu, otwórz przeglądarkę internetową i przejdź do `http://<external-ip>:<port>:/swagger/index.html` do interfejsu API struktury swagger strony widoku.

## <a name="customize-helm-charts"></a>Dostosowywanie wykresów rozwiązania Helm

Narzędzia Helm są hierarchiczne. Jest hierarchiczna umożliwia dziedziczenia wykresu on również przeznaczony dla koncepcji specyficzności, których ustawienia, które są bardziej szczegółowe zastępują reguły odziedziczone.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat instalowania aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS) [można znaleźć w tym miejscu][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontenery usługi cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
