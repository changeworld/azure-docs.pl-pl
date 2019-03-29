---
title: 'Pobierz tabele ARP — Rozwiązywanie problemów — ExpressRoute: Azure| Microsoft Docs'
description: Ta strona zawiera instrukcje dotyczące pobieranie tabel ARP dla obwodu usługi ExpressRoute
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 76e242adb07f4e6176bbdc6c03c75950e3732c2b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622080"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Pobieranie tabel ARP w modelu wdrażania usługi Resource Manager
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule przedstawiono kroki, aby dowiedzieć się, tabele ARP dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Ten dokument ma ułatwić diagnozowanie i rozwiązywanie problemów z prostego. Nie ma być zastępczy pomocy technicznej firmy Microsoft. Należy otworzyć bilet pomocy technicznej za pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nie możesz rozwiązać problem, korzystając ze wskazówek opisanych poniżej.
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adres tabel Resolution Protocol (ARP) i protokołu ARP
Protokół ARP (Address Resolution) to protokół warstwy 2 zdefiniowane w [RFC 826](https://tools.ietf.org/html/rfc826). ARP jest używany do mapowania adres ethernetowy (adres MAC) z adresem ip.

Tabeli protokołu ARP zapewnia mapowanie adresu ipv4 i adres MAC dla konkretnego komunikacji równorzędnej. Tabeli protokołu ARP dla obwodu usługi ExpressRoute komunikacji równorzędnej zawiera następujące informacje dla każdego interfejsu (podstawowe i pomocnicze)

1. Mapowanie adresu ip interfejsu routera lokalnego adres MAC
2. Mapowanie adresu ip interfejsu routera ExpressRoute adres MAC
3. Wiek mapowania

Tabele ARP może pomóc sprawdzić poprawność konfiguracji warstwy 2 i rozwiązywanie problemów z podstawowych warstwy 2 problemy z łącznością. 

Tabeli protokołu ARP przykład: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Poniższa sekcja zawiera informacje dotyczące sposobu wyświetlania tabele ARP widzianych przez routery graniczne usługi ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Wymagania wstępne dotyczące uczenia tabele ARP
Upewnij się, że następujące przed postępu w dalszych

* Nieprawidłowa obwód usługi ExpressRoute skonfigurowane z co najmniej jeden element równorzędny. Obwód musi być w pełni skonfigurowane przez dostawcę połączenia. Użytkownik (lub dostawca połączenia) należy skonfigurować co najmniej jeden z komunikacji równorzędnej (Azure prywatnej i publicznej Azure i Microsoft) w tym obwodzie.
* Zakresy adresów IP używane podczas konfigurowania komunikacji równorzędnej (Azure prywatnej i publicznej Azure i Microsoft). Przejrzyj przykłady przypisania adresów ip w [na stronie wymagania dotyczące routingu usługi ExpressRoute](expressroute-routing.md) ułatwią zrozumienie sposobu adresy ip są mapowane do interfejsów ze strony i po stronie usługi ExpressRoute. Informacje o konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [strony konfiguracji z komunikacji równorzędnej usługi ExpressRoute](expressroute-howto-routing-arm.md).
* Informacje od zespołu sieciowych / dostawcy łączności adresów MAC interfejsów używane z tych adresów IP.
* Konieczne jest posiadanie najnowszy moduł programu PowerShell dla platformy Azure (wersja 1,50 lub nowsza).

> [!NOTE]
> Jeśli warstwa 3 są dostarczane przez dostawcę usług i tabele ARP są puste w portalu/danych wyjściowych poniżej, należy odświeżyć konfiguracji obwodu, używając przycisku Odśwież w portalu. Ta operacja wejdzie odpowiednią konfigurację routingu w obwodu. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Pobieranie tabel ARP dla obwodu usługi ExpressRoute
Ta sekcja zawiera instrukcje dotyczące sposobu wyświetlania tabele ARP na komunikację równorzędną przy użyciu programu PowerShell. Użytkownik lub dostawcą połączenia, należy skonfigurować komunikację równorzędną przed dalsze postępu. Każdy obwód ma dwie ścieżki (podstawowych i pomocniczych). Możesz sprawdzić tabeli protokołu ARP w przypadku poszczególnych ścieżek niezależnie.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla prywatnej komunikacji równorzędnej Azure
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla prywatnej komunikacji równorzędnej Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Przykładowe dane wyjściowe poniżej pokazano dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla publicznej komunikacji równorzędnej Azure
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla publicznej komunikacji równorzędnej Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Przykładowe dane wyjściowe poniżej pokazano dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabele ARP dla komunikacji równorzędnej firmy Microsoft
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla komunikacji równorzędnej firmy Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Przykładowe dane wyjściowe poniżej pokazano dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak używać tych informacji
Tabeli protokołu ARP komunikacji równorzędnej może służyć do określenia sprawdzania poprawności warstwy 2, konfiguracją i łącznością. Ta sekcja zawiera omówienie wygląd tabel ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabeli protokołu ARP, gdy obwód jest w stan operacyjny (oczekiwany stan)
* Tabeli protokołu ARP ma wpis dla strony w środowisku lokalnym przy użyciu prawidłowego adresu IP i adres MAC i podobne wpis po stronie firmy Microsoft. 
* Ostatni oktet adres ip lokalnego zawsze jest liczbą nieparzystą.
* Ostatni oktet adresu ip firmy Microsoft będą zawsze liczbą parzystą.
* Ten sam adres MAC pojawi się po stronie firmy Microsoft dla wszystkich 3 komunikacji równorzędnej (podstawowy / pomocniczy). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabeli protokołu ARP, gdy lokalne / po stronie dostawcy łączności występują problemy
Jeśli występują problemy z lokalną lub dostawcy łączności, które mogą być widoczne, albo tylko jeden wpis pojawi się w tabeli protokołu ARP lub adres MAC na lokalnym pokaże niekompletne. Spowoduje to wyświetlenie mapowanie między adresu MAC i adres IP używany po stronie firmy Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

lub
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Otwórz żądanie obsługi z dostawcą połączenia, aby debugować takich problemów. Jeśli tabeli protokołu ARP nie ma adresy IP interfejsów mapowane na adresy MAC, przejrzyj następujące informacje:
> 
> 1. Jeśli pierwszy adres IP/30 podsieci przypisane łącze między MSEE (PR) i rozwiązania MSEE jest używany w interfejsie żądania ściągnięcia MSEE Platforma Azure zawsze używa drugiego adresu IP dla Msee.
> 2. Upewnij się, jeśli klienta (C-Tag) i znaczników sieci VLAN usługi (S-Tag) odpowiadają na parę MSEE (PR) i rozwiązania MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabeli protokołu ARP w przypadku problemów po stronie firmy Microsoft
* Nie będą widzieć tabeli protokołu ARP wyświetlane dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft. 
* Otwórz bilet pomocy technicznej za pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, czy masz problem z łącznością warstwy 2. 

## <a name="next-steps"></a>Następne kroki
* Sprawdź poprawność konfiguracji warstwy 3 dla obwodu usługi ExpressRoute
  * Pobierz podsumowanie, aby określić stan sesji protokołu BGP trasy 
  * Pobierz tabelę tras w celu ustalenia, które prefiksy są anonsowane przez usługi ExpressRoute
* Zweryfikuj transfer danych, przeglądając bajty We / Wy
* Otwórz bilet pomocy technicznej za pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nadal występują problemy.

