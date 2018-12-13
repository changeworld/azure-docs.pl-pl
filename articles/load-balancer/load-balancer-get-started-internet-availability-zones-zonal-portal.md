---
title: Tworzenie standardowego modułu równoważenia obciążenia przy użyciu adresu IP frontonu strefowych — witryna Azure portal
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczny Load Balancer w warstwie standardowa przy użyciu strefowy publiczny adres IP adres serwera sieci Web za pomocą witryny Azure portal
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: e109504fe8657436d73870cc022ed4bc81c559f5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095340"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-public-ip-address-frontend-using-azure-portal"></a>Tworzenie publicznego Load Balancer w warstwie standardowa przy użyciu strefowy publiczny adres IP adres serwera sieci Web przy użyciu witryny Azure portal

W tym artykule opisano proces tworzenia publicznego [standardowego modułu równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) za pomocą frontonu strefowych. Aby dowiedzieć się, jak działają strefach dostępności przy użyciu standardowego modułu równoważenia obciążenia, zobacz [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Tworzenie modułu równoważenia obciążenia przy użyciu adresu IP frontonu strefowej

1. Przejdź do witryny Azure portal w przeglądarce: [ http://portal.azure.com ](http://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W lewym górnym rogu ekranu wybierz **Utwórz zasób** > **sieć** > **modułu równoważenia obciążenia.**
3. W **Tworzenie modułu równoważenia obciążenia** w obszarze **nazwa** typu **myLoadBalancer**.
4. W obszarze **Typ** wybierz opcję **Publiczny**.
5. W ramach jednostki SKU i wybierz **standardowa**.
6. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie w **tworzenie publicznego adresu IP** stronie w obszarze Nazwa, typ **myPublicIPZonal**, w ramach jednostki SKU, wybierz **standardowa**, strefy dostępności, wybierz **1**.
    
>[!NOTE] 
> Publicznego adresu IP, utworzone w tym kroku jest standardowej jednostki SKU domyślnie.

7. Dla **grupy zasobów**, kliknij przycisk **Utwórz nową**, a następnie wpisz **myResourceGroupZLB** jako nazwę grupy zasobów.
8. Aby uzyskać **lokalizacji**, wybierz opcję **Europa Zachodnia**, a następnie kliknij przycisk **OK**. Rozpocznie się wdrażanie modułu równoważenia obciążenia. Potrwa to kilka minut.

    ![Tworzenie strefowo nadmiarowego Load Balancer w warstwie standardowa przy użyciu witryny Azure portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



