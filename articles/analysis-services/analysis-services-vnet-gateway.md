---
title: Użyj bramy danych lokalnych źródeł danych sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować serwer, aby użyć bramy dla źródeł danych w sieci wirtualnej.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7cb1fb7bcd3a50532d5ff994afac56e226fb2018
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596910"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Użyj bramy dla źródeł danych w sieci wirtualnej platformy Azure (VNet)

W tym artykule opisano **AlwaysUseGateway** właściwości serwera do użycia w przypadku źródeł danych na [sieci wirtualnej platformy Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Dostęp do serwera ze źródłami danych sieci wirtualnej

Jeśli źródeł danych są dostępne za pośrednictwem sieci wirtualnej, serwer usług Azure Analysis Services musi połączyć się z tych źródeł danych tak, jakby znajdują się w infrastrukturze lokalnej, we własnym środowisku. Można skonfigurować **AlwaysUseGateway** właściwości serwera w celu określenia serwera dostępu do wszystkich danych źródła danych za pośrednictwem [brama lokalna](analysis-services-gateway.md). 

> [!NOTE]
> Ta właściwość jest efektywne tylko wtedy, gdy [bramy danych lokalnych](analysis-services-gateway.md) jest zainstalowana i skonfigurowana. Brama może być w sieci wirtualnej.

## <a name="configure-alwaysusegateway-property"></a>Skonfiguruj właściwości AlwaysUseGateway

1. W programie SSMS > serwera > **właściwości** > **ogólne**, wybierz pozycję **właściwości Pokaż zaawansowane (wszystkie)**.
2. W **ASPaaS\AlwaysUseGateway**, wybierz pozycję **true**.

    ![Zawsze używaj właściwości bramy](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zobacz także
[Łączenie ze źródłami danych lokalnych](analysis-services-gateway.md)   
[Zainstaluj i skonfiguruj bramę danych lokalnych](analysis-services-gateway-install.md)   
[Sieć wirtualna platformy Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

