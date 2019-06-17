---
title: (PRZESTARZAŁE) Monitorowanie klastra usługi Azure Kubernetes za pomocą usługi CoScale
description: Monitorowanie klastra Kubernetes w usłudze Azure Container Service przy użyciu pomocą usługi CoScale
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 895346447e33926dcaa5ca09302f35c9d6636ed9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60713081"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service Kubernetes za pomocą usługi CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

W tym artykule pokazujemy, jak wdrożyć [pomocą usługi CoScale](https://web.archive.org/web/20180317071550/ https://www.coscale.com/) agenta, aby monitorować wszystkie węzły, jak i kontenery w klastrze Kubernetes w usłudze Azure Container Service. Wymagane jest konto, za pomocą usługi CoScale dla tej konfiguracji. 


## <a name="about-coscale"></a>Temat pomocą usługi CoScale 

Pomocą usługi coScale to platforma monitorowania, która zbiera metryki i zdarzenia z wszystkich kontenerów na wielu platformach aranżacji. Pomocą usługi coScale oferuje kompleksowym monitorowaniu środowiskami usługi Kubernetes. Zapewnia wszystkie warstwy stosu wizualizacji i analizy: OS, Kubernetes, Docker i aplikacji uruchamianych wewnątrz kontenerów. Pomocą usługi coScale oferuje kilka wbudowanych pulpitów nawigacyjnych monitorowania i ma wbudowaną funkcję wykrywania anomalii umożliwia operatorów i deweloperom szybko znaleźć problemy dotyczące infrastruktury i aplikacji.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

Jak pokazano w tym artykule, można zainstalować agentów w klastrze Kubernetes, aby uruchomić pomocą usługi CoScale jako rozwiązanie SaaS. Jeśli chcesz zachować dane na miejscu, pomocą usługi CoScale jest również dostępna do instalacji lokalnych.


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz [Utwórz konto pomocą usługi CoScale](https://web.archive.org/web/20170507123133/ https://www.coscale.com/free-trial).

W tym przewodniku przyjęto założenie, iż [utworzone za pomocą usługi Azure Container Service klastra Kubernetes](container-service-kubernetes-walkthrough.md).

Zakłada również, że masz `az` wiersza polecenia platformy Azure i `kubectl` narzędzia są zainstalowane.

Można sprawdzić, czy `az` zainstalowane, uruchamiając narzędzie:

```azurecli
az --version
```

Jeśli nie masz `az` narzędzie jest zainstalowane, istnieją instrukcje [tutaj](/cli/azure/install-azure-cli).

Można sprawdzić, czy `kubectl` zainstalowane, uruchamiając narzędzie:

```bash
kubectl version
```

Jeśli nie masz `kubectl` zainstalowany, możesz uruchomić:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalowanie agenta pomocą usługi CoScale z DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) są używane przez rozwiązania Kubernetes, aby uruchomić pojedyncze wystąpienie kontenera na każdym hoście w klastrze.
Są idealne do uruchamiania agentów monitorowania, takich jak agent pomocą usługi CoScale.

Po zalogowaniu się w pomocą usługi CoScale, przejdź do [agent strona](https://app.coscale.com/) instalacji pomocą usługi CoScale agentów w klastrze za pomocą DaemonSet. Interfejs użytkownika pomocą usługi CoScale zawiera kroki konfiguracji z przewodnikiem, aby utworzyć agenta i rozpocząć monitorowanie pełną klastra Kubernetes.

![Konfiguracja agenta pomocą usługi coScale](./media/container-service-kubernetes-coscale/installation.png)

Aby uruchomić agenta w klastrze, uruchom polecenie dostarczony:

![Uruchom agenta pomocą usługi CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

To wszystko! Po skonfigurowaniu i uruchomieniu agentów dane w konsoli powinny być widoczne w ciągu kilku minut. Odwiedź stronę [agent strona](https://app.coscale.com/) znajduje się pozycja klastra podsumowanie wykonać dodatkowe czynności konfiguracyjne i wyświetlić pulpity nawigacyjne, takie jak **klaster Kubernetes — omówienie**.

![Omówienie klastra Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Agent pomocą usługi CoScale jest automatycznie wdrażane na nowych komputerach w klastrze. Agent jest aktualizowana automatycznie, gdy wydaniu nowej wersji.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [pomocą usługi CoScale dokumentacji](https://web.archive.org/web/20180415164304/ http://docs.coscale.com:80/) i [blogu](https://web.archive.org/web/20170501021344/ http://www.coscale.com:80/blog) Aby uzyskać więcej informacji na temat pomocą usługi CoScale rozwiązania do monitorowania. 

