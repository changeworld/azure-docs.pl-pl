---
title: Dodawanie lub usuwanie delegowania podsieci w sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak dodać lub usunąć podsieć delegowaną dla usługi platformy Azure na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 9bf44aa5ab18f94ec91650548dc13360ce1b1f3d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938501"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Dodawanie lub usuwanie delegowania podsieci

Delegowanie podsieci daje jawne uprawnienia do usługi w celu tworzenia zasobów specyficznych dla usługi w podsieci przy użyciu unikatowego identyfikatora podczas wdrażania usługi. W tym artykule opisano sposób dodawania lub usuwania delegowanej podsieci usługi platformy Azure.

## <a name="sign-in-to-azure"></a>Zaloguj się do platformy Azure

Zaloguj się do Azure Portal przy https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć, która zostanie później delegowana do usługi platformy Azure.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Networking** > **Sieć wirtualna**.
1. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź *10.0.0.0/16*. |
    | Ramach | Wybierz swoją subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, wprowadź nazwę *zasobu*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz pozycję **Wschodnie**.|
    | Nazwa podsieci | Wprowadź nazwę moja *podsieć*. |
    | Zakres adresów podsieci | Wprowadź *10.0.0.0/24*. |
    |||
1. Pozostaw resztę jako domyślną, a następnie wybierz pozycję **Utwórz**.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegowanie podsieci do usługi platformy Azure

W tej sekcji utworzysz delegowaną podsieć utworzoną w poprzedniej sekcji do usługi platformy Azure.

1. Na pasku wyszukiwania portalu wprowadź *myVirtualNetwork*. Gdy **myVirtualNetwork** pojawia się w wynikach wyszukiwania, wybierz ją.
2. W wynikach wyszukiwania wybierz pozycję *myVirtualNetwork*.
3. Wybierz pozycję **podsieci**, w obszarze **Ustawienia**, a następnie wybierz pozycję Moja **podsieć**.
4. Na stronie Moja *podsieć* na liście **delegowanie podsieci** wybierz pozycję z usług wymienionych w obszarze **delegowanie podsieci do usługi** (na przykład **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Usuwanie delegowania podsieci z usługi platformy Azure

1. Na pasku wyszukiwania portalu wprowadź *myVirtualNetwork*. Gdy **myVirtualNetwork** pojawia się w wynikach wyszukiwania, wybierz ją.
2. W wynikach wyszukiwania wybierz pozycję *myVirtualNetwork*.
3. Wybierz pozycję **podsieci**, w obszarze **Ustawienia**, a następnie wybierz pozycję Moja **podsieć**.
4. Na stronie Moja *podsieć* na liście **delegowanie podsieci** wybierz pozycję **Brak** z usług wymienionych w obszarze **delegowanie podsieci do usługi**. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [zarządzać podsieciami na platformie Azure](virtual-network-manage-subnet.md).
