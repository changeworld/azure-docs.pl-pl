---
title: 'Azure ExpressRoute: przepływy pracy konfiguracji obwodu'
description: Na tej stronie przedstawiono przepływy pracy dotyczące konfigurowania obwodu i komunikacji równorzędnej usługi ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864370"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodu
Ta strona przeprowadzi Cię przez przepływy pracy obsługi administracyjnej i routingu routingu na wysokim poziomie.

![przepływ pracy obwodu](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Poniższy rysunek i odpowiednie kroki przedstawiają zadania do aprowizowania obwodu usługi ExpressRoute end-to-end. 

1. Użyj programu PowerShell, aby skonfigurować obwód usługi ExpressRoute. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami w artykule [Tworzenie obwodów usługi ExpressRoute.](expressroute-howto-circuit-classic.md)
2. Zamów łączność od usługodawcy. Proces ten jest różny. Skontaktuj się z dostawcą łączności, aby uzyskać więcej informacji na temat zamawiania łączności.
3. Upewnij się, że obwód został pomyślnie zainicjowany, weryfikując stan inicjowania obsługi administracyjnej obwodu usługi ExpressRoute za pośrednictwem programu PowerShell. 
4. Konfigurowanie domen routingu. Jeśli dostawca łączności zarządza konfiguracją warstwy 3, skonfiguruje routing dla obwodu. Jeśli dostawca łączności oferuje tylko usługi warstwy 2, należy skonfigurować routing zgodnie z wytycznymi opisanymi na stronach [konfiguracji routingu](expressroute-routing.md) i [routingu.](expressroute-howto-routing-classic.md)
   
   * Włącz prywatną komunikację równorzędną platformy Azure — włącz tę komunikację równorzędną, aby połączyć się z maszynami wirtualnymi/ usługami w chmurze wdrożonymi w sieciach wirtualnych.

   * Włącz komunikację równorzędną firmy Microsoft — włącz dostęp do usług online firmy Microsoft, takich jak Office 365. Wszystkie usługi PaaS platformy Azure są dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft.
     
     > [!IMPORTANT]
     > Należy upewnić się, że używasz oddzielnego serwera proxy / krawędzi, aby połączyć się z firmą Microsoft niż ten, którego używasz w Internecie. Użycie tej samej krawędzi zarówno dla usługi ExpressRoute, jak i Internetu spowoduje asymetryczne routingu i spowoduje przerwy w łączności dla sieci.
     > 
     > 
     
     ![przepływy pracy routingu](./media/expressroute-workflows/routing-workflow.png)
5. Łączenie sieci wirtualnych z obwodami usługi ExpressRoute — sieci wirtualne można połączyć z obwodem usługi ExpressRoute. Postępuj zgodnie z [instrukcjami, aby połączyć sieci wirtualne](expressroute-howto-linkvnet-arm.md) z obwodem. Te sieci wirtualne mogą być w tej samej subskrypcji platformy Azure co obwód usługi ExpressRoute lub mogą być w innej subskrypcji.

## <a name="expressroute-circuit-provisioning-states"></a>Stany inicjowania obsługi administracyjnej obwodów usługi ExpressRoute
Każdy obwód usługi ExpressRoute ma dwa stany:

* Stan inicjowania obsługi administracyjnej dostawcy usług
* Stan

Stan reprezentuje stan inicjowania obsługi administracyjnej firmy Microsoft. Ta właściwość jest ustawiona na Włączone podczas tworzenia obwodu usługi Expressroute

Stan inicjowania obsługi administracyjnej dostawcy łączności reprezentuje stan po stronie dostawcy łączności. Może to być *notProvisioned*, *Inicjowanie obsługi administracyjnej*lub *aprowizowana*. Obwód usługi ExpressRoute musi być w stanie aprowizowanym, aby skonfigurować komunikację równorzędną.

### <a name="possible-states-of-an-expressroute-circuit"></a>Możliwe stany obwodu usługi ExpressRoute
W tej sekcji wymieniono możliwe stany obwodu usługi ExpressRoute.

**W czasie tworzenia**

Obwód usługi ExpressRoute będzie raportowany następujące stany podczas tworzenia zasobów.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Gdy dostawca łączności jest w trakcie inicjowania obsługi administracyjnej obwodu**

Obwód usługi ExpressRoute zgłosi następujące stany, podczas gdy dostawca łączności pracuje nad aprowizacją obwodu.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po zakończeniu procesu inicjowania obsługi administracyjnej przez dostawcę łączności**

Obwód usługi ExpressRoute zgłosi następujące stany, gdy dostawca łączności pomyślnie zaaprowizował obwód.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Gdy dostawca łączności usuwa rozdziałanie obwodu**

Jeśli obwód usługi ExpressRoute musi zostać wyrejestrowany, obwód zgłosi następujące stany po zakończeniu procesu anulowania obsługi administracyjnej przez dostawcę usług.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


W razie potrzeby można ponownie włączyć go lub uruchomić polecenia cmdlet programu PowerShell, aby usunąć obwód.  

> [!IMPORTANT]
> Nie można usunąć obwodu, gdy ServiceProviderProvisioningState jest inicjowanie obsługi administracyjnej lub aprowizowana. Dostawca łączności musi anulować aprowizję obwodu, zanim będzie można go usunąć. Firma Microsoft będzie nadal rozliczać obwód, dopóki zasób obwodu usługi ExpressRoute nie zostanie usunięty na platformie Azure.
> 

## <a name="routing-session-configuration-state"></a>Stan konfiguracji sesji routingu
Stan inicjowania obsługi administracyjnej protokołu BGP raportuje, czy sesja BGP została włączona na krawędzi firmy Microsoft. Stan musi być włączony do korzystania z prywatnej lub microsoft komunikacji równorzędnej.

Ważne jest, aby sprawdzić stan sesji BGP szczególnie dla komunikacji równorzędnej firmy Microsoft. Oprócz stanu inicjowania obsługi administracyjnej BGP istnieje inny stan o nazwie *anonsowane prefiksy publiczne stanu*. Anonsowane prefiksy publiczne stan musi być w stanie *skonfigurowanym,* zarówno dla sesji BGP, aby być w górę i routingu do pracy end-to-end. 

Jeśli stan anonsowanego prefiksu publicznego jest ustawiony na stan *sprawdzania poprawności potrzebny,* sesja BGP nie jest włączona, ponieważ anonsowane prefiksy nie pasują do numeru AS w żadnym z rejestrów routingu. 

> [!IMPORTANT]
> Jeśli stan anonsowanych prefiksów publicznych jest w stanie *ręcznej weryfikacji,* należy otworzyć bilet pomocy technicznej z [pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i dostarczyć dowodów, że jesteś właścicielem adresów IP anonsowanych wraz z numerem skojarzonego systemu autonomicznego.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)

