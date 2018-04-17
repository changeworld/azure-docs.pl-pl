---
title: Utwórz sieciowej grupy zabezpieczeń - portalu Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: dd05df542327f9d8dae924b7097d247980a0558b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-network-security-groups-using-the-azure-portal"></a>Tworzenie grup zabezpieczeń za pomocą portalu Azure w sieci

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano model wdrażania usługi Resource Manager. Możesz również [tworzenia grup NSG w klasycznym modelu wdrażania](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

W powyższym scenariuszu na podstawie próbek PowerShell poniższe polecenia oczekiwać środowisku niezłożonym już utworzone. Jeśli chcesz uruchomić polecenia wyświetlaną w tym dokumencie, wdrażając najpierw utworzyć środowisko testowe [ten szablon](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), kliknij przycisk **wdrażanie na platformie Azure**, Zastąp domyślne wartości parametrów, jeśli to konieczne i postępuj zgodnie z instrukcjami w portalu. Kroki użyj **NSG zarządcy zasobów** jako nazwę grupy zasobów, szablon został wdrożony.

## <a name="create-the-nsg-frontend-nsg"></a>Tworzenie grupy NSG frontonu NSG
Aby utworzyć **frontonu NSG** NSG, jak pokazano w scenariuszu powyżej, wykonaj poniższe kroki.

1. W przeglądarce przejdź do witryny http://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij przycisk **Przeglądaj >** > **sieciowej grupy zabezpieczeń**.
   
    ![Portal Azure — grup NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. W **sieciowej grupy zabezpieczeń** bloku, kliknij przycisk **Dodaj**.
   
    ![Portal Azure — grup NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. W **Utwórz grupę zabezpieczeń sieci** bloku Utwórz grupy NSG o nazwie *frontonu NSG* w *NSG zarządcy zasobów* grupy zasobów, a następnie kliknij przycisk **Utwórz**.
   
    ![Portal Azure — grup NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Tworzenie reguł w istniejącej sieciowej grupie zabezpieczeń
Aby utworzyć reguły w istniejącej grupy NSG z portalu Azure, wykonaj poniższe kroki.

1. Kliknij przycisk **Przeglądaj >** > **sieciowej grupy zabezpieczeń**.
2. Na liście grup NSG, kliknij **frontonu NSG** > **reguły zabezpieczeń dla ruchu przychodzącego**
   
    ![Portal Azure — frontonu NSG](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Na liście **reguły zabezpieczeń dla ruchu przychodzącego**, kliknij przycisk **Dodaj**.
   
    ![Portal Azure — Dodaj regułę](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. W **Dodaj regułę zabezpieczeń dla ruchu przychodzącego** bloku Utwórz reguły o nazwie *zasada sieci web* z priorytet *200* zezwalania na dostęp za pośrednictwem *TCP* do portu *80* do żadnej maszyny Wirtualnej z dowolnego źródła, a następnie kliknij przycisk **OK**. Zwróć uwagę, że większość tych ustawień są wartościami domyślnymi już.
   
    ![Portal Azure — ustawienia reguły](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Po kilku sekundach zostanie wyświetlone nowe zasady w grupie NSG.
   
    ![Portal Azure — nową regułę](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Powtórz kroki od 6 do utworzenia reguły ruchu przychodzącego o nazwie *reguły protokołu rdp* z priorytet *250* zezwalania na dostęp za pośrednictwem *TCP* do portu *3389* do żadnej maszyny Wirtualnej z dowolnego źródła.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią FrontEnd
1. Kliknij przycisk **Przeglądaj >** > **grup zasobów** > **NSG zarządcy zasobów**.
2. W **NSG zarządcy zasobów** bloku, kliknij przycisk **...**   >  **TestVNet**.
   
    ![Portal Azure — TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. W **ustawienia** bloku, kliknij przycisk **podsieci** > **frontonu** > **sieciowej grupy zabezpieczeń** > **frontonu NSG**.
   
    ![Portal Azure — ustawienia podsieci](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. W **frontonu** bloku, kliknij przycisk **zapisać**.
   
    ![Portal Azure — ustawienia podsieci](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Tworzenie grupy NSG wewnętrznej bazy danych grupy NSG
Aby utworzyć **zaplecza NSG** NSG i powiązać ją do **wewnętrznej bazy danych** podsieci, wykonaj poniższe kroki.

1. Powtórz kroki [utworzyć NSG frontonu NSG](#Create-the-NSG-FrontEnd-NSG) do utworzenia grupy NSG o nazwie *wewnętrznej bazy danych grupy NSG*
2. Powtórz kroki [tworzenia reguł w istniejącej grupy NSG](#Create-rules-in-an-existing-NSG) utworzyć **przychodzących** reguły w poniższej tabeli.
   
   | Reguła ruchu przychodzącego | Reguła ruchu wychodzącego |
   | --- | --- |
   | ![Portal Azure — reguły dla ruchu przychodzącego](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portal Azure — Reguła ruchu wychodzącego](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Powtórz kroki [kojarzenie grupy NSG do podsieci frontonu](#Associate-the-NSG-to-the-FrontEnd-subnet) do skojarzenia **zaplecza NSG** grupy NSG **zaplecza** podsieci.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [Zarządzanie istniejących grup NSG](manage-network-security-group.md)
* [Włącz rejestrowanie](virtual-network-nsg-manage-log.md) dla grup NSG.

