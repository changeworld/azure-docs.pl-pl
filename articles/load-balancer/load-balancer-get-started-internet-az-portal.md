---
title: Tworzenie Load Balancer za pomocą Strefowo nadmiarowego frontonu — Azure Portal
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczny moduł równoważenia obciążenia standardowego przy użyciu strefowo nadmiarowy publiczny adres IP adres serwera sieci Web za pomocą witryny Azure portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: bd85e062b2b45abc32269b94ce678ebf63fb3fc1
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894454"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Tworzenie standardowego modułu równoważenia obciążenia za pomocą frontonu strefowo nadmiarowy za pomocą witryny Azure portal

W tym artykule opisano sposób tworzenia publicznej [Usługa Load Balancer w warstwie Standardowa](https://aka.ms/azureloadbalancerstandard) ze strefowo nadmiarowym adresem IP przy użyciu adresu w standardzie Public. Strefowo nadmiarowe domyślnie jest adresu IP frontonu jednego standardowego modułu równoważenia obciążenia.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
>  Obsługa strefy dostępności jest dostępna dla wybieranych zasobów platformy Azure i regionami i rodzinami rozmiarów maszyn wirtualnych. Aby uzyskać więcej informacji na temat rozpocząć pracę i które zasoby platformy Azure, regionów i rodzinami rozmiarów maszyn wirtualnych można wypróbować strefy dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Utwórz moduł równoważenia obciążenia nadmiarowe strefy

1. Przejdź do witryny Azure portal w przeglądarce: [ https://portal.azure.com ](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W lewym górnym rogu ekranu wybierz **Utwórz zasób** > **sieć** > **modułu równoważenia obciążenia.**
3. W **Tworzenie modułu równoważenia obciążenia** w obszarze **nazwa** typu **myLoadBalancer**.
4. W obszarze **Typ** wybierz opcję **Publiczny**.
5. W ramach jednostki SKU i wybierz **standardowa**.
6. Kliknij przycisk **publiczny adres IP**, kliknij przycisk **Utwórz nową**, a następnie w **tworzenie publicznego adresu IP** stronie w obszarze Nazwa, typ **myPublicIPStandard**.
    >[!NOTE] 
    > Publicznego adresu IP, utworzone w tym kroku jest dla standardowej jednostki SKU i strefowo nadmiarowe domyślnie. 
8. W obszarze **lokalizacji**, wybierz opcję **wschodnie stany USA 2**, a następnie kliknij przycisk **OK**. Rozpocznie się wdrażanie modułu równoważenia obciążenia. Potrwa to kilka minut.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



