---
title: PRZESTARZAŁE Monitorowanie klastra usługi Azure Kubernetes przy użyciu funkcji skalowania
description: Monitorowanie klastra Kubernetes w Azure Container Service przy użyciu funkcji skalowania w poziomie
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271096"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service Kubernetes przy użyciu funkcji skalowania w poziomie

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

W tym artykule pokazano, jak wdrożyć agenta [skalowania](https://web.archive.org/web/20180317071550/https://www.coscale.com/) w celu monitorowania wszystkich węzłów i kontenerów w klastrze Kubernetes w Azure Container Service. Do tej konfiguracji jest potrzebne konto ze skalowaniem. 


## <a name="about-coscale"></a>Skalowanie — informacje 

Skalowanie jest platformą monitorowania, która zbiera metryki i zdarzenia ze wszystkich kontenerów na kilku platformach aranżacji. Funkcja skalowania oferuje pełne monitorowanie w środowiskach Kubernetes. Udostępnia wizualizacje i analizy dla wszystkich warstw na stosie: system operacyjny, Kubernetes, Docker i aplikacje działające w kontenerach. Funkcja skalowania oferuje kilka wbudowanych pulpitów nawigacyjnych monitorowania i ma wbudowaną funkcję wykrywania anomalii, która umożliwia operatorom i deweloperom szybkie znajdowanie problemów z infrastrukturą i aplikacjami.

![Interfejs użytkownika do skalowania](./media/container-service-kubernetes-coscale/coscale.png)

Jak pokazano w tym artykule, można zainstalować agentów w klastrze Kubernetes, aby uruchamiać skalowanie jako rozwiązanie SaaS. Jeśli chcesz zachować dostęp do danych w lokacji, funkcja skalowania jest również dostępna dla instalacji lokalnej.


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz [utworzyć konto do skalowania](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

W tym instruktażu przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu Azure Container Service](container-service-kubernetes-walkthrough.md).

Przyjęto również założenie, że masz zainstalowaną `az` interfejs wiersza polecenia platformy Azure i narzędzia `kubectl`.

Możesz sprawdzić, czy masz narzędzie `az` zainstalowane, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego narzędzia `az`, w [tym miejscu](/cli/azure/install-azure-cli)znajdują się instrukcje.

Możesz sprawdzić, czy masz narzędzie `kubectl` zainstalowane, uruchamiając:

```bash
kubectl version
```

Jeśli nie masz zainstalowanego `kubectl`, możesz uruchomić następujące polecenie:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalowanie agenta współskali z elementu daemonset
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) są używane przez Kubernetes do uruchamiania pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są one idealne do uruchamiania agentów monitorowania, takich jak Agent skalowania.

Po zalogowaniu się do obszaru skalowania przejdź do [strony agenta](https://app.coscale.com/) , aby zainstalować agentów współskalowania w klastrze przy użyciu elementu daemonset. Interfejs użytkownika z przedziałem międzyskalowania zawiera kroki konfiguracji z przewodnikiem umożliwiające utworzenie agenta i rozpoczęcie monitorowania całego klastra Kubernetes.

![Konfiguracja agenta skalowania](./media/container-service-kubernetes-coscale/installation.png)

Aby uruchomić agenta w klastrze, uruchom dostarczone polecenie:

![Uruchom agenta skalowania](./media/container-service-kubernetes-coscale/agent_script.png)

Gotowe. Gdy agenci działają i działają, dane w konsoli powinny być widoczne w ciągu kilku minut. Odwiedź [stronę agenta](https://app.coscale.com/) , aby zobaczyć podsumowanie klastra, wykonać dodatkowe czynności konfiguracyjne i zapoznać się z pulpitami nawigacyjnymi, takimi jak **klaster Kubernetes**.

![Omówienie klastra Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Agent skalowania w poziomie jest automatycznie wdrażany na nowych maszynach w klastrze. Agent jest automatycznie aktualizowany po wydaniu nowej wersji.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązań do monitorowania w ramach skalowania, zobacz [dokumentację dotyczącą rozbudowy](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) i [blog](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) . 

