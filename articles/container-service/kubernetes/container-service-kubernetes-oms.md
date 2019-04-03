---
title: (PRZESTARZAŁE) Monitorowanie klastra Azure Kubernetes — zarządzanie operacjami
description: Monitorowanie klastra Kubernetes w usłudze Azure Container Service przy użyciu usługi Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: d7370fc14a5ede23744e04ac9d35140f2368e21f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877401"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service z usługą Log Analytics

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu, który korzysta z usługi Azure Kubernetes Service, zobacz [usługi Azure Monitor dla kontenerów](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, iż [utworzone za pomocą usługi Azure Container Service klastra Kubernetes](container-service-kubernetes-walkthrough.md).

Zakłada również, że masz `az` wiersza polecenia platformy Azure i `kubectl` narzędzia są zainstalowane.

Można sprawdzić, czy `az` zainstalowane, uruchamiając narzędzie:

```console
$ az --version
```

Jeśli nie masz `az` narzędzie jest zainstalowane, istnieją instrukcje [tutaj](https://github.com/azure/azure-cli#installation).
Alternatywnie, można użyć [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który ma `az` wiersza polecenia platformy Azure i `kubectl` narzędzia są już zainstalowane dla Ciebie.

Można sprawdzić, czy `kubectl` zainstalowane, uruchamiając narzędzie:

```console
$ kubectl version
```

Jeśli nie masz `kubectl` zainstalowany, możesz uruchomić:
```console
$ az acs kubernetes install-cli
```

Aby sprawdzić, czy klucze kubernetes zainstalowane w można uruchamiać narzędzie kubectl:
```console
$ kubectl get nodes
```

Jeśli powyższe błędy polecenia limit, należy zainstalować klucze klastra kubernetes do swojego narzędzia kubectl. Możesz to zrobić za pomocą następującego polecenia:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorowania kontenerów za pomocą usługi Log Analytics

Log Analytics jest firmy Microsoft oparte na chmurze rozwiązanie zarządzania IT, która ułatwia zarządzanie i chronić lokalne i infrastruktury chmury. Rozwiązanie kontenerów to rozwiązanie w usłudze Log Analytics, która ułatwia wyświetlanie dzienników kontenera magazynu, wydajności i w obrębie jednej lokalizacji. Możesz inspekcji, rozwiązywanie problemów z kontenerów, wyświetlając dzienniki w centralnej lokalizacji i Znajdź generujące dużo alertów, wykorzystywanie nadmiarowe kontenera na hoście.

![](media/container-service-monitoring-oms/image1.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenerów można znaleźć [kontenera rozwiązanie Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalowanie usługi Log Analytics na platformie Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Uzyskaj identyfikator obszaru roboczego i klucz usługi
Dla usługi Log Analytics agent do komunikacji z usługą musi skonfigurować identyfikator obszaru roboczego i klucz obszaru roboczego. Aby uzyskać identyfikator obszaru roboczego i klucz, musisz utworzyć konto w <https://mms.microsoft.com>.
Wykonaj poniższe kroki, aby utworzyć konto. Po wprowadzeniu zrobić, tworząc konto, można uzyskać Identyfikatora i klucza, klikając **usługi Log Analytics** bloku, a następnie nazwę obszaru roboczego. Następnie w obszarze **Zaawansowane ustawienia**, **połączone źródła**, a następnie **serwerów z systemem Linux**, można znaleźć informacje potrzebne, jak pokazano poniżej.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalowanie agenta usługi Log Analytics, za pomocą DaemonSet
DaemonSets są używane przez rozwiązanie Kubernetes do uruchomienia pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są idealne do uruchamiania agentów monitorowania.

Oto [pliku DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Zapisz go w pliku o nazwie `oms-daemonset.yaml` i Zastąp symbol zastępczy wartości `WSID` i `KEY` przy użyciu swojego Identyfikatora obszaru roboczego i klucz w pliku.

Po dodaniu swój identyfikator obszaru roboczego i klucz w konfiguracji DaemonSet można zainstalować agenta usługi Log Analytics w klastrze za pomocą `kubectl` narzędzie wiersza polecenia:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalowanie agenta usługi Log Analytics, za pomocą wpisu tajnego rozwiązania Kubernetes
Do ochrony klucza i identyfikator obszaru roboczego usługi Log Analytics umożliwia wpisie tajnym rozwiązania Kubernetes jako część pliku DaemonSet YAML.

- Skopiuj skrypt, plik klucza tajnego szablonu i pliku DaemonSet YAML (z [repozytorium](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) i upewnij się, że znajdują się w tym samym katalogu.
  - Generowanie skryptu - gen.sh klucz tajny klucz tajny
  - Szablon tajny - template.yaml wpisu tajnego
    - DaemonSet YAML file - omsagent-ds-secrets.yaml
- Uruchom skrypt. Skrypt poprosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy. Wstaw, i skrypt utworzy plik yaml wpisu tajnego, aby można było go uruchomić.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - Utwórz pod wpisami tajnymi, uruchamiając następujące: ```kubectl create -f omsagentsecret.yaml```

  - Aby sprawdzić, uruchom następujące polecenie:

  ```
  root@ubuntu16-13db:~# kubectl get secrets
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Utwórz swoje omsagent demona zestawu, uruchamiając ```kubectl create -f omsagent-ds-secrets.yaml```

### <a name="conclusion"></a>Podsumowanie
Gotowe. Po kilku minutach można wyświetlić danych przepływających do pulpitu nawigacyjnego usługi Log Analytics.
