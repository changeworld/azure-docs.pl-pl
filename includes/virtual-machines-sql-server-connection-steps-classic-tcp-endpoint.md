---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183174"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Tworzenie punktu końcowego TCP dla maszyny wirtualnej
Aby uzyskać dostęp do programu SQL Server z Internetu, maszyna wirtualna musi mieć punkt końcowy do nasłuchiwać przychodzącej komunikacji TCP. Ten krok konfiguracji platformy Azure kieruje przychodzący ruch portowy TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

> [!NOTE]
> Jeśli łączysz się w ramach tej samej usługi w chmurze lub sieci wirtualnej, nie trzeba tworzyć publicznie dostępnego punktu końcowego. W takim przypadku można przejść do następnego kroku. Aby uzyskać więcej informacji, zobacz [Scenariusze połączeń](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. W witrynie Azure Portal wybierz pozycję **Maszyny wirtualne (klasyczne).**
2. Następnie wybierz maszynę wirtualną programu SQL Server.
3. Wybierz **pozycję Punkty końcowe**, a następnie kliknij przycisk **Dodaj** u góry bloku Punkty końcowe.
   
    ![Kroki portalu do tworzenia punktu końcowego](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. W **bloku Dodaj punkt końcowy** podaj **nazwę,** taką jak SQLEndpoint.
5. Wybierz wartość **TCP** w polu **Protokół**.
6. W przypadku **portu publicznego**należy określić numer portu, taki jak **57500**.
7. W przypadku **portu prywatnego**określ port nasłuchiwania programu SQL Server, który domyślnie ma **wartość 1433**.
8. Kliknij **przycisk Ok,** aby utworzyć punkt końcowy.

