---
title: (PRZESTARZAŁE) Monitorowanie klastra kubernetes platformy Azure za pomocą datadoga
description: Monitorowanie klastra Kubernetes w usłudze Azure Container Service przy użyciu programu Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371175"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi kontenera platformy Azure za pomocą witryny DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu usługi Azure Container Service.](container-service-kubernetes-walkthrough.md)

Zakłada się również, że `az` masz `kubectl` zainstalowany plik cli platformy Azure i narzędzia.

Można sprawdzić, czy `az` masz zainstalowane narzędzie, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego `az` narzędzia, instrukcje są [tutaj](https://github.com/azure/azure-cli#installation).

Można sprawdzić, czy `kubectl` masz zainstalowane narzędzie, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego, możesz uruchomić:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog (Polski)
Datadog to usługa monitorowania, która zbiera dane monitorowania z kontenerów w klastrze usługi Azure Container Service. Datadog ma pulpit nawigacyjny integracji platformy Docker, gdzie można zobaczyć określone metryki w kontenerach. Metryki zebrane z kontenerów są zorganizowane przez procesor, pamięć, sieć i we/wy. Datadog dzieli metryki na kontenery i obrazy.

Najpierw musisz [utworzyć konto](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalowanie agenta Datadog z zestawem demonów
Zestawy demonów są używane przez kubernetes do uruchamiania pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Doskonale nadają się do uruchamiania agentów monitorujących.

Po zalogowaniu się do datadog, można wykonać [instrukcje Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) zainstalować agentów Datadog w klastrze przy użyciu DaemonSet.

## <a name="conclusion"></a>Podsumowanie
Gotowe. Po uruchomieniu agentów powinny być widoczne dane w konsoli w ciągu kilku minut. Możesz odwiedzić zintegrowany [pulpit nawigacyjny kubernetes,](https://app.datadoghq.com/screen/integration/kubernetes) aby wyświetlić podsumowanie klastra.
