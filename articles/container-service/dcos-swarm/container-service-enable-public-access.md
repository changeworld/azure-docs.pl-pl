---
title: (PRZESTARZAŁE) Zapewnianie dostępu do aplikacji kontenera usługi Azure DC/OS
description: Jak włączyć publiczny dostęp do kontenerów DC/OS w usłudze Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457388"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(PRZESTARZAŁE) Włączanie dostępu publicznego do aplikacji usługi Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Dowolnego kontenera platformy DC/OS usługi ACS [puli agenta publicznego](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automatycznie połączone z Internetem. Domyślnie porty **80**, **443**, **8080** są otwarte, i dowolnego kontenera (publicznego) nasłuchuje na te porty są dostępne. W tym artykule pokazano, jak otworzyć porty więcej aplikacji w usłudze Azure Container Service.

## <a name="open-a-port-portal"></a>Otwórz port (portal)
Najpierw należy otworzyć port, którego potrzebujemy.

1. Zaloguj się do portalu.
2. Znajdź grupę zasobów, która została wdrożona w usłudze Azure Container Service do.
3. Wybierz moduł równoważenia obciążenia agenta (nosi podobne do **XXXX-agent-lb-XXXX**).
   
    ![Moduł równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kliknij przycisk **sondy** i następnie **Dodaj**.
   
    ![Sondy modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-probe.png)
5. Wypełnij formularz sondowania, a następnie kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Name (Nazwa) |Opisowa nazwa sondy. |
   | Port |Port kontenera do testowania. |
   | Ścieżka |(Jeśli w trybie HTTP) Ścieżka względna witryny sieci Web do sondowania. Protokół HTTPS nie jest obsługiwane. |
   | Interval |Ilość czasu między sondy prób w ciągu kilku sekund. |
   | Próg złej kondycji |Liczba kolejnych sondy prób zanim będzie można uznać kontenera w złej kondycji. |
6. W właściwości modułu równoważenia obciążenia agenta, kliknij przycisk **reguły równoważenia obciążenia** i następnie **Dodaj**.
   
    ![Reguły modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Wypełnij formularz usługi równoważenia obciążenia, a następnie kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Name (Nazwa) |Opisowa nazwa modułu równoważenia obciążenia. |
   | Port |Publiczny port przychodzący. |
   | Port zaplecza |Port wewnętrzny publicznego kontenera do kierowania ruchu do. |
   | Pula zaplecza |Kontenery w tej puli będzie cel dla tego modułu równoważenia obciążenia. |
   | Sondy |Sondowania użyte do określenia, czy element docelowy w **puli zaplecza** jest w dobrej kondycji. |
   | Trwałość sesji |Określa sposób obsługi ruchu w kliencie na czas trwania sesji.<br><br>**Brak**: Kolejne żądania z tego samego klienta mogą być obsługiwane przez żaden kontener.<br>**Klient IP**: Kolejne żądania z tego samego adresu IP klienta są obsługiwane przez ten sam kontener.<br>**Klient IP i protokół**: Kolejne żądania z tej samej kombinacji adresu IP i Protokół klienta są obsługiwane przez ten sam kontener. |
   | Limit czasu bezczynności |(Tylko TCP) W ciągu kilku minut, czas do klienta protokołu TCP/HTTP Otwórz bez polegania na *keep-alive* wiadomości. |

## <a name="add-a-security-rule-portal"></a>Dodawanie reguły zabezpieczeń (portal)
Następnie należy dodać regułę zabezpieczeń, która kieruje ruch z naszych otwartego portu przez zaporę.

1. Zaloguj się do portalu.
2. Znajdź grupę zasobów, która została wdrożona w usłudze Azure Container Service do.
3. Wybierz **publicznych** agenta sieciowej grupy zabezpieczeń (nosi podobne do **XXXX-agent-public-sieciowej grupy zabezpieczeń XXXX**).
   
    ![Usługa Azure container service sieciowej grupy zabezpieczeń](./media/container-service-enable-public-access/agent-nsg.png)
4. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** i następnie **Dodaj**.
   
    ![Reguły sieciowych grup zabezpieczeń usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Wypełnij reguły zapory, aby zezwolić na publiczny port, a następnie kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Name (Nazwa) |Opisowa nazwa reguły zapory. |
   | Priorytet |Ranga priorytet reguły. Im mniejsza liczba wyższy priorytet. |
   | source |Ogranicz przychodzące zakres adresów IP, aby być dozwolony lub odrzucany przez tę regułę. Użyj **wszelkie** aby nie określać ograniczenia. |
   | Usługa |Wybierz zestaw wstępnie zdefiniowanych usług, których dotyczy ta reguła zabezpieczeń. W przeciwnym razie użyj **niestandardowe** utworzyć własny. |
   | Protocol |Ograniczanie ruchu na podstawie **TCP** lub **UDP**. Użyj **wszelkie** aby nie określać ograniczenia. |
   | Zakres portów |Gdy **usługi** jest **niestandardowe**, określa zakres portów, których dotyczy ta reguła. Można użyć pojedynczy port, taki jak **80**, lub zakres, takich jak **1024 1500**. |
   | Akcja |Zezwolenie lub zablokowanie ruchu sieciowego, która spełnia kryteria. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat różnic między [publicznych i prywatnych agentów DC/OS](container-service-dcos-agents.md).

Przeczytaj więcej informacji na temat [Zarządzanie kontenerów DC/OS](container-service-mesos-marathon-ui.md).

