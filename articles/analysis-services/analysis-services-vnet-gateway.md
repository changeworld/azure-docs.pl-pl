---
title: Użyj bramy danych lokalnych źródeł danych w usłudze Azure Virtual Network | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować serwer, aby użyć bramy dla źródeł danych w sieci wirtualnej.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1cf447f27509bc62cc71a9c64237ff7dc3d7b30
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446091"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Używanie bramy dla źródeł danych usługi Azure Virtual Network (VNet)

W tym artykule opisano **AlwaysUseGateway** właściwości serwera do użycia w przypadku źródeł danych na [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Dostęp do serwera ze źródłami danych w sieci wirtualnej

Jeśli źródła danych są dostępne za pośrednictwem sieci wirtualnej, serwer usług Azure Analysis Services musi nawiązać tych źródeł danych tak, jakby są one lokalnie we własnym środowisku. Można skonfigurować **AlwaysUseGateway** właściwości serwera w celu określenia serwera dostępu do wszystkich danych źródła danych za pośrednictwem [bramy On-premises](analysis-services-gateway.md). 

> [!NOTE]
> Ta właściwość jest efektywne tylko wtedy, gdy [lokalnej bramy danych](analysis-services-gateway.md) jest zainstalowany i skonfigurowany. Brama może być w sieci wirtualnej.

## <a name="configure-alwaysusegateway-property"></a>Konfigurowanie właściwości AlwaysUseGateway

1. W programie SSMS > serwer > **właściwości** > **ogólne**, wybierz opcję **Pokaż zaawansowane (wszystkie) właściwości**.
2. W **ASPaaS\AlwaysUseGateway**, wybierz opcję **true**.

    ![Zawsze używaj właściwości bramy](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zobacz także
[Nawiązywanie połączenia z lokalnymi źródłami danych](analysis-services-gateway.md)   
[Instalowanie i konfigurowanie bramy danych lokalnych](analysis-services-gateway-install.md)   
[Usługa Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

