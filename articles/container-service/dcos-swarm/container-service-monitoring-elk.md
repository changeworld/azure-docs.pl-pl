---
title: Monitor klaster Azure DC/OS - ŁOSI stosu
description: Monitorowanie klastra DC/OS w klastrze usługi kontenera platformy Azure z ŁOSI (Elasticsearch Logstash i Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: dc863894d8846e066c90bdf7b309f141d32a1186
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163184"
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Monitor klastra usługi kontenera platformy Azure z ŁOSI

W tym artykule przedstawiony sposób wdrażania stosu ŁOSI (Elasticsearch, Logstash, Kibana) w klastrze DC/OS usługi kontenera platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdrażanie](container-service-deployment.md) i [połączyć](../container-service-connect.md) klastra DC/OS konfigurowane za pomocą usługi kontenera platformy Azure. Eksplorowanie usługi Pulpit nawigacyjny DC/OS i Marathon [tutaj](container-service-mesos-marathon-ui.md). Zainstaluj również [modułu równoważenia obciążenia platformy Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ŁOSI (Elasticsearch, Logstash, Kibana)
Stos ŁOSI jest kombinacją Elasticsearch, Logstash i Kibana, która zapewnia kompleksowe stosu, który może służyć do monitorowania i analizować dzienniki w klastrze.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Skonfigurować stosu ŁOSI w klastrze DC/OS
Dostęp za pośrednictwem interfejsu użytkownika DC/OS [ http://localhost:80/ ](http://localhost:80/) raz w Interfejsie użytkownika DC/OS przejdź do **Universe**. Wyszukiwanie i instalowanie Elasticsearch, Logstash i Kibana z Universe DC/OS i w tej kolejności. Więcej informacji o konfiguracji z **instalacji zaawansowanym** łącza.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Po ŁOSI kontenery i czy do pracy, musisz włączyć Kibana przy użyciu platformy Marathon-LB. Przejdź do **usług** > **kibana**i kliknij przycisk **Edytuj** jak pokazano poniżej.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Przełącz, aby **tryb JSON** i przewiń w dół do sekcji etykiety.
Konieczne jest dodanie `"HAPROXY_GROUP": "external"` wpis tutaj jak pokazano poniżej.
Po kliknięciu **wdrażać zmiany**, ponownym uruchomieniu kontenera.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Aby sprawdzić, czy Kibana jest zarejestrowany jako usługa na pulpicie nawigacyjnym HAPROXY, należy otworzyć port 9090 w klastrze agenta, ponieważ HAPROXY działa na porcie 9090.
Domyślnie, możemy otworzyć porty 80, 8080 i 443 w klastrze DC/OS agenta.
Podano instrukcje dotyczące otwierania portu i podaj publiczny oceny [tutaj](container-service-enable-public-access.md).

Aby uzyskać dostęp do pulpitu nawigacyjnego HAPROXY, otwórz interfejsu administracyjnego warstwa Marathon-LB w: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Po przejściu do adresu URL, powinien zostać wyświetlony pulpit nawigacyjny HAPROXY w sposób przedstawiony poniżej i powinna być widoczna dla Kibana wpisu usługi.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Do uzyskiwania dostępu Kibana pulpitu nawigacyjnego, który jest wdrażana na porcie 5601, należy otworzyć port 5601. Postępuj zgodnie z instrukcjami [tutaj](container-service-enable-public-access.md). Następnie otwórz pulpit nawigacyjny Kibana w: `http://localhost:5601`.

## <a name="next-steps"></a>Kolejne kroki

* Dla dziennika systemu i aplikacji przekazywania i ustawień, zobacz [zarządzanie dziennikiem w DC/OS z ŁOSI](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Aby filtrować dzienników, zobacz [filtrowania dzienniki z ŁOSI](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

