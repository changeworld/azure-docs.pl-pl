---
title: 'Azure ExpressRoute: przepływy pracy konfiguracji obwodu'
description: Ta strona zawiera przepływy pracy do skonfigurowania obwodu usługi ExpressRoute i połączeń komunikacji równorzędnej
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: ae6c2b7257ee6a8184f3a5bb002f24cb75a86d67
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083321"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodu
Ta strona przeprowadzi Cię przez usługę aprowizacji i routing konfiguracji przepływów pracy na wysokim poziomie.

![przepływ pracy obwodu](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Poniższa ilustracja i odpowiadające jej kroki pokazują zadania, które należy wykonać, aby mogła mieć obwodu usługi ExpressRoute zainicjowanego end-to-end. 

1. Konfigurowanie obwodu usługi ExpressRoute przy użyciu programu PowerShell. Postępuj zgodnie z instrukcjami w [obwodów usługi ExpressRoute z tworzenia](expressroute-howto-circuit-classic.md) artykuł, aby uzyskać więcej informacji.
2. Łączność kolejność od dostawcy usług. Ten proces różni się. Skontaktuj się z dostawcą połączenia, aby uzyskać więcej informacji na temat kolejności łączności.
3. Upewnij się, że obwód zainicjowano pomyślnie, sprawdzając stan za pomocą programu PowerShell inicjowania obsługi obwodu usługi ExpressRoute. 
4. Konfigurowanie domen routingu. Jeśli dostawca połączenia zarządza warstwy 3, firma chce skonfigurować routing dla obwodu. Jeśli dostawca połączenia oferuje tylko usługi warstwy 2, należy skonfigurować routing na wytycznych określonych w [wymagania dotyczące routingu](expressroute-routing.md) i [konfiguracji routingu](expressroute-howto-routing-classic.md) stron.
   
   * Włączanie prywatnej komunikacji równorzędnej Azure — Włącz tę komunikację równorzędną można nawiązać połączenie z maszynami wirtualnymi / wdrożonego w ramach sieci wirtualnych usługi w chmurze.

   * Włącz komunikację równorzędną firmy Microsoft — Włącz tę opcję, aby uzyskać dostęp do pakietu Office 365. Ponadto wszystkie usługi PaaS platformy Azure są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft.
     
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

Stan inicjowania obsługi administracyjnej dostawcy łączności reprezentuje stan po stronie dostawcy połączenia. Może to być *NotProvisioned*, *aprowizacji*, lub *Aprowizowana*. Obwód usługi ExpressRoute musi być w stanie Aprowizowana, aby można było go używać.

### <a name="possible-states-of-an-expressroute-circuit"></a>Możliwe stany obwodu usługi ExpressRoute
W tej sekcji przedstawiono się możliwe stany dla obwodu usługi ExpressRoute.

**W czasie tworzenia**

Obwód usługi ExpressRoute w następującym stanie zostaną wyświetlone, zaraz po uruchomieniu polecenia cmdlet programu PowerShell, aby utworzyć obwód usługi ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Gdy trwa inicjowanie obsługi administracyjnej obwodu dostawcy łączności**

Obwód usługi ExpressRoute w następującym stanie zostanie wyświetlony natychmiast przekazać klucz usługi do dostawcy łączności i uruchomienia procesu aprowizacji.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po zakończeniu procesu aprowizacji dostawcy łączności**

Obwód usługi ExpressRoute w następującym stanie zostaną wyświetlone tak szybko, jak dostawca połączenia zostało zakończone, proces inicjowania obsługi.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Elastycznie i włączone jest tylko stan obwodu mogą znajdować się w dla Ciebie można było go używać. Jeśli używasz dostawcy warstwy 2, można skonfigurować routing dla obwodu tylko wtedy, gdy znajduje się w tym stanie.

**Jeśli dostawca połączenia anuluje obwodu**

Jeśli zażądano dostawcy usług, aby anulować aprowizację obwodu usługi ExpressRoute, zobaczysz obwodu, ustaw następujący stan po zakończeniu dostawca usług ma proces anulowania aprowizacji.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Można ponownie włączyć, jeśli potrzebne lub uruchamiania poleceń cmdlet programu PowerShell, można usunąć obwodu.  

> [!IMPORTANT]
> Po uruchomieniu polecenia cmdlet programu PowerShell, aby usunąć obwód podczas inicjowania obsługi administracyjnej ServiceProviderProvisioningState lub Aprowizowana, operacja zakończy się niepowodzeniem. Skontaktuj się z dostawcą połączenia, aby najpierw anulować aprowizację obwodu usługi ExpressRoute, a następnie usuń obwód. Firma Microsoft będzie obciążana płatnościami za obwodu, dopóki nie zostanie uruchomione polecenie cmdlet programu PowerShell, można usunąć obwodu.
> 
> 

## <a name="routing-session-configuration-state"></a>Stan konfiguracji sesji routingu
Stan inicjowania obsługi protokołu BGP informuje o tym, gdy sesja protokołu BGP został włączony w przeglądarce Microsoft edge. Stan musi być włączona dla Ciebie można było używać komunikacji równorzędnej.

Należy sprawdzić stan sesji protokołu BGP, szczególnie w przypadku komunikacji równorzędnej firmy Microsoft. Oprócz protokołu BGP, stan inicjowania obsługi, jest inny stan, o nazwie *stanu publiczne prefiksy anonsowane*. Stan anonsowane prefiksy publiczne musi znajdować się w *skonfigurowane* stanu, zarówno dla sesji protokołu BGP rozpocząć i routing do pracy end-to-end. 

Jeśli ustawiono stan anonsowanego prefiksu publicznych *konieczna weryfikacja* stanu sesji protokołu BGP nie jest włączone, ponieważ prefiksy anonsowane niezgodny numer AS w żadnym z rejestrów routingu. 

> [!IMPORTANT]
> Jeśli stan anonsowane prefiksy publiczne *ręcznej weryfikacji* stanu, należy otworzyć bilet pomocy technicznej za pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i przedstawić dowody, że jesteś właścicielem, adresy IP anonsowane wraz z programem skojarzone z nimi numery systemu autonomicznego.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)

