---
title: 'Azure ExpressRoute: Przenoszenie publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft'
description: W tym artykule przedstawiono kroki, aby przenieść publiczne komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft w usłudze ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436840"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Move a public peering to Microsoft peering (Przechodzenie z publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft)

Ten artykuł ułatwia przenoszenie konfiguracji komunikacji równorzędnej publicznej do komunikacji równorzędnej firmy Microsoft bez przestojów. Usługa ExpressRoute obsługuje komunikację równorzędną firmy Microsoft z filtrami tras na potrzeby usług typu PaaS na platformie Azure, takich jak Azure Storage i Azure SQL Database. Teraz potrzebujesz tylko jednej domeny routingu, aby mieć dostęp do usług PaaS i SaaS firmy Microsoft. Filtry tras umożliwiają selektywne anonsowanie prefiksów usług PaaS dla regionów świadczenia usługi Azure, z których chcesz korzystać.

Publiczna komunikacja równorzędna platformy Azure ma 1 adres IP NAT skojarzony z każdą sesją BGP. Komunikacja równorzędna firmy Microsoft umożliwia konfigurowanie własnych alokacji translatora i odpowiedzi na usługi, a także używanie filtrów tras do selektywnych reklam prefiksów. Komunikacja równorzędna publiczna to jednokierunkowa usługa, za pomocą której łączność jest zawsze inicjowana z sieci WAN do usług platformy Microsoft Azure. Usługi platformy Microsoft Azure nie będą mogły inicjować połączeń z siecią za pośrednictwem tej domeny routingu.

Po włączeniu komunikacji równorzędnej publicznej można połączyć się ze wszystkimi usługami platformy Azure. Nie zezwalamy na selektywne wybieranie usług, do których reklamujemy trasy. Podczas komunikacji równorzędnej firmy Microsoft jest dwukierunkowa łączność, gdzie połączenie może być inicjowane z usługi Microsoft Azure wraz z siecią WAN. Aby uzyskać więcej informacji na temat routingu domen i komunikacji równorzędnej, zobacz [Obwody usługi ExpressRoute i domeny routingu](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Aby połączyć się z komunikacją równorzędną firmy Microsoft, należy skonfigurować translatora z translatora i zarządzania siecią NAT. Dostawca łączności może skonfigurować translatora i obsługi sieciowej jako usługi zarządzanej. Jeśli planujesz uzyskać dostęp do usług Azure PaaS i Azure SaaS w komunikacji równorzędnej firmy Microsoft, ważne jest, aby poprawnie rozmiar puli adresów IP nat. Aby uzyskać więcej informacji na temat translatora nat dla usługi ExpressRoute, zobacz [wymagania dotyczące translatora i nat dla komunikacji równorzędnej firmy Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Po nawiązaniu połączenia z firmą Microsoft za pośrednictwem usługi Azure ExpressRoute (komunikacja równorzędna firmy Microsoft) masz wiele łączy do firmy Microsoft. Jedno połączenie to istniejące połączenie internetowe, a drugie to usługa ExpressRoute. Część ruchu do firmy Microsoft może wychodzić przez Internet ale wracać przez usługę ExpressRoute lub odwrotnie.

![Łączność dwukierunkowa](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Pula adresów IP translatora adresów sieciowych anonsowana do firmy Microsoft nie może być anonsowana do Internetu. Spowodowałoby to przerwanie łączności z innymi usługami firmy Microsoft.

Przed skonfigurowaniem konfiguracji komunikacji równorzędnej firmy Microsoft należy zapoznać się [z routingiem asymetrycznym z wieloma ścieżkami sieciowymi,](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) aby uzyskać ostrzeżenia dotyczące routingu asymetrycznego.

* Jeśli używasz komunikacji równorzędnej publicznej i obecnie masz reguły sieci IP dla publicznych adresów IP, które są używane do [uzyskiwania](../storage/common/storage-network-security.md) dostępu do usługi Azure Storage lub [usługi Azure SQL Database,](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)należy upewnić się, że pula adresów IP nat skonfigurowana z komunikacją równorzędną firmy Microsoft znajduje się na liście publicznych adresów IP dla konta magazynu platformy Azure lub konta SQL platformy Azure.<br>
* Aby przejść do komunikacji równorzędnej firmy Microsoft bez przestojów, należy wykonać kroki opisane w tym artykule w kolejności, w jakiej zostały przedstawione.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Tworzenie komunikacji równorzędnej firmy Microsoft

Jeśli nie utworzono komunikacji równorzędnej firmy Microsoft, użyj dowolnego z poniższych artykułów, aby utworzyć komunikację równorzędną firmy Microsoft. Jeśli dostawca łączności oferuje usługi zarządzanej warstwy 3, możesz poprosić dostawcę łączności o włączenie komunikacji równorzędnej firmy Microsoft dla obwodu.

Jeśli warstwa 3 jest przez Ciebie zarządzana, przed kontynuowaniem wymagane są następujące informacje:

* Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego użytecznego adresu IP dla swojego routera.<br>
* Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego użytecznego adresu IP dla swojego routera.<br>
* Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Zarówno dla podstawowych i pomocniczych łączy należy użyć tego samego identyfikatora sieci VLAN.<br>
* Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.<br>
* Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłać zestaw prefiksów, możesz wysłać listę oddzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.<br>
* Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.

* **Opcjonalnie** — numer ASN klienta: Jeśli masz prefiksy reklamowe, które nie są zarejestrowane na numer AS komunikacji równorzędnej, możesz określić numer AS, na który są zarejestrowane.<br>
* **Opcjonalnie** — skrót MD5, jeśli zdecydujesz się go użyć.

Szczegółowe instrukcje umożliwiające obsługę komunikacji równorzędnej firmy Microsoft można znaleźć w następujących artykułach:

* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu portalu Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Sprawdzanie poprawności komunikacji równorzędnej firmy Microsoft jest włączone

Sprawdź, czy komunikacja równorzędna firmy Microsoft jest włączona, a anonsowane prefiksy publiczne są w skonfigurowanym stanie.

* [Portal Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Skonfiguruj i podłącz filtr trasy do obwodu

Domyślnie nowe komunikacji równorzędnej firmy Microsoft nie anonsują żadnych prefiksów, dopóki filtr trasy nie zostanie dołączony do obwodu. Podczas tworzenia reguły filtru trasy można określić listę społeczności usług dla regionów platformy Azure, które mają być używane dla usług PaaS platformy Azure. Zapewnia to elastyczność filtrowania tras zgodnie z wymaganiami, jak pokazano na poniższym zrzucie ekranu:

![Scalanie komunikacji równorzędnej publicznej](./media/how-to-move-peering/routefilter.jpg)

Skonfiguruj filtry marszruty przy użyciu dowolnego z następujących artykułów:

* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal](how-to-routefilter-portal.md)<br>
* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Skreślenie publicznego komunikacji równorzędnej

Po sprawdzeniu, czy komunikacja równorzędna firmy Microsoft jest skonfigurowana, a prefiksy, które chcesz wykorzystać, są poprawnie anonsowane w komunikacji równorzędnej firmy Microsoft, można następnie usunąć publiczną komunikację równorzędna. Aby usunąć publiczne komunikacji równorzędnej, użyj dowolnego z następujących artykułów:

* [Usuwanie publicznej komunikacji równorzędnej platformy Azure przy użyciu programu Azure PowerShell](about-public-peering.md#powershell)
* [Usuwanie publicznej komunikacji równorzędnej platformy Azure przy użyciu interfejsu wiersza polecenia](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Wyświetlanie komunikacji równorzędnej
  
Listę wszystkich obwodów i komunikacji równorzędnej usługi ExpressRoute można wyświetlić w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie szczegółów komunikacji równorzędnej firmy Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
