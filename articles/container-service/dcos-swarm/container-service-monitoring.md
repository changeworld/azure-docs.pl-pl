---
title: Monitor klastra Azure DC/OS - Datadog
description: Monitorowanie klastra usługi kontenera platformy Azure z Datadog. Użyj interfejsu użytkownika sieci web platformy DC/OS, aby wdrożyć agentów Datadog do klastra.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 029f36e4362704fcec240f6e88da5c96e903c317
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Monitor klastra usługi kontenera platformy Azure DC/OS z Datadog

W tym artykule wdrożymy Datadog agentów dla wszystkich węzłów w klastrze usługi kontenera platformy Azure agenta. Musisz mieć konto z Datadog dla tej konfiguracji. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [połącz](../container-service-connect.md) klaster skonfigurowany przez usługę Azure Container Service. Przegląd [interfejsu użytkownika platformy Marathon](container-service-mesos-marathon-ui.md). Przejdź do [ http://datadoghq.com ](http://datadoghq.com) skonfigurować konto Datadog. 

## <a name="datadog"></a>Datadog
Datadog jest usługą monitorowania, która gromadzi dane monitorowania z kontenerów w ramach klastra usługi kontenera platformy Azure. Datadog ma pulpitu nawigacyjnego Docker integracji umożliwia wyświetlenie określonych metryk w kontenerów. Metryki zebrane z kontenerów są zorganizowane według Procesora, pamięci, sieci i we/wy. Datadog dzieli metryki na kontenery i obrazów. Przykładem jak wygląda interfejs użytkownika użycia procesora CPU jest poniżej.

![Datadog interfejsu użytkownika](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurowanie wdrożenia Datadog przy użyciu platformy Marathon
Te kroki opisano sposób konfigurowania i wdrażania aplikacji Datadog do klastra przy użyciu platformy Marathon. 

Dostęp za pośrednictwem interfejsu użytkownika platformy DC/OS [ http://localhost:80/ ](http://localhost:80/). Raz w Interfejsie użytkownika DC/OS przejdź do "Universe", który znajduje się w lewym dolnym rogu okna, następnie odszukaj "Datadog" i kliknij przycisk Zainstaluj.

![Pakiet Datadog w Uniwersum DC/OS](./media/container-service-monitoring/datadog1.png)

Teraz w celu ukończenia konfiguracji konieczne będzie konto Datadog lub bezpłatne konto próbne. Gdy jest zalogowany Datadog wyglądu witryny sieci Web w lewo i przejdź do integracji -> następnie [interfejsów API](https://app.datadoghq.com/account/settings#api). 

![Klucz interfejsu API Datadog](./media/container-service-monitoring/datadog2.png)

Następnie wprowadź klucz interfejsu API w konfiguracji Datadog w Uniwersum DC/OS. 

![Konfiguracja Datadog na całym świecie DC/OS](./media/container-service-monitoring/datadog3.png) 

W powyższej konfiguracji wystąpienia są ustawione na 10000000 to zawsze, gdy nowy węzeł zostanie dodany do klastra Datadog automatycznie wdraża agenta do tego węzła. To rozwiązanie tymczasowe. Po zainstalowaniu pakietu, należy przejść z powrotem do witryny sieci Web Datadog i Znajdź "[pulpity nawigacyjne](https://app.datadoghq.com/dash/list)." Z tego miejsca pojawi się niestandardowy i integracja z pulpitów nawigacyjnych. [Pulpitu nawigacyjnego Docker](https://app.datadoghq.com/screen/integration/docker) będą miały metryki kontenera potrzebne do monitorowania sieci klastra. 

