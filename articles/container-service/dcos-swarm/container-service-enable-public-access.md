---
title: (PRZESTARZAŁE) Włączanie dostępu do aplikacji kontenera kontrolera domeny/systemu operacyjnego platformy Azure
description: Jak włączyć publiczny dostęp do kontenerów kontrolera domeny/systemu operacyjnego w usłudze kontenerów platformy Azure.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61457388"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(PRZESTARZAŁE) Włączanie publicznego dostępu do aplikacji usługi Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Każdy kontener dc/os w [puli agentów publicznych](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) usługi ACS jest automatycznie udostępniane w Internecie. Domyślnie porty **80,** **443**, **8080** są otwierane, a każdy (publiczny) kontener nasłuchiwania na tych portach jest dostępny. W tym artykule pokazano, jak otworzyć więcej portów dla aplikacji w usłudze kontenera platformy Azure.

## <a name="open-a-port-portal"></a>Otwieranie portu (portal)
Po pierwsze, musimy otworzyć port, którego chcemy.

1. Zaloguj się do portalu.
2. Znajdź grupę zasobów, do której wdrożono usługę Azure Container Service.
3. Wybierz moduł równoważenia obciążenia agenta (który nosi nazwę podobną do **XXXX-agent-lb-XXXX**).
   
    ![Moduł równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kliknij **pozycję Sondy,** a następnie **dodaj**.
   
    ![Sondy modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-probe.png)
5. Wypełnij formularz sondy i kliknij **przycisk OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa sondy. |
   | Port |Port kontenera do przetestowania. |
   | Ścieżka |(W trybie HTTP) Względna ścieżka witryny do sondy. Protokół HTTPS nie jest obsługiwany. |
   | Interval |Czas między próbami sondy w sekundach. |
   | Niezdrowy próg |Liczba kolejnych prób sondy przed rozważeniem kontenera w złej kondycji. |
6. Po powrocie do właściwości modułu równoważenia obciążenia agenta kliknij pozycję **Reguły równoważenia obciążenia,** a następnie **dodaj**.
   
    ![Reguły modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Wypełnij formularz modułu równoważenia obciążenia i kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa modułu równoważenia obciążenia. |
   | Port |Publiczny port przychodzący. |
   | Port zaplecza |Wewnętrzny port publiczny kontenera do kierowania ruchu do. |
   | Pula zaplecza |Kontenery w tej puli będzie celem dla tego modułu równoważenia obciążenia. |
   | Sonda |Sonda używana do określenia, czy obiekt docelowy w **puli wewnętrznej bazy danych** jest w dobrej kondycji. |
   | Trwałość sesji |Określa, jak ruch z klienta powinny być obsługiwane przez czas trwania sesji.<br><br>**Brak:** Kolejne żądania od tego samego klienta mogą być obsługiwane przez dowolny kontener.<br>**Adres IP klienta:** Kolejne żądania z tego samego adresu IP klienta są obsługiwane przez ten sam kontener.<br>**Adres IP i protokół klienta:** Kolejne żądania z tego samego adresu IP klienta i kombinacji protokołów są obsługiwane przez ten sam kontener. |
   | Limit czasu bezczynnego |(tylko TCP) W ciągu kilku minut czas na utrzymanie otwartego klienta TCP/HTTP bez polegania na *komunikatach keep-alive.* |

## <a name="add-a-security-rule-portal"></a>Dodawanie reguły zabezpieczeń (portal)
Następnie musimy dodać regułę zabezpieczeń, która kieruje ruch z naszego otwartego portu przez zaporę.

1. Zaloguj się do portalu.
2. Znajdź grupę zasobów, do której wdrożono usługę Azure Container Service.
3. Wybierz grupę zabezpieczeń sieci agentów **publicznych** (o nazwie podobnej do **XXXX-agent-public-nsg-XXXX**).
   
    ![Grupa zabezpieczeń sieci usług kontenerowych platformy Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Wybierz **pozycję Przychodzące reguły zabezpieczeń,** a następnie **dodaj**.
   
    ![Reguły grupy zabezpieczeń sieci usług kontenerowych platformy Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Wypełnij regułę zapory, aby zezwolić na port publiczny i kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa reguły zapory. |
   | Priorytet |Ranga priorytetu dla reguły. Im mniejsza liczba, tym wyższy priorytet. |
   | Element źródłowy |Ogranicz przychodzący zakres adresów IP, który ma być dozwolony lub odrzucany przez tę regułę. Użyj **any,** aby nie określać ograniczenia. |
   | Usługa |Wybierz zestaw wstępnie zdefiniowanych usług, dla które jest regułą zabezpieczeń. W przeciwnym razie użyj **custom** do tworzenia własnych. |
   | Protocol (Protokół) |Ograniczanie ruchu na podstawie **protokołu TCP** lub **UDP**. Użyj **any,** aby nie określać ograniczenia. |
   | Zakres portów |Gdy **usługa** jest **niestandardowa,** określa zakres portów, na który ma wpływ ta reguła. Można użyć jednego portu, takiego jak **80**lub zakresu, takiego jak **1024-1500.** |
   | Akcja |Zezwalaj lub odmawiaj ruchu, który spełnia kryteria. |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o różnicy między [publicznymi a prywatnymi agentami DC/OS](container-service-dcos-agents.md).

Przeczytaj więcej informacji na temat [zarządzania kontenerami DC/OS](container-service-mesos-marathon-ui.md).

