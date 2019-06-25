---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183176"
---
W tym kroku należy przetestować odbiornika grupy dostępności przy użyciu aplikacji klienckiej, która działa w tej samej sieci.

Łączności klienta ma następujące wymagania:

* Połączenia klientów z odbiornikiem muszą pochodzić z komputerów, które znajdują się w innej usługi w chmurze niż ten, który jest hostem repliki dostępności Always On.
* Jeśli zawsze włączonych replik znajdują się w różnych podsieciach, klienci muszą określić *MultisubnetFailover = True* w parametrach połączenia. Ten warunek wynikiem próby nawiązania połączenia równoległego do replik w różnych podsieciach. Ten scenariusz obejmuje między regionami zawsze na dostępność grupy wdrożenia.

Przykładem jest nawiązać połączenia z odbiornikiem z jednej z maszyn wirtualnych w tej samej sieci wirtualnej platformy Azure (ale nie jeden, który obsługuje replikę). Prosty sposób, aby wykonać ten test jest próby nawiązania połączenia z SQL Server Management Studio do odbiornika grupy dostępności. Inną metodą prosty jest uruchomienie [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), wykonując następujące czynności:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Jeśli ma wartość EndpointPort *1433*, nie należy go określić w wywołaniu. Poprzednie wywołanie przyjęto założenie, że komputer klienta sprzężony z tej samej domenie i czy obiekt wywołujący ma udzielone uprawnienia w bazie danych przy użyciu uwierzytelniania Windows.
> 
> 

Podczas testowania odbiornik, pamiętaj, że w trybie Failover grupy dostępności, aby upewnić się, że klienci mogą łączyć się odbiornika w tryb failover.

