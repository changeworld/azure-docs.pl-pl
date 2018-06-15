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
ms.openlocfilehash: 815a217526117325ff80759701141b2b4d148514
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805305"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Tworzenie klasycznej sieci wirtualnej w portalu Azure
Aby utworzyć sieć wirtualną oparta na powyższym scenariuszu klasyczny, wykonaj następujące kroki.

1. W przeglądarce przejdź do witryny http://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. Zwróć uwagę, że **wybierz model wdrożenia** listy już pokazuje **klasycznego**. 3. Kliknij przycisk **Utwórz** jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Na **sieci wirtualnej** okienka, typ **nazwa** sieci wirtualnej, a następnie kliknij przycisk **przestrzeni adresów**. Skonfiguruj ustawienia przestrzeni adresów sieci wirtualnej i jej pierwszej podsieci, a następnie kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia bloku CIDR do naszego scenariusza.
   
    ![Okienko przestrzeni adresów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kliknij przycisk **grupy zasobów** i wybierz grupę zasobów, aby dodać sieć wirtualną do, lub kliknij przycisk **Utwórz nową grupę zasobów** można dodać sieci wirtualnej do nowej grupy zasobów. Na poniższej ilustracji przedstawiono zasobu ustawienia grupy dla nowej grupy zasobów o nazwie **TestRG**. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Utwórz okienko grupy zasobów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. W razie potrzeby zmień wartości ustawień **Subskrypcja** i **Lokalizacja** sieci wirtualnej. 
7. Jeśli nie chcesz, aby sieć wirtualna była wyświetlana jako kafelek na **tablicy startowej**, wyłącz opcję **Przypnij do tablicy startowej**. 
8. Kliknij przycisk **Utwórz** zobaczysz Kafelek o nazwie **tworzenie sieci wirtualnej** jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Poczekaj, aż do utworzenia sieci wirtualnej, a gdy pojawi się Kafelek, kliknij go, aby dodać więcej podsieci.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Powinny pojawić się **konfiguracji** dla sieci wirtualnej, jak pokazano. 
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kliknij przycisk **podsieci** > **Dodaj**, wpisz **nazwa** i określ **(blok CIDR) zakres adresów** dla podsieci, a następnie Kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia bieżącego scenariusza.
    
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

