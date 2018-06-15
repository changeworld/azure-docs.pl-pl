---
title: 'Pobieranie ARP tabel: klasycznym: Azure ExpressRoute Rozwiązywanie problemów z | Dokumentacja firmy Microsoft'
description: Ta strona zawiera instrukcje dotyczące pobierania protokołu ARP tabel dla obwodu usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: b5856acf-03c2-4933-8111-6ce12998d92a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: fcc847b7e30fd55ca759830e0254ab7542e7663e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850798"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Pobieranie tabel ARP w klasycznym modelu wdrażania
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-troubleshooting-arp-classic.md)
> 
> 

W tym artykule przedstawiono kroki pobierania tabeli protokołu ARP (Address Resolution Protocol) dla obwodu usługi ExpressRoute Azure.

> [!IMPORTANT]
> Ten dokument jest przeznaczony ułatwiające diagnozowanie i rozwiązywanie problemów proste. Nie ma być zastępczy pomocy technicznej firmy Microsoft. Jeśli nie rozwiąże problemu, korzystając z poniższych wskazówek, otwórz żądanie pomocy technicznej o [Microsoft Azure Pomoc i obsługa techniczna](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabele Resolution Protocol (ARP) i protokołu ARP adresów
ARP to protokół warstwy 2, który jest zdefiniowany w [RFC 826](https://tools.ietf.org/html/rfc826). ARP są używane do mapowania adres Ethernet (adresu MAC) z adresem IP.

Tabeli protokołu ARP udostępnia mapowanie adresu IPv4 i adres MAC dla konkretnego komunikacji równorzędnej. W tabeli protokołu ARP dla obwodu usługi ExpressRoute komunikacji równorzędnej zawiera następujące informacje dla każdego interfejsu (podstawowych i pomocniczych):

1. Mapowanie adresu IP interfejsu lokalnego routera adres MAC
2. Mapowanie adresu IP interfejsu routera ExpressRoute adres MAC.
3. Wiek mapowania

Tabele ARP może pomóc z sprawdzanie poprawności konfiguracji warstwy 2 i rozwiązywanie problemów z podstawowych problemów z łącznością warstwy 2.

Poniżej przedstawiono przykładowy tabeli protokołu ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Poniższa sekcja zawiera informacje o sposobie wyświetlania tabel ARP, które są widoczne przez routery brzegowe ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Wymagania wstępne dotyczące korzystania z protokołu ARP tabel
Upewnij się, że masz następujące przed kontynuowaniem:

* Nieprawidłowa obwodu ExpressRoute, skonfigurowanej z co najmniej jeden komunikacji równorzędnej. Obwód musi być w pełni skonfigurowane przez dostawcę łączności. Użytkownik (lub dostawcą połączenia) należy skonfigurować co najmniej jeden z komunikacji równorzędnych (Azure publicznego prywatne, Azure lub Microsoft) w tym obwodzie.
* Zakresy adresów IP, które są używane do konfigurowania komunikacji równorzędnych (Azure publicznego prywatne, Azure i firmy Microsoft). Przejrzyj przykłady przypisania adresów IP w [strony wymagania routingu usługi ExpressRoute](expressroute-routing.md) do zawierają opis sposobu adresy IP są mapowane na interfejsy użytkownika aise i po stronie usługi ExpressRoute. Informacje o konfiguracji komunikacji równorzędnej można uzyskać, przeglądając [strony konfiguracji komunikacji równorzędnej ExpressRoute](expressroute-howto-routing-classic.md).
* Informacje z dostawcą sieci zespołu lub łączności adresy MAC interfejsów, które są używane z tych adresów IP.
* Najnowsze modułu programu Windows PowerShell dla platformy Azure (wersja 1,50 lub nowsza).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabele ARP dla obwodu usługi ExpressRoute
Ta sekcja zawiera instrukcje dotyczące wyświetlania tabele ARP dla każdego typu komunikacji równorzędnej przy użyciu programu PowerShell. Przed kontynuowaniem należy lub dostawcą połączenia musi skonfigurować komunikację równorzędną. Każdy obwód ma dwie ścieżki (podstawowych i pomocniczych). Można sprawdzić w tabeli protokołu ARP dla każdej ścieżki niezależnie.

### <a name="arp-tables-for-azure-private-peering"></a>Tabele ARP dla platformy Azure prywatnej komunikacji równorzędnej
Następujące polecenie cmdlet udostępnia protokołu ARP tabel Azure prywatnej komunikacji równorzędnej:

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


### <a name="arp-tables-for-azure-public-peering"></a>Tabele ARP dla platformy Azure publicznej komunikacji równorzędnej:
Następujące polecenie cmdlet udostępnia protokołu ARP tabel Azure publicznej komunikacji równorzędnej:

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


Poniżej przedstawiono przykładowe dane wyjściowe dla jednej ze ścieżek:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak używać tych informacji
W tabeli protokołu ARP komunikacji równorzędnej może służyć do sprawdzania poprawności konfiguracji warstwy 2 i połączenia. Ta sekcja zawiera omówienie wygląd tabele ARP w różnych scenariuszach.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabeli protokołu ARP, kiedy obwód jest w stan operacyjny (oczekiwany)
* W tabeli protokołu ARP ma wpis dla strony lokalnymi z prawidłowym adresem IP i MAC, a podobne objęcia po stronie firmy Microsoft.
* Ostatni oktet lokalny adres IP zawsze jest liczbą nieparzystą.
* Ostatni oktet adresu IP firmy Microsoft jest zawsze liczbą parzystą.
* Ten sam adres MAC jest wyświetlany po stronie firmy Microsoft dla wszystkich trzech komunikacji równorzędnych (Podstawowe/pomocnicze).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabeli protokołu ARP, gdy jest lokalnymi lub po stronie dostawcy łączności ma problemy z
 W tabeli protokołu ARP zostanie wyświetlony tylko jeden wpis. Przedstawia on mapowanie między adres MAC i adres IP, który jest używany po stronie firmy Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Jeśli wystąpi problem podobny do tego, otwórz żądanie obsługi z dostawcą połączenia go rozwiązać.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabeli protokołu ARP problemów po stronie firmy Microsoft
* Nie będą widzieć tabeli protokołu ARP wyświetlany dla komunikacji równorzędnej, jeśli występują problemy po stronie firmy Microsoft.
* Otwórz żądanie pomocy technicznej o [Microsoft Azure Pomoc i obsługa techniczna](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Określ, czy masz problem z łącznością warstwy 2.

## <a name="next-steps"></a>Kolejne kroki
* Sprawdź poprawność konfiguracji warstwy 3 dla obwodu usługi ExpressRoute:
  * Pobierz podsumowanie do ustalenia stanu sesji BGP trasy.
  * Pobierz tabelę tras, aby określić prefiksy, które są rozgłaszane między ExpressRoute.
* Zweryfikuj transferu danych, przeglądając bajtów i wylogowanie.
* Otwórz żądanie pomocy technicznej o [Microsoft Azure Pomoc i obsługa techniczna](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nadal występują problemy.

