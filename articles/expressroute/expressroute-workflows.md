---
title: Przepływy pracy dotyczące konfigurowania obwodu usługi ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona przeprowadzi Cię przez przepływy pracy dotyczące konfigurowania obwodu ExpressRoute i komunikacji równorzędnych
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850777"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodu
Ta strona przeprowadzi Cię przez usługę routing przepływy pracy configuration na wysokim poziomie i inicjowania obsługi administracyjnej.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Poniższa ilustracja i odpowiadające jej kroki Pokaż zadania, które należy wykonać, aby przypisać obwodu usługi ExpressRoute udostępniane na trasie. 

1. Za pomocą programu PowerShell skonfigurować obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami [obwody tworzenia usługi ExpressRoute](expressroute-howto-circuit-classic.md) artykułu, aby uzyskać więcej informacji.
2. Kolejność łączność z dostawcy usług. Ten proces może być różna. Aby uzyskać więcej informacji o sposobie kolejność łączności, skontaktuj się z dostawcą połączenia.
3. Upewnij się, że obwód zainicjowano pomyślnie sprawdzając stan za pomocą programu PowerShell inicjowania obwód usługi expressroute. 
4. Konfigurowanie domen routingu. Jeśli dostawca połączenia zarządza warstwy 3, firma chce skonfigurować routing dla obwodu. Jeśli dostawca połączenia udostępnia tylko usługi warstwy 2, należy skonfigurować routing według wskazówek opisanych w [wymagania dotyczące routingu](expressroute-routing.md) i [konfiguracji routingu](expressroute-howto-routing-classic.md) stron.
   
   * Włącz Azure prywatnej komunikacji równorzędnej — należy włączyć komunikacji równorzędnej można nawiązać połączenia z maszynami wirtualnymi / wdrożone w ramach sieci wirtualnej usługi w chmurze.
   * Włącz Azure publicznej komunikacji równorzędnej — należy włączyć publicznej komunikacji równorzędnej platformy Azure, jeśli chcesz połączyć się z usługami platformy Azure hostowanej na publiczne adresy IP. Jest to wymagane, aby uzyskać dostęp do zasobów platformy Azure, jeśli chcesz włączyć routing domyślny dla platformy Azure prywatnej komunikacji równorzędnej.
   * Włączanie komunikacji równorzędnej firmy Microsoft — należy włączyć to z dostępem do usługi Office 365 i Dynamics 365. 
     
     > [!IMPORTANT]
     > Należy upewnić się, że używasz oddzielnego serwera proxy / krawędzi połączenia z firmą Microsoft w niż można użyć w Internecie. Zarówno ExpressRoute, jak i z Internetu przy użyciu tej samej granicy powoduje asymetrycznego routingu i powodować awarie łączność w sieci.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Łączenie sieci wirtualne obwody usługi ExpressRoute — sieci wirtualne można połączyć z obwodem usługi ExpressRoute. Postępuj zgodnie z instrukcjami [połączyć sieci wirtualnych](expressroute-howto-linkvnet-arm.md) do obwodu. Te sieci wirtualnych może być w tej samej subskrypcji platformy Azure jako obwodu ExpressRoute lub może być w innej subskrypcji.

## <a name="expressroute-circuit-provisioning-states"></a>Inicjowanie obsługi administracyjnej stanów obwodu usługi expressroute
Każdy obwód usługi ExpressRoute ma dwa stany:

* Stan inicjowania obsługi administracyjnej dostawcy usługi
* Stan

Stan reprezentuje stan inicjowania obsługi administracyjnej firmy Microsoft. Ta właściwość jest ustawiony na włączone po utworzeniu obwodu usługi Expressroute

Stan inicjowania obsługi administracyjnej dostawcy łączności reprezentuje stan po stronie dostawcy łączności. Może to być *NotProvisioned*, *inicjowania obsługi administracyjnej*, lub *obsługiwane administracyjnie*. Obwód usługi expressroute musi być w stanie obsługiwane administracyjnie dla Ciebie można było go używać.

### <a name="possible-states-of-an-expressroute-circuit"></a>Możliwe stany obwodu usługi ExpressRoute
Ta sekcja zawiera się możliwe stany obwodu usługi ExpressRoute.

**W czasie tworzenia**

Zobaczysz obwodu usługi expressroute w następującym stanie zaraz po uruchomieniu polecenia cmdlet programu PowerShell, aby utworzyć obwodu usługi expressroute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Gdy dostawca połączenia Trwa inicjowanie obsługi administracyjnej obwodu**

Zobaczysz obwodu usługi expressroute w następującym stanie natychmiast po klucz usługi są przekazywane do dostawcy łączności i uruchomienia procesu inicjowania obsługi administracyjnej.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po zakończeniu procesu udostępniania dostawcy łączności**

Zostanie wyświetlone obwodu usługi expressroute w następującym stanie, jak dostawca połączenia zostało ukończone proces inicjowania obsługi administracyjnej.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Zainicjowano obsługę administracyjną i włączone jest tylko stan obwodu może być w dla Ciebie można było go używać. Jeśli używasz dostawcy warstwy 2, można skonfigurować routing dla obwodu tylko wtedy, gdy znajduje się w tym stanie.

**Gdy dostawca połączenia jest anulowania obsługi obwodu**

Jeśli żądanego dostawcy usług w anulowanie zastrzeżenia obwodu ExpressRoute, zostanie wyświetlony obwodu ustawioną następującym stanie po zakończeniu procesu anulowania obsługi dostawcy usług.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Można ją włączyć ponownie, jeśli potrzebne lub uruchamiania poleceń cmdlet programu PowerShell, można usunąć obwodu.  

> [!IMPORTANT]
> Po uruchomieniu polecenia cmdlet programu PowerShell, aby usunąć obwód podczas inicjowania obsługi administracyjnej ServiceProviderProvisioningState lub obsługiwane administracyjnie operacja zakończy się niepowodzeniem. We współpracy z dostawcą połączenia, aby najpierw anulowanie zastrzeżenia obwodu ExpressRoute, a następnie usuń obwodu. Firma Microsoft będzie naliczać opłaty obwód, dopóki nie zostanie uruchomione polecenie cmdlet programu PowerShell, można usunąć obwodu.
> 
> 

## <a name="routing-session-configuration-state"></a>Stan konfiguracji sesji routingu
BGP stan inicjowania obsługi pozwala sprawdzić, czy włączono sesję protokołu BGP na krawędzi firmy Microsoft. Stan musi być włączona dla Ciebie można było użyć komunikację równorzędną.

Należy sprawdzić stan sesji protokołu BGP, szczególnie w przypadku komunikacji równorzędnej firmy Microsoft. Oprócz BGP stan inicjowania obsługi, istnieje inny stan, o nazwie *stanu publiczne prefiksy anonsowane*. Stan anonsowany prefiksów publicznych musi być w *skonfigurowane* stanu, zarówno na można się sesji protokołu BGP i routingu do pracy na trasie. 

Jeśli ustawiono stan anonsowany prefiks publicznego *weryfikacji potrzebne* stanu sesji BGP nie jest włączone, ponieważ anonsowane prefiksy nie odpowiada numerowi AS w żadnym z rejestrów routingu. 

> [!IMPORTANT]
> Jeśli stan anonsowane prefiksy publiczny jest *weryfikowanie ręczne* stanu, należy otworzyć bilet pomocy technicznej z [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i udowodnić, że posiadanych adresów IP anonsowane wraz z programem skojarzone z nimi numery systemu autonomicznego.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)

