---
title: Skonfiguruj prywatne adresy IP dla maszyn wirtualnych — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP maszyn wirtualnych za pomocą witryny Azure portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: 31aeab946b9ad740e2f56eb1ecaafd3e76cc42b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723793"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej przy użyciu witryny Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [Program PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Witryna Azure portal (klasyczny)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klasyczny)](virtual-networks-static-private-ip-classic-ps.md)
> * [Interfejs wiersza polecenia platformy Azure (wersja klasyczna)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano model wdrażania usługi Resource Manager. Możesz również [Zarządzanie statyczny prywatny adres IP w klasycznym modelu wdrażania](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Następujące przykładowe oczekiwać proste środowisko już utworzony. Jeśli chcesz uruchomić kroki, jak są one wyświetlane w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [tworzenie sieci wirtualnej](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Jak utworzyć Maszynę wirtualną do testowania statyczne prywatne adresy IP
Nie można ustawić statyczny prywatny adres IP podczas tworzenia maszyny Wirtualnej w trybie wdrażania usługi Resource Manager przy użyciu witryny Azure portal. Należy najpierw utworzyć maszynę Wirtualną, a następnie ustaw jego prywatny adres IP jako statyczną.

Aby utworzyć Maszynę wirtualną o nazwie *DNS01* w *frontonu* podsieci sieci wirtualnej o nazwie *TestVNet*, wykonaj następujące kroki:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij przycisk **Utwórz zasób** > **obliczenia** > **systemu Windows Server 2012 R2 Datacenter**, zwróć uwagę, że **wybierz wdrożenie model** już liście widać **usługi Resource Manager**, a następnie kliknij przycisk **Utwórz**, jak pokazano na poniższej ilustracji.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. W **podstawy** okienku, wprowadź nazwę maszyny Wirtualnej, aby utworzyć (*DNS01* w scenariuszu), konto administratora lokalnego i hasło, jak pokazano na poniższej ilustracji.
   
    ![W okienku podstawy](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Upewnij się, że **lokalizacji** wybrana *środkowe stany USA*, następnie kliknij przycisk **wybierz istniejący** w obszarze **grupy zasobów**, następnie kliknij przycisk **Grupy zasobów** ponownie, następnie kliknij przycisk *TestRG*, a następnie kliknij przycisk **OK**.
   
    ![W okienku podstawy](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. W **wybierz rozmiar** okienku wybierz **standardowa A1**, a następnie kliknij przycisk **wybierz**.
   
    ![Wybierz okienko rozmiar](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. W **ustawienia** okienko, pamiętaj, są konfigurowane przy użyciu następujących wartości właściwości, a następnie kliknij przycisk **OK**.
   
    -**Konto magazynu**: *vnetstorage*
   
   * **Sieć**: *TestVNet*
   * **Podsieć**: *Frontonu*
     
     ![Wybierz okienko rozmiar](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. W **Podsumowanie** okienku kliknij **OK**. Zwróć uwagę, następujący Kafelek wyświetlany na pulpicie nawigacyjnym.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia. Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej utworzone za pomocą powyższych kroków, wykonaj następujące czynności.

1. W witrynie Azure portal, kliknij przycisk **PRZEGLĄDAJ wszystkie** > **maszyn wirtualnych** > **DNS01** > **wszystkie ustawienia**  >  **Interfejsy sieciowe** a następnie kliknij polecenie interfejsu sieciowego, na liście.
   
    ![Kafelek wdrażanie maszyny Wirtualnej](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. W **interfejs sieciowy** okienku kliknij **wszystkie ustawienia** > **adresów IP** i zwróć uwagę, **przypisania** i  **Adres IP** wartości.
   
    ![Kafelek wdrażanie maszyny Wirtualnej](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny Wirtualnej
Aby dodać statyczny prywatny adres IP do maszyny Wirtualnej utworzone za pomocą powyższych kroków, wykonaj następujące kroki:

1. Z **adresów IP** okienku pokazanych powyżej, kliknij polecenie **statyczne** w obszarze **przypisania**.
2. Typ *192.168.1.101* dla **adresu IP**, a następnie kliknij przycisk **Zapisz**.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Jeśli po kliknięciu przycisku **Zapisz**, zwróć uwagę, że przypisanie nadal jest ustawiona na **dynamiczne**, oznacza, że wpisany adres IP jest już używana. Wypróbuj różne adresy IP.
> 
> 

Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia. Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny Wirtualnej
Aby usunąć statyczny prywatny adres IP z maszyny Wirtualnej utworzonej powyżej, wykonaj następujący krok:

Z **adresów IP** okienku pokazanych powyżej, kliknij polecenie **dynamiczne** w obszarze **przypisania**, a następnie kliknij przycisk **Zapisz**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia. Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zarządzaniu [ustawienia adresu IP](virtual-network-network-interface-addresses.md).

