---
title: 'Pobierz ARP tabel — Rozwiązywanie problemów z usługi ExpressRoute: klasycznym: Azure| Microsoft Docs'
description: Ta strona zawiera instrukcje dotyczące pobieranie tabel ARP dla obwodu usługi ExpressRoute — klasycznego modelu wdrażania.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 3e49a1da0e8ea83faf5fc5a10d4c01a41d62fa88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883100"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Pobieranie tabel ARP w klasycznym modelu wdrażania
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ten artykuł zawiera opis kroków w celu uzyskania tabeli protokołu ARP (Address Resolution) dla obwodu usługi ExpressRoute platformy Azure.

> [!IMPORTANT]
> Ten dokument ma ułatwić diagnozowanie i rozwiązywanie problemów z prostego. Nie ma być zastępczy pomocy technicznej firmy Microsoft. Jeśli nie rozwiąże problemu, korzystając z poniższych wskazówek, otwórz żądanie obsługi z [Microsoft Azure, Pomoc i obsługa techniczna](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adres tabel Resolution Protocol (ARP) i protokołu ARP
ARP to protokół warstwy 2, która jest zdefiniowana w [RFC 826](https://tools.ietf.org/html/rfc826). ARP jest używany do mapowania adresu IP adres Ethernet (adres MAC).

Tabeli protokołu ARP zapewnia mapowanie adresu IPv4 i adres MAC dla konkretnego komunikacji równorzędnej. Tabeli protokołu ARP dla obwodu usługi ExpressRoute komunikacji równorzędnej zawiera następujące informacje dla każdego interfejsu (podstawowych i pomocniczych):

1. Mapowanie adresu IP interfejsu routera lokalnego adres MAC
2. Mapowanie adresu IP interfejsu routera ExpressRoute adres MAC.
3. Wiek mapowania

Tabele ARP, może pomóc z sprawdzanie poprawności konfiguracji warstwy 2 i rozwiązywanie problemów z łącznością podstawowe warstwy 2.

Poniżej znajduje się przykładem tabeli protokołu ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Poniższa sekcja zawiera informacje o sposobie wyświetlania tabel ARP, które są widoczne dla routerów granicznych usługi ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Wymagania wstępne dotyczące korzystania z tabel ARP
Upewnij się, że masz następujące elementy, przed kontynuowaniem:

* Nieprawidłowa obwód usługi ExpressRoute, który jest skonfigurowany z co najmniej jeden element równorzędny. Obwód musi być w pełni skonfigurowane przez dostawcę połączenia. Użytkownik (lub dostawca połączenia) należy skonfigurować co najmniej jeden z komunikacji równorzędnej (Azure prywatnej i publicznej Azure lub Microsoft) w tym obwodzie.
* Zakresy adresów IP, które są używane do konfigurowania komunikacji równorzędnej (Azure prywatnej i publicznej Azure i Microsoft). Przejrzyj przykłady przypisania adresów IP w [na stronie wymagania dotyczące routingu usługi ExpressRoute](expressroute-routing.md) ułatwią zrozumienie sposobu adresy IP są mapowane do interfejsów ze strony i po stronie usługi ExpressRoute. Informacje o konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [strony konfiguracji z komunikacji równorzędnej usługi ExpressRoute](expressroute-howto-routing-classic.md).
* Informacje od sieci dostawcy łączności lub zespołu o adresów MAC interfejsów, które są używane wraz z tych adresów IP.
* Najnowszy moduł programu Windows PowerShell dla platformy Azure (wersja 1,50 lub nowszej).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabele ARP dla obwodów usługi ExpressRoute
Ta sekcja zawiera instrukcje dotyczące wyświetlania tabele ARP dla każdego typu komunikacji równorzędnej przy użyciu programu PowerShell. Zanim będziesz kontynuować, użytkownik lub dostawcą połączenia, musi skonfigurować komunikację równorzędną. Każdy obwód ma dwie ścieżki (podstawowych i pomocniczych). Możesz sprawdzić tabeli protokołu ARP w przypadku poszczególnych ścieżek niezależnie.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla prywatnej komunikacji równorzędnej Azure
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla prywatnej komunikacji równorzędnej Azure:

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


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla publicznej komunikacji równorzędnej Azure:
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla publicznej komunikacji równorzędnej Azure:

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
Następujące polecenie cmdlet udostępnia protokołu ARP tabel dla komunikacji równorzędnej firmy Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Poniżej przedstawiono przykładowe dane wyjściowe do jednej ze ścieżek:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak używać tych informacji
Tabeli protokołu ARP komunikacji równorzędnej może służyć do sprawdzania poprawności warstwy 2, konfiguracją i łącznością. Ta sekcja zawiera omówienie wygląd tabel ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabeli protokołu ARP, gdy obwód jest w stan operacyjny (oczekiwana)
* Tabeli protokołu ARP zawiera wpis dla strony w środowisku lokalnym przy użyciu prawidłowego adresu IP i MAC i podobne wpis po stronie firmy Microsoft.
* Ostatni oktet adresu IP w środowisku lokalnym, zawsze jest liczbą nieparzystą.
* Ostatni oktet adresu IP firmy Microsoft, zawsze jest liczbą parzystą.
* Ten sam adres MAC jest wyświetlana po stronie firmy Microsoft dla wszystkich trzech komunikacji równorzędnej (podstawowy/dodatkowy).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabeli protokołu ARP, gdy jest on-premises lub po stronie dostawcy łączności występują problemy
 W tabeli protokołu ARP pojawi się tylko jedna pozycja. Przedstawia on mapowanie między adresu MAC i adres IP, który jest używany po stronie firmy Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Jeśli wystąpi problem następująco, otwórz żądanie obsługi z dostawcą połączenia, aby go rozwiązać.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabeli protokołu ARP w przypadku problemów po stronie firmy Microsoft
* Nie będą widzieć tabeli protokołu ARP wyświetlane dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft.
* Otwórz żądanie obsługi z [Microsoft Azure, Pomoc i obsługa techniczna](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, czy masz problem z łącznością warstwy 2.

## <a name="next-steps"></a>Kolejne kroki
* Sprawdź poprawność konfiguracji warstwy 3 dla obwodu usługi ExpressRoute:
  * Pobierz trasę podsumowania w celu określenia stanu sesji protokołu BGP.
  * Pobierz tabelę tras w celu ustalenia, które prefiksy są anonsowane przez usługi ExpressRoute.
* Sprawdź poprawność transfer danych wewnątrz i na zewnątrz przeglądając bajtów.
* Otwórz żądanie obsługi z [Microsoft Azure, Pomoc i obsługa techniczna](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nadal występują problemy.

