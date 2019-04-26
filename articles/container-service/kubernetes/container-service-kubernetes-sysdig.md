---
title: (PRZESTARZAŁE) Monitorowanie klastra Kubernetes na platformie Azure — usługi Sysdig
description: Monitorowanie klastra Kubernetes w usłudze Azure Container Service za pomocą narzędzia Sysdig
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309275"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service Kubernetes za pomocą narzędzia Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku przyjęto założenie, iż [utworzone za pomocą usługi Azure Container Service klastra Kubernetes](container-service-kubernetes-walkthrough.md).

Przyjęto również założenie, że masz azure narzędzi interfejsu wiersza polecenia i narzędzia kubectl zainstalowanych.

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

## <a name="sysdig"></a>Sysdig
Sysdig jest zewnętrzne monitorowania firma usługa, która umożliwia monitorowanie kontenerów w klastrze Kubernetes działających na platformie Azure. Za pomocą narzędzia Sysdig wymaga aktywne konto usługi Sysdig.
Zarejestruj się do konta na ich [witryny](https://app.sysdigcloud.com).

Po zalogowaniu w witrynie sieci Web chmury Sysdig kliknij swoją nazwę użytkownika, a na stronie powinien zostać wyświetlony klucz dostępu. 

![Klucz interfejsu API usługi Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalowanie agentów rozwiązania Sysdig usłudze Kubernetes
Do monitorowania kontenerów, usługa Sysdig uruchamia proces na każdym komputerze, przy użyciu usługi Kubernetes `DaemonSet`.
DaemonSets są obiektami interfejsu API rozwiązania Kubernetes, systemem pojedynczego wystąpienia kontenera na komputerze.
Są one doskonałe dotyczące instalowania narzędzi, takich jak agent monitorowania usługi Sysdig.

Aby zainstalować daemonset usługi Sysdig, należy najpierw pobrać [szablonu](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) z usługi sysdig. Zapisz ten plik jako `sysdig-daemonset.yaml`.

W systemie Linux i OS X, można uruchomić:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

W programie PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Następnie Edytuj ten plik, aby wstawić klucz dostępu uzyskany ze swojego konta usługi Sysdig.

Na koniec Utwórz element DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Wyświetl monitorowania
Gdy zainstalowana i uruchomiona, agentów należy pompy dane z powrotem do usługi Sysdig.  Wróć do [pulpit nawigacyjny usługi sysdig](https://app.sysdigcloud.com) i powinny być widoczne informacje o Twoich kontenerów.

Można także zainstalować pulpity nawigacyjne specyficzne dla rozwiązania Kubernetes za pomocą [Kreatora nowego pulpitu nawigacyjnego](https://app.sysdigcloud.com/#/dashboards/new).
