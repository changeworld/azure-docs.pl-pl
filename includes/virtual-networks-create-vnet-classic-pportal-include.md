---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa88bf3bd6c5037b41c09e9ffe47921f1b9dc9be
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183145"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Jak utworzyć klasyczną sieć wirtualną w witrynie Azure portal
Aby utworzyć klasycznej sieci wirtualnej na podstawie których poprzedni scenariusz, wykonaj następujące kroki.

1. W przeglądarce przejdź do witryny http://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. Należy zauważyć, że **wybierz model wdrożenia** już liście widać **klasycznego**. 3. Kliknij przycisk **Utwórz** jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Na **sieć wirtualna** okienko, typ **nazwa** sieci wirtualnej, a następnie kliknij przycisk **przestrzeń adresowa**. Skonfiguruj ustawienia przestrzeni adresów dla sieci wirtualnej i jej pierwszej podsieci, a następnie kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia bloku CIDR w naszym scenariuszu.
   
    ![Okienko przestrzeni adresów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kliknij przycisk **grupy zasobów** i wybierz grupę zasobów, aby dodać sieci wirtualnej lub kliknij **Utwórz nową grupę zasobów** można dodać sieci wirtualnej do nowej grupy zasobów. Na poniższej ilustracji przedstawiono zasobu ustawienia grupy dla nowej grupy zasobów o nazwie **TestRG**. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Utwórz okienko grupy zasobów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. W razie potrzeby zmień wartości ustawień **Subskrypcja** i **Lokalizacja** sieci wirtualnej. 
7. Jeśli nie chcesz, aby sieć wirtualna była wyświetlana jako kafelek na **tablicy startowej**, wyłącz opcję **Przypnij do tablicy startowej**. 
8. Kliknij przycisk **Utwórz** zobaczysz Kafelek o nazwie **tworzenie sieci wirtualnej** jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Poczekaj, aż sieci wirtualnej, która ma zostać utworzony, a po wyświetleniu kafelka, kliknij go, aby dodać więcej podsieci.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Powinien zostać wyświetlony **konfiguracji** danej sieci wirtualnej, jak pokazano. 
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kliknij przycisk **podsieci** > **Dodaj**, a następnie wpisz **nazwa** i określ **zakres adresów (blok CIDR)** dla podsieci, a następnie Kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia w naszym scenariuszu bieżącego.
    
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

