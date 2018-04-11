---
title: "Zarządzanie grup NSG przy użyciu portalu Azure | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak zarządzać istniejących grup NSG, przy użyciu portalu Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Grupy NSG za pomocą portalu zarządzania

> [!div class="op_single_selector"]
> * [Portal](virtual-network-manage-nsg-arm-portal.md)
> * [Program PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania Menedżera zasobów, które firma Microsoft zaleca w przypadku większości nowych wdrożeń zamiast klasycznym modelu wdrażania.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Pobieranie informacji
Można wyświetlić istniejących grup NSG, pobrać reguł dla istniejącej grupy NSG i dowiedzieć się, jakie zasoby grupy NSG jest skojarzona z.

### <a name="view-existing-nsgs"></a>Wyświetlanie istniejących grup NSG

Aby wyświetlić wszystkich istniejących grup NSG w ramach subskrypcji, wykonaj następujące kroki:

1. W przeglądarce przejdź do strony http://portal.azure.com i w razie potrzeby zaloguj się przy użyciu konta platformy Azure.

2. Kliknij przycisk **Przeglądaj >** > **sieciowej grupy zabezpieczeń**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Zapoznaj się z listą grup NSG w **sieciowej grupy zabezpieczeń** bloku.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Widok grup NSG, w grupie zasobów

Aby wyświetlić listę grup NSG w **NSG zarządcy zasobów** zasobów grupy, wykonaj następujące czynności:

1. Kliknij przycisk **grup zasobów >** > **NSG zarządcy zasobów** > **...** .

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Na liście zasobów szukać elementów Wyświetlanie ikony NSG, jak pokazano w **zasobów** bloku poniżej.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Wyświetl listę wszystkich reguł dla grupy NSG

Aby wyświetlić reguły NSG o nazwie **frontonu NSG**, wykonaj następujące czynności:

1. Z **sieciowej grupy zabezpieczeń** bloku lub **zasobów** bloku przedstawionych powyżej, kliknij przycisk **frontonu NSG**.

2. W **ustawienia** , kliknij pozycję **reguły zabezpieczeń dla ruchu przychodzącego**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. **Reguły zabezpieczeń dla ruchu przychodzącego** bloku jest wyświetlana, jak pokazano poniżej.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. W **ustawienia** , kliknij pozycję **reguł zabezpieczeń dla ruchu wychodzącego** wyświetlić reguły ruchu wychodzącego.

    > [!NOTE]
    > Aby wyświetlić reguły domyślne, kliknij przycisk **domyślne zasady** ikonę w górnej części bloku, który wyświetla reguły.
    >

### <a name="view-nsgs-associations"></a>Wyświetlanie grup NSG powiązań

Aby wyświetlić zasobów **frontonu NSG** grupa NSG jest skojarzony z, wykonaj następujące czynności:

1. Z **sieciowej grupy zabezpieczeń** bloku lub **zasobów** bloku przedstawionych powyżej, kliknij przycisk **frontonu NSG**.

2. W **ustawienia** , kliknij pozycję **podsieci** do wyświetlania, podsieci, które są skojarzone z grupy NSG.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. W **ustawienia** , kliknij pozycję **interfejsy sieciowe** do wyświetlania, co skojarzony z grupy NSG są karty sieciowe.

## <a name="manage-rules"></a>Zarządzaj regułami
Można dodać reguły do istniejącej grupy NSG, edytować istniejące zasady i Usuń reguły.

### <a name="add-a-rule"></a>Dodawanie reguły
Można dodać, dzięki czemu reguły **przychodzących** ruch do portu **443** z dowolnej maszyny do **frontonu NSG** NSG, wykonaj następujące czynności:

1. Z **sieciowej grupy zabezpieczeń** bloku lub **zasobów** bloku przedstawionych powyżej, kliknij przycisk **frontonu NSG**.
2. W **ustawienia** , kliknij pozycję **reguły zabezpieczeń dla ruchu przychodzącego**.
3. W **reguły zabezpieczeń dla ruchu przychodzącego** bloku, kliknij przycisk **Dodaj**. Następnie w **Dodaj regułę zabezpieczeń dla ruchu przychodzącego** bloku wypełnienia wartości, jak pokazano poniżej, a następnie kliknij przycisk **OK**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Po kilku sekundach zauważyć nowe reguły w **reguły zabezpieczeń dla ruchu przychodzącego** bloku.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Zmień reguły
Aby zmienić reguły utworzone powyżej, aby zezwalać na ruch przychodzący z **Internet** , wykonaj następujące czynności:

1. Z **sieciowej grupy zabezpieczeń** bloku lub **zasobów** bloku przedstawionych powyżej, kliknij przycisk **frontonu NSG**.
2. W **ustawienia** , kliknij pozycję reguły utworzone powyżej.
3. W **Zezwalaj https** bloku, zmień **źródła** właściwości, jak pokazano poniżej, a następnie kliknij przycisk **zapisać**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Usuwanie reguły

Aby usunąć regułę utworzone powyżej, wykonaj następujące kroki:

1. Z **sieciowej grupy zabezpieczeń** bloku lub **zasobów** bloku przedstawionych powyżej, kliknij przycisk **frontonu NSG**.
2. W **ustawienia** , kliknij pozycję reguły utworzone powyżej.
3. W **Zezwalaj https** bloku, kliknij przycisk **usunąć**, a następnie kliknij przycisk **tak**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Zarządzanie skojarzenia
Możesz skojarzyć grupy NSG do podsieci i karty sieciowe. Można również usunąć skojarzenie grupy NSG z dowolnego zasobu, który jest ona skojarzona.

### <a name="associate-an-nsg-to-a-nic"></a>Kojarzenie grupy NSG z kartą sieciową
Aby skojarzyć **frontonu NSG** grupy NSG **TestNICWeb1** karty Sieciowej, wykonaj następujące czynności:

1. Z **sieciowej grupy zabezpieczeń** bloku lub **zasobów** bloku przedstawionych powyżej, kliknij przycisk **frontonu NSG**.
2. W **ustawienia** , kliknij pozycję **interfejsy sieciowe** > **skojarzyć** > **TestNICWeb1**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Usuń skojarzenie grupy NSG z karty Sieciowej

Aby usunąć skojarzenie **frontonu NSG** grupy NSG z **TestNICWeb1** karty Sieciowej, wykonaj następujące czynności:

1. W portalu Azure kliknij **grup zasobów >** > **NSG zarządcy zasobów** > **...**   >  **TestNICWeb1**.

2. W **TestNICWeb1** bloku, kliknij przycisk **zmiany zabezpieczeń...**   >  **Brak**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> Aby skojarzyć kartę Sieciową do żadnych istniejących NSG umożliwia także tego bloku.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Usuń skojarzenie grupy NSG z podsiecią

Aby usunąć skojarzenie **frontonu NSG** grupy NSG z **frontonu** podsieci, wykonaj następujące czynności:

1. W portalu Azure kliknij **grup zasobów >** > **NSG zarządcy zasobów** > **...**   >  **TestVNet**.

2. W **ustawienia** bloku, kliknij przycisk **podsieci** > **frontonu** > **sieciowej grupy zabezpieczeń** > **Brak**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. W **frontonu** bloku, kliknij przycisk **zapisać**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Kojarzenie grupy NSG z podsiecią

Aby skojarzyć **frontonu NSG** grupy NSG **FronEnd** podsieci, wykonaj następujące czynności:

1. W portalu Azure kliknij **grup zasobów >** > **NSG zarządcy zasobów** > **...**   >  **TestVNet**.
2. W **ustawienia** bloku, kliknij przycisk **podsieci** > **frontonu** > **sieciowej grupy zabezpieczeń** > **frontonu NSG**.
3. W **frontonu** bloku, kliknij przycisk **zapisać**.

> [!NOTE]
> Grupy NSG można skojarzyć z podsiecią, z thh NSG **ustawienia** bloku.
>

## <a name="delete-an-nsg"></a>Usuwanie grupy NSG
Grupy NSG można usuwać tylko, jeśli nie został skojarzony z żadnym zasobem. Aby usunąć grupy NSG, wykonaj następujące kroki:.

1. W portalu Azure kliknij **grup zasobów >** > **NSG zarządcy zasobów** > **...**   >  **Frontonu NSG**.
2. W **ustawienia** bloku, kliknij przycisk **interfejsy sieciowe**.
3. Jeśli ma żadnych kart sieciowych na liście, kliknij kartę Sieciową i wykonaj krok 2 w [skojarzenie grupy NSG z karty Sieciowej](#Dissociate-an-NSG-from-a-NIC).
4. Powtórz krok 3 dla każdej karty sieciowej.
5. W **ustawienia** bloku, kliknij przycisk **podsieci**.
6. Jeśli ma żadnych podsieci na liście, kliknij podsieć i wykonaj kroki 2 i 3 w [skojarzenie grupy NSG z podsiecią](#Dissociate-an-NSG-from-a-subnet).
7. Przewija od lewej do **frontonu NSG** bloku, następnie kliknij przycisk **usunąć** > **tak**.

    ![Portal Azure — grup NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Kolejne kroki
* [Włącz rejestrowanie](virtual-network-nsg-manage-log.md) dla grup NSG.
