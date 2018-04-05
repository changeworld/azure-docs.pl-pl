---
title: Utwórz publiczny obciążenia standardowego równoważenia z zonal frontonu adres publiczny adres IP za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Informacje o tworzeniu publicznego obciążenia standardowego równoważenia z zonal frontonu adres publiczny adres IP z portalu Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 533c48b3a004f85dfbd2970d73dcf7de21811dca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-public-ip-address-frontend-using-azure-portal"></a>Utwórz publiczny obciążenia standardowego równoważenia z zonal frontonu adres publiczny adres IP za pomocą portalu Azure

W tym artykule opisano przez proces tworzenia publiczny [standardowe usługi równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z zonal frontonu. Aby zrozumieć, jak działają stref dostępności z modułem równoważenia obciążenia standardowego, zobacz [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md). 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Obsługa stref dostępności jest dostępna dla wybierz zasobów platformy Azure i regiony i rodziny rozmiar maszyny Wirtualnej. Więcej informacji na temat rozpocząć, które zasobów platformy Azure, regiony i rodziny rozmiar maszyny Wirtualnej można spróbować stref dostępności przy, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Aby uzyskać pomoc techniczną, możesz skorzystać z witryny [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) lub [otworzyć bilet pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Utwórz usługę równoważenia obciążenia z adresem IP frontonu zonal

1. W przeglądarce przejdź do portalu Azure: [ http://portal.azure.com ](http://portal.azure.com) i zaloguj się za pomocą konta platformy Azure.
2. Po lewej stronie górnej części ekranu, wybierz **Utwórz zasób** > **sieci** > **usługi równoważenia obciążenia.**
3. W **modułu równoważenia obciążenia Utwórz** w obszarze **nazwa** typu **myLoadBalancer**.
4. W obszarze **Typ** wybierz opcję **Publiczny**.
5. W obszarze jednostka SKU, wybierz **standardowe**.
6. Kliknij przycisk **do publicznego adresu IP wybierz**, kliknij przycisk **Utwórz nowy**i w **tworzenie publicznego adresu IP** strony, w obszarze nazwy typu **myPublicIPZonal**, Jednostka SKU, wybierz **standardowe**, strefy dostępności, wybierz **1**.
    
>[!NOTE] 
> Publicznego adresu IP utworzone w tym kroku jest standardowy SKU domyślnie.

7. Dla **grupy zasobów**, kliknij przycisk **Utwórz nowy**, a następnie wpisz **myResourceGroupZLB** jako nazwę grupy zasobów.
8. Dla **lokalizacji**, wybierz pozycję **Europa**, a następnie kliknij przycisk **OK**. Rozpocznie się wdrażanie modułu równoważenia obciążenia. Potrwa to kilka minut.

    ![Utwórz strefowo nadmiarowy standardowe usługi równoważenia obciążenia z portalu Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia i dostępność strefy](load-balancer-standard-availability-zones.md).



