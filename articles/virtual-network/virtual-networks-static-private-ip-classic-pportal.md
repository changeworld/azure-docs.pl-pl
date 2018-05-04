---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych (klasyczne) — portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP maszyn wirtualnych (klasyczne) za pomocą portalu Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1aae74d8077a75e5ab556703b1c1531f540bbdb4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej (klasyczne) przy użyciu portalu Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [Zarządzanie statycznego prywatnego adresu IP w modelu wdrażania usługi Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Wykonaj kroki przykładowe oczekiwać środowisku niezłożonym już utworzone. Wykonanie kroków wyświetlaną w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [utworzyć sieć wirtualną](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Sposób określania statycznego prywatnego adresu IP, podczas tworzenia maszyny Wirtualnej
Aby utworzyć Maszynę wirtualną o nazwie *DNS01* w *frontonu* podsieci sieci wirtualnej o nazwie *TestVNet* z statycznego prywatnego adresu IP z *192.168.1.101*pełne następujące kroki:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Wybierz **nowy** > **obliczeniowe** > **systemu Windows Server 2012 R2 Datacenter**, zwróć uwagę, że **wybierz model wdrożenia** listy już pokazuje **klasycznego**, a następnie wybierz **Utwórz**.
   
    ![Tworzenie maszyny Wirtualnej w portalu Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. W obszarze **Utwórz maszynę Wirtualną**, wprowadź nazwę maszyny Wirtualnej, która ma zostać utworzony (*DNS01* w scenariuszu), konto administratora lokalnego i hasło.
   
    ![Tworzenie maszyny Wirtualnej w portalu Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Wybierz **konfiguracji opcjonalnej** > **sieci** > **sieci wirtualnej**, a następnie wybierz **TestVNet** . Jeśli **TestVNet** jest niedostępny, upewnij się, że używasz *środkowe stany USA* lokalizacji i utworzeniu środowisko testowe opisane na początku tego artykułu.
   
    ![Tworzenie maszyny Wirtualnej w portalu Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. W obszarze **sieci**, upewnij się, że jest obecnie wybranej podsieci *frontonu*, a następnie wybierz pozycję **adresów IP**w obszarze **przypisywania adresów IP** Wybierz **statycznych**, a następnie wprowadź *192.168.1.101* dla **adres IP** jak pokazano poniżej.
   
    ![Tworzenie maszyny Wirtualnej w portalu Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Wybierz **OK** w obszarze **adresów IP**, wybierz pozycję **OK** w obszarze **sieci**, a następnie wybierz **OK** w obszarze **Opcjonalne config**.
7. W obszarze **Utwórz maszynę Wirtualną**, wybierz pozycję **Utwórz**. Uwaga poniżej kafelków wyświetlanych na pulpicie nawigacyjnym:
   
    ![Tworzenie maszyny Wirtualnej w portalu Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statycznych prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić statyczny prywatne informacje o adresie IP dla maszyny Wirtualnej utworzone za pomocą kroków opisanych powyżej, wykonaj poniższe kroki.

1. W portalu Azure wybierz **PRZEGLĄDAJ wszystko** > **maszyn wirtualnych (klasyczne)** > **DNS01** > **wszystkie ustawienia** > **adresów IP** i zwróć uwagę przypisywania adresów IP i adres IP, jak pokazano poniżej.
   
    ![Tworzenie maszyny Wirtualnej w portalu Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statycznego prywatnego adresu IP z maszyny Wirtualnej

W obszarze **adresów IP**, wybierz pozycję **dynamiczne** z prawej strony **przypisywania adresów IP**, wybierz pozycję **zapisać**, a następnie wybierz  **Tak**, jak pokazano na poniższej ilustracji:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statycznego prywatnego adresu IP do istniejącej maszyny Wirtualnej

1. W obszarze **adresów IP**pokazano wcześniej, wybierz opcję **statycznych** z prawej strony **przypisywania adresów IP**.
2. Typ *192.168.1.101* dla **adres IP**, wybierz pozycję **zapisać**, a następnie wybierz **tak**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawianie adresów IP w ramach systemu operacyjnego

Zaleca się, że nie zostanie statycznie przypisany prywatny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny Wirtualnej, o ile to konieczne. Jeśli ręcznie ustawić prywatnego adresu IP w ramach systemu operacyjnego, upewnij się, że jest ten sam adres prywatny adres IP przypisany do maszyny Wirtualnej platformy Azure lub można utracić łączność z maszyną wirtualną. Ręcznie nigdy nie należy przypisywać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [zastrzeżone publicznego adresu IP](virtual-networks-reserved-public-ip.md) adresów.
* Dowiedz się więcej o [poziomie wystąpienia publicznego adresu IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adresów.
* Zapoznaj się [zastrzeżone interfejsów API REST IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

