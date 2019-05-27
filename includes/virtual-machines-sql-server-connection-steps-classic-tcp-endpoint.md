---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165514"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Tworzenie punktu końcowego TCP dla maszyny wirtualnej
Aby uzyskać dostęp do programu SQL Server, z Internetu, maszyna wirtualna musi mieć punktu końcowego do nasłuchiwania pod kątem przychodzącą komunikację protokołu TCP. Ten krok konfiguracji platformy Azure kieruje ruch przychodzący port TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

> [!NOTE]
> Jeśli łączysz się w ramach jednej usługi w chmurze lub sieci wirtualnej, nie należy utworzyć publicznie dostępnym punkcie końcowym. W takim przypadku można kontynuować do następnego kroku. Aby uzyskać więcej informacji, zobacz [scenariusze łączenia](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. W witrynie Azure Portal wybierz **maszyny wirtualne (klasyczne)**.
2. Następnie wybierz maszynę wirtualną programu SQL Server.
3. Wybierz **punktów końcowych**, a następnie kliknij przycisk **Dodaj** znajdujący się u góry bloku punktów końcowych.
   
    ![Portal kroki tworzenia punktu końcowego](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. Na **Dodaj punkt końcowy** bloku zapewniają **nazwa** takich jak SQLEndpoint.
5. Wybierz **TCP** dla **protokołu**.
6. Aby uzyskać **port publiczny**, takich jak Podaj numer portu **57500**.
7. Aby uzyskać **port prywatny**, określ portu nasłuchiwania serwera SQL, która domyślnie **1433**.
8. Kliknij przycisk **Ok** do utworzenia punktu końcowego.

