---
title: Azure VMware Solutions (Automatyczna synchronizacja) — automatyczna synchronizacja chmur prywatnych
description: Dowiedz się więcej na temat automatycznej synchronizacji chmur i koncepcji prywatnych.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2688edf281a6d8bc3d61e8e294c920f115f0f3f6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024957"
---
# <a name="avs-private-cloud-overview"></a>Omówienie automatycznej synchronizacji chmury prywatnej

Automatyczna synchronizacja przekształca i rozszerza obciążenia oprogramowania VMware do chmur publicznych w ciągu kilku minut. Za pomocą usługi automatycznej synchronizacji można wdrożyć oprogramowanie VMware natywnie na platformie Azure bez systemu operacyjnego. Wdrożenie jest przechowywane w lokalizacjach platformy Azure i w pełni integruje się z pozostałą częścią chmury platformy Azure.

Rozwiązanie do automatycznej synchronizacji zapewnia pełną ciągłość działania programu VMware. To rozwiązanie zapewnia korzyści z chmury publicznej:

* Elastyczność
* Innowacja
* Wydajność

Dzięki automatycznej synchronizacji można korzystać z modelu zużycia w chmurze, który obniża całkowity koszt posiadania. Oferuje on również Inicjowanie obsługi administracyjnej na żądanie, płatność zgodnie z oczekiwaniami i optymalizację wydajności.

Automatyczna synchronizacja jest w pełni zgodna z:

* Istniejące narzędzia
* Nowych
* Procesy

Ta zgodność umożliwia zespołom zarządzanie obciążeniami w chmurze platformy Azure bez zakłócania tego typu zasad:

* Network (Sieć)
* Zabezpieczenia  
* Ochrona danych  
* Inspekcja

Funkcja automatycznej synchronizacji zarządza infrastrukturą i wszystkimi niezbędnymi usługami sieci i zarządzania. Usługa automatycznej synchronizacji umożliwia zespołowi skoncentrowanie się na:

* Wartość biznesowa
* Inicjowanie obsługi aplikacji
* Ciągłość działania
* Pomoc techniczna
* Wymuszanie zasad

## <a name="avs-private-cloud-environment-overview"></a>Omówienie automatycznej synchronizacji środowiska chmury prywatnej

Chmura prywatna w wersji zaautomatycznej to izolowany stos oprogramowania VMware, który obsługuje:

* Hosty ESXi
* vCenter
* Sieci vSAN
* NSX

W portalu do automatycznej synchronizacji są zarządzane chmury prywatne. Mają własny serwer vCenter we własnej domenie zarządzania.

Stos działa na:

* Węzły dedykowane
* Węzły sprzętu izolowanych od zera

Użytkownicy wykorzystują stos za pomocą natywnych narzędzi VMware, w tym:

* vCenter
* NSX Manager

Można wdrażać dedykowane węzły w lokalizacjach platformy Azure. Następnie możesz zarządzać nimi przy użyciu platformy Azure i automatycznej synchronizacji. Chmura prywatna do automatycznej synchronizacji składa się z co najmniej jednego klastra vSphere, a każdy klaster zawiera od 3 do 16 węzłów.

Chmurę prywatną można utworzyć, korzystając z zakupionych węzłów płatność zgodnie z rzeczywistym użyciem lub zarezerwowanych, dedykowanych węzłów.

Można podłączyć chmurę prywatną automatycznej synchronizacji do środowiska lokalnego i sieci platformy Azure przy użyciu następujących połączeń:

* Bezpieczeństwo
* Prywatna sieć VPN
* Usługa ExpressRoute systemu Azure

Środowisko chmury prywatnej do automatycznej synchronizacji zostało zaprojektowane z myślą o eliminacji pojedynczych punktów awarii:

* Klastry ESXi są skonfigurowane z wysoką dostępnością vSphere i mają rozmiar co najmniej jeden węzeł zapasowy na potrzeby odporności.
* Sieci vSAN zapewnia nadmiarowy magazyn podstawowy. Sieci vSAN wymaga co najmniej trzech węzłów w celu zapewnienia ochrony przed pojedynczym awarią. Można skonfigurować sieci vSAN w celu zapewnienia większej odporności dla większych klastrów.
* Aby zapewnić ochronę przed awariami magazynu, można skonfigurować maszyny wirtualne programu vCenter, PSC i NSX Manager przy użyciu zasad magazynu RAID-10. vSphere HA chroni przed awariami węzłów i sieci.

## <a name="scenarios-for-deploying-an-avs-private-cloud"></a>Scenariusze wdrażania chmury prywatnej do automatycznej synchronizacji

Oto przykładowe przypadki użycia do automatycznej synchronizacji wdrożenia chmury prywatnej.

### <a name="data-center-retirement-or-migration"></a>Wycofanie lub migracja centrum danych

* Uzyskaj dodatkową pojemność po osiągnięciu limitów istniejących centrów danych lub odświeżania sprzętu.
* Dodaj wymaganą pojemność w chmurze i Usuń zarządzaniem mu towarzysząę zarządzania odświeżeniami sprzętowymi.
* Zmniejszenie ryzyka i kosztów migracji w chmurze w porównaniu do czasochłonnych operacji konwersji lub ponownej architektury.
* Przyspiesz migracje w chmurze przy użyciu znanych narzędzi i umiejętności programu VMware. W chmurze korzystaj z usług platformy Azure w celu modernizacji aplikacji w Twoim tempie.

### <a name="expand-on-demand"></a>Rozwiń na żądanie

* Rozwiń chmurę, aby zaspokoić nieprzewidziane potrzeby, takie jak nowe środowiska programistyczne lub sezonowe zmiany pojemności.
* Utwórz nową pojemność na żądanie i Zachowaj ją tylko tak długo, jak to konieczne.
* Ogranicz inwestycje z góry, przyspiesz szybkość aprowizacji i zmniejsz złożoność dzięki tej samej architekturze i zasadom zarówno w środowisku lokalnym, jak i w chmurze.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Odzyskiwanie po awarii i pulpity wirtualne w chmurze platformy Azure

* Ustanów zdalny dostęp do danych, aplikacji i pulpitów w chmurze platformy Azure. Połączenia o wysokiej przepustowości umożliwiają szybkie przekazywanie i pobieranie danych w celu odzyskania zdarzeń. Sieci o małym opóźnieniu zapewniają krótszy czas odpowiedzi, który użytkownicy oczekują od aplikacji klasycznej.

* Replikuj wszystkie zasady i sieci w chmurze przy użyciu portalu automatycznej synchronizacji i znanych narzędzi VMware. Replikacja zmniejsza nakład pracy i ryzyko tworzenia i zarządzania implementacjami odzyskiwania po awarii i infrastruktury VDI.

### <a name="high-performance-applications-and-databases"></a>Aplikacje i bazy danych o wysokiej wydajności

* Uruchamiaj najbardziej wymagające obciążenia przy użyciu architektury ze zbyt zbieżną architekturą zapewnianą przez Autosynchronizacja.
* Uruchamiaj oprogramowanie Oracle, Microsoft SQL Server, systemy oprogramowania pośredniczącego i bazy danych bez SQL o wysokiej wydajności.
* Poznaj chmurę jako własne centrum danych z szybkimi połączeniami sieciowymi o szybkości 25 GB/s. Połączenia o dużej szybkości umożliwiają uruchamianie aplikacji hybrydowych obejmujących lokalne, VMware na platformie Azure i prywatne obciążenia platformy Azure bez naruszania wydajności.

### <a name="true-hybrid"></a>Prawdziwe hybrydowe

* Ujednolicenie DevOps w ramach usług VMware i Azure.
* Optymalizuj administrację VMware dla usług i rozwiązań platformy Azure, które można stosować do wszystkich obciążeń.
* Uzyskaj dostęp do usług w chmurze publicznej bez konieczności rozszerzania centrum danych ani tworzenia architektury aplikacji.
* Scentralizowanie tożsamości, zasad kontroli dostępu, rejestrowania i monitorowania aplikacji VMware na platformie Azure.

## <a name="limits"></a>Limity

W poniższej tabeli wymieniono limity węzłów dotyczące zasobów chmury prywatnej automatycznej synchronizacji.

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej do automatycznej synchronizacji | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej automatycznej synchronizacji | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej automatycznej synchronizacji | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak utworzyć chmurę prywatną do automatycznej synchronizacji](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej do automatycznej synchronizacji](quickstart-create-private-cloud.md)
