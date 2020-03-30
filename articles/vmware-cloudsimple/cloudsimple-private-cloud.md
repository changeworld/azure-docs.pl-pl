---
title: Rozwiązanie Azure VMware firmy CloudSimple — chmury prywatne
description: Dowiedz się więcej o chmurach prywatnych CloudSimple i pojęciach.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024957"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple Private Cloud — omówienie

CloudSimple przekształca i rozszerza obciążenia VMware do chmur publicznych w ciągu kilku minut. Za pomocą usługi CloudSimple, można wdrożyć VMware natywnie na infrastrukturze platformy Azure bez systemu operacyjnego. Wdrożenie jest dostępne w lokalizacjach platformy Azure i w pełni integruje się z resztą chmury platformy Azure.

Rozwiązanie CloudSimple zapewnia pełną ciągłość działania VMware. To rozwiązanie zapewnia korzyści chmury publicznej:

* Elastyczność
* Innowacje
* Wydajność

CloudSimple korzysta z modelu zużycia chmury, który obniża całkowity koszt posiadania. Oferuje również aprowizacji na żądanie, pay-as-you-grow i optymalizacji pojemności.

CloudSimple jest w pełni kompatybilny z:

* Istniejące narzędzia
* Umiejętności
* Procesy

Ta zgodność umożliwia zespołom zarządzanie obciążeniami w chmurze platformy Azure bez zakłócania tych typów zasad:

* Network (Sieć)
* Zabezpieczenia  
* Ochrona danych  
* Inspekcja

CloudSimple zarządza infrastrukturą i wszystkimi niezbędnymi usługami sieciowymi i zarządzania. Usługa CloudSimple umożliwia zespołowi skupienie się na:

* Wartość biznesowa
* Aprowizacja aplikacji
* Ciągłość działalności biznesowej
* Pomoc techniczna
* Wymuszanie zasad

## <a name="private-cloud-environment-overview"></a>Omówienie środowiska chmury prywatnej

Private Cloud to izolowany stos VMware, który obsługuje:

* Hosty ESXi
* vCenter
* Vsan
* Nsx

Chmury prywatne są zarządzane za pośrednictwem portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania.

Stos działa na:

* Węzły dedykowane
* Odizolowane węzły sprzętowe z gołym metalem

Użytkownicy korzystają ze stosu za pośrednictwem natywnych narzędzi VMware, w tym:

* vCenter
* Menedżer NSX

Dedykowane węzły można wdrożyć w lokalizacjach platformy Azure. Następnie można zarządzać nimi za pomocą platformy Azure i CloudSimple. Chmura prywatna składa się z jednego lub więcej klastrów vSphere, a każdy klaster zawiera od 3 do 16 węzłów.

Chmurę prywatną można utworzyć przy użyciu zakupionych węzłów płatności zgodnie z rzeczywistym użyciem lub zarezerwowanych węzłów dedykowanych.

Chmura prywatna można połączyć ze środowiskiem lokalnym i siecią platformy Azure przy użyciu następujących połączeń:

* Bezpieczeństwo
* Prywatna sieć VPN
* Azure ExpressRoute

Środowisko private cloud zostało zaprojektowane w celu wyeliminowania pojedynczych punktów awarii:

* Klastry ESXi są skonfigurowane z vSphere wysokiej dostępności i mają rozmiar, aby mieć co najmniej jeden węzeł zapasowy dla odporności.
* vSAN zapewnia nadmiarową pamięć podstawową. vSan wymaga co najmniej trzech węzłów, aby zapewnić ochronę przed pojedynczym błędem. Można skonfigurować vSAN, aby zapewnić większą odporność większych klastrów.
* Maszyny wirtualne vCenter, PSC i NSX Manager można skonfigurować za pomocą zasad magazynu RAID-10 w celu ochrony przed awarią magazynu. VSphere HA chroni przed awariami węzłów i sieci.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenariusze wdrażania chmury prywatnej

Oto kilka przykładowych przypadków użycia dla wdrożenia w chmurze prywatnej.

### <a name="data-center-retirement-or-migration"></a>Wycofywanie lub migracja centrum danych

* Uzyskaj dodatkową pojemność po osiągnięciu limitów istniejącego centrum danych lub odświeżeniu sprzętu.
* Dodaj potrzebną pojemność w chmurze i wyeliminuj problemy związane z zarządzaniem odświeżakami sprzętu.
* Zmniejsz ryzyko i koszty migracji do chmury w porównaniu z czasochłonnymi konwersjami lub chybieniami.
* Korzystaj ze znanych narzędzi i umiejętności VMware, aby przyspieszyć migrację do chmury. W chmurze użyj usług platformy Azure, aby zmodernizować aplikacje w swoim tempie.

### <a name="expand-on-demand"></a>Rozwiń na żądanie

* Rozwiń do chmury, aby sprostać nieoczekiwanym potrzebom, takim jak nowe środowiska programistyczne lub sezonowe serie zdolności produkcyjnych.
* Twórz nową pojemność na żądanie i przechowuj ją tylko tak długo, jak jej potrzebujesz.
* Zmniejsz swoje inwestycje z góry, przyspiesz szybkość inicjowania obsługi administracyjnej i zmniejsz złożoność dzięki tej samej architekturze i zasadom zarówno w środowisku lokalnym, jak i w chmurze.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Odzyskiwanie po awarii i pulpity wirtualne w chmurze platformy Azure

* Ustanawianie zdalnego dostępu do danych, aplikacji i pulpitów w chmurze platformy Azure. Dzięki połączeniom o wysokiej przepustowości przesyłasz / pobierasz dane szybko, aby odzyskać dane po zdarzeniach. Sieci o małych opóźnieniach zapewniają szybkie czasy reakcji, których użytkownicy oczekują od aplikacji klasycznej.

* Replikuj wszystkie zasady i sieci w chmurze za pomocą portalu CloudSimple i znanych narzędzi VMware. Replikacja zmniejsza nakład pracy i ryzyko tworzenia i zarządzania implementacjami odzyskiwania po awarii i VDI.

### <a name="high-performance-applications-and-databases"></a>Aplikacje i bazy danych o wysokiej wydajności

* Uruchamiaj najbardziej wymagające obciążenia dzięki architekturze hiperkonwergentnej udostępnianej przez CloudSimple.
* Uruchom oracle, microsoft SQL server, systemy oprogramowania pośredniczącego i wysokowydajne bazy danych bez SQL.
* Poznaj chmurę jako własne centrum danych dzięki szybkim połączeniom sieciowym o szybkości 25 Gb/s. Szybkie połączenia umożliwiają uruchamianie aplikacji hybrydowych obejmujących lokalne, VMware na platformie Azure i prywatne obciążenia platformy Azure bez utraty wydajności.

### <a name="true-hybrid"></a>Prawdziwa hybryda

* Ujednolicenie devops w usługach VMware i Azure.
* Optymalizacja administracji VMware pod kątem usług i rozwiązań platformy Azure, które mogą być stosowane we wszystkich obciążeniach.
* Uzyskaj dostęp do publicznych usług w chmurze bez konieczności rozszerzania centrum danych lub ponownego wychwytywowania aplikacji.
* Scentralizuj tożsamości, zasady kontroli dostępu, rejestrowanie i monitorowanie aplikacji VMware na platformie Azure.

## <a name="limits"></a>Limity

W poniższej tabeli wymieniono limity węzłów zasobów chmury prywatnej.

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
