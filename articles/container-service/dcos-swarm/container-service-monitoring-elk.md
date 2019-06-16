---
title: (PRZESTARZAŁE) Monitorowanie klastra usługi Azure DC/OS — stos ELK
description: Monitorowanie klastra DC/OS w klastrze usługi Azure Container Service za pomocą rozwiązania ELK (Elasticsearch, Logstash i Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467772"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Azure Container Service za pomocą rozwiązania ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokażemy, jak wdrożyć stosu ELK (Elasticsearch, Logstash, Kibana) w klastrze DC/OS w usłudze Azure Container Service. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdrażanie](container-service-deployment.md) i [połączyć](../container-service-connect.md) klastra DC/OS, skonfigurowane przez usługę Azure Container Service. Zapoznaj się z pulpitu nawigacyjnego platformy DC/OS i usługi Marathon [tutaj](container-service-mesos-marathon-ui.md). Zainstaluj również [Marathon Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>Rozwiązania ELK (Elasticsearch, Logstash, Kibana)
Stos ELK jest kombinacją Elasticsearch, Logstash i Kibana, zapewniająca stosu typu end to end, który może służyć do monitorowania i analizy dzienników w klastrze.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Skonfigurować stos ELK w klastrze DC/OS
Dostęp do interfejsu użytkownika platformy DC/OS za pośrednictwem [ http://localhost:80/ ](http://localhost:80/) raz w Interfejsie użytkownika platformy DC/OS przejdź do **wszechświat**. Wyszukiwanie i instalowanie rozwiązań Elasticsearch, Logstash i Kibana Uniwersum DC/OS i w tej kolejności. Możesz dowiedzieć się więcej na temat konfiguracji po przejściu do **zaawansowane instalacji** łącza.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Gdy kontenerów ELK i czy działanie, musisz włączyć Kibana przy użyciu platformy Marathon-LB. Przejdź do **usług** > **kibana**i kliknij przycisk **Edytuj** jak pokazano poniżej.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Przełącz, aby **tryb JSON** i przewiń w dół do sekcji etykiety.
Musisz dodać `"HAPROXY_GROUP": "external"` wpis tutaj jak pokazano poniżej.
Po kliknięciu **wdrożyć zmiany**, ponownym uruchomieniu kontenera.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Aby sprawdzić, czy program Kibana jest zarejestrowana jako usługa na pulpicie nawigacyjnym HAPROXY, musisz otworzyć port 9090 w klastrze agent, ponieważ HAPROXY działa na porcie 9090.
Domyślnie, możemy otworzyć porty 80, 8080 i 443 w klastrze agentów platformy DC/OS.
Znajdują się instrukcje, aby otworzyć port i podaj oceny publicznego [tutaj](container-service-enable-public-access.md).

Aby uzyskać dostęp do pulpitu nawigacyjnego HAPROXY, Otwórz interfejs administracyjny warstwa Marathon-LB przy: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Po przejściu do adresu URL powinien zostać wyświetlony pulpit nawigacyjny HAPROXY, jak pokazano poniżej, a powinien zostać wyświetlony wpis usługi rozwiązania kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Aby dostęp do pulpitu nawigacyjnego Kibana, który będzie wdrażany na porcie 5601, musisz otworzyć port 5601. Postępuj zgodnie z instrukcjami [tutaj](container-service-enable-public-access.md). Następnie otwórz pulpitu nawigacyjnego Kibana w: `http://localhost:5601`.

## <a name="next-steps"></a>Kolejne kroki

* Dla dziennika systemu i aplikacji funkcji przekazywania, instalacji, zobacz [Zarządzanie dziennikami na platformie DC/OS za pomocą rozwiązania ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Filtruj dzienniki, zobacz [filtrowanie dzienników za pomocą rozwiązania ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

