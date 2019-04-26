---
title: (PRZESTARZAŁE) Klaster DC/OS usługi Azure Monitor — pomocą usługi Datadog
description: Monitorowanie klastra usługi Azure Container Service za pomocą usługi Datadog. Użyj interfejsu użytkownika sieci web platformy DC/OS, aby wdrożyć agentów pomocą usługi Datadog do klastra.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a094369d467b3b1f3d5fe93f870dccc9eae7519c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60508119"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service DC/OS za pomocą usługi Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule wdrożymy agentów pomocą usługi Datadog do wszystkich węzłów agenta w klastrze usługi Azure Container Service. Konieczne będzie konto za pomocą usługi Datadog dla tej konfiguracji. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [połącz](../container-service-connect.md) klaster skonfigurowany przez usługę Azure Container Service. Przegląd [interfejsu użytkownika platformy Marathon](container-service-mesos-marathon-ui.md). Przejdź do [ https://datadoghq.com ](https://datadoghq.com) skonfigurować konto pomocą usługi Datadog. 

## <a name="datadog"></a>Pomocą usługi Datadog
Pomocą usługi Datadog jest usługą monitorowania, która gromadzi dane monitorowania z kontenerów w klastrze usługi Azure Container Service. Pomocą usługi Datadog ma na pulpicie nawigacyjnym integracji platformy Docker, którym można zobaczyć określonych metryk w Twoich kontenerów. Metryki zebrane z kontenerów są uporządkowane według procesora CPU, pamięci, sieci i we/wy. Pomocą usługi Datadog dzieli metryki na kontenerach i obrazy. Przykładem takich jak wygląda interfejs użytkownika użycia procesora CPU jest niższa.

![Interfejs użytkownika z pomocą usługi Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurowanie wdrożenia pomocą usługi Datadog przy użyciu platformy Marathon
Poniższe kroki pokazują jak konfigurować i wdrażać aplikacje pomocą usługi Datadog z klastrem przy użyciu platformy Marathon. 

Dostęp do interfejsu użytkownika platformy DC/OS za pośrednictwem [ http://localhost:80/ ](http://localhost:80/). Jeden raz w Interfejsie użytkownika platformy DC/OS Przejdź "wszechświat", który znajduje się w lewym dolnym, wyszukaj "Pomocą usługi Datadog" i kliknij przycisk "Zainstaluj".

![Pakiet pomocą usługi Datadog we wszechświecie DC/OS](./media/container-service-monitoring/datadog1.png)

Teraz ukończyć konfiguracji konieczne będzie konto pomocą usługi Datadog lub konto bezpłatnej wersji próbnej. Gdy jest zalogowany pomocą usługi Datadog wyglądu witryny sieci Web po lewej stronie i przejdź do integracji -> następnie [interfejsów API](https://app.datadoghq.com/account/settings#api). 

![Klucz interfejsu API pomocą usługi Datadog](./media/container-service-monitoring/datadog2.png)

Następnie wprowadź klucz interfejsu API do konfiguracji pomocą usługi Datadog we wszechświecie DC/OS. 

![Konfiguracja pomocą usługi Datadog we wszechświecie DC/OS](./media/container-service-monitoring/datadog3.png) 

W powyższej konfiguracji wystąpienia są ustawione na 10 000 000 tak po każdym dodaniu nowego węzła do klastra pomocą usługi Datadog automatycznie wdroży agenta w tym węźle. Jest to rozwiązanie tymczasowe. Po zainstalowaniu pakietu, należy przejść z powrotem do witryny sieci Web z pomocą usługi Datadog i Znajdź "[pulpity nawigacyjne](https://app.datadoghq.com/dash/list)." W tym miejscu zobaczysz, niestandardowe i integracja z pulpitów nawigacyjnych. [Pulpit nawigacyjny platformy Docker](https://app.datadoghq.com/screen/integration/docker) będzie miał wszystkie metryki kontenera niezbędne do monitorowania klastra. 

