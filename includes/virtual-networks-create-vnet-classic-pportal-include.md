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
ms.openlocfilehash: ac5599a38c3fcbc4a6c331d2b14c6b6c55c31d82
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850441"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Jak utworzyć klasyczną sieć wirtualną w Azure Portal
Aby utworzyć klasyczną sieć wirtualną na podstawie poprzedniego scenariusza, wykonaj następujące kroki.

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. Zauważ, że na liście **Wybierz model wdrażania jest** już widoczna **klasyczna**. 3. Kliknij przycisk **Utwórz** , jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. W okienku **Sieć wirtualna** wpisz **nazwę** sieci wirtualnej, a następnie kliknij pozycję **przestrzeń adresowa**. Skonfiguruj ustawienia przestrzeni adresów dla sieci wirtualnej i jej pierwszej podsieci, a następnie kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia blokowania CIDR w naszym scenariuszu.
   
    ![Okienko przestrzeni adresów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kliknij pozycję **Grupa zasobów** i wybierz grupę zasobów, do której chcesz dodać sieć wirtualną, lub kliknij pozycję **Utwórz nową grupę zasobów** , aby dodać sieć wirtualną do nowej grupy zasobów. Na poniższej ilustracji przedstawiono ustawienia grupy zasobów dla nowej grupy zasobów o nazwie **TestRG**. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Okienko Tworzenie grupy zasobów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. W razie potrzeby zmień wartości ustawień **Subskrypcja** i **Lokalizacja** sieci wirtualnej. 
7. Jeśli nie chcesz, aby sieć wirtualna była wyświetlana jako kafelek na **tablicy startowej**, wyłącz opcję **Przypnij do tablicy startowej**. 
8. Kliknij przycisk **Utwórz** i zwróć uwagę na kafelek o nazwie **Tworzenie sieci wirtualnej** , jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Poczekaj na utworzenie sieci wirtualnej, a po wyświetleniu kafelka kliknij go, aby dodać więcej podsieci.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Powinna zostać wyświetlona **Konfiguracja** sieci wirtualnej, jak pokazano. 
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kliknij pozycję **podsieci** > **Dodaj**, następnie wpisz **nazwę** i określ **zakres adresów (blok CIDR)** dla podsieci, a następnie kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia bieżącego scenariusza.
    
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

