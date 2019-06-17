---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165356"
---
W tym kroku ręcznie utworzyć odbiornik grupy dostępności w Menedżerze klastra trybu Failover i SQL Server Management Studio.

1. Otwórz Menedżera klastra trybu Failover z węzła, który obsługuje replikę podstawową.

2. Wybierz **sieci** węzeł, a następnie Uwaga Nazwa sieciowa klastra. Ta nazwa jest używana w zmiennej $ClusterNetworkName w skrypcie programu PowerShell.

3. Rozwiń nazwę klastra, a następnie kliknij przycisk **role**.

4. W **role** okienku kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz **Dodaj zasób** > **punktu dostępu klienta**.
   
    ![Dodaj punkt dostępu klienta dla grupy dostępności](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. W **nazwa** pola, Utwórz nazwę tego nowy odbiornik, kliknij przycisk **dalej** dwa razy, a następnie kliknij przycisk **Zakończ**.  
    Nie należy przełączać odbiornika lub zasób w tryb online w tym momencie.

6. Kliknij przycisk **zasobów** kartę, a następnie rozwiń utworzony punkt dostępu klienta. 
    Zasób adresu IP dla każdej sieci klastra w klastrze jest wyświetlany. Jeśli jest to rozwiązanie tylko z platformą Azure, jest wyświetlany tylko jeden zasób adresu IP.

7. Wykonaj jedną z następujących czynności:
   
   * Aby skonfigurować rozwiązanie hybrydowe:
     
        a. Kliknij prawym przyciskiem myszy zasób adresu IP, który odnosi się do podsieci lokalnej, a następnie wybierz **właściwości**. Należy pamiętać, nazwa adresu IP i nazwa sieciowa.
   
        b. Wybierz **statyczny adres IP**, przypisać nieużywany adres IP, a następnie kliknij przycisk **OK**.
 
   * Aby skonfigurować tylko na platformie Azure rozwiązania:

        a. Kliknij prawym przyciskiem myszy zasób adresu IP, który odnosi się do podsieci środowiska platformy Azure, a następnie wybierz **właściwości**.
       
       > [!NOTE]
       > Jeśli odbiornik ulegnie awarii później do trybu online ze względu na konflikt adresu IP wybrana przez protokół DHCP, można skonfigurować statyczny adres IP, w tym oknie właściwości.
       > 
       > 

       b. W tym samym **adresu IP** oknie Właściwości zmień **nazwa adresu IP**.  
        Ta nazwa jest używana w zmiennej $IPResourceName skryptu programu PowerShell. Jeśli rozwiązanie obejmuje wiele sieci wirtualnych platformy Azure, powtórz ten krok dla każdego zasobu adresu IP.

