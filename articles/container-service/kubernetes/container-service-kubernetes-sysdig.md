---
title: Monitorowanie Azure Kubernetes klaster - Sysdig
description: Monitorowanie Kubernetes klastra usługi kontenera platformy Azure przy użyciu Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 275e71ce054b83c16b9f9cbfe621c6a7e31f79c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Monitorowanie klastra Kubernetes usługi kontenera platformy Azure przy użyciu Sysdig

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że [utworzony klaster Kubernetes za pomocą usługi kontenera platformy Azure](container-service-kubernetes-walkthrough.md).

Przyjęto założenie, że masz azure narzędzi interfejsu wiersza polecenia i kubectl zainstalowanych.

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

## <a name="sysdig"></a>Sysdig
Sysdig jest zewnętrznych monitorowanie jako firmy usługi, które można monitorować kontenery w klastrze Kubernetes działające na platformie Azure. Przy użyciu Sysdig wymaga aktywnego konta Sysdig.
Można założyć konto ich [lokacji](https://app.sysdigcloud.com).

Po zalogowaniu w witrynie sieci Web chmury Sysdig kliknij swoją nazwę użytkownika, a na stronie powinien zostać wyświetlony klucz dostępu. 

![Klucz interfejsu API usługi Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalowanie agentów Sysdig do Kubernetes
Aby monitorować kontenerów, Sysdig uruchamia proces na każdym komputerze za pomocą Kubernetes `DaemonSet`.
DaemonSets są obiektami Kubernetes interfejsu API, które uruchomienia pojedynczego wystąpienia kontenera dla poszczególnych komputerów.
Są one idealne w przypadku instalowania narzędzi, takich jak Sysdig agenta monitorowania.

Aby zainstalować Sysdig daemonset, należy najpierw pobrać [szablon](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml) z sysdig. Zapisz ten plik jako `sysdig-daemonset.yaml`.

W systemie Linux i OS X, można uruchomić:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

W programie PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Następnie edytować ten plik, aby wstawić klucz dostępu, uzyskany z Twojego konta Sysdig.

Na koniec Utwórz DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Wyświetlanie, monitorowanie
Gdy zainstalowana i uruchomiona, agentów należy pompa Sysdig danych.  Wróć do [pulpitu nawigacyjnego sysdig](https://app.sysdigcloud.com) i powinny być widoczne informacje o kontenerów.

Można także zainstalować pulpity nawigacyjne Kubernetes określonego za pomocą [Kreator nowego pulpitu nawigacyjnego](https://app.sysdigcloud.com/#/dashboards/new).
