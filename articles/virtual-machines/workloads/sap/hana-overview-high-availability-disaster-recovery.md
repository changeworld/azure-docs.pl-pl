---
title: Wysoka dostępność i odzyskiwanie po awarii usługi SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Ustanowienie wysokiej dostępności i planu odzyskiwania po awarii sap HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0585c1251ba18e1390f3eee28a989edee6eb8591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616942"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Sap HANA Duże wystąpienia wysoka dostępność i odzyskiwanie po awarii na platformie Azure 

>[!IMPORTANT]
>Ta dokumentacja nie zastępuje dokumentacji administracyjnej SAP HANA ani notatek SAP. Oczekuje się, że czytelnik ma solidne zrozumienie i wiedzę w zakresie administracji i operacji SAP HANA, szczególnie w przypadku kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii.

Ważne jest, aby wykonywać kroki i procesy podejmowane w środowisku oraz z wersjami i wersjami HANA. Niektóre procesy opisane w tej dokumentacji są uproszczone w celu lepszego zrozumienia ogólnego i nie są przeznaczone do użycia jako szczegółowe kroki dla ewentualnych podręczników operacji. Jeśli chcesz utworzyć podręczniki operacji dla konfiguracji, musisz przetestować i wykonywać procesy i udokumentować procesy związane z określonymi konfiguracjami. 


Wysoka dostępność i odzyskiwanie po awarii (DR) są kluczowymi aspektami uruchamiania krytycznego rozwiązania SAP HANA na serwerze platformy Azure (duże wystąpienia). Ważne jest, aby współpracować z sap, integrator systemu lub firmy Microsoft, aby prawidłowo zaprojektować i wdrożyć odpowiednie strategie wysokiej dostępności i odzyskiwania po awarii. Należy również wziąć pod uwagę cel punktu odzyskiwania (RPO) i cel czasu odzyskiwania, które są specyficzne dla danego środowiska.

Firma Microsoft obsługuje niektóre funkcje wysokiej dostępności SAP HANA z dużymi wystąpieniami HANA. Te funkcje obejmują:

- **Replikacja magazynu:** system magazynu możliwość replikowania wszystkich danych do innego sygnatury wystąpienia dużych HANA w innym regionie platformy Azure. SAP HANA działa niezależnie od tej metody. Ta funkcja jest domyślny mechanizm odzyskiwania po awarii oferowanych dla hana dużych wystąpień.
- **Replikacja systemu HANA:** [Replikacja wszystkich danych w SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do oddzielnego systemu SAP HANA. Cel czasu odzyskiwania jest zminimalizowany za pomocą replikacji danych w regularnych odstępach czasu. SAP HANA obsługuje tryby asynchroniczne, synchroniczne w pamięci i synchroniczne. Tryb synchroniczne jest używany tylko dla systemów SAP HANA, które znajdują się w tym samym centrum danych lub mniej niż 100 km od siebie. Przy bieżącym projekcie sygnatur dużych wystąpień HANA replikacja systemu HANA może służyć do wysokiej dostępności tylko w jednym regionie. Replikacja systemu HANA wymaga odwrotnego serwera proxy lub składnika routingu innej firmy dla konfiguracji odzyskiwania po awarii do innego regionu platformy Azure. 
- **Automatyczne przetwarzanie danych awaryjnych hosta:** lokalne rozwiązanie do odzyskiwania błędów dla systemu SAP HANA, które jest alternatywą dla replikacji systemu HANA. Jeśli węzeł główny stanie się niedostępny, należy skonfigurować jeden lub więcej węzłów SAP HANA w trybie skalowania w poziomie, a sap HANA automatycznie przechodzi w trybie awaryjnym do węzła rezerwowego.

Sap HANA na platformie Azure (duże wystąpienia) jest oferowany w dwóch regionach platformy Azure w czterech obszarach geopolitycznych (USA, Australia, Europa i Japonia). Dwa regiony w obszarze geopolitycznym, w których znajdują się znaczki dużych wystąpień HANA, są połączone z oddzielnymi obwodami sieciowymi. Są one używane do replikowania migawek magazynu w celu zapewnienia metod odzyskiwania po awarii. Replikacja nie jest ustanawiana domyślnie, ale jest skonfigurowana dla klientów, którzy zamawiają funkcje odzyskiwania po awarii. Replikacja magazynu zależy od użycia migawek magazynu dla dużych wystąpień HANA. Nie można wybrać regionu platformy Azure jako regionu odzyskiwania po awarii, który znajduje się w innym obszarze geopolitycznym. 

W poniższej tabeli przedstawiono obecnie obsługiwane metody i kombinacje wysokiej dostępności i odzyskiwania po awarii:

| Scenariusz obsługiwany w dużych wystąpieniach HANA | Opcja wysokiej dostępności | Opcja odzyskiwania po awarii | Komentarze |
| --- | --- | --- | --- |
| Jeden węzeł | Niedostępne. | Dedykowana konfiguracja odzyskiwania po awarii.<br /> Konfiguracja wielofunkcyjnej pamięci DR. | |
| Automatyczne przewijowanie w trybie failover hosta: skalowanie w poziomie (ze wstrzymanie lub bez)<br /> w tym 1+1 | Możliwe przy podejmowaniu aktywnej roli w trybie czuwania.<br /> HANA steruje przełącznikiem roli. | Dedykowana konfiguracja odzyskiwania po awarii.<br /> Konfiguracja wielofunkcyjnej pamięci DR.<br /> Synchronizacja odzyskiwania po awarii przy użyciu replikacji magazynu. | Zestawy woluminów HANA są dołączone do wszystkich węzłów.<br /> Lokacja odzyskiwania po awarii musi mieć taką samą liczbę węzłów. |
| Replikacja systemu HANA | Możliwe w konfiguracji podstawowej lub pomocniczej.<br /> Pomocnicze przenosi się do roli podstawowej w przypadku pracy awaryjnej.<br /> Replikacja systemu HANA i sterowanie systemem operacyjnym w pracy awaryjnej. | Dedykowana konfiguracja odzyskiwania po awarii.<br /> Konfiguracja wielofunkcyjnej pamięci DR.<br /> Synchronizacja odzyskiwania po awarii przy użyciu replikacji magazynu.<br /> Odzyskiwania po awarii przy użyciu replikacji systemu HANA nie jest jeszcze możliwe bez składników innych firm. | Do każdego węzła jest dołączony oddzielny zestaw woluminów dyskowych.<br /> Tylko woluminy dysków repliki pomocniczej w lokacji produkcyjnej są replikowane do lokalizacji odzyskiwania po awarii.<br /> W lokacji odzyskiwania po awarii wymagany jest jeden zestaw woluminów. | 

Dedykowana konfiguracja odzyskiwania po awarii to miejsce, w którym jednostka dużych wystąpień HANA w lokacji odzyskiwania po awarii nie jest używana do uruchamiania innego obciążenia lub systemu nieprodukcyjnego. Jednostka jest pasywna i jest wdrażana tylko wtedy, gdy wykonywana jest awaria awaryjna. Jednak ta konfiguracja nie jest preferowanym wyborem dla wielu klientów.

Zapoznaj się z [scenariuszami obsługiwanymi przez HLI,](hana-supported-scenario.md) aby poznać układ magazynu i szczegóły sieci Ethernet dla twojej architektury.

> [!NOTE]
> [Wdrożenia MCOD SAP HANA](https://launchpad.support.sap.com/#/notes/1681092) (wiele wystąpień HANA na jednej jednostce) jako nakładające się scenariusze działają z metodami wysokiej klasy i odzyskiwania po awarii wymienionymi w tabeli. Wyjątkiem jest użycie replikacji systemu HANA z automatycznym klastrem trybu failover opartym na rozruszniku serca. Taki przypadek obsługuje tylko jedno wystąpienie HANA na jednostkę. W przypadku wdrożeń [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) tylko metody wysokiej klasy i odzyskiwania po awarii nieoparte na magazynach działają, jeśli wdrożono więcej niż jedną dzierżawę. Po wdrożeniu jednej dzierżawy wszystkie wymienione metody są prawidłowe.  

Konfiguracja wielofunkcyjnej pamięci dr. Zdrugiejsca to miejsce, w którym jednostka dużych wystąpień HANA w lokacji odzyskiwania po awarii uruchamia obciążenie nieprodukcyjne. W przypadku awarii zamknij system nieprodukcyjny, zainstaluj zreplikowane magazynowo (dodatkowe) zestawy woluminów, a następnie uruchom produkcyjne wystąpienie HANA. Większość klientów korzystających z funkcji odzyskiwania po awarii dużych wystąpień HANA korzysta z tej konfiguracji. 


Więcej informacji na temat wysokiej dostępności SAP HANA można znaleźć w następujących artykułach SAP: 

- [Oficjalny dokument SAP HANA o wysokiej dostępności](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Przewodnik administracyjny SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Wideo SAP HANA Academy na replikacji systemu SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Uwaga dotycząca obsługi SAP #1999880 — często zadawane pytania dotyczące replikacji systemu SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Uwaga dotycząca obsługi SAP #2165547 — SAP HANA — obsługa zapasowa i przywracanie w środowisku replikacji systemu SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Uwaga dotycząca obsługi SAP #1984882 — korzystanie z replikacji systemu SAP HANA do wymiany sprzętu z minimalnym/zerowym przestojem](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Zagadnienia dotyczące sieci odzyskiwania po awarii za pomocą dużych wystąpień HANA

Aby skorzystać z funkcji odzyskiwania po awarii dużych wystąpień HANA, należy zaprojektować łączność sieciową do dwóch regionów platformy Azure. Potrzebujesz połączenia obwodu usługi Azure ExpressRoute z lokalnego w głównym regionie platformy Azure i innego połączenia obwodu z lokalnego do regionu odzyskiwania po awarii. Ten środek obejmuje sytuację, w której występuje problem w regionie platformy Azure, w tym lokalizacji routera Microsoft Enterprise Edge Router (MSEE).

Jako drugi środek można połączyć wszystkie sieci wirtualne platformy Azure, które łączą się z SAP HANA na platformie Azure (duże wystąpienia) w jednym regionie do obwodu usługi ExpressRoute, który łączy duże wystąpienia HANA w innym regionie. Dzięki temu *krzyżowemu połączeniu*usługi uruchomione w sieci wirtualnej platformy Azure w regionie 1 mogą łączyć się z jednostkami dużych wystąpień HANA w regionie 2 i na odwrót. Ten środek dotyczy przypadku, w którym tylko jedna z lokalizacji MSEE, która łączy się z lokalizacją lokalną z platformą Azure przechodzi w tryb offline.

Na poniższej ilustracji przedstawiono odporną konfigurację w przypadku odzyskiwania po awarii:

![Optymalna konfiguracja do odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Inne wymagania dotyczące replikacji magazynu dużych wystąpień HANA w celu odzyskiwania po awarii

Oprócz powyższych wymagań dotyczących konfiguracji odzyskiwania po awarii z dużymi wystąpieniami HANA należy:

- Zamów sap HANA na jednostkach SKU platformy Azure (duże wystąpienia) o tym samym rozmiarze co jednostki SKU produkcyjne i wdrażaj je w regionie odzyskiwania po awarii. W bieżących wdrożeniach klientów te wystąpienia są używane do uruchamiania nieprodukcyjnych wystąpień HANA. Te konfiguracje są określane jako *konfiguracje wielofunkcyjnej odzyskiwania po awarii*.   
- Zamów dodatkowy magazyn w lokacji odzyskiwania po awarii dla każdego urządzenia SAP HANA na jednostkach SKU platformy Azure (duże wystąpienia), które chcesz odzyskać w lokacji odzyskiwania po awarii. Zakup dodatkowego magazynu umożliwia przydzielenie woluminów magazynu. Woluminy, które są celem replikacji magazynu z regionu platformy Azure produkcji do regionu platformy Azure odzyskiwania po awarii.
- W przypadku, gdy masz konfigurację HSR na podstawowej i skonfigurować replikację opartą na magazynie do lokacji odzyskiwania po awarii, należy zakupić dodatkowe miejsce do magazynowania w lokacji odzyskiwania po awarii, aby dane węzłów podstawowych i pomocniczych były replikowane do lokacji odzyskiwania po awarii.

  **Następne kroki**
- Poleć [utwór Kopia zapasowa i przywróć](hana-backup-restore.md).













