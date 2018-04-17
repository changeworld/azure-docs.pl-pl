---
title: Tworzenie sieci wirtualnej — Szybki start — Azure Portal | Microsoft Docs
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu witryny Azure Portal. Dzięki sieci wirtualnej zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie ze sobą i z Internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7107dc72686004141d8bea0083089cba065a9f4c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu witryny Azure Portal

Dzięki sieci wirtualnej zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie ze sobą i z Internetem. W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną. Po utworzeniu sieci wirtualnej, wdrożysz w niej dwie maszyny wirtualne. Następnie nawiążesz połączenie z jedną z maszyn wirtualnych z Internetu, a następnie rozpoczniesz prywatną komunikację między dwiema maszynami wirtualnymi.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|

    ![Wprowadzanie podstawowych informacji o sieci wirtualnej](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVm1|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|

    ![Podstawowe informacje dotyczące maszyny wirtualnej](./media/quick-create-portal/virtual-machine-basics.png)

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Ustawienia** zaakceptuj wszystkie wartości domyślne i wybierz przycisk **OK**.

    ![Ustawienia maszyny wirtualnej](./media/quick-create-portal/virtual-machine-settings.png)

6. W obszarze **Utwórz** na stronie **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej. Wdrożenie maszyny wirtualnej potrwa kilka minut. 

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Wykonaj ponownie kroki od 1 do 6, ale w kroku 3 nadaj maszynie wirtualnej nazwę *myVm2*.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

1. Po utworzeniu maszyny wirtualnej *myVm1* nawiąż z nią połączenie. W górnej części witryny Azure Portal wprowadź *myVm1*. Gdy pozycja **myVm1** pojawi się w wynikach wyszukiwania, wybierz ją. Wybierz przycisk **Połącz**.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/quick-create-portal/connect-to-virtual-machine.png)

2. Po wybraniu przycisku **Połącz** zostanie utworzony i pobrany na komputer plik Remote Desktop Protocol (rdp).  
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. Z poziomu programu PowerShell wprowadź polecenie `ping myvm2`. Polecenie ping nie powiedzie się, ponieważ korzysta z protokołu ICMP (Internet Control Message Protocol), którego ruch domyślnie nie jest przepuszczany przez zaporę systemu Windows.
2. Aby w kolejnym kroku umożliwić wykonanie polecenia ping z maszyny wirtualnej *myVm2* do maszyny wirtualnej *myVm1*, z poziomu programu PowerShell wprowadź następujące polecenie, które zezwala na ruch przychodzący z protokołu ICMP przez zaporę systemu Windows:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm1*. 

4. Ponownie wykonaj kroki opisane w sekcji [Nawiązywanie połączenia z maszyną wirtualną z Internetu](#connect-to-a-vm-from-the-internet), ale tym razem nawiąż połączenie z maszyną wirtualną *myVm2*. W wierszu polecenia wprowadź polecenie `ping myvm1`.

    Z maszyny wirtualnej *myVm1* zostanie odebrana odpowiedź, ponieważ w poprzednim kroku umożliwiono przekazywanie ruchu protokołu ICMP przez zaporę systemu Windows na maszynie wirtualnej *myVm1*.

5. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. Z Internetu nawiązano połączenie z jedną z maszyn wirtualnych, a następnie rozpoczęto prywatną komunikację między jedną i drugą maszyną wirtualną. Aby dowiedzieć się więcej na temat ustawień sieci wirtualnych, zobacz [Manage a virtual network](manage-virtual-network.md) (Zarządzanie siecią wirtualną).

Domyślnie platforma Azure zezwala na nieograniczoną komunikację prywatną między maszynami wirtualnymi, ale w przypadku funkcji podłączania pulpitu zdalnego zezwala tylko na ruch przychodzący z Internetu do maszyn wirtualnych z systemem Windows. Aby dowiedzieć się, jak ograniczać różnego rodzaju komunikację sieciową lub zezwalać na nią do i z maszyn wirtualnych, przejdź do samouczka [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md).