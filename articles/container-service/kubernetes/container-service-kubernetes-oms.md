---
title: (PRZESTARZAŁE) Monitorowanie klastra kubernetes platformy Azure — zarządzanie operacjami
description: Monitorowanie klastra Kubernetes w usłudze Azure Container Service przy użyciu usługi Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371158"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi kontenerów platformy Azure za pomocą usługi Log Analytics

> [!TIP]
> Aby zapoznać się ze zaktualizowaną wersją tego artykułu, która korzysta z usługi Azure Kubernetes, zobacz [Usługa Azure Monitor dla kontenerów.](../../azure-monitor/insights/container-insights-overview.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu usługi Azure Container Service.](container-service-kubernetes-walkthrough.md)

Zakłada się również, że `az` masz `kubectl` zainstalowany plik cli platformy Azure i narzędzia.

Można sprawdzić, czy `az` masz zainstalowane narzędzie, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego `az` narzędzia, instrukcje są [tutaj](https://github.com/azure/azure-cli#installation).
Alternatywnie można użyć [usługi Azure Cloud](https://docs.microsoft.com/azure/cloud-shell/overview) `az` Shell , `kubectl` który ma cli platformy Azure i narzędzia już zainstalowane dla Ciebie.

Można sprawdzić, czy `kubectl` masz zainstalowane narzędzie, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego, możesz uruchomić:

```azurecli
az acs kubernetes install-cli
```

Aby sprawdzić, czy w narzędziu kubectl są zainstalowane klucze kubernetes:

```console
kubectl get nodes
```

Jeśli powyższe polecenie jest błędem, należy zainstalować klucze klastra kubernetes w narzędziu kubectl. Można to zrobić za pomocą następującego polecenia:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorowanie kontenerów za pomocą analizy dzienników

Usługa Log Analytics to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania it, które pomaga zarządzać infrastrukturą lokalną i chmurową oraz chronić ją.Rozwiązanie kontenera to rozwiązanie w usłudze Log Analytics, które ułatwia wyświetlanie zapasów kontenerów, wydajności i dzienników w jednej lokalizacji. Można przeprowadzić inspekcję, rozwiązywać problemy z kontenerami, wyświetlając dzienniki w scentralizowanej lokalizacji i znaleźć hałaśliwe zużycie nadmiaru kontenera na hoście.

![](media/container-service-monitoring-oms/image1.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenera, zapoznaj się z [analizą dziennika rozwiązania kontenerowego.](../../azure-monitor/insights/containers.md)

## <a name="installing-log-analytics-on-kubernetes"></a>Instalowanie analizy dzienników w usłudze Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Aby agent usługi Log Analytics rozmawiał z usługą, musi być skonfigurowany z identyfikatorem obszaru roboczego i kluczem obszaru roboczego. Aby uzyskać identyfikator obszaru roboczego i klucz, <https://mms.microsoft.com>musisz utworzyć konto w pliku .
Wykonaj kroki, aby utworzyć konto. Po zakończeniu tworzenia konta, można uzyskać swój identyfikator i klucz, klikając na **Log Analytics** bloku, a następnie nazwę obszaru roboczego. Następnie, w obszarze **Ustawienia zaawansowane**, **Połączone źródła**, a następnie **serwery Linuksa**, znajdziesz potrzebne informacje, jak pokazano poniżej.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Instalowanie agenta usługi Log Analytics przy użyciu zestawu Demonów
Zestawy demonów są używane przez kubernetes do uruchamiania pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Doskonale nadają się do uruchamiania agentów monitorujących.

Oto [plik DAEMonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Zapisz go w `oms-daemonset.yaml` pliku o nazwie i `WSID` zastąp wartości uchwytu miejsca dla i `KEY` z identyfikatorem obszaru roboczego i kluczem w pliku.

Po dodaniu identyfikatora obszaru roboczego i klucza do konfiguracji Demonset można zainstalować agenta `kubectl` usługi Log Analytics w klastrze za pomocą narzędzia wiersza polecenia:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalowanie agenta usługi Log Analytics przy użyciu klucza tajnego kubernetes
Aby chronić identyfikator obszaru roboczego usługi Log Analytics i klucz, można użyć klucza tajnego Kubernetes jako części pliku DAEMonSet YAML.

- Skopiuj skrypt, tajny plik szablonu i plik DAEMONSet YAML (z [repozytorium)](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)i upewnij się, że znajdują się w tym samym katalogu.
  - tajny skrypt generujący - secret-gen.sh
  - tajny szablon - secret-template.yaml
    - DaemonSet YAML plik - omsagent-ds-secrets.yaml
- Uruchom skrypt. Skrypt poprosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy. Wstaw to, a skrypt utworzy tajny plik yaml, aby można go uruchomić.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Utwórz zasobnik wpisów tajnych, uruchamiając następujące elementy:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Aby sprawdzić, uruchom następujące czynności:

  ```console
  kubectl get secrets
  ```

  ```output
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

  - Stwórz swój demon omsagent,uruchamiając następujące:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Podsumowanie
Gotowe. Po kilku minutach powinieneś być w stanie zobaczyć dane przesyłane do pulpitu nawigacyjnego usługi Log Analytics.
