---
title: Usługa Azure Stack sieć różnice i zagadnienia | Dokumentacja firmy Microsoft
description: Poznaj różnice i zagadnienia podczas pracy z siecią w usłudze Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: brenduns
ms.date: 08/02/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: 50fe3c0c7fda745047c71afb8eedf7fa8806c4ec
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055502"
---
# <a name="considerations-for-azure-stack-networking"></a>Zagadnienia dotyczące sieci usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Sieć usługi Azure Stack ma wiele funkcji oferowanych przez sieci platformy Azure. Jednakże istnieją niektóre podstawowe różnice, które należy zrozumieć przed wdrożeniem sieci usługi Azure Stack.

Ten artykuł zawiera przegląd unikatową zagadnienia dotyczące sieci usługi Azure Stack i jej funkcji. Aby dowiedzieć się więcej o różnicach wysokiego poziomu usługi Azure Stack i platformą Azure, zobacz [kluczowe zagadnienia](azure-stack-considerations.md) artykułu.

## <a name="cheat-sheet-networking-differences"></a>Ściągawka: różnice sieci

| Usługa | Cecha | Azure (globalna) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Wielodostępne DNS | Obsługiwane | Nie jest jeszcze obsługiwany |
|  | Rekordy DNS AAAA | Obsługiwane | Nieobsługiwane |
|  | Strefy DNS na subskrypcję | 100 (ustawienie domyślne)<br>Można zwiększyć na żądanie. | 100 |
|  | Zestawy rekordów DNS dla strefy | 5000 (ustawienie domyślne)<br>Można zwiększyć na żądanie. | 5000 |
|  | Serwery nazw dla delegowania strefy | System Azure oferuje cztery serwery nazw dla każdej strefy użytkownika (dzierżawcy), który jest tworzony. | Usługa Azure Stack oferuje dwa serwery nazw dla każdej strefy użytkownika (dzierżawcy), który jest tworzony. |
| Virtual Network | Wirtualne sieci równorzędne | Łączenie dwóch sieci wirtualnych w tym samym regionie za pośrednictwem sieci szkieletowej platformy Azure. | Nie jest jeszcze obsługiwany |
|  | Adresy IPv6 | Można przypisać adresu IPv6 jako część [konfiguracji interfejsu sieciowego](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Obsługiwany jest tylko protokół IPv4. |
|  | Plan ochrony przed atakami DDoS | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Konfiguracje adresów IP zestawu skalowania | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Usługi dostępu do prywatnych (podsieć) | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Punkty końcowe usługi | Obsługiwane w przypadku wewnętrznego połączenie (innych niż Internet) z usługami Azure. | Nie jest jeszcze obsługiwany. |
| Obsługiwany jest tylko protokół IPv4. | Zasad punktów końcowych usługi | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Tunele usługi | Obsługiwane | Nie jest jeszcze obsługiwany.  |
| Grupy zabezpieczeń sieci | Rozszerzone reguły zabezpieczeń | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Efektywne reguły zabezpieczeń | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Grupy zabezpieczeń aplikacji | Obsługiwane | Nie jest jeszcze obsługiwany. |
| Bramy sieci wirtualnej | Brama sieci VPN typu punkt lokacja | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Brama sieci wirtualnej między sieciami wirtualnymi | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Typ bramy sieci wirtualnej | Platforma Azure obsługuje sieć VPN<br> ExpressRoute <br> Hyper Net | Usługa Azure Stack obsługuje tylko typ sieci VPN w tej chwili. |
|  | Jednostki SKU bramy sieci VPN | Obsługa Basic GW1, GW2, GW3, standardowe o wysokiej wydajności, niezwykle wysokiej wydajności. | Obsługa podstawowa, standardowa i jednostki SKU o wysokiej wydajności. |
|  | Typ sieci VPN | Platforma Azure obsługuje zarówno oparte na zasadach i na podstawie trasy. | Usługa Azure Stack obsługuje na podstawie trasy tylko. |
|  | Ustawienia protokołu BGP | Platforma Azure obsługuje konfigurację adres komunikacji równorzędnej BGP i waga elementów równorzędnych. | Adres komunikacji równorzędnej BGP i waga elementów równorzędnych są automatycznie skonfigurowane w usłudze Azure Stack. Nie ma możliwości dla użytkownika skonfigurować te ustawienia przy użyciu własnych wartości. |
|  | Domyślna witryna bramy | Platforma Azure obsługuje konfigurację domyślną witrynę dla wymuszonego tunelowania. | Nie jest jeszcze obsługiwany. |
|  | Zmiana rozmiaru bramy | Platforma Azure obsługuje zmienianie rozmiaru bramy po wdrożeniu. | Ponowne ustalanie rozmiaru nie obsługiwane. |
|  | Konfiguracja aktywny/aktywny | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Zasad IKE/IPSec | Usługa Azure obsługuje niestandardowej konfiguracji zasad IPSec. | Nie jest jeszcze obsługiwany. |
|  | UsePolicyBasedTrafficSelectors | Platforma Azure obsługuje przy użyciu opartych na zasadach selektorów ruchu połączeń bramy oparte na trasach. | Nie jest jeszcze obsługiwany. |
| Moduł równoważenia obciążenia | SKU | Podstawowa i standardowa usługi równoważenia obciążenia są obsługiwane. | Obsługiwane jest tylko podstawowy moduł równoważenia obciążenia.  Właściwość SKU nie jest obsługiwana. |
|  | Strefy | Strefy dostępności są obsługiwane. | Nie jest jeszcze obsługiwany |
|  | Reguły NAT dla ruchu przychodzącego Obsługa punktów końcowych usługi | Platforma Azure obsługuje Określanie punktów końcowych usługi dla reguły NAT ruchu przychodzącego. | Usługa Azure Stack nie obsługuje jeszcze punktów końcowych usługi, więc te nie mogą być określone. |
|  | Protokół | Platforma Azure obsługuje określanie GRE lub ESP. | Klasa protokołu nie jest obsługiwana w usłudze Azure Stack. |
| Publiczny adres IP | Wersja adresowania publicznego adresu IP | Platforma Azure obsługuje IPv6 i IPv4 | Obsługiwany jest tylko protokół IPv4. |
| Interfejs sieciowy | Pobierz tabelę trasy obowiązujące od | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Pobierz efektywne list ACL | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Włącz przyspieszona sieć | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Przesyłanie dalej IP | Domyślnie wyłączone.  Można ją włączyć. | Przełączenie tego ustawienia nie jest obsługiwane.  Na domyślnie. |
|  | Wiele konfiguracji adresów IP dla każdego interfejsu | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Grupy zabezpieczeń aplikacji | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Etykieta nazwy wewnętrznego serwera DNS | Obsługiwane | Nie jest jeszcze obsługiwany. |
|  | Prywatny adres IP w wersji | Obsługiwane są zarówno protokołu IPv6 i IPv4. | Obsługiwany jest tylko protokół IPv4. |
|  | Konfiguracja podstawowego adresu IP | Obsługiwane. Identyfikuje podstawowa konfiguracja adresu IP w interfejsie. | Nie jest jeszcze obsługiwany. |
| Network Watcher | Network Watcher dzierżawy możliwości monitorowania sieci | Obsługiwane | Nie jest jeszcze obsługiwany. |
| CDN | Profilów sieci dostarczania zawartości | Obsługiwane | Nie jest jeszcze obsługiwany. |
| Brama aplikacji | Równoważenie obciążenia warstwy 7 | Obsługiwane | Nie jest jeszcze obsługiwany. |
| Traffic Manager | Kierowanie ruchem przychodzącym, aby uzyskać optymalną wydajność aplikacji i niezawodności. | Obsługiwane | Nie jest jeszcze obsługiwany. |
| ExpressRoute | Skonfiguruj szybkie, prywatne połączenie z usługami w chmurze firmy Microsoft z lokalnych funkcji infrastruktury lub środowiskiem obejmującym kilka lokalizacji. | Obsługiwane | Pomoc techniczna dla usługi Azure Stack nawiązywania połączenia z obwodu usługi Express Route. |

## <a name="next-steps"></a>Kolejne kroki

[Usługa DNS w usłudze Azure Stack](azure-stack-dns.md)