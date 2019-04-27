---
title: (PRZESTARZAŁE) Monitorowanie klastra Kubernetes na platformie Azure za pomocą usługi Datadog
description: Monitorowanie klastra Kubernetes w usłudze Azure Container Service przy użyciu pomocą usługi Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712342"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service za pomocą usługi DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, iż [utworzone za pomocą usługi Azure Container Service klastra Kubernetes](container-service-kubernetes-walkthrough.md).

Zakłada również, że masz `az` wiersza polecenia platformy Azure i `kubectl` narzędzia są zainstalowane.

Można sprawdzić, czy `az` zainstalowane, uruchamiając narzędzie:

```console
$ az --version
```

Jeśli nie masz `az` narzędzie jest zainstalowane, istnieją instrukcje [tutaj](https://github.com/azure/azure-cli#installation).

Można sprawdzić, czy `kubectl` zainstalowane, uruchamiając narzędzie:

```console
$ kubectl version
```

Jeśli nie masz `kubectl` zainstalowany, możesz uruchomić:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Pomocą usługi Datadog jest usługą monitorowania, która gromadzi dane monitorowania z kontenerów w klastrze usługi Azure Container Service. Pomocą usługi Datadog ma na pulpicie nawigacyjnym integracji platformy Docker, którym można zobaczyć określonych metryk w Twoich kontenerów. Metryki zebrane z kontenerów są uporządkowane według procesora CPU, pamięci, sieci i we/wy. Pomocą usługi Datadog dzieli metryki na kontenerach i obrazy.

Najpierw musisz [Tworzenie konta usługi](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalowanie agenta pomocą usługi Datadog z DaemonSet
DaemonSets są używane przez rozwiązanie Kubernetes do uruchomienia pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są idealne do uruchamiania agentów monitorowania.

Po zalogowaniu się w pomocą usługi Datadog, możesz wykonać [instrukcje pomocą usługi Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) instalacji pomocą usługi Datadog agentów w klastrze za pomocą DaemonSet.

## <a name="conclusion"></a>Podsumowanie
Gotowe. Po skonfigurowaniu i uruchomieniu agentów dane w konsoli powinny być widoczne w ciągu kilku minut. Możesz odwiedzić stronę zintegrowane [pulpit nawigacyjny platformy kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) Aby wyświetlić podsumowanie Twojego klastra.
