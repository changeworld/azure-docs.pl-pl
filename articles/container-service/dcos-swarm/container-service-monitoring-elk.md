---
title: (PRZESTARZAŁE) Monitorowanie klastra kontrolera domeny/systemu operacyjnego platformy Azure — stos ELK
description: Monitoruj klaster kontrolera domeny/systemu operacyjnego w klastrze usługi azure container service za pomocą usługi ELK (Elasticsearch, Logstash i Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277774"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi kontenerów platformy Azure za pomocą usługi ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule zademonstrujemy sposób wdrażania stosu ELK (Elasticsearch, Logstash, Kibana) w klastrze DC/OS w usłudze Azure Container Service. 

## <a name="prerequisites"></a>Wymagania wstępne
[Wdrażanie](container-service-deployment.md) i [łączenie](../container-service-connect.md) klastra dc/os skonfigurowanego przez usługę Azure Container Service. Zapoznaj się z pulpitem nawigacyjnym DC/OS i usługami Marathon [tutaj.](container-service-mesos-marathon-ui.md) Zainstaluj również [moduł balanser obciążenia Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Stos ELK to połączenie Elasticsearch, Logstash i Kibana, który zapewnia stos end-to-end, który może służyć do monitorowania i analizowania dzienników w klastrze.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurowanie stosu ELK w klastrze dc/os
Dostęp do interfejsu użytkownika DC/OS można uzyskać za pośrednictwem [http://localhost:80/](http://localhost:80/) systemu Once w interfejsie użytkownika DC/OS, aby przejść do **uniwersum.** Szukaj i instaluj Elasticsearch, Logstash i Kibana z DC / OS Universe i w tej konkretnej kolejności. Więcej informacji na temat konfiguracji można uzyskać, jeśli przejdziesz do łącza **Instalacja zaawansowana.**

![Łok1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2 (Ł. 2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3 (300 zł)](./media/container-service-monitoring-elk/elk3.PNG) 

Po kontenerach ELK i są uruchomione, należy włączyć Kibana być dostępne za pośrednictwem Marathon-LB. Przejdź do **usługi** > **kibana**i **kliknij** edytuj, jak pokazano poniżej.

![ELK4 (ELK4)](./media/container-service-monitoring-elk/elk4.PNG)


Przełącz do **trybu JSON** i przewiń w dół do sekcji etykiet.
Musisz dodać wpis `"HAPROXY_GROUP": "external"` tutaj, jak pokazano poniżej.
Po **kliknięciu przycisku Wdrażanie zmian**kontener zostanie ponownie uruchomiony.

![ELK5 (500 zł)](./media/container-service-monitoring-elk/elk5.PNG)


Jeśli chcesz sprawdzić, czy Kibana jest zarejestrowana jako usługa na pulpicie nawigacyjnym HAPROXY, musisz otworzyć port 9090 w klastrze agenta, gdy HAPROXY działa na porcie 9090.
Domyślnie otwieramy porty 80, 8080 i 443 w klastrze agentów DC/OS.
Instrukcje otwarcia portu i zapewnienia publicznej oceny znajdują się [tutaj](container-service-enable-public-access.md).

Aby uzyskać dostęp do pulpitu nawigacyjnego HAPROXY, otwórz interfejs administratora Marathon-LB pod adresem: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Po przejściu do adresu URL powinieneś zobaczyć pulpit nawigacyjny HAPROXY, jak pokazano poniżej, a powinieneś zobaczyć wpis usługi dla Kibana.

![ELK6 (właśc.](./media/container-service-monitoring-elk/elk6.PNG)


Aby uzyskać dostęp do pulpitu nawigacyjnego Kibana, który jest wdrożony na porcie 5601, musisz otworzyć port 5601. Postępuj zgodnie z instrukcjami [tutaj](container-service-enable-public-access.md). Następnie otwórz deskę rozdzielczą Kibana pod adresem: `http://localhost:5601`.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o przekazywaniu i konfiguracji dziennika systemu i aplikacji, zobacz [Zarządzanie dziennikami w systemie DC/OS z systemem ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Aby filtrować dzienniki, zobacz [Filtrowanie dzienników za pomocą pliku ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

