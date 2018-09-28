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
ms.date: 09/13/2018
ms.author: cynthn
ms.openlocfilehash: b9cce5658b705e9d3255d2662b2a0157a2e548c3
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409035"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Sposób nawiązywania połączenia z maszyną wirtualną platformy Azure z systemem Windows oraz logowania się do niej
Korzystając z przycisku **Połącz** w witrynie Azure Portal, uruchomisz sesję pulpitu zdalnego z poziomu pulpitu systemu Windows. Najpierw nawiążesz połączenie z maszyną wirtualną, a następnie logowania.

Aby połączyć się z maszyny Wirtualnej z systemem Windows z poziomu komputera Mac, konieczne będzie zainstalowanie klienta RDP dla komputerów Mac, takie jak [Pulpit zdalny Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **maszyn wirtualnych**.
3. Wybierz maszynę wirtualną z listy.
4. Górnej części strony dla maszyny wirtualnej, wybierz **Connect**.
2. Na **Połącz z maszyną wirtualną** strony, wybierz odpowiednie opcje i wybierz **pliku RDP Pobierz**.
2. Otwórz pobrany plik RDP, a następnie wybierz pozycję **Connect** po wyświetleniu monitu. 
2. Zostanie wyświetlone ostrzeżenie, że plik RDP pochodzi od nieznanego wydawcy. Jest to oczekiwane. W **Podłączanie pulpitu zdalnego** wybierz **Connect** aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/connect-logon/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wprowadź poświadczenia dla konta na maszynie wirtualnej, a następnie wybierz pozycję **OK**.
   
     **Konto lokalne**: jest to zazwyczaj konta lokalnego, nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny**: Jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domeny*&#92;*Username*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny**: Jeśli maszyna wirtualna jest kontrolerem domeny, wprowadź nazwę użytkownika i hasło konta administratora domeny dla tej domeny.
4. Wybierz **tak** do zweryfikowania tożsamości maszyny wirtualnej i zakończyć logowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Jeśli **Connect** przycisk w portalu jest nieaktywny i nie są połączone na platformie Azure za pośrednictwem [Express Route](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) połączenie, musisz utworzyć i Przypisz publiczny adres IP maszyny Wirtualnej przed użyciem pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [publiczne adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Kolejne kroki
Jeśli masz trudności z połączeniem, zobacz [połączeń pulpitu zdalnego Rozwiązywanie problemów z](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

