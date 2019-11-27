---
title: Tworzenie Load Balancer przy użyciu stref frontonu — Azure Portal
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć usługa Load Balancer w warstwie Standardowa z strefą frontonu z Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225340"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Tworzenie usługa Load Balancer w warstwie Standardowa z strefą frontonu przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia [Usługa Load Balancer w warstwie Standardowa](https://aka.ms/azureloadbalancerstandard) publicznego z konfiguracją adresów IP frontonu. Aby zrozumieć, jak strefy dostępności współpracują z usługa Load Balancer w warstwie Standardowa, zobacz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpoczynania pracy oraz zasobów platformy Azure, regionów i rodzin rozmiarów maszyn wirtualnych, można wypróbować strefy dostępności za pomocą programu, zobacz [omówienie strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Tworzenie modułu równoważenia obciążenia przy użyciu adresu IP frontonu strefowej

1. W przeglądarce przejdź do Azure Portal: [https://portal.azure.com](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **sieci** > **Load Balancer.**
3. Na stronie **Tworzenie modułu równoważenia obciążenia** w obszarze **Nazwa** wpisz **myLoadBalancer**.
4. W obszarze **Typ** wybierz opcję **Publiczny**.
5. W obszarze jednostka SKU wybierz pozycję **standardowa**.
6. Kliknij pozycję **Wybierz publiczny adres IP**, kliknij **pozycję Utwórz nowy**, a następnie w obszarze **Utwórz publiczny adres IP** w obszarze Nazwa wpisz **myPublicIPZonal**, w polu jednostka SKU wybierz pozycję **standardowa**, w obszarze strefa dostępności wybierz pozycję **1**.
    
>[!NOTE] 
> Publicznego adresu IP, utworzone w tym kroku jest standardowej jednostki SKU domyślnie.

1. W obszarze **Grupa zasobów**kliknij pozycję **Utwórz nową**, a następnie wpisz **myResourceGroupZLB** jako nazwę grupy zasobów.
1. W obszarze **Lokalizacja**wybierz pozycję **Europa Zachodnia**, a następnie kliknij przycisk **OK**. Rozpocznie się wdrażanie modułu równoważenia obciążenia. Potrwa to kilka minut.

    ![Utwórz strefowo nadmiarowe usługa Load Balancer w warstwie Standardowa z Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [stref usługa Load Balancer w warstwie Standardowa i dostępności](load-balancer-standard-availability-zones.md).



