---
title: Sieci integracji zagadnienia dotyczące usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planować Integracja sieci centrum danych z wieloma węzłami usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 97fcfa20e474edb8108474ef02c6542688d627ff
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243491"
---
# <a name="network-connectivity"></a>Połączenie sieciowe
Ten artykuł zawiera informacje infrastruktury sieci usługi Azure Stack, aby ułatwić podjęcie decyzji o sposobie najlepiej Zintegruj usługę Azure Stack z istniejącym środowiskiem sieci. 

> [!NOTE]
> Aby rozwiązać zewnętrzne nazwy DNS z usługi Azure Stack (na przykład www.bing.com), musisz podać serwery DNS do przesyłania żądań DNS. Aby uzyskać więcej informacji na temat wymagań dotyczących usługi Azure Stack DNS Zobacz [Integracja z centrum danych usługi Azure Stack — DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Projekt sieci fizycznej
Rozwiązanie usługi Azure Stack wymaga odpornej i wysoce dostępnej infrastruktury fizycznej do obsługi jego działania i usług. Łącza nadrzędne z sieci ToR do przełączników obramowania są ograniczone do SFP + lub SFP28 nośnika i 1 GB, 10 GB lub szybkości 25 GB. Skontaktuj się z dostawcą sprzętu producenta sprzętu (OEM) dla dostępności. Na poniższym diagramie przedstawiono naszych zalecany projekt:

![Zalecany projekt sieci usługi Azure Stack](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Sieci logiczne
Sieci logiczne stanowią abstrakcję podstawowej infrastruktury sieci fizycznej. Służą one do organizacji i uproszczenia przypisań sieci dla hostów, maszyn wirtualnych i usług. W ramach tworzenia sieci logicznej Lokacje sieciowe są tworzone na definiowanie wirtualnych sieci lokalnych (VLAN), podsieci IP i adresów IP pary podsieć/VLAN, które są skojarzone z siecią logiczną, w każdej lokalizacji fizycznej.

W poniższej tabeli przedstawiono sieci logiczne i skojarzone podsieci zakresy adresów IPv4 należy zaplanować:

| Sieć logiczna | Opis | Rozmiar | 
| -------- | ------------- | ------------ | 
| Publicznych adresów VIP | Korzysta z usługi Azure Stack daje w sumie 31 adresów z tą siecią. Osiem publiczne adresy IP są używane dla małej grupy usługi Azure Stack, a pozostałe są używane przez maszyny wirtualne dzierżawcy. Jeśli planujesz używać usługi App Service i dostawców zasobów SQL 7 większej liczby adresów są używane. Pozostałe 15 adresy IP są zastrzeżone dla przyszłych usług platformy Azure. | / 26 (62 hostów) - /22 (1022 hostów)<br><br>Zalecane = prefiksie/24 (254 hostów) | 
| Przełącznik infrastruktury | Point-to-Point adresów IP dla celów routingu dedykowanego przełącznika/interfejsów zarządzania i adresy sprzężenia zwrotnego przypisany do przełącznika. | /26 | 
| Infrastruktura | Używane przez usługę Azure Stack wewnętrznych składników do komunikacji. | /24 |
| Private | Używane dla sieci magazynu i prywatnych adresów VIP. | /24 | 
| BMC | Używany do komunikacji ze składnikami bmc na hostach fizycznych. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Infrastruktura sieciowa
Infrastruktura sieci dla usługi Azure Stack składa się z kilku sieci logicznych, które są skonfigurowane na temat parametrów. Na poniższym diagramie przedstawiono te sieci logiczne oraz sposób ich integracji z top-of-rack (TOR) kontrolera zarządzania płytą główną (BMC) i obramowanie przełączników (sieci klienta).

![Połączenia diagram i przełącznik sieci logicznej](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Sieci kontrolera zarządzania płytą GŁÓWNĄ
Ta sieć jest dedykowany do procesu łączenia wszystkie kontrolery zarządzania płytą główną (znany także jako service procesorów, na przykład iDRAC iLO, iBMC, itp.) do sieci zarządzania. Jeśli jest obecny, Host cyklu życia sprzętu (HLH) znajduje się w tej sieci i może dostarczyć oprogramowanie specyficzne dla producenta OEM konserwacji sprzętu lub monitorowania. 

HLH obsługuje również wdrażanie maszyny Wirtualnej (Menedżer DVM). Menedżer DVM jest używany podczas wdrażania usługi Azure Stack i zostanie usunięta po zakończeniu wdrożenia. Menedżer DVM wymaga dostępu do Internetu w scenariuszach wdrażania podłączonych do testowania, weryfikacji i dostęp do wielu składników. Te składniki mogą być i spoza sieci firmowej. na przykład NTP, DNS i platformy Azure. Aby uzyskać więcej informacji na temat wymagań dotyczących łączności, zobacz [sekcji translatora adresów Sieciowych w usłudze Azure Stack Integracja z zaporą](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Sieć prywatna
Ta prefiksie/24 (254 hostów adresy IP) sieci jest prywatna w regionie usługi Azure Stack (nie jest powiększony, poza urządzeń przełącznika obramowanie obszaru usługi Azure Stack) i jest podzielony na dwie podsieci:

- **Sieć magazynowania**. /25 (host 126 adresów IP) sieci używanej do obsługi ruchu sieciowego magazynu bezpośrednich miejsc do magazynowania i bloku komunikatów serwera (SMB) i migracji na żywo maszyny wirtualnej. 
- **Wewnętrzną siecią wirtualną IP**. A/25 sieci dedykowanych do wewnętrznych adresów VIP dla równoważenia obciążenia oprogramowania.

### <a name="azure-stack-infrastructure-network"></a>Sieci infrastruktury w usłudze Azure Stack
To/24 sieć jest przeznaczona do obsługi wewnętrznych składników usługi Azure Stack, tak aby komunikować się i wymieniać dane między sobą. Ta podsieć wymaga rutowalne adresy IP, ale pozostaje kluczem prywatnym do rozwiązania przy użyciu list kontroli dostępu (ACL). Nie powinien być kierowany poza przełączników obramowanie, z wyjątkiem niewielki zakres równoważne rozmiar/27 sieci wykorzystywane przez niektóre z tych usług, gdy potrzebują dostępu do zasobów zewnętrznych i/lub Internetu. 

### <a name="public-vip-network"></a>Sieci publicznych adresów VIP
Sieci publicznych adresów VIP jest przypisany do kontrolera sieci w usłudze Azure Stack. Nie jest sieć logiczną na przełączniku. SLB korzysta z puli adresów i przypisuje/32 sieci dla obciążenia dzierżaw. W tabeli routingu przełącznika tych 32 adresów IP są anonsowane jako trasę w protokole dostępne za pośrednictwem protokołu BGP. Ta sieć zawiera adresy IP dostępne zewnętrznego lub publicznej. Infrastruktury Azure Stack rezerwuje pierwszych 31 adresów z tej sieci publicznych adresów VIP, podczas gdy reszta jest używany przez dzierżawione maszyny wirtualne. Rozmiar sieci w tej podsieci może wynosić od co najmniej /26 (64 hostów), maksymalnie /22 (1022 hostów), zaleca się planowanie prefiksie/24 sieci.

### <a name="switch-infrastructure-network"></a>Przełącznik sieci infrastruktury
To/26 sieć jest podsieci, która zawiera podsieci (host 2 adresy IP) / 30 IP Routing typu punkt-punkt i sprzężenia zwrotne, które są zasobami dedykowanymi/32 podsieci wewnątrzpasmowe przełącznika zarządzania i identyfikator protokołu BGP routera. Ten zakres adresów IP muszą być routowalne zewnętrznie z rozwiązania usługi Azure Stack w swoim centrum danych, mogą być prywatne lub publiczne adresy IP.

### <a name="switch-management-network"></a>Przełącz sieć zarządzania
Tym rozmiarze/29 (host 6 adresów IP) sieci jest dedykowany do procesu łączenia porty zarządzania przełączników. Umożliwia dostęp poza pasmem dla wdrażania, zarządzania i rozwiązywania problemów. Jest ona obliczana od przełącznika sieci infrastruktury wymienionych powyżej.

## <a name="publish-azure-stack-services"></a>Publikowanie usługi Azure Stack
Należy udostępnić usługi Azure Stack użytkownikom z zewnętrznej usługi Azure Stack. Usługa Azure Stack konfiguruje różne punkty końcowe dla jego role infrastruktury. Te punkty końcowe są przypisywane adresy VIP z puli publicznych adresów IP. Wpis DNS jest tworzony dla każdego punktu końcowego w strefie DNS zewnętrznego została określona w czasie wdrażania. Na przykład portal użytkowników jest przypisany wpis hosta DNS w portalu.  *&lt;region >.&lt; Nazwa FQDN >*.

### <a name="ports-and-urls"></a>Porty i adresy URL
Usługi Azure Stack (takich jak portale, usługi Azure Resource Manager, DNS, itp.) dostępne z sieciami zewnętrznymi, musisz zezwolić na ruch przychodzący do tych punktów końcowych dla określonych adresów URL, porty i protokoły.
 
W przypadku wdrożenia w przypadku, gdy pasm przezroczystym serwerem proxy, aby serwer proxy tradycyjnych musisz zezwolić na określone porty i adresy URL dla obu [dla ruchu przychodzącego](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) i [wychodzącego](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) komunikacji. Obejmują one porty i adresy URL dla tożsamości, portalu marketplace, poprawek i aktualizacji, rejestracji i użycia danych.

## <a name="next-steps"></a>Kolejne kroki
[Łączność obramowania](azure-stack-border-connectivity.md)
