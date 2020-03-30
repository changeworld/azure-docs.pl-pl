---
title: 'Praca zdalna: Zagadnienia dotyczące wirtualnego urządzenia sieciowego (NVA) dla pracy zdalnej | Brama sieci VPN platformy Azure'
description: Ten artykuł pomaga zrozumieć rzeczy, które należy wziąć pod uwagę pracy z sieciowych urządzeń wirtualnych (NVA) na platformie Azure podczas pandemii COVID-19.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337091"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Praca zdalna: zagadnienia dotyczące wirtualnego urządzenia sieciowego (NVA) dla pracy zdalnej

>[!NOTE]
>W tym artykule opisano, jak można wykorzystać urządzenia wirtualne sieci, platformę Azure, sieć firmy Microsoft i ekosystem partnerów platformy Azure do pracy zdalnej i łagodzenia problemów sieciowych, które występują z powodu kryzysu COVID-19.
>

Niektórzy klienci platformy Azure korzystają z usług wirtualnych urządzeń sieciowych innych firm (NVA) z witryny Azure Marketplace w celu świadczenia krytycznych usług, takich jak sieć VPN typu punkt-lokacja dla swoich pracowników, którzy pracują z domu podczas epidemii COVID-19. W tym artykule przedstawiono niektóre wskazówki wysokiego poziomu, które należy wziąć pod uwagę podczas korzystać z usług NVA na platformie Azure w celu zapewnienia rozwiązań dostępu zdalnego.

## <a name="nva-performance-considerations"></a>Zagadnienia dotyczące wydajności urządzenia NVA

Wszyscy główni dostawcy sieci WUS w portalu Azure Marketplace powinni mieć zalecenia dotyczące rozmiaru maszyny Wirtualnej i liczby wystąpień, które mają być używane podczas wdrażania ich rozwiązań.  Podczas gdy prawie wszyscy dostawcy urządzenia WUS pozwolą Ci wybrać dowolny rozmiar, który jest dostępny w danym regionie, bardzo ważne jest, aby postępować zgodnie z zaleceniami dostawców dotyczącymi rozmiarów wystąpień maszyn wirtualnych platformy Azure, ponieważ te zalecenia są rozmiarami maszyn wirtualnych, które dostawca wykonał wydajność testowania na platformie Azure.  

### <a name="consider-the-following"></a>Należy wziąć pod uwagę następujące kwestie:

- **Pojemność i liczba równoczesnych użytkowników** — ten numer jest szczególnie ważny dla użytkowników sieci VPN typu punkt-lokacja, ponieważ każdy podłączony użytkownik utworzy jeden tunel szyfrowany (IPSec lub SSL VPN).  
- **Przepływność agregacji** — jaka jest łączna przepustowość, która będzie potrzebna do uwzględnienia liczby użytkowników, których potrzebujesz, aby zapewnić dostęp zdalny.
- **Rozmiar maszyny Wirtualnej, który będzie potrzebny** — należy zawsze używać rozmiarów maszyn wirtualnych zalecanych przez dostawcę urządzenia WUS.  W przypadku sieci VPN typu punkt-lokacja, jeśli będziesz mieć wiele równoczesnych połączeń użytkownika, należy używać większych rozmiarów maszyn wirtualnych, takich jak maszyny wirtualne [z serii Dv2 i DSv2.](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Seria Dv2 i Dsv2") Te maszyny wirtualne mają zwykle więcej procesorów wirtualnych i mogą obsługiwać więcej równoczesnych sesji sieci VPN.  Oprócz posiadania większej liczby rdzeni wirtualnych większe rozmiary maszyn wirtualnych na platformie Azure mają większą pojemność zagregowanej przepustowości niż mniejsze rozmiary maszyn wirtualnych.
    > **Ważne:** Każdy dostawca korzysta z zasobów w inny sposób.  Jeśli nie jest jasne, jakie rozmiary wystąpień należy użyć do uwzględnienia szacowanego obciążenia użytkownika, należy skontaktować się bezpośrednio z dostawcą oprogramowania i poprosić o zalecenie.
- **Liczba wystąpień** — jeśli oczekujesz, że duża liczba użytkowników i połączeń, istnieją ograniczenia, co skalowanie rozmiarów wystąpienia urządzenia WUS można osiągnąć.  Należy rozważyć wdrożenie wielu wystąpień maszyn wirtualnych.
- **IPSec VPN vs SSL VPN** - W ogólnych implementacjach IPSec VPN działają lepiej niż implementacje SSL VPN.  
- **Licencjonowanie** — upewnij się, że licencje na oprogramowanie zakupione dla rozwiązania NVA pokryją nagły wzrost, którego może wystąpić podczas epidemii COVID-19.  Wiele programów licencyjnych NVA ogranicza liczbę połączeń lub przepustowość, do czego może być zdolne rozwiązanie.
- **Przyspieszona sieć** — rozważ rozwiązanie WUS, które obsługuje przyspieszoną sieć.  Przyspieszona sieć umożliwia wirtualną wirtualną wirtualną wirtualizację pojedynczego katalogu głównego (SR-IOV), znacznie poprawiając jej wydajność sieciową. Ta ścieżka o wysokiej wydajności omija hosta ze ścieżki danych, zmniejszając opóźnienia, jitter i wykorzystanie procesora CPU do użytku z najbardziej wymagających obciążeń sieciowych na obsługiwanych typach maszyn wirtualnych. Przyspieszona sieć jest obsługiwana w większości rozmiarów wystąpień ogólnego przeznaczenia i zoptymalizowanych pod kątem obliczeń z co najmniej dwoma procesorami wirtualnymi.

## <a name="monitoring-resources"></a>Monitorowanie zasobów

Każde rozwiązanie NVA ma własne narzędzia i zasoby do monitorowania wydajności urządzenia WUS.  Zapoznaj się z dokumentacją dostawców, aby upewnić się, że rozumiesz ograniczenia wydajności i możesz wykryć, kiedy urządzenie WUS znajduje się w pobliżu lub osiąga pojemność.  Oprócz tego można spojrzeć na usługi Azure Monitor Network Insights i zobaczyć podstawowe informacje o wydajności dla urządzeń wirtualnych sieci, takich jak:

- Wykorzystanie procesora
- Sieć — wejście
- Sieć — wyjście
- Przepływy przychodzące
- Przepływy wychodzące

## <a name="next-steps"></a>Następne kroki

Większość głównych partnerów NVA opublikowała wytyczne dotyczące skalowania dla nagłego, nieoczekiwanego wzrostu podczas COVID-19. Oto kilka przydatnych linków do zasobów partnerów.

[Barracuda Włącz pracę z domu podczas zabezpieczania danych podczas COVID-19](https://www.barracuda.com/covid-19/work-from-home "Włącz pracę z domu podczas zabezpieczania danych podczas covid-19")

[Cisco AnyConnect Implementation and Performance/Scaling Reference for COVID-19 Preparation](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect Implementation and Performance/Scaling Reference for COVID-19 Preparation")

[Centrum pomocy technicznej Citrix COVID-19](https://www.citrix.com/support/covid-19-coronavirus.html "Centrum pomocy technicznej Citrix COVID-19")

[F5 Wytyczne, aby rozwiązać dramatyczny wzrost liczby pracowników zdalnych](https://www.f5.com/business-continuity "F5 Wytyczne, aby rozwiązać dramatyczny wzrost liczby pracowników zdalnych")

[Aktualizacje Fortinet COVID-19 dla klientów i partnerów](https://www.fortinet.com/covid-19.html "Aktualizacje COVID-19 dla klientów i partnerów")

[Palo Alto Networks CENTRUM Odpowiedzi COVID-19](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks CENTRUM Odpowiedzi COVID-19")
