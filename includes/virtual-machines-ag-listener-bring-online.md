---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165417"
---
1. W Menedżerze klastra trybu Failover rozwiń **role**, a następnie zaznacz grupy dostępności.  

2. Na **zasobów** kartę, kliknij prawym przyciskiem myszy odbiornik o nazwie, a następnie kliknij **właściwości**.

3. Kliknij przycisk **zależności** kartę. Jeśli wiele zasobów nie są wyświetlane, sprawdź, czy adresy IP są lub nie oraz zależności.  

4. Kliknij przycisk **OK**.

5. Kliknij prawym przyciskiem myszy odbiornik o nazwie, a następnie kliknij przycisk **przejdź do trybu Online**.

6. Po odbiornika jest w trybie online na **zasobów** kartę, kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij **właściwości**.
   
    ![Skonfiguruj zasób grupy dostępności](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Zależność od zasobu Nazwa odbiornika (nie nazwa adresu IP zasobów), a następnie kliknij polecenie **OK**.
   
    ![Dodaj zależność na odbiornik o nazwie](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Uruchom program SQL Server Management Studio, a następnie połącz się z repliką podstawową.

9. Przejdź do **AlwaysOn wysokiej dostępności** > **grup dostępności** >  **\<AvailabilityGroupName\>**   >  **Odbiorników grup dostępności**.  
    Powinna być wyświetlana nazwa odbiornika, który został utworzony w Menedżerze klastra trybu Failover.

10. Kliknij prawym przyciskiem myszy odbiornik o nazwie, a następnie kliknij przycisk **właściwości**.

11. W **portu** polu Określ numer portu odbiornika grupy dostępności przy użyciu $EndpointPort użytego wcześniej (w tym samouczku 1433 była wartość domyślna), a następnie kliknij przycisk **OK**.

