---
title: 'Azure ExpressRoute: tabele ARP — rozwiązywanie problemów: klasyczne'
description: Ta strona zawiera instrukcje dotyczące uzyskiwania tabel ARP dla obwodu usługi ExpressRoute — klasyczny model wdrażania.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: charwen
ms.openlocfilehash: a7a24fc6674adca21e01d2502263c9767510469e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618625"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Uzyskiwanie tabel ARP w klasycznym modelu wdrażania
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule oszukuje się, jak uzyskać tabele protokołu ARP (Address Resolution Protocol) dla obwodu usługi Azure ExpressRoute.

> [!IMPORTANT]
> Ten dokument ma na celu pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest przeznaczony do zastąpienia pomocy technicznej firmy Microsoft. Jeśli nie możesz rozwiązać problemu, korzystając z następujących wskazówek, otwórz żądanie pomocy technicznej za pomocą [pomocy technicznej platformy Microsoft Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabele ARP (Address Resolution Protocol) i ARP
ARP to protokół warstwy 2 zdefiniowany w [RFC 826](https://tools.ietf.org/html/rfc826). ARP służy do mapowania adresu Ethernet (adres MAC) na adres IP.

Tabela ARP zawiera mapowanie adresu IPv4 i adresu MAC dla określonej komunikacji równorzędnej. Tabela ARP dla komunikacji równorzędnej obwodu usługi ExpressRoute zawiera następujące informacje dla każdego interfejsu (podstawowego i pomocniczego):

1. Mapowanie adresu IP interfejsu routera lokalnego na adres MAC
2. Mapowanie adresu IP interfejsu routera usługi ExpressRoute na adres MAC
3. Wiek mapowania

Tabele ARP mogą pomóc w sprawdzaniu poprawności konfiguracji warstwy 2 i rozwiązywaniu podstawowych problemów z łącznością warstwy 2.

Oto przykład tabeli ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Poniższa sekcja zawiera informacje dotyczące sposobu wyświetlania tabel ARP, które są widoczne dla routerów brzegowych usługi ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Wymagania wstępne dotyczące korzystania z tabel ARP
Przed kontynuowaniem upewnij się, że masz następujące elementy:

* Prawidłowy obwód usługi ExpressRoute, który jest skonfigurowany z co najmniej jednym elementem równorzędnym. Obwód musi być w pełni skonfigurowany przez dostawcę łączności. Użytkownik (lub dostawca łączności) musi skonfigurować co najmniej jedną z komunikacji równorzędnej (prywatna platforma Azure, platforma azure public lub microsoft) w tym obwodzie.
* Zakresy adresów IP, które są używane do konfigurowania komunikacji równorzędnej (azure private, Azure public, i Microsoft). Przejrzyj przykłady przypisywania adresów IP na [stronie Wymagania dotyczące routingu usługi ExpressRoute,](expressroute-routing.md) aby dowiedzieć się, jak adresy IP są mapowane do interfejsów po twojej stronie i po stronie usługi ExpressRoute. Informacje o konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [stronę konfiguracji komunikacji równorzędnej usługi ExpressRoute](expressroute-howto-routing-classic.md).
* Informacje od zespołu sieciowego lub dostawcy łączności o adresach MAC interfejsów używanych z tymi adresami IP.
* Najnowszy moduł programu Windows PowerShell dla platformy Azure (wersja 1.50 lub nowsza).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabele ARP dla obwodu usługi ExpressRoute
W tej sekcji przedstawiono instrukcje dotyczące sposobu wyświetlania tabel ARP dla każdego typu komunikacji równorzędnej przy użyciu programu PowerShell. Przed kontynuowaniem, ty lub dostawca łączności musi skonfigurować komunikacji równorzędnej. Każdy obwód ma dwie ścieżki (podstawowej i pomocniczej). Tabelę ARP można sprawdzić niezależnie dla każdej ścieżki.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla prywatnej komunikacji równorzędnej platformy Azure
Następujące polecenie cmdlet udostępnia tabele ARP dla prywatnej komunikacji równorzędnej platformy Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla publicznej komunikacji równorzędnej platformy Azure:
Następujące polecenie cmdlet udostępnia tabele ARP dla publicznej komunikacji równorzędnej platformy Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabele ARP dla komunikacji równorzędnej firmy Microsoft
Następujące polecenie cmdlet zawiera tabele ARP dla komunikacji równorzędnej firmy Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Przykładowe dane wyjściowe są pokazane poniżej dla jednej ze ścieżek:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak wykorzystać te informacje
Tabela ARP komunikacji równorzędnej może służyć do sprawdzania poprawności konfiguracji i łączności warstwy 2. W tej sekcji przedstawiono omówienie wyglądu tabel ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela ARP, gdy obwód jest w stanie operacyjnym (oczekiwanym)
* Tabela ARP ma wpis po stronie lokalnej z prawidłowym adresem IP i MAC oraz podobny wpis po stronie firmy Microsoft.
* Ostatni oktet lokalnego adresu IP jest zawsze numerem nieparzystym.
* Ostatni oktet adresu IP firmy Microsoft jest zawsze liczbą parzysty.
* Ten sam adres MAC pojawia się po stronie firmy Microsoft dla wszystkich trzech komunikacji równorzędnej (podstawowej/pomocniczej).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>tabela ARP, gdy jest lokalnie lub gdy po stronie dostawcy łączności występują problemy
 W tabeli ARP pojawia się tylko jeden wpis. Pokazuje mapowanie między adresem MAC a adresem IP, który jest używany po stronie firmy Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Jeśli wystąpi taki problem, otwórz żądanie pomocy technicznej u dostawcy łączności, aby go rozwiązać.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela ARP, gdy strona Microsoft ma problemy
* Nie będzie widoczna tabela ARP wyświetlana dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft.
* Otwórz żądanie pomocy technicznej za pomocą [pomocy technicznej pomocy technicznej platformy Microsoft Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Określ, że występuje problem z łącznością warstwy 2.

## <a name="next-steps"></a>Następne kroki
* Sprawdzanie poprawności konfiguracji warstwy 3 dla obwodu usługi ExpressRoute:
  * Pobierz podsumowanie trasy, aby określić stan sesji BGP.
  * Pobierz tabelę marszruty, aby określić, które prefiksy są anonsowane w umorzonej trasie ekspresowej.
* Sprawdzanie poprawności transferu danych przez przeglądanie bajtów i obecnie.
* Otwórz żądanie pomocy technicznej za pomocą [pomocy technicznej platformy Microsoft Azure,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) jeśli nadal występują problemy.

