---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych — Azure Portal
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych przy użyciu Azure Portal.
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
ms.openlocfilehash: b1019b15463a03282c5d1bd8f0a878433d7f488e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199567"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej przy użyciu Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

W poniższych przykładowych krokach oczekuje się, że proste środowisko zostało już utworzone. Jeśli chcesz uruchomić kroki w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe opisane w temacie [Tworzenie sieci wirtualnej](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Jak utworzyć maszynę wirtualną do testowania statycznych prywatnych adresów IP
Nie można ustawić statycznego prywatnego adresu IP podczas tworzenia maszyny wirtualnej w trybie wdrażania Menedżer zasobów przy użyciu Azure Portal. Najpierw należy utworzyć maszynę wirtualną, a następnie ustawić jej prywatny adres IP jako statyczny.

Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *frontonu* sieci wirtualnej o nazwie *TestVNet*, wykonaj następujące kroki:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób** > **COMPUTE** > **Windows Server 2012 R2 Datacenter**, Zauważ, że na liście **Wybierz model wdrażania jest** już widoczny **Menedżer zasobów**, a następnie kliknij przycisk **Utwórz**, jak pokazano na poniższej ilustracji.
   
    ![Tworzenie maszyny wirtualnej w Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. W okienku **podstawowe** wprowadź nazwę maszyny wirtualnej do utworzenia (*DNS01* w scenariuszu), konto administratora lokalnego i hasło, jak pokazano na poniższej ilustracji.
   
    ![Okienko podstawowe](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Upewnij się, że wybrana **Lokalizacja** jest *środkowe stany USA*, a następnie kliknij pozycję **Wybierz istniejące** w obszarze **Grupa zasobów**, a następnie kliknij pozycję **Grupa zasobów** ponownie, a następnie kliknij pozycję *TestRG*, a następnie kliknij przycisk **OK**.
   
    ![Okienko podstawowe](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. W okienku **Wybierz rozmiar** wybierz pozycję **a1 Standard**, a następnie kliknij pozycję **Wybierz**.
   
    ![Wybierz okienko rozmiaru](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. W okienku **Ustawienia** upewnij się, że właściwości są ustawione przy użyciu następujących wartości, a następnie kliknij przycisk **OK**.
   
    -**konto magazynu**: *vnetstorage*
   
   * **Sieć**: *TestVNet*
   * **Podsieć**: *fronton*
     
     ![Wybierz okienko rozmiaru](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. W okienku **Podsumowanie** kliknij przycisk **OK**. Zwróć uwagę na następujący kafelek wyświetlony na pulpicie nawigacyjnym.
   
    ![Tworzenie maszyny wirtualnej w Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile nie jest to konieczne, [na przykład podczas przypisywania wielu adresów IP do maszyny wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure, lub utracisz łączność z maszyną wirtualną. Dowiedz się więcej o ustawieniach [prywatnych adresów IP](virtual-network-network-interface-addresses.md#private) . Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić informacje o statycznym prywatnym adresie IP dla maszyny wirtualnej utworzonej za pomocą powyższych kroków, wykonaj następujące czynności.

1. W Azure Portal kliknij pozycję **Przeglądaj wszystkie** > **maszyny wirtualne** > **DNS01** > **wszystkie ustawienia** > **interfejsy sieciowe** , a następnie kliknij na liście jedyny interfejs sieciowy.
   
    ![Wdrażanie kafelka maszyny wirtualnej](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. W okienku **interfejs sieciowy** kliknij pozycję **wszystkie ustawienia** > **adresy IP** i zwróć uwagę na wartości **przydziału** i **adresu IP** .
   
    ![Wdrażanie kafelka maszyny wirtualnej](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny wirtualnej
Aby dodać statyczny prywatny adres IP do maszyny wirtualnej utworzonej przy użyciu powyższych kroków, wykonaj następujące kroki:

1. W pokazanym powyżej okienku **adresy IP** kliknij pozycję **statyczne** w obszarze **przypisanie**.
2. Wpisz *192.168.1.101* dla **adresu IP**, a następnie kliknij przycisk **Zapisz**.
   
    ![Tworzenie maszyny wirtualnej w Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Jeśli po kliknięciu przycisku **Zapisz**zauważysz, że przypisanie nadal ma ustawioną wartość **dynamiczną**, oznacza to, że wprowadzony adres IP jest już używany. Spróbuj użyć innego adresu IP.
> 
> 

Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile nie jest to konieczne, [na przykład podczas przypisywania wielu adresów IP do maszyny wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure, lub utracisz łączność z maszyną wirtualną. Dowiedz się więcej o ustawieniach [prywatnych adresów IP](virtual-network-network-interface-addresses.md#private) . Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny Wirtualnej
Aby usunąć statyczny prywatny adres IP z utworzonej powyżej maszyny wirtualnej, wykonaj następujące czynności:

W pokazanym powyżej okienku **adresy IP** kliknij pozycję **dynamiczne** w obszarze **przypisanie**, a następnie kliknij przycisk **Zapisz**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile nie jest to konieczne, [na przykład podczas przypisywania wielu adresów IP do maszyny wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure, lub utracisz łączność z maszyną wirtualną. Dowiedz się więcej o ustawieniach [prywatnych adresów IP](virtual-network-network-interface-addresses.md#private) . Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zarządzania [ustawieniami adresów IP](virtual-network-network-interface-addresses.md).

