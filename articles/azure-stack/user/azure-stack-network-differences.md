---
title: Stos Azure networking różnice i zagadnienia dotyczące | Dokumentacja firmy Microsoft
description: Więcej informacji na temat różnic i zagadnienia dotyczące podczas pracy z sieci w programie Azure stosu.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: faff52ba5b5e2f0d573a67633d3a8411b2d7de74
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606430"
---
# <a name="considerations-for-azure-stack-networking"></a>Zagadnienia dotyczące sieci Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Azure stosu sieci zawiera funkcje oferowane przez sieć platformy Azure. Istnieją jednak niektóre podstawowe różnice, które należy zrozumieć przed wdrożeniem w sieci Azure stosu.

Ten artykuł zawiera omówienie unikatowy zagadnienia dotyczące sieci Azure stosu i ich funkcje. Aby uzyskać informacje ogólne różnice między stosu Azure i usługi Azure, zobacz [kluczowe zagadnienia dotyczące](azure-stack-considerations.md) tematu.

## <a name="cheat-sheet-networking-differences"></a>Ściągawka: różnice sieci

|Usługa | Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- | --- |
| DNS | Wielodostępne DNS | Obsługiwane| Nie jest jeszcze obsługiwane|
| |Rekordów AAAA usługi DNS|Obsługiwane|Nieobsługiwane|
| |Strefy DNS na subskrypcję|100 (ustawienie domyślne)<br>Można zwiększyć na żądanie.|100|
| |Zestawy rekordów DNS dla strefy|5000 (ustawienie domyślne)<br>Można zwiększyć na żądanie.|5000|
||Serwery nazw dla delegowania strefy|Platforma Azure udostępnia cztery serwery nazw dla każdej strefy użytkownika (dzierżawcy), która jest tworzona.|Stos Azure udostępnia dwa serwery nazw dla każdej strefy użytkownika (dzierżawcy), która jest tworzona.|
| Sieć wirtualna|Wirtualne sieci równorzędne|Połącz dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci Azure sieci szkieletowej.|Nie jest jeszcze obsługiwane|
| |Adresy IPv6|Można przypisać adres IPv6 w ramach [Konfiguracja interfejsu sieciowego](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Obsługiwany jest tylko protokół IPv4.|
|Bramy sieci VPN|Brama sieci VPN typu punkt lokacja|Obsługiwane|Nie jest jeszcze obsługiwane|
| |Brama sieci wirtualnej do sieci wirtualnej|Obsługiwane|Nie jest jeszcze obsługiwane|
| |Jednostki SKU bramy sieci VPN|Obsługa Basic, GW1, GW2, GW3, standardowe wysokiej wydajności, niezwykle wydajny. |Obsługa podstawowa, standardowa i jednostki SKU wysokiej wydajności.|
|Moduł równoważenia obciążenia|Publiczne adresy IP protokołu IPv6|Obsługa przypisywanie IPv6 publicznego adresu IP do modułu równoważenia obciążenia.|Obsługiwany jest tylko protokół IPv4.|
|Network Watcher|Monitor sieci dzierżawcy możliwości monitorowania sieci|Obsługiwane|Nie jest jeszcze obsługiwane|
|CDN|Profile sieci dostarczania zawartości|Obsługiwane|Nie jest jeszcze obsługiwane|
|Brama aplikacji|Równoważenia obciążenia warstwy 7|Obsługiwane|Nie jest jeszcze obsługiwane|
|Traffic Manager|Kierującą ruch przychodzący do aplikacji optymalnej wydajności i niezawodności.|Obsługiwane|Nie jest jeszcze obsługiwane|
|ExpressRoute|Skonfigurowanie istnieje w przypadku szybkiego połączenia do usług chmurowych firmy Microsoft z zakładzie infrastruktury lub wspólnej lokalizacji lokalnej.|Obsługiwane|Obsługa nawiązywania stosu Azure obwodzie usługi Express Route.|

## <a name="next-steps"></a>Kolejne kroki

[Usługa DNS w usłudze Azure Stack](azure-stack-dns.md)