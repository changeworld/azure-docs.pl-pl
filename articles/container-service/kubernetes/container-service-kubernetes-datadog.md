---
title: Monitor Azure Kubernetes klaster z Datadog
description: Monitorowanie Kubernetes klastra usługi kontenera platformy Azure przy użyciu Datadog
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0a3f0baa4998dbc594023935575d659f7d45bbb9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Monitor klastra usługi kontenera platformy Azure z DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że [utworzony klaster Kubernetes za pomocą usługi kontenera platformy Azure](container-service-kubernetes-walkthrough.md).

Założono również, że masz `az` interfejsu wiersza polecenia platformy Azure i `kubectl` narzędzia są zainstalowane.

Możesz przetestować, jeśli masz `az` zainstalowany, uruchamiając narzędzie:

```console
$ az --version
```

Jeśli nie masz `az` narzędzie zainstalowane, nie ma instrukcji [tutaj](https://github.com/azure/azure-cli#installation).

Możesz przetestować, jeśli masz `kubectl` zainstalowany, uruchamiając narzędzie:

```console
$ kubectl version
```

Jeśli nie masz `kubectl` zainstalowany, możesz uruchomić:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog jest usługą monitorowania, która gromadzi dane monitorowania z kontenerów w ramach klastra usługi kontenera platformy Azure. Datadog ma pulpitu nawigacyjnego Docker integracji umożliwia wyświetlenie określonych metryk w kontenerów. Metryki zebrane z kontenerów są zorganizowane według Procesora, pamięci, sieci i we/wy. Datadog dzieli metryki na kontenery i obrazów.

Należy najpierw [Tworzenie konta usługi](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalowanie agenta Datadog z DaemonSet
DaemonSets są używane przez Kubernetes do uruchomienia pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są one idealne w przypadku uruchamiania agenci monitorowania.

Po zalogowania do Datadog, można użyć [instrukcje Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) do instalacji agentów Datadog w klastrze za pomocą DaemonSet.

## <a name="conclusion"></a>Podsumowanie
Gotowe. Po skonfigurowaniu i uruchomieniu agentów danych w konsoli powinna zostać wyświetlona za kilka minut. Użytkownik może odwiedzić zintegrowanego [pulpitu nawigacyjnego kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) wyświetlić podsumowanie klastra.
