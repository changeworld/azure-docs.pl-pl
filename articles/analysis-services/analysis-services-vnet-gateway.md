---
title: Korzystanie z lokalnej bramy danych dla usługi Azure Virtual Network źródła danych | Microsoft Docs
description: Dowiedz się, jak skonfigurować serwer do korzystania z bramy dla źródeł danych w sieci wirtualnej.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301223"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Używanie bramy dla źródeł danych w usłudze Azure Virtual Network (Sieć wirtualna)

W tym artykule opisano właściwość serwera **AlwaysUseGateway** , która ma być używana, gdy źródła danych znajdują się w usłudze [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Dostęp serwera do źródeł danych sieci wirtualnej

Jeśli dostęp do źródeł danych odbywa się za pomocą sieci wirtualnej, serwer Azure Analysis Services musi połączyć się z tymi źródłami danych, tak jakby znajdowały się one lokalnie, w Twoim środowisku. Można skonfigurować właściwość serwera **AlwaysUseGateway** , aby określić serwer, który ma uzyskać dostęp do wszystkich źródeł danych za pomocą [bramy lokalnej](analysis-services-gateway.md). 

Azure SQL Database źródła danych wystąpienia zarządzanego są uruchamiane w sieci wirtualnej platformy Azure z prywatnym adresem IP. Jeśli w wystąpieniu jest włączony publiczny punkt końcowy, Brama nie jest wymagana. Jeśli publiczny punkt końcowy nie jest włączony, wymagana jest lokalna Brama danych i Właściwość AlwaysUseGateway musi mieć wartość true.

> [!NOTE]
> Ta właściwość obowiązuje tylko wtedy, gdy [lokalna Brama danych](analysis-services-gateway.md) została zainstalowana i skonfigurowana. Brama może znajdować się w sieci wirtualnej.

## <a name="configure-alwaysusegateway-property"></a>Skonfiguruj Właściwość AlwaysUseGateway

1. W obszarze SSMS > Server > **właściwości** > **Ogólne**wybierz pozycję **Pokaż zaawansowane (wszystkie) właściwości**.
2. W **ASPaaS\AlwaysUseGateway**wybierz **wartość true**.

    ![Zawsze używaj właściwości bramy](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zobacz także
[Łączenie z lokalnymi źródłami danych](analysis-services-gateway.md)   
[Instalowanie i Konfigurowanie lokalnej bramy danych](analysis-services-gateway-install.md)   
[Virtual Network platformy Azure (Sieć wirtualna)](../virtual-network/virtual-networks-overview.md)   

