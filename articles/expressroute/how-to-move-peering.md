---
title: Przenieś publicznej komunikacji równorzędnej do firmy Microsoft komunikacji równorzędnej — usługi Azure ExpressRoute | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono kroki, aby przenieść swoje publicznej komunikacji równorzędnej do firmy Microsoft komunikacji równorzędnej usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6b2bce6b488698db0a72c9a17f67c2555c6afa5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883148"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Przenieś publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft

Ten artykuł ułatwia przenoszenie konfiguracji publicznej komunikacji równorzędnej do komunikacji równorzędnej bez przestojów firmy Microsoft. Usługa ExpressRoute obsługuje komunikację równorzędną firmy Microsoft z filtrami tras na potrzeby usług typu PaaS na platformie Azure, takich jak Azure Storage i Azure SQL Database. Teraz potrzebujesz tylko jednej domeny routingu, aby mieć dostęp do usług PaaS i SaaS firmy Microsoft. Filtry tras umożliwiają selektywne anonsowanie prefiksów usług PaaS dla regionów świadczenia usługi Azure, z których chcesz korzystać. Aby uzyskać więcej informacji na temat routingu domen i komunikacji równorzędnej, zobacz [ExpressRoute obwody i domeny routingu](expressroute-circuit-peerings.md).

## <a name="before"></a>Przed rozpoczęciem

* Aby nawiązać połączenie komunikacji równorzędnej firmy Microsoft, musisz skonfigurować i zarządzać nimi translatora adresów sieciowych. Dostawcą połączenia, mogą konfigurować oraz zarządzać nimi translatora adresów Sieciowych jako usługa zarządzana. Jeśli planujesz uzyskiwać dostęp do modelu PaaS platformy Azure i usług SaaS platformy Azure w komunikacji równorzędnej firmy Microsoft, jest poprawnie rozmiar puli adresów IP translatora adresów Sieciowych. Aby uzyskać więcej informacji na temat translatora adresów Sieciowych dla usługi ExpressRoute, zobacz [wymagania translatora adresów Sieciowych dla komunikacji równorzędnej firmy Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Jeśli korzystają z publicznej komunikacji równorzędnej, a obecnie ma reguł sieci IP dla publicznych adresów IP, które są używane do dostępu [usługi Azure Storage](../storage/common/storage-network-security.md) lub [usługi Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), należy upewnić się, że konfiguracja puli adresów IP translatora adresów Sieciowych z firmą Microsoft komunikacji równorzędnej znajduje się na liście publiczne adresy IP dla konta usługi Azure storage lub konta usługi Azure SQL.

* Aby można było przenieść do firmy Microsoft komunikacji równorzędnej bez przestojów, wykonaj kroki w tym artykule w kolejności, w jakiej są przedstawione.

## <a name="create"></a>1. Tworzenie komunikacji równorzędnej firmy Microsoft

Jeśli komunikacji równorzędnej firmy Microsoft nie został utworzony, należy użyć dowolnego z następujących artykułów do utworzenia komunikacji równorzędnej firmy Microsoft. Jeśli swoje oferty dostawcy łączności, zarządzane w warstwie 3 usługi, możesz poprosić dostawcy łączności, aby włączyć komunikację równorzędną Microsoft dla obwodu.

  * [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu wiersza polecenia platformy Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Sprawdź poprawność Microsoft komunikacja równorzędna jest włączona

Sprawdź, czy komunikacja równorzędna firmy Microsoft jest włączona, i anonsowane prefiksy publiczne są w stanie skonfigurowany.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurowanie i dołączanie filtru tras do obwodu

Domyślnie nowe komunikacji równorzędnej firmy Microsoft nie anonsuje wszelkie prefiksy do czasu podłączenia filtru tras do obwodu. Podczas tworzenia reguły filtru trasy, należy określić listę społeczności usług dla regionów platformy Azure, które chcesz używać dla usług PaaS platformy Azure, jak pokazano na poniższym zrzucie ekranu:

![Scal publicznej komunikacji równorzędnej](./media/how-to-move-peering/public.png)

Konfigurowanie filtrów tras, przy użyciu dowolnej z następujących artykułów:

  * [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal](how-to-routefilter-portal.md)
  * [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure PowerShell](how-to-routefilter-powershell.md)
  * [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu wiersza polecenia platformy Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Usuwanie publicznej komunikacji równorzędnej

Po zweryfikowaniu, że skonfigurowano komunikacji równorzędnej firmy Microsoft, a prefiksy, które chcesz wykorzystać poprawnie są anonsowane w komunikacji równorzędnej firmy Microsoft, możesz usunąć publiczną komunikację równorzędną. Aby usunąć publiczną komunikację równorzędną, należy użyć dowolnego z następujących artykułów:

  * [Usuwanie publicznej komunikacji równorzędnej Azure za pomocą witryny Azure portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Usuwanie publicznej komunikacji równorzędnej Azure za pomocą programu Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Usuwanie publicznej komunikacji równorzędnej Azure za pomocą interfejsu wiersza polecenia](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Komunikacja równorzędna widoku
  
Można wyświetlić listę wszystkich obwodów usługi ExpressRoute i połączeń komunikacji równorzędnej w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [szczegóły dotyczące komunikacji równorzędnej Microsoft widoku](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
