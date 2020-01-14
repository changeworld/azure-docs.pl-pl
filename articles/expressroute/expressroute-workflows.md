---
title: 'Azure ExpressRoute: przepływy pracy konfiguracji obwodu'
description: Ta strona zawiera przepływy pracy do skonfigurowania obwodu usługi ExpressRoute i połączeń komunikacji równorzędnej
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864370"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Przepływy pracy ExpressRoute inicjowania obsługi administracyjnej obwodów i stany obwodów
Ta strona przeprowadzi Cię przez usługę aprowizacji i routing konfiguracji przepływów pracy na wysokim poziomie.

![przepływ pracy obwodu](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Poniższy rysunek i odpowiednie kroki przedstawiają zadania w celu zapewnienia kompleksowego obwodu ExpressRoute. 

1. Konfigurowanie obwodu usługi ExpressRoute przy użyciu programu PowerShell. Postępuj zgodnie z instrukcjami w [obwodów usługi ExpressRoute z tworzenia](expressroute-howto-circuit-classic.md) artykuł, aby uzyskać więcej informacji.
2. Łączność kolejność od dostawcy usług. Ten proces różni się. Skontaktuj się z dostawcą połączenia, aby uzyskać więcej informacji na temat kolejności łączności.
3. Upewnij się, że obwód zainicjowano pomyślnie, sprawdzając stan za pomocą programu PowerShell inicjowania obsługi obwodu usługi ExpressRoute. 
4. Konfigurowanie domen routingu. Jeśli dostawca połączenia zarządza konfiguracją warstwy 3, skonfiguruje Routing dla obwodu. Jeśli dostawca połączenia oferuje tylko usługi warstwy 2, należy skonfigurować Routing zgodnie z wytycznymi opisanymi na stronach [wymagania dotyczące routingu](expressroute-routing.md) i [konfiguracji routingu](expressroute-howto-routing-classic.md) .
   
   * Włączanie prywatnej komunikacji równorzędnej Azure — Włącz tę komunikację równorzędną można nawiązać połączenie z maszynami wirtualnymi / wdrożonego w ramach sieci wirtualnych usługi w chmurze.

   * Włącz komunikację równorzędną firmy Microsoft — Włącz tę opcję, aby uzyskać dostęp do programu Microsoft Usługi online, takiego jak Office 365. Wszystkie usługi PaaS platformy Azure są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft.
     
     > [!IMPORTANT]
     > Upewnij się, że używasz serwera proxy oddzielne / urządzeniami brzegowymi w celu łączenia z firmą Microsoft w niż ta, można użyć do korzystania z Internetu. Przy użyciu tej samej granicy dla usługi ExpressRoute oraz sieci Internet będzie powodować routingu asymetrycznego i powodować awarie łączności dla sieci.
     > 
     > 
     
     ![przepływy pracy routingu](./media/expressroute-workflows/routing-workflow.png)
5. Łączenie sieci wirtualnych obwodów usługi ExpressRoute — można połączyć sieci wirtualnych na obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami [połączyć sieci wirtualne](expressroute-howto-linkvnet-arm.md) do obwodu. Te sieci wirtualne mogą być w tej samej subskrypcji platformy Azure jako obwód usługi ExpressRoute lub mogą znajdować się w innej subskrypcji.

## <a name="expressroute-circuit-provisioning-states"></a>Obwód usługi ExpressRoute w Stanach aprowizacji
Każdy obwód usługi ExpressRoute ma dwa stany:

* Stan inicjowania obsługi administracyjnej dostawcy usługi
* Stan

Stan reprezentuje stan inicjowania obsługi administracyjnej firmy Microsoft. Ta właściwość jest ustawiony na włączone, po utworzeniu obwodu usługi Expressroute

Stan inicjowania obsługi administracyjnej dostawcy łączności reprezentuje stan po stronie dostawcy połączenia. Może to być *NotProvisioned*, *aprowizacji*, lub *Aprowizowana*. Obwód ExpressRoute musi być w stanie aprowizacji, aby można było skonfigurować komunikację równorzędną.

### <a name="possible-states-of-an-expressroute-circuit"></a>Możliwe stany obwodu usługi ExpressRoute
Ta sekcja zawiera listę możliwych stanów obwodu ExpressRoute.

**W czasie tworzenia**

Obwód ExpressRoute będzie zgłaszał następujące stany podczas tworzenia zasobu.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Gdy trwa inicjowanie obsługi administracyjnej obwodu dostawcy łączności**

Obwód usługi ExpressRoute będzie zgłaszał następujące stany, gdy dostawca łączności pracuje nad udostępnieniem obwodu.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po zakończeniu procesu aprowizacji dostawcy łączności**

Obwód usługi ExpressRoute będzie zgłaszał następujące stany, gdy dostawca połączenia pomyślnie udostępnił obwód.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Jeśli dostawca połączenia anuluje obwodu**

Jeśli obwód usługi ExpressRoute musi zostać anulowany, obwód będzie zgłaszał następujące stany, gdy dostawca usług ukończy proces anulowania aprowizacji.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Można ponownie włączyć, jeśli potrzebne lub uruchamiania poleceń cmdlet programu PowerShell, można usunąć obwodu.  

> [!IMPORTANT]
> Nie można usunąć obwodu, gdy ServiceProviderProvisioningState jest aprowizacji lub aprowizacji. Dostawca łączności wymaga anulowania aprowizacji obwodu, zanim będzie można go usunąć. Firma Microsoft będzie kontynuować rozliczanie obwodu do momentu usunięcia zasobu obwodu usługi ExpressRoute na platformie Azure.
> 

## <a name="routing-session-configuration-state"></a>Stan konfiguracji sesji routingu
Stan inicjowania obsługi protokołu BGP jest raportowany, jeśli sesja BGP została włączona w przeglądarce Microsoft Edge. Stan musi być włączony, aby można było używać komunikacji równorzędnej prywatnej lub firmy Microsoft.

Należy sprawdzić stan sesji protokołu BGP, szczególnie w przypadku komunikacji równorzędnej firmy Microsoft. Oprócz protokołu BGP, stan inicjowania obsługi, jest inny stan, o nazwie *stanu publiczne prefiksy anonsowane*. Stan anonsowanych publicznych prefiksów musi znajdować się w *skonfigurowanym* stanie, zarówno w przypadku sesji protokołu BGP, jak i dla routingu do końca. 

Jeśli ustawiono stan anonsowanego prefiksu publicznych *konieczna weryfikacja* stanu sesji protokołu BGP nie jest włączone, ponieważ prefiksy anonsowane niezgodny numer AS w żadnym z rejestrów routingu. 

> [!IMPORTANT]
> Jeśli anonsowane publiczne prefiksy stanu są w stanie *walidacji ręcznej* , należy otworzyć bilet pomocy technicznej w ramach [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i podać dowód, że adresy IP są anonsowane wraz ze skojarzonym numerem systemu autonomicznego.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)

