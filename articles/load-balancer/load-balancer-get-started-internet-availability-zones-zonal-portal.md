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
ms.openlocfilehash: 7395da5bc1bf3829cf652374a9a0bb60b4621042
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894519"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Tworzenie usługa Load Balancer w warstwie Standardowa z strefą frontonu przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia [Usługa Load Balancer w warstwie Standardowa](https://aka.ms/azureloadbalancerstandard) publicznego z konfiguracją adresów IP frontonu. Aby dowiedzieć się, jak działają strefach dostępności przy użyciu standardowego modułu równoważenia obciążenia, zobacz [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Tworzenie modułu równoważenia obciążenia przy użyciu adresu IP frontonu strefowej

1. Przejdź do witryny Azure portal w przeglądarce: [ https://portal.azure.com ](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W lewym górnym rogu ekranu wybierz **Utwórz zasób** > **sieć** > **modułu równoważenia obciążenia.**
3. W **Tworzenie modułu równoważenia obciążenia** w obszarze **nazwa** typu **myLoadBalancer**.
4. W obszarze **Typ** wybierz opcję **Publiczny**.
5. W ramach jednostki SKU i wybierz **standardowa**.
6. Kliknij przycisk **wybierz publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie w **tworzenie publicznego adresu IP** stronie w obszarze Nazwa, typ **myPublicIPZonal**, w ramach jednostki SKU, wybierz **standardowa**, strefy dostępności, wybierz **1**.
    
>[!NOTE] 
> Publicznego adresu IP, utworzone w tym kroku jest standardowej jednostki SKU domyślnie.

1. Dla **grupy zasobów**, kliknij przycisk **Utwórz nową**, a następnie wpisz **myResourceGroupZLB** jako nazwę grupy zasobów.
1. Aby uzyskać **lokalizacji**, wybierz opcję **Europa Zachodnia**, a następnie kliknij przycisk **OK**. Rozpocznie się wdrażanie modułu równoważenia obciążenia. Potrwa to kilka minut.

    ![Utwórz strefowo nadmiarowe usługa Load Balancer w warstwie Standardowa z Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



