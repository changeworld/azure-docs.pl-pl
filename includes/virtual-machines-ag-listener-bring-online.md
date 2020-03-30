---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183182"
---
1. W Menedżerze klastrów trybu failover rozwiń węzeł **Role**, a następnie wyróżnij grupę dostępności.  

2. Na karcie **Zasoby** kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Właściwości**.

3. Kliknij kartę **Zależności.** Jeśli na liście znajduje się wiele zasobów, sprawdź, czy adresy IP mają zależności LUB, a nie i.  

4. Kliknij przycisk **OK**.

5. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Przetwórdź w trybie online**.

6. Po udostępnieniu odbiornika na karcie **Zasoby** kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij polecenie **Właściwości**.
   
    ![Konfigurowanie zasobu grupy dostępności](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Utwórz zależność od zasobu nazwy odbiornika (nie nazwę zasobów adresów IP), a następnie kliknij przycisk **OK**.
   
    ![Dodawanie zależności od nazwy odbiornika](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Uruchom program SQL Server Management Studio, a następnie połącz się z repliką podstawową.

9. Przejdź do funkcji **AlwaysOn High** > **Availability Availability Groups** > **\<AvailabilityGroupName\>** > **Availability Group Listeners**.  
    Powinna być wyświetlana nazwa odbiornika utworzona w Menedżerze klastrów trybu failover.

10. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie kliknij polecenie **Właściwości**.

11. W polu **Port** określ numer portu dla odbiornika grupy dostępności, używając $EndpointPort, które były używane wcześniej (w tym samouczku wartość domyślna 1433 była domyślna), a następnie kliknij przycisk **OK**.

