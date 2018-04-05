---
title: Przenieś publicznej komunikacji równorzędnej Azure ExpressRoute do komunikacji równorzędnej firmy Microsoft | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono kroki, aby przenieść sieci publicznej komunikacji równorzędnej do firmy Microsoft komunikacji równorzędnej ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: f34fabc95d5b56edc6e37c323bebf60bd98c8b90
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Przenieś publicznej komunikacji równorzędnej się do komunikacji równorzędnej firmy Microsoft

Obsługuje ExpressRoute za pomocą programu Microsoft komunikacji równorzędnej z filtrami trasy dla usług Azure PaaS, takich jak usługa Azure storage i bazy danych SQL Azure. Teraz należy tylko jednej domeny routingu dostęp do usług SaaS i PaaS firmy Microsoft. Filtry tras umożliwia selektywne anonsowanie prefiksy usługi PaaS w regionach platformy Azure, który chcesz wykorzystać.

W tym artykule opisano, jak przenieść publicznej konfiguracji komunikacji równorzędnej firmy Microsoft, równorzędna bez przestojów. Aby uzyskać więcej informacji o routingu domeny i komunikacji równorzędnych, zobacz [ExpressRoute obwody i domeny routingu](expressroute-circuit-peerings.md).


## <a name="before"></a>Przed rozpoczęciem

* Aby nawiązać komunikacji równorzędnej firmy Microsoft, należy skonfigurować i zarządzać nimi translatora adresów sieciowych. Dostawca połączenia może oraz zarządzać nimi translatora adresów Sieciowych jako usługa zarządzana. Jeśli planujesz dostęp do usług Azure SaaS w komunikacji równorzędnej firmy Microsoft i Azure PaaS, należy poprawnie rozmiaru puli adresów IP translatora adresów Sieciowych. Aby uzyskać więcej informacji o NAT ExpressRoute, zobacz [NAT wymagania dotyczące komunikacji równorzędnej firmy Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Jeśli korzystają z publicznej komunikacji równorzędnej, a obecnie ma zasady sieci IP dla publicznych adresów IP, które są używane do dostępu [usługi Azure Storage](../storage/common/storage-network-security.md) lub [bazy danych SQL Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), należy upewnić się, że konfiguracja puli adresów IP translatora adresów Sieciowych z firmą Microsoft równorzędna znajduje się na liście publicznych adresów IP dla konta magazynu Azure lub konta usługi Azure SQL.

* Aby przenieść do firmy Microsoft, równorzędna bez przestojów, wykonaj kroki w tym artykule w kolejności, które mają być przedstawiane.

## <a name="create"></a>1. Utwórz komunikacji równorzędnej firmy Microsoft

Jeśli komunikacji równorzędnej firmy Microsoft nie został utworzony, należy użyć dowolnego z następujących artykułów utworzyć komunikacji równorzędnej firmy Microsoft. Jeśli oferty dostawcy łączności zarządzane warstwy 3 usługi, poproś dostawca łączności do włączenia komunikacji równorzędnej dla obwodu firmy Microsoft.

  * [Utwórz komunikacji równorzędnej firmy Microsoft przy użyciu portalu Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Utwórz komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Utwórz komunikacji równorzędnej firmy Microsoft przy użyciu wiersza polecenia platformy Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Sprawdź poprawność Microsoft równorzędna jest włączona

Sprawdź, czy włączono komunikacji równorzędnej firmy Microsoft i anonsowany prefiksów publicznych znajdują się w stanie skonfigurowany.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurowanie i dołączanie filtr trasy do obwodu

Domyślnie nowe Microsoft komunikacji równorzędnych nie anonsuje wszystkie prefiksy dopóki filtr tras jest dołączony do obwodu. Podczas tworzenia reguły filtru trasy, można określić listy Wspólnot usługi, które regiony platformy Azure, które chcesz korzystać z usług Azure PaaS, jak pokazano na poniższym zrzucie ekranu:

![Scal publicznej komunikacji równorzędnej](.\media\how-to-move-peering\public.png)

Skonfiguruj filtry tras za pomocą dowolnego z następujących artykułów:

  * [Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft przy użyciu portalu Azure](how-to-routefilter-portal.md)
  * [Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure PowerShell](how-to-routefilter-powershell.md)
  * [Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft przy użyciu wiersza polecenia platformy Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Usuń publicznej komunikacji równorzędnej

Po zweryfikowaniu, że skonfigurowano komunikacji równorzędnej firmy Microsoft i prefiksy, który chcesz wykorzystać są poprawnie anonsowane w komunikacji równorzędnej firmy Microsoft, możesz następnie usunąć publicznej komunikacji równorzędnej. Aby usunąć publicznej komunikacji równorzędnej, należy użyć dowolnego z następujących artykułów:

  * [Usuń publicznej komunikacji równorzędnej platformy Azure przy użyciu portalu Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Usuń publicznej komunikacji równorzędnej platformy Azure przy użyciu programu Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Usuń publicznej komunikacji równorzędnej platformy Azure przy użyciu interfejsu wiersza polecenia](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Widok komunikacji równorzędnych
  
Można wyświetlić listę wszystkich obwody usługi ExpressRoute i komunikacji równorzędnych w portalu Azure. Aby uzyskać więcej informacji, zobacz [Microsoft widoku szczegółów komunikacji równorzędnej](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
