---
title: Zaloguj się do klasycznej maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Użyj portalu Azure do logowania się do systemu Windows maszyny wirtualnej utworzonej z klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Logowanie do maszyny wirtualnej systemu Windows przy użyciu witryny Azure Portal
W portalu Azure, użyj **Connect** przycisk, aby rozpocząć sesję pulpitu zdalnego i zaloguj się do maszyny Wirtualnej systemu Windows.

Czy chcesz połączyć z maszyną wirtualną systemu Linux Zobacz [jak zalogować się do maszyny wirtualnej z systemem Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby uzyskać informacje o tym, jak zalogować się do maszyny Wirtualnej przy użyciu modelu Resource Manager, zobacz [tutaj](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Zaloguj się do Portalu Azure.
2. Kliknij maszynę wirtualną, którą chcesz uzyskać dostęp. Nazwa jest wyświetlana w **wszystkie zasoby** okienka.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. Kliknij przycisk **Connect** przycisk na stronie właściwości maszyny wirtualnej. 
2. W **nawiązywanie połączenia z maszyną wirtualną** , przechowywać, wybierz odpowiednie opcje i kliknij przycisk **plik RDP Pobierz**.
2. Otwórz pobrany plik RDP, a następnie kliknij przycisk **Connect** po wyświetleniu monitu. 
2. Zostanie wyświetlone ostrzeżenie, że plik `.rdp` pochodzi od nieznanego wydawcy. Jest to normalne. W oknie pulpitu zdalnego kliknij przycisk **Połącz**, aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/connect-logon/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz poświadczenia konta na maszynie wirtualnej, a następnie kliknij przycisk **OK**.
   
     **Konto lokalne** — zazwyczaj jest to nazwa użytkownika konta lokalnego i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny** — jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domena*&#92;*nazwa_użytkownika*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny** — jeśli maszyna wirtualna jest kontrolerem domeny, wpisz nazwę użytkownika i hasło konta administratora danej domeny.
4. Kliknij przycisk **Tak**, aby potwierdzić tożsamość maszyny wirtualnej i zakończyć logowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>Kolejne kroki
* Jeśli **Connect** przycisk jest nieaktywny lub występują inne problemy z połączeniem pulpitu zdalnego, spróbuj zresetować konfigurację. Kliknij przycisk **Zresetuj dostęp zdalny** z poziomu pulpitu nawigacyjnego maszyny wirtualnej.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Problemy z hasłem spróbuj zresetować urządzenie. Kliknij przycisk **resetowania hasła** wzdłuż lewej krawędzi wirtualnej maszyny pulpitu nawigacyjnego, w obszarze **pomocy technicznej i rozwiązywania problemów**.

    ![Zresetuj hasło](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Te porady nie działa lub nie są potrzebne, zobacz [połączeń Rozwiązywanie problemów z pulpitu zdalnego do systemu Windows Azure maszyny wirtualnej](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W tym artykule przedstawiono sposób diagnozowania i rozwiązywania typowych problemów.
