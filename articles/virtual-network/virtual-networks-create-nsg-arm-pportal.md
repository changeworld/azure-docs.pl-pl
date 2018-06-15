---
title: Utwórz grupę zabezpieczeń sieci - portalu Azure | Dokumenty Microsoft
description: Dowiedz się, jak utworzyć i wdrożyć grup zabezpieczeń sieci przy użyciu portalu Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793647"
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Utwórz grupę zabezpieczeń sieci przy użyciu portalu Azure

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano model wdrażania usługi Resource Manager. Możesz również [tworzenia grup NSG w klasycznym modelu wdrażania](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Tworzenie grupy NSG frontonu NSG
Aby utworzyć **frontonu NSG** NSG, jak pokazano w scenariuszu, wykonaj następujące czynności:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Wybierz **+ Utwórz zasób >** > **grup zabezpieczeń sieci**.
   
    ![Portal Azure — grup NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. W obszarze **sieciowej grupy zabezpieczeń**, wybierz pozycję **Dodaj**.
   
    ![Portal Azure — grup NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. W obszarze **Utwórz grupę zabezpieczeń sieci**, Utwórz grupy NSG o nazwie *frontonu NSG* w *NSG zarządcy zasobów* zasobu, grupy, a następnie wybierz **Utwórz** .
   
    ![Portal Azure — grup NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Tworzenie reguł w istniejącej sieciowej grupie zabezpieczeń
Aby utworzyć reguły w istniejącej grupy NSG z portalu Azure, wykonaj następujące kroki:

1. Wybierz **wszystkie usługi**, następnie wyszukaj **sieciowej grupy zabezpieczeń**. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane, wybierz ją.
2. Na liście grup NSG, wybierz **frontonu NSG** > **reguły zabezpieczeń dla ruchu przychodzącego**
   
    ![Portal Azure — frontonu NSG](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Na liście **reguły zabezpieczeń dla ruchu przychodzącego**, wybierz pozycję **Dodaj**.
   
    ![Portal Azure — Dodaj regułę](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. W obszarze **Dodaj regułę zabezpieczeń dla ruchu przychodzącego**, tworzyć reguły o nazwie *zasada sieci web* z priorytet *200* zezwalania na dostęp za pośrednictwem *TCP* do portu *80* do żadnej maszyny Wirtualnej z dowolnego źródła, a następnie wybierz **OK**. Zwróć uwagę, że większość tych ustawień są wartościami domyślnymi już.
   
    ![Portal Azure — ustawienia reguły](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Po kilku sekundach zobacz temat nową regułę w grupie NSG.
   
    ![Portal Azure — nową regułę](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Powtórz kroki od 6 do utworzenia reguły ruchu przychodzącego o nazwie *reguły protokołu rdp* z priorytet *250* zezwalania na dostęp za pośrednictwem *TCP* do portu *3389* do żadnej maszyny Wirtualnej z dowolnego źródła.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią FrontEnd

1. Wybierz **wszystkie usługi >**, wprowadź **grup zasobów**, wybierz pozycję **grup zasobów** Jeśli wygląda na to, następnie wybierz **NSG zarządcy zasobów**.
2. W obszarze **NSG zarządcy zasobów**, wybierz pozycję **...**   >  **TestVNet**.
   
    ![Portal Azure — TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. W obszarze **ustawienia**, wybierz pozycję **podsieci** > **frontonu** > **sieciowej grupy zabezpieczeń**  >  **Frontonu NSG**.
   
    ![Portal Azure — ustawienia podsieci](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. W **frontonu** bloku, wybierz opcję **zapisać**.
   
    ![Portal Azure — ustawienia podsieci](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Tworzenie grupy NSG wewnętrznej bazy danych grupy NSG
Aby utworzyć **zaplecza NSG** NSG i powiązać ją do **wewnętrznej bazy danych** podsieci, wykonaj następujące czynności:

1. Aby utworzyć grupy NSG o nazwie *wewnętrznej bazy danych grupy NSG*, powtórz kroki opisane w [utworzyć NSG NSG frontonu](#Create-the-NSG-FrontEnd-NSG).
2. Aby utworzyć **przychodzących** reguły w tabeli poniżej, powtórz kroki [tworzenia reguł w istniejącej grupy NSG](#Create-rules-in-an-existing-NSG).
   
   | Reguła ruchu przychodzącego | Reguła ruchu wychodzącego |
   | --- | --- |
   | ![Portal Azure — reguły dla ruchu przychodzącego](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portal Azure — Reguła ruchu wychodzącego](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Aby skojarzyć **zaplecza NSG** grupy NSG **wewnętrznej bazy danych** podsieci, powtórz kroki [kojarzenie grupy NSG do podsieci frontonu](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [Zarządzanie istniejących grup NSG](manage-network-security-group.md)
* [Włącz rejestrowanie](virtual-network-nsg-manage-log.md) dla grup NSG.