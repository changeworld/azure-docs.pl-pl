---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183180"
---
W tym kroku ręcznie utworzysz odbiornik grupy dostępności w Menedżerze klastrów trybu failover i programie SQL Server Management Studio.

1. Otwórz Menedżera klastrów trybu failover z węzła obsługującego replikę podstawową.

2. Wybierz węzeł **Sieci,** a następnie zanotuj nazwę sieci klastra. Ta nazwa jest używana w zmiennej $ClusterNetworkName w skrypcie programu PowerShell.

3. Rozwiń nazwę klastra, a następnie kliknij pozycję **Role**.

4. W okienku **Role** kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz polecenie Dodaj**punkt dostępu klienta** **zasobów** > .
   
    ![Dodawanie punktu dostępu klienta dla grupy dostępności](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. W polu **Nazwa** utwórz nazwę tego nowego odbiornika, kliknij przycisk **Dalej** dwa razy, a następnie kliknij przycisk **Zakończ**.  
    W tym momencie nie należy przewprowadzić odbiornika ani zasobu do trybu online.

6. Kliknij kartę **Zasoby,** a następnie rozwiń utworzony właśnie punkt dostępu klienta. 
    Zostanie wyświetlony zasób adresu IP dla każdej sieci klastra w klastrze. Jeśli jest to rozwiązanie tylko do platformy Azure, wyświetlany jest tylko jeden zasób adresu IP.

7. Wykonaj jedną z następujących czynności:
   
   * Aby skonfigurować rozwiązanie hybrydowe:
     
        a. Kliknij prawym przyciskiem myszy zasób adresu IP odpowiadający podsieci lokalnej, a następnie wybierz polecenie **Właściwości**. Zanotuj nazwę adresu IP i nazwę sieci.
   
        b. Wybierz **statyczny adres IP**, przypisz nieużyny adres IP, a następnie kliknij przycisk **OK**.
 
   * Aby skonfigurować rozwiązanie tylko dla platformy Azure:

        a. Kliknij prawym przyciskiem myszy zasób adresu IP odpowiadający podsieci platformy Azure, a następnie wybierz polecenie **Właściwości**.
       
       > [!NOTE]
       > Jeśli później detektor nie przejdzie do trybu online z powodu sprzecznego adresu IP wybranego przez protokół DHCP, można skonfigurować prawidłowy statyczny adres IP w tym oknie właściwości.
       > 
       > 

       b. W tym samym oknie właściwości **adresu IP** zmień nazwę **adresu IP**.  
        Ta nazwa jest używana w zmiennej $IPResourceName skryptu programu PowerShell. Jeśli rozwiązanie obejmuje wiele sieci wirtualnych platformy Azure, powtórz ten krok dla każdego zasobu IP.

