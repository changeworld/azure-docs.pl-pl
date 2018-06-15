---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z maszyną wirtualną z systemem Windows oraz zalogować się do niej za pomocą witryny Azure Portal i modelu wdrażania przy użyciu usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012645"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Sposób nawiązywania połączenia z maszyną wirtualną platformy Azure z systemem Windows oraz logowania się do niej
Korzystając z przycisku **Połącz** w witrynie Azure Portal, uruchomisz sesję pulpitu zdalnego z poziomu pulpitu systemu Windows. Najpierw nawiążesz połączenie z maszyną wirtualną, a następnie zalogujesz się.

W przypadku próby nawiązania połączenia z maszyną wirtualną z systemem Windows z poziomu komputera Mac konieczne jest zainstalowanie klienta RDP dla komputerów Mac, takiego jak [Pulpit zdalny Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W lewym menu kliknij pozycję **Virtual Machines**.
3. Wybierz maszynę wirtualną z listy.
4. W górnej części strony dla maszyny wirtualnej, kliknij przycisk ![Obraz przycisku Połącz.](./media/connect-logon/connect.png) Dodaj...
2. W **nawiązywanie połączenia z maszyną wirtualną** , wybierz odpowiednie opcje i kliknij przycisk **plik RDP Pobierz**.
2. Otwórz pobrany plik RDP, a następnie kliknij przycisk **Connect** po wyświetleniu monitu. 
2. Zostanie wyświetlone ostrzeżenie, że plik `.rdp` pochodzi od nieznanego wydawcy. Jest to normalne. W oknie pulpitu zdalnego kliknij przycisk **Połącz**, aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/connect-logon/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz poświadczenia konta na maszynie wirtualnej, a następnie kliknij przycisk **OK**.
   
     **Konto lokalne** — zazwyczaj jest to nazwa użytkownika konta lokalnego i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny** — jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domena*&#92;*nazwa_użytkownika*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny** — jeśli maszyna wirtualna jest kontrolerem domeny, wpisz nazwę użytkownika i hasło konta administratora danej domeny.
4. Kliknij przycisk **Tak**, aby potwierdzić tożsamość maszyny wirtualnej i zakończyć logowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Jeśli przycisk **Połącz** w portalu jest nieaktywny i nie masz połączenia typu [ExpressRoute](../../expressroute/expressroute-introduction.md) lub Sieć VPN typu [lokacja do lokacji](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) z platformą Azure, musisz utworzyć i przypisać maszynie wirtualnej publiczny adres IP przed użyciem pulpitu zdalnego. Więcej informacji o [publicznych adresach IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Kolejne kroki
Jeśli podczas próby połączenia wystąpiły problemy, zobacz [Troubleshoot Remote Desktop connections](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Rozwiązywanie problemów z połączeniami pulpitu zdalnego). W tym artykule przedstawiono sposób diagnozowania i rozwiązywania typowych problemów.

