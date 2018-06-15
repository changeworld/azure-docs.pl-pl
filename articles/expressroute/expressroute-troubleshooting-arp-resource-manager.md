---
title: 'Pobieranie ARP tabel: Menedżer zasobów: Azure ExpressRoute Rozwiązywanie problemów z | Dokumentacja firmy Microsoft'
description: Ta strona zawiera instrukcje dotyczące pobierania protokołu ARP tabel dla obwodu usługi ExpressRoute
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850784"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Pobieranie ARP tabele w modelu wdrażania usługi Resource Manager
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule przedstawiono kroki, aby dowiedzieć się więcej tabel ARP dla obwodu usługi ExpressRoute. 

> [!IMPORTANT]
> Ten dokument jest przeznaczony ułatwiające diagnozowanie i rozwiązywanie problemów proste. Nie ma być zastępczy pomocy technicznej firmy Microsoft. Należy otworzyć bilet pomocy technicznej z [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nie możesz rozwiązać problemu, przy użyciu wskazówek opisanych poniżej.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabele Resolution Protocol (ARP) i protokołu ARP adresów
Protokół ARP (Address Resolution Protocol) to protokół warstwy 2 zdefiniowane w [RFC 826](https://tools.ietf.org/html/rfc826). ARP są używane do mapowania adres Ethernet (adresu MAC) z adresem ip.

W tabeli protokołu ARP udostępnia mapowanie adresu ipv4 i adres MAC dla konkretnego komunikacji równorzędnej. W tabeli protokołu ARP dla obwodu usługi ExpressRoute komunikacji równorzędnej zawiera następujące informacje dla każdego interfejsu (podstawowych i pomocniczych)

1. Mapowanie adresu ip interfejsu lokalnego routera z adresem MAC
2. Mapowanie adresu ip interfejsu routera ExpressRoute adres MAC
3. Wiek mapowania

Tabele ARP może pomóc sprawdzić poprawność konfiguracji warstwy 2 i rozwiązywanie problemów z podstawowych warstwy 2 problemy z połączeniem. 

Przykład tabeli protokołu ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Poniższa sekcja zawiera informacje dotyczące sposobu wyświetlania tabele ARP widziana przez routery brzegowe ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Wymagania wstępne dotyczące uczenia tabele ARP
Sprawdź, czy mają następujące przed postęp w dalszych

* Nieprawidłowa obwodu usługi expressroute skonfigurowana z co najmniej jeden komunikacji równorzędnej. Obwód musi być w pełni skonfigurowane przez dostawcę łączności. Użytkownik (lub dostawcą połączenia) należy skonfigurować co najmniej jeden z komunikacji równorzędnych (Azure publicznego prywatne, Azure i firmy Microsoft) w tym obwodzie.
* Zakresów adresów IP używanych do konfigurowania komunikacji równorzędnych (Azure publicznego prywatne, Azure i firmy Microsoft). Przejrzyj przykłady przypisania adresów ip w [strony wymagania routingu usługi ExpressRoute](expressroute-routing.md) do zawierają opis sposobu adresy ip są mapowane na interfejsy użytkownika po stronie i po stronie usługi ExpressRoute. Można uzyskać informacji o konfiguracji komunikacji równorzędnej, przeglądając [strony konfiguracji komunikacji równorzędnej ExpressRoute](expressroute-howto-routing-arm.md).
* Informacje z zespołem sieci / dostawca połączenia na adresach MAC interfejsy używane z tych adresów IP.
* Musi mieć najnowsze modułu programu PowerShell dla platformy Azure (wersja 1,50 lub nowsza).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Pobieranie protokołu ARP tabel dla obwodu usługi ExpressRoute
Ta sekcja zawiera instrukcje na sposób wyświetlania tabel ARP na komunikacji równorzędnej przy użyciu programu PowerShell. Użytkownik lub dostawcą połączenia należy skonfigurować komunikację równorzędną przed osiągnięciem dodatkowe. Każdy obwód ma dwie ścieżki (podstawowych i pomocniczych). Można sprawdzić w tabeli protokołu ARP dla każdej ścieżki niezależnie.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla platformy Azure prywatnej komunikacji równorzędnej
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla platformy Azure prywatnej komunikacji równorzędnej

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Przykładowe dane wyjściowe są wyświetlane poniżej dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla platformy Azure publicznej komunikacji równorzędnej
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla platformy Azure publicznej komunikacji równorzędnej

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Przykładowe dane wyjściowe są wyświetlane poniżej dla jednej ze ścieżek

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
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Przykładowe dane wyjściowe są wyświetlane poniżej dla jednej ze ścieżek

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak używać tych informacji
W tabeli protokołu ARP komunikacji równorzędnej może służyć do określenia Sprawdź poprawność konfiguracji warstwy 2 i połączenia. Ta sekcja zawiera omówienie wygląd tabele ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabeli protokołu ARP, kiedy obwód jest w stan operacyjny (oczekiwany stan)
* W tabeli protokołu ARP będzie mieć wpis dla strony lokalnymi z prawidłowym adresem IP i adres MAC i podobne objęcia po stronie firmy Microsoft. 
* Ostatni oktet lokalny adres ip zawsze jest liczbą nieparzystą.
* Ostatni oktet adresu ip firmy Microsoft jest zawsze liczbą parzystą.
* Ten sam adres MAC pojawią się po stronie firmy Microsoft dla wszystkich 3 komunikacji równorzędnych (podstawowy / dodatkowej). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabeli protokołu ARP, kiedy lokalnymi / po stronie dostawcy połączenie, ma problemy
Jeśli występują problemy z lokalnymi lub dostawca połączenia, które mogą być widoczne czy albo tylko jeden wpis pojawią się w tabeli protokołu ARP lub adres MAC lokalnego będą wyświetlane niekompletne. Wyświetli mapowanie między adres MAC i adres IP używany po stronie firmy Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

lub
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Otwórz żądanie obsługi z dostawcą połączenia debugowanie takich problemów. Jeśli w tabeli protokołu ARP nie mają adresy IP interfejsów mapowane na adresy MAC, przejrzyj następujące informacje:
> 
> 1. Jeśli adres IP pierwszego /30 podsieci dla skojarzenia między MSEE PR i MSEE jest używany w interfejsie MSEE PR. Azure zawsze używa jako drugiego adresu IP dla MSEEs.
> 2. Upewnij się, jeśli klienta (C-znacznik) oraz znaczników sieci VLAN usługi (S-Tag) zgodne na pary MSEE PR i MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabeli protokołu ARP problemów po stronie firmy Microsoft
* Nie będą widzieć tabeli protokołu ARP wyświetlany dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft. 
* Otwórz bilet pomocy technicznej z [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, czy masz problem z łącznością warstwy 2. 

## <a name="next-steps"></a>Następne kroki
* Sprawdź poprawność konfiguracji warstwy 3 dla obwodu usługi ExpressRoute
  * Uzyskać podsumowanie do ustalenia stanu sesji BGP trasy 
  * Pobierz tabelę tras, aby określić prefiksy, które są rozgłaszane między ExpressRoute
* Zweryfikuj transferu danych, przeglądając bajtów we / wy
* Otwórz bilet pomocy technicznej z [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nadal występują problemy.

