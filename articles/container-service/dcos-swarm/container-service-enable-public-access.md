---
title: Zapewnianie dostępu do aplikacji kontenera Azure DC/OS
description: Jak włączyć publicznego dostępu do kontenerów DC/OS usługi kontenera platformy Azure.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: aedc97335a0b9ad00cf653477b62bf530b556900
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2017
ms.locfileid: "26332283"
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Włącz publiczny dostęp do aplikacji usługi kontenera platformy Azure

Każdy kontener DC/OS w ACS [puli agenta publicznego](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automatycznie połączenie z Internetem. Domyślnie porty **80**, **443**, **8080** są otwarte i każdy kontener (publicznych) nasłuchuje na te porty są dostępne. W tym artykule przedstawiono sposób otwierania więcej portów dla aplikacji w usłudze kontenera platformy Azure.

## <a name="open-a-port-portal"></a>Otwórz port (portal)
Najpierw należy otworzyć port, którego chcemy.

1. Zaloguj się do portalu.
2. Znajdź wdrożonej usługi kontenera platformy Azure do grupy zasobów.
3. Wybierz usługę równoważenia obciążenia agenta (nosi podobny do **XXXX-agent-lb-XXXX**).
   
    ![Moduł równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kliknij przycisk **sondy** , a następnie **dodać**.
   
    ![Sondy modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-probe.png)
5. Wypełnij formularz sondowania, a następnie kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa sondy. |
   | Port |Port kontenera do testowania. |
   | Ścieżka |(Podczas w trybie HTTP) Ścieżka względna witryny sieci Web do sondowania. Protokół HTTPS nie jest obsługiwane. |
   | Interwał |Ilość czasu między sondowania prób w sekundach. |
   | Próg złej kondycji |Liczba kolejnych sondowania prób przed uwzględnieniu kontenera złej kondycji. |
6. W właściwości usługi równoważenia obciążenia agenta, kliknij przycisk **reguły równoważenia obciążenia** , a następnie **Dodaj**.
   
    ![Reguły modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Wypełnij formularz usługi równoważenia obciążenia, a następnie kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa usługi równoważenia obciążenia. |
   | Port |Port publiczny przychodzących. |
   | Port zaplecza |Port wewnętrzny publicznego kontenera, aby kierować ruchem do. |
   | Puli wewnętrznej bazy danych |Kontenery w tej puli będzie docelowej dla tej usługi równoważenia obciążenia. |
   | Sondy |Sonda używany do określenia, czy element docelowy w **puli zaplecza** jest w dobrej kondycji. |
   | Trwałość sesji |Określa sposób obsługi ruchu w kliencie na czas trwania sesji.<br><br>**Brak**: kolejne żądania z tego samego klienta mogą być obsługiwane przez wszystkie kontenera.<br>**Klient IP**: kolejne żądania z tego samego adresu IP klienta są obsługiwane przez tego samego kontenera.<br>**Klient IP i protokół**: kolejne żądania z tej samej kombinacji adresu IP i Protokół klienta są obsługiwane przez tego samego kontenera. |
   | Limit czasu bezczynności |(Tylko TCP) W minutach czas przechowywania klienta TCP/HTTP Otwórz bez polegania na *keep-alive* wiadomości. |

## <a name="add-a-security-rule-portal"></a>Dodawanie reguły zabezpieczeń (portal)
Następnie należy dodać regułę zabezpieczeń, który przekierowuje ruch z naszych otwarty port przez zaporę.

1. Zaloguj się do portalu.
2. Znajdź wdrożonej usługi kontenera platformy Azure do grupy zasobów.
3. Wybierz **publicznego** agenta sieciowej grupy zabezpieczeń (nosi podobny do **XXXX-agent publicznego nsg-XXXX**).
   
    ![Grupy zabezpieczeń sieci usługi kontenera platformy Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** , a następnie **Dodaj**.
   
    ![Reguły grupy zabezpieczeń sieci usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Wypełnianie reguły zapory, aby umożliwić port publiczny i kliknij pozycję **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa reguły zapory. |
   | Priorytet |Ranga priorytet reguły. Im mniejsza liczba wyższy priorytet. |
   | Element źródłowy |Ogranicz przychodzące zakres adresów IP, aby być dozwolony lub odrzucany przez tę regułę. Użyj **żadnych** aby nie określać ograniczenie. |
   | Usługa |Wybierz zestaw wstępnie zdefiniowanych usług, których dotyczy ta zasada zabezpieczeń. W przeciwnym razie użyj **niestandardowy** do tworzenia własnych. |
   | Protokół |Ograniczanie ruchu na podstawie **TCP** lub **UDP**. Użyj **żadnych** aby nie określać ograniczenie. |
   | Zakres portów |Gdy **usługi** jest **niestandardowe**, określa zakres portów, które ma wpływ ta reguła. Można użyć jednego portu, takich jak **80**, lub zakres, takich jak **1024 1500**. |
   | Akcja |Zezwalaj lub Odmów ruchu, który spełnia kryteria. |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat różnic między [publiczne i prywatne agentów DC/OS](container-service-dcos-agents.md).

Przeczytaj więcej informacji na temat [Zarządzanie kontenerów DC/OS](container-service-mesos-marathon-ui.md).

