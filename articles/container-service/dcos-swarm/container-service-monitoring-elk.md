---
title: PRZESTARZAŁE Monitorowanie klastra DC/OS platformy Azure — stos ELK
description: Monitoruj klaster DC/OS w klastrze Azure Container Service z ELK (Elasticsearch, logstash i Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277774"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service przy użyciu ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokazano, jak wdrożyć stos ELK (Elasticsearch, logstash, Kibana) w klastrze DC/OS w Azure Container Service. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdróż](container-service-deployment.md) i [Podłącz](../container-service-connect.md) klaster DC/OS skonfigurowany przez Azure Container Service. Poznaj pulpit nawigacyjny platformy DC/OS i usługi Marathon [tutaj](container-service-mesos-marathon-ui.md). Zainstaluj również [Load Balancer Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, logstash, Kibana)
ELK Stack to kombinacja Elasticsearch, logstash i Kibana, która zapewnia kompleksowy stos, który może służyć do monitorowania i analizowania dzienników w klastrze.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurowanie stosu ELK w klastrze DC/OS
Uzyskaj dostęp do interfejsu użytkownika DC/OS za pośrednictwem [http://localhost:80/](http://localhost:80/) raz w interfejsie użytkownika DC/OS przejdź do programu **Universe**. Wyszukaj i zainstaluj Elasticsearch, logstash i Kibana z platformy DC/OS i w tej konkretnej kolejności. Jeśli przejdziesz do linku **Instalacja zaawansowana** , możesz dowiedzieć się więcej na temat konfiguracji.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Po zakończeniu i uruchomieniu kontenerów ELK należy włączyć dostęp do Kibana przy użyciu Marathon-LB. Przejdź do **usługi** > **Kibana**, a następnie kliknij pozycję **Edytuj** , jak pokazano poniżej.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Przełącz się do **trybu JSON** i przewiń w dół do sekcji etykiety.
Musisz tutaj dodać wpis `"HAPROXY_GROUP": "external"`, jak pokazano poniżej.
Po kliknięciu przycisku **Wdróż zmiany**, kontener zostanie ponownie uruchomiony.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Jeśli chcesz sprawdzić, czy Kibana jest zarejestrowany jako usługa na pulpicie nawigacyjnym HAPROXY, musisz otworzyć port 9090 w klastrze agentów jako HAPROXY uruchomiony na porcie 9090.
Domyślnie otwieramy porty 80, 8080 i 443 w klastrze agentów DC/OS.
Instrukcje dotyczące otwierania portu i zapewniania publicznej oceny są dostępne [tutaj](container-service-enable-public-access.md).

Aby uzyskać dostęp do pulpitu nawigacyjnego HAPROXY, Otwórz interfejs administratora Marathon-LB w: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Po przejściu do adresu URL powinien zostać wyświetlony pulpit nawigacyjny HAPROXY, jak pokazano poniżej i powinien zostać wyświetlony wpis usługi dla Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Aby uzyskać dostęp do pulpitu nawigacyjnego Kibana, który został wdrożony na porcie 5601, należy otworzyć port 5601. Postępuj zgodnie z instrukcjami [tutaj](container-service-enable-public-access.md). Następnie otwórz pulpit nawigacyjny Kibana w lokalizacji: `http://localhost:5601`.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać przekazywanie i konfigurację dzienników systemu i aplikacji, zobacz [Zarządzanie dziennikami w systemie DC/OS przy użyciu Elk](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Aby filtrować dzienniki, zobacz [filtrowanie dzienników za pomocą Elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

