---
title: (PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego platformy Azure — datadog
description: Monitoruj klaster usługi kontenera platformy Azure za pomocą datadoga. Użyj interfejsu użytkownika sieci Web kontrolera domeny/systemu operacyjnego, aby wdrożyć agentów Datadog w klastrze.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275252"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego usługi kontenera platformy Azure za pomocą datadoga

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule wdrożymy agentów Datadog do wszystkich węzłów agenta w klastrze usługi azure kontenera. Będziesz potrzebować konta z Datadog dla tej konfiguracji. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [połącz](../container-service-connect.md) klaster skonfigurowany przez usługę Azure Container Service. Poznaj [interfejs użytkownika maratonu](container-service-mesos-marathon-ui.md). Przejdź [https://datadoghq.com](https://datadoghq.com) do, aby skonfigurować konto Datadog. 

## <a name="datadog"></a>Datadog (polski)
Datadog to usługa monitorowania, która zbiera dane monitorowania z kontenerów w klastrze usługi Azure Container Service. Datadog ma pulpit nawigacyjny integracji platformy Docker, gdzie można zobaczyć określone metryki w kontenerach. Metryki zebrane z kontenerów są zorganizowane przez procesor, pamięć, sieć i we/wy. Datadog dzieli metryki na kontenery i obrazy. Poniżej znajduje się przykład interfejsu użytkownika dla użycia procesora CPU.

![Interfejs użytkownika datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurowanie wdrożenia datadoga za pomocą programu Marathon
Te kroki pokażą, jak skonfigurować i wdrożyć aplikacje Datadog w klastrze z marathonem. 

Uzyskaj dostęp do interfejsu użytkownika [http://localhost:80/](http://localhost:80/)dc/os za pośrednictwem programu . Raz w interfejsie użytkownika DC/OS przejdź do "Wszechświata", który znajduje się w lewym dolnym rogu, a następnie wyszukaj "Datadog" i kliknij "Zainstaluj".

![Pakiet Datadog w uniwersum DC/OS](./media/container-service-monitoring/datadog1.png)

Teraz, aby ukończyć konfigurację, musisz mieć konto Datadog lub bezpłatne konto próbne. Po zalogowaniu się do witryny Datadog spójrz w lewo i przejdź do Integrations -> następnie [interfejsów API](https://app.datadoghq.com/account/settings#api). 

![Klucz interfejsu API programu Datadog](./media/container-service-monitoring/datadog2.png)

Następnie wprowadź klucz INTERFEJSU API do konfiguracji Datadog w obrębie dc/os wszechświata. 

![Konfiguracja datadoga w uniwersum DC/OS](./media/container-service-monitoring/datadog3.png) 

W powyższych wystąpieniach konfiguracji są ustawione na 10000000, więc za każdym razem, gdy nowy węzeł zostanie dodany do programu Datadog klastra, automatycznie wdroży agenta w tym węźle. Jest to rozwiązanie tymczasowe. Po zainstalowaniu pakietu należy przejść z powrotem do witryny Datadog i znaleźć["Pulpity nawigacyjne](https://app.datadoghq.com/dash/list)". Stamtąd zobaczysz niestandardowe i integracji pulpitów nawigacyjnych. Pulpit [nawigacyjny platformy Docker](https://app.datadoghq.com/screen/integration/docker) będzie miał wszystkie metryki kontenera potrzebne do monitorowania klastra. 

