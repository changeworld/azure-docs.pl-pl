---
title: 'Azure ExpressRoute: przenoszenie publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft'
description: W tym artykule przedstawiono kroki, aby przenieść swoje publicznej komunikacji równorzędnej do firmy Microsoft komunikacji równorzędnej usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436840"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Przenieś publicznej komunikacji równorzędnej do komunikacji równorzędnej firmy Microsoft

Ten artykuł ułatwia przenoszenie konfiguracji publicznej komunikacji równorzędnej do komunikacji równorzędnej bez przestojów firmy Microsoft. Usługa ExpressRoute obsługuje komunikację równorzędną firmy Microsoft z filtrami tras na potrzeby usług typu PaaS na platformie Azure, takich jak Azure Storage i Azure SQL Database. Teraz potrzebujesz tylko jednej domeny routingu, aby mieć dostęp do usług PaaS i SaaS firmy Microsoft. Filtry tras umożliwiają selektywne anonsowanie prefiksów usług PaaS dla regionów świadczenia usługi Azure, z których chcesz korzystać.

Publiczna Komunikacja równorzędna Azure ma 1 adres IP translatora adresów sieciowych skojarzony z każdą sesją BGP. Komunikacja równorzędna firmy Microsoft umożliwia skonfigurowanie własnych alokacji NAT, a także Używanie filtrów tras dla selektywnych anonsów prefiksów. Publiczna Komunikacja równorzędna to jednokierunkowa usługa, za pomocą której połączenie jest zawsze inicjowane z sieci WAN do Microsoft Azure usług. Usługi Microsoft Azure nie będą mogły inicjować połączeń do sieci za pomocą tej domeny routingu.

Po włączeniu publicznej komunikacji równorzędnej, możesz połączyć się do wszystkich usług platformy Azure. Firma Microsoft nie umożliwiają selektywne wybierz usługi, dla których firma Microsoft anonsować trasy do. Podczas gdy Komunikacja równorzędna firmy Microsoft jest łącznością dwukierunkową, w której można inicjować połączenie z usługi Microsoft Azure Service wraz z siecią WAN. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [obwody usługi ExpressRoute i domeny routingu](expressroute-circuit-peerings.md).

## <a name="before"></a>Przed rozpoczęciem

Aby nawiązać połączenie komunikacji równorzędnej firmy Microsoft, musisz skonfigurować i zarządzać nimi translatora adresów sieciowych. Dostawcą połączenia, mogą konfigurować oraz zarządzać nimi translatora adresów Sieciowych jako usługa zarządzana. Jeśli planujesz uzyskiwać dostęp do modelu PaaS platformy Azure i usług SaaS platformy Azure w komunikacji równorzędnej firmy Microsoft, jest poprawnie rozmiar puli adresów IP translatora adresów Sieciowych. Aby uzyskać więcej informacji na temat translatora adresów Sieciowych dla usługi ExpressRoute, zobacz [wymagania translatora adresów Sieciowych dla komunikacji równorzędnej firmy Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Po nawiązaniu połączenia z firmą Microsoft za pomocą usługi Azure ExpressRoute (komunikacji równorzędnej firmy Microsoft) masz wiele linków do firmy Microsoft. Jedno połączenie to istniejące połączenie internetowe, a drugie to usługa ExpressRoute. Część ruchu do firmy Microsoft może wychodzić przez Internet ale wracać przez usługę ExpressRoute lub odwrotnie.

![Łączność dwukierunkowa](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Pula adresów IP translatora adresów sieciowych anonsowana do firmy Microsoft nie może być anonsowana do Internetu. Spowodowałoby to przerwanie łączności z innymi usługami firmy Microsoft.

Zapoznaj się z [routingiem asymetrycznym z wieloma ścieżkami sieciowymi](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) , aby zapamiętać Routing asymetryczny przed skonfigurowaniem komunikacji równorzędnej firmy Microsoft.

* Jeśli korzystają z publicznej komunikacji równorzędnej, a obecnie ma reguł sieci IP dla publicznych adresów IP, które są używane do dostępu [usługi Azure Storage](../storage/common/storage-network-security.md) lub [usługi Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), należy upewnić się, że konfiguracja puli adresów IP translatora adresów Sieciowych z firmą Microsoft komunikacji równorzędnej znajduje się na liście publiczne adresy IP dla konta usługi Azure storage lub konta usługi Azure SQL.<br>
* Aby można było przenieść do firmy Microsoft komunikacji równorzędnej bez przestojów, wykonaj kroki w tym artykule w kolejności, w jakiej są przedstawione.

## <a name="create"></a>1. Utwórz komunikację równorzędną firmy Microsoft

Jeśli komunikacji równorzędnej firmy Microsoft nie został utworzony, należy użyć dowolnego z następujących artykułów do utworzenia komunikacji równorzędnej firmy Microsoft. Jeśli swoje oferty dostawcy łączności, zarządzane w warstwie 3 usługi, możesz poprosić dostawcy łączności, aby włączyć komunikację równorzędną Microsoft dla obwodu.

Jeśli warstwa 3 jest zarządzana przez następujące informacje są wymagane przed kontynuowaniem:

* Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.<br>
* Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.<br>
* Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Łączy podstawowe i pomocnicze należy używać tego samego identyfikatora sieci VLAN.<br>
* Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.<br>
* Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę rozdzielonych przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.<br>
* Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.

* **Opcjonalne** — ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić numer AS, do którego są rejestrowane.<br>
* **Opcjonalnie** — skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.

Szczegółowe instrukcje dotyczące włączania komunikacji równorzędnej firmy Microsoft można znaleźć w następujących artykułach:

* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Tworzenie komunikacji równorzędnej firmy Microsoft przy użyciu wiersza polecenia platformy Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Sprawdź poprawność komunikacji równorzędnej firmy Microsoft

Sprawdź, czy komunikacja równorzędna firmy Microsoft jest włączona, i anonsowane prefiksy publiczne są w stanie skonfigurowany.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Skonfiguruj i Dołącz filtr tras do obwodu

Domyślnie Nowa Komunikacja równorzędna firmy Microsoft nie anonsuje żadnych prefiksów do momentu dołączenia do obwodu filtru tras. Podczas tworzenia reguły filtru trasy można określić listę społeczności usługi dla regionów platformy Azure, które mają być używane dla usług Azure PaaS Services. Zapewnia to elastyczność filtrowania tras zgodnie z wymaganiami, jak pokazano na poniższym zrzucie ekranu:

![Scal publicznej komunikacji równorzędnej](./media/how-to-move-peering/routefilter.jpg)

Konfigurowanie filtrów tras, przy użyciu dowolnej z następujących artykułów:

* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu witryny Azure portal](how-to-routefilter-portal.md)<br>
* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu programu Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft przy użyciu wiersza polecenia platformy Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Usuń publiczną komunikację równorzędną

Po zweryfikowaniu, że skonfigurowano komunikacji równorzędnej firmy Microsoft, a prefiksy, które chcesz wykorzystać poprawnie są anonsowane w komunikacji równorzędnej firmy Microsoft, możesz usunąć publiczną komunikację równorzędną. Aby usunąć publiczną komunikację równorzędną, należy użyć dowolnego z następujących artykułów:

* [Usuwanie publicznej komunikacji równorzędnej Azure za pomocą programu Azure PowerShell](about-public-peering.md#powershell)
* [Usuwanie publicznej komunikacji równorzędnej Azure za pomocą interfejsu wiersza polecenia](about-public-peering.md#cli)
  
## <a name="view"></a>5. Wyświetlanie komunikacji równorzędnej
  
Można wyświetlić listę wszystkich obwodów usługi ExpressRoute i połączeń komunikacji równorzędnej w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [szczegóły dotyczące komunikacji równorzędnej Microsoft widoku](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
