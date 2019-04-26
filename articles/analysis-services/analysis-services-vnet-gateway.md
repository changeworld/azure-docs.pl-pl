---
title: Użyj bramy danych lokalnych źródeł danych w usłudze Azure Virtual Network | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować serwer, aby użyć bramy dla źródeł danych w sieci wirtualnej.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7e97bd50e3d37218e0f88f722387fd1a53167e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534171"
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

