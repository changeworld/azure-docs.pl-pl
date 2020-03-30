---
title: (PRZESTARZAŁE) Monitorowanie klastra platformy Azure Kubernetes — Sysdig
description: Monitorowanie klastra Kubernetes w usłudze Kontener azure przy użyciu narzędzia Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371103"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(PRZESTARZAŁE) Monitorowanie klastra kubernetes usługi kontenera platformy Azure przy użyciu narzędzia Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu usługi Azure Container Service.](container-service-kubernetes-walkthrough.md)

Zakłada się również, że masz zainstalowane narzędzia azure cli i kubectl.

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

## <a name="sysdig"></a>Sysdig
Sysdig jest zewnętrznym monitorowaniem jako firma usługowa, która może monitorować kontenery w klastrze kubernetes uruchomionym na platformie Azure. Korzystanie z Sysdig wymaga aktywnego konta Sysdig.
Możesz założyć konto na swojej [stronie.](https://app.sysdigcloud.com)

Po zalogowaniu w witrynie sieci Web chmury Sysdig kliknij swoją nazwę użytkownika, a na stronie powinien zostać wyświetlony klucz dostępu. 

![Klucz interfejsu API usługi Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalowanie agentów Sysdiga w umięsnetes
Aby monitorować kontenery, Sysdig uruchamia proces na każdym `DaemonSet`komputerze przy użyciu aplikacji Kubernetes .
DaemonSets są Kubernetes obiektów interfejsu API, które uruchamiają pojedyncze wystąpienie kontenera na komputerze.
Są idealne do instalowania narzędzi, takich jak agent monitorujący Sysdiga.

Aby zainstalować demona Sysdiga, należy najpierw pobrać [szablon](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) z sysdiga. Zapisz ten `sysdig-daemonset.yaml`plik jako .

Na Linuksie i OS X można uruchomić:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

W programie PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Następnie edytuj ten plik, aby wstawić klucz dostępu, który został uzyskany z konta Sysdig.

Na koniec utwórz Zestaw Demonów:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Wyświetlanie monitorowania
Po zainstalowaniu i uruchomieniu agenci powinni pompować dane z powrotem do Sysdig.  Wróć do [pulpitu nawigacyjnego sysdiga](https://app.sysdigcloud.com) i powinieneś zobaczyć informacje o kontenerach.

Pulpity nawigacyjne specyficzne dla aplikacji Kubernetes można również zainstalować za pomocą [nowego kreatora pulpitu nawigacyjnego.](https://app.sysdigcloud.com/#/dashboards/new)
