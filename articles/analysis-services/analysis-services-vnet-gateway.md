---
title: Konfigurowanie usług Analizy platformy Azure dla źródeł danych sieci wirtualnych | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować serwer usług Azure Analysis Services do używania bramy dla źródeł danych w sieci wirtualnej platformy Azure.Learn how to configure an Azure Analysis Services server to use a gateway for data sources on Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572274"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Użyj bramy dla źródeł danych w sieci wirtualnej platformy Azure

W tym artykule opisano właściwość serwera Usługi Azure Analysis Services **AlwaysUseGateway** do użycia, gdy źródła danych znajdują się w [sieci wirtualnej platformy Azure ."](../virtual-network/virtual-networks-overview.md)

## <a name="server-access-to-vnet-data-sources"></a>Dostęp serwera do źródeł danych sieci wirtualnej

Jeśli źródła danych są dostępne za pośrednictwem sieci wirtualnej, serwer usług Azure Analysis Services musi połączyć się z tymi źródłami danych, tak jakby były one lokalne, w twoim środowisku. Właściwość serwera **AlwaysUseGateway** można skonfigurować w celu określenia serwera, aby uzyskać dostęp do wszystkich źródeł danych za pośrednictwem [bramy lokalnej](analysis-services-gateway.md). 

Źródła danych wystąpienia zarządzanego usługi Azure SQL Database są uruchamiane w sieci wirtualnej platformy Azure z prywatnym adresem IP. Jeśli publiczny punkt końcowy jest włączony w wystąpieniu, brama nie jest wymagana. Jeśli publiczny punkt końcowy nie jest włączony, lokalna brama danych jest wymagana, a właściwość AlwaysUseGateway musi być ustawiona na true.

> [!NOTE]
> Ta właściwość jest skuteczna tylko wtedy, gdy [lokalna brama danych](analysis-services-gateway.md) jest zainstalowana i skonfigurowana. Brama może znajdować się w sieci wirtualnej.

## <a name="configure-alwaysusegateway-property"></a>Konfigurowanie właściwości AlwaysUseGateway

1. W > > **właściwości** > serwera SSMS **,** wybierz opcję **Pokaż właściwości zaawansowane (wszystkie).**
2. W **aspaas\AlwaysUseGateway**wybierz **true**.

    ![Zawsze używaj właściwości bramy](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Zobacz też
[Łączenie się z lokalnymi źródłami danych](analysis-services-gateway.md)   
[Instalowanie i konfigurowanie lokalnej bramy danych](analysis-services-gateway-install.md)   
[Wirtualna sieć platformy Azure (vnet)](../virtual-network/virtual-networks-overview.md)   

