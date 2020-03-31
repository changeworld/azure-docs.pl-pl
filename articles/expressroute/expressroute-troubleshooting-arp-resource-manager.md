---
title: 'Azure ExpressRoute: tabele ARP — rozwiązywanie problemów'
description: Ta strona zawiera instrukcje dotyczące uzyskiwania tabel ARP dla obwodu usługi ExpressRoute
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 4f1bd064dbc0909be3deba9180be1d8b3c066fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076580"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Uzyskiwanie tabel ARP w modelu wdrażania Menedżera zasobów
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule otrzymasz od kroków, aby poznać tabele ARP dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Ten dokument ma na celu pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest przeznaczony do zastąpienia pomocy technicznej firmy Microsoft. Jeśli nie można rozwiązać problemu, należy otworzyć bilet pomocy technicznej z [pomocą techniczną firmy Microsoft,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) korzystając ze wskazówek opisanych poniżej.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabele ARP (Address Resolution Protocol) i ARP
Address Resolution Protocol (ARP) to protokół warstwy 2 zdefiniowany w [RFC 826](https://tools.ietf.org/html/rfc826). ARP służy do mapowania adresu Ethernet (adres MAC) z adresem IP.

Tabela ARP zawiera mapowanie adresu ipv4 i adresu MAC dla określonej komunikacji równorzędnej. Tabela ARP dla komunikacji równorzędnej obwodu usługi ExpressRoute zawiera następujące informacje dla każdego interfejsu (podstawowego i pomocniczego)

1. Mapowanie adresu IP interfejsu routera lokalnego na adres MAC
2. Mapowanie adresu IP interfejsu routera Usługi ExpressRoute na adres MAC
3. Wiek mapowania

Tabele ARP mogą pomóc w weryfikacji konfiguracji warstwy 2 i rozwiązywaniu podstawowych problemów z łącznością warstwy 2. 

Przykładowa tabela ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Poniższa sekcja zawiera informacje na temat sposobu wyświetlania tabel ARP widzianych przez routery brzegowe usługi ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Warunki wstępne do nauki tabel ARP
Przed dalszym postępem upewnij się, że masz następujące

* Prawidłowy obwód usługi ExpressRoute skonfigurowany z co najmniej jednym elementem równorzędnym. Obwód musi być w pełni skonfigurowany przez dostawcę łączności. Użytkownik (lub twój dostawca łączności) musi skonfigurować co najmniej jedną z komunikacji równorzędnej (prywatna platforma Azure, platforma azure public i microsoft) w tym obwodzie.
* Zakresy adresów IP używane do konfigurowania komunikacji równorzędnej (prywatna platforma Azure, platforma Azure public i microsoft). Przejrzyj przykłady przypisywania adresów IP na [stronie Wymagania dotyczące routingu usługi ExpressRoute,](expressroute-routing.md) aby dowiedzieć się, jak adresy IP są mapowane do interfejsów po twojej stronie i po stronie usługi ExpressRoute. Informacje na temat konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [stronę konfiguracji komunikacji równorzędnej usługi ExpressRoute](expressroute-howto-routing-arm.md).
* Informacje od zespołu sieciowego / dostawcy łączności na adresach MAC interfejsów używanych z tymi adresami IP.
* Musisz mieć najnowszy moduł programu PowerShell dla platformy Azure (wersja 1.50 lub nowsza).

> [!NOTE]
> Jeśli warstwa 3 jest dostarczana przez usługodawcę, a tabele ARP są puste w portalu/danych wyjściowych poniżej, odśwież konfigurację obwodu za pomocą przycisku odświeżania w portalu. Ta operacja spowoduje zastosowanie odpowiedniej konfiguracji routingu w obwodzie. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Uzyskiwanie tabel ARP dla obwodu usługi ExpressRoute
W tej sekcji przedstawiono instrukcje dotyczące sposobu wyświetlania tabel ARP na komunikację równorzędną przy użyciu programu PowerShell. Użytkownik lub dostawca łączności musi skonfigurować komunikację równorzędnej przed postępem. Każdy obwód ma dwie ścieżki (podstawowej i pomocniczej). Tabelę ARP można sprawdzić niezależnie dla każdej ścieżki.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla prywatnej komunikacji równorzędnej platformy Azure
Następujące polecenie cmdlet udostępnia tabele ARP dla prywatnej komunikacji równorzędnej platformy Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Przykładowy wynik jest pokazany poniżej dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla publicznej komunikacji równorzędnej platformy Azure
Następujące polecenie cmdlet udostępnia tabele ARP dla publicznej komunikacji równorzędnej platformy Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Przykładowy wynik jest pokazany poniżej dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabele ARP dla komunikacji równorzędnej firmy Microsoft
Następujące polecenie cmdlet zawiera tabele ARP dla komunikacji równorzędnej firmy Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Przykładowy wynik jest pokazany poniżej dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak wykorzystać te informacje
Tabela ARP komunikacji równorzędnej może służyć do określenia sprawdzania poprawności konfiguracji warstwy 2 i łączności. W tej sekcji przedstawiono omówienie wyglądu tabel ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela ARP, gdy obwód jest w stanie operacyjnym (stan oczekiwany)
* Tabela ARP będzie miała wpis po stronie lokalnej z prawidłowym adresem IP i adresem MAC oraz podobnym wpisem po stronie firmy Microsoft. 
* Ostatni oktet lokalnego adresu IP będzie zawsze numerem nieparzystym.
* Ostatni oktet adresu IP firmy Microsoft zawsze będzie liczbą parzysty.
* Ten sam adres MAC pojawi się po stronie firmy Microsoft dla wszystkich 3 komunikacji równorzędnej (podstawowy / pomocniczy). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela ARP, gdy po stronie lokalnego / dostawcy łączności występują problemy
Jeśli występują problemy z lokalnym lub dostawcą łączności, może się okazać, że tylko jeden wpis pojawi się w tabeli ARP lub lokalny adres MAC będzie wyświetlany jako niekompletny. Spowoduje to wyświetle mapowanie między adresem MAC a adresem IP używanym po stronie firmy Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

lub
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Otwórz żądanie pomocy technicznej z dostawcą łączności, aby debugować takie problemy. Jeśli tabela ARP nie ma adresów IP interfejsów mapowanych na adresy MAC, zapoznaj się z następującymi informacjami:
> 
> 1. Jeśli pierwszy adres IP podsieci /30 przypisany do łącza między MSEE-PR i MSEE jest używany w interfejsie MSEE-PR. Platforma Azure zawsze używa drugiego adresu IP dla msee.
> 2. Sprawdź, czy tagi VLAN klienta (C-Tag) i usługi (S-Tag) są zgodne zarówno na parze MSEE-PR, jak i MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela ARP, gdy po stronie firmy Microsoft występują problemy
* Nie będzie widoczna tabela ARP wyświetlana dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft. 
* Otwórz bilet pomocy technicznej z [pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, że występuje problem z łącznością warstwy 2. 

## <a name="next-steps"></a>Następne kroki
* Sprawdzanie poprawności konfiguracji warstwy 3 dla obwodu usługi ExpressRoute
  * Pobierz podsumowanie trasy w celu określenia stanu sesji BGP 
  * Pobierz tabelę tras, aby określić, które prefiksy są anonsowane w uliczce usługi ExpressRoute
* Sprawdzanie poprawności transferu danych przez przeglądanie bajtów w / na zewnątrz
* Otwórz bilet pomocy technicznej z [pomocą techniczną firmy Microsoft,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) jeśli nadal występują problemy.

