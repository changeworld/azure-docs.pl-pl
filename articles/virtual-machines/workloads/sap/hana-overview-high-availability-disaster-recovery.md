---
title: Wysoka dostępność i odzyskiwanie po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Ustanów wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 078ce7e2acd93ecab6b37407f460672d4acb1853
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707324"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Duże wystąpienia SAP HANA wysoką dostępność i odzyskiwanie awaryjne na platformie Azure 

>[!IMPORTANT]
>Ta dokumentacja jest nie można zastąpić dokumentacji administracyjnej platformy SAP HANA lub SAP Notes. Oczekuje się, że czytelnik ma stałych zrozumienie i doświadczeń w administracyjnej platformy SAP HANA oraz operacji, szczególnie w przypadku tematów, tworzenie kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii.

Należy pamiętać, że wykonujesz kroki i procesy wykonywane w danym środowisku i z platformy HANA wersji i wydań. Niektóre procesy, opisane w tej dokumentacji są uproszczone, aby lepiej zrozumieć ogólny i nie są przeznaczone do służyć jako szczegółowy opis kroków podręczników ostatecznej operacji. Jeśli chcesz utworzyć podręczników operacja konfiguracji usługi, należy do testowania i wykonywania procesów i dokumentowanie procesów związanych z określonej konfiguracji. 


Wysoka dostępność i odzyskiwanie po awarii (DR) to kluczowe aspekty uruchomiony o kluczowym znaczeniu SAP HANA na serwerze usługi Azure (duże wystąpienia). Należy do pracy z SAP, integratora systemów lub Microsoft prawidłowo zaprojektować i zaimplementować odpowiednie wysokiej dostępności i strategie odzyskiwania po awarii. Należy również wziąć pod uwagę cel punktu odzyskiwania (RPO) i cel czasu odzyskiwania, które są specyficzne dla danego środowiska.

Firma Microsoft obsługuje niektóre funkcje wysokiej dostępności platformy SAP HANA z dużymi wystąpieniami platformy HANA. Te funkcje obejmują:

- **Replikacja magazynu**: System magazynowania możliwość replikowania wszystkie dane do innej sygnatury dużych wystąpień HANA w innym regionie platformy Azure. SAP HANA działa niezależnie od tej metody. Ta funkcja jest domyślny mechanizm odzyskiwania po awarii, oferowana w przypadku dużych wystąpień HANA.
- **Replikacji systemu HANA**: [Replikacji wszystkich danych na platformie SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do oddzielnego systemu SAP HANA. Cel czasu odzyskiwania jest zminimalizowany przez funkcję replikacji danych w regularnych odstępach czasu. SAP HANA obsługuje asynchroniczne, synchronicznej tryby w pamięci i synchroniczne. Synchroniczne tryb jest używany tylko w przypadku systemów SAP HANA, które znajdują się w tym samym centrum danych lub mniej niż 100 km od siebie. W bieżącym projekcie sygnatury dużych wystąpień HANA replikacji systemu HANA może służyć do wysokiej dostępności w ramach jednego regionu tylko. Replikacji systemu HANA wymaga zwrotny serwer proxy innych firm lub składnik routingu w przypadku konfiguracji odzyskiwania po awarii w innym regionie platformy Azure. 
- **Hostowanie automatyczny tryb failover**: Rozwiązania lokalnego odzyskiwania po uszkodzeniu platformy SAP Hana, który stanowi alternatywę dla replikacji systemu HANA. Jeśli węzła głównego staje się niedostępny, skonfiguruj rezerwy co najmniej jeden węzeł oprogramowania SAP HANA w trybie skalowalnego w poziomie i SAP HANA automatycznie przełącza się do węzła wstrzymania.

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jest oferowana w dwóch regionach platformy Azure w czterech obszarów geopolitycznych (USA, Australia, Europie i Japonia). Dwoma regionami w regionie geopolitycznym, obsługujące sygnatury dużych wystąpień HANA są podłączone do oddzielnej sieci dedykowanej obwodów. Są one używane do replikacji migawek magazynu zapewnienie metody odzyskiwania po awarii. Replikacja nie zostanie nawiązane domyślnie, ale jest skonfigurowane dla klientów, którzy kolejność funkcji odzyskiwania po awarii. Replikacja magazynu jest zależny od użycia magazynu migawek dla dużych wystąpień HANA. Nie jest możliwe wybrać region platformy Azure jako regionie odzyskiwania po awarii, który znajduje się w różnych obszarze geopolitycznych. 

W poniższej tabeli przedstawiono aktualnie obsługiwany wysoką dostępność i odzyskiwanie po awarii metod odzyskiwania i kombinacji:

| Scenariusz jest obsługiwany w dużych wystąpień HANA | Opcję wysokiej dostępności | Opcję odzyskiwania po awarii | Komentarze |
| --- | --- | --- | --- |
| Jeden węzeł | Niedostępne. | Dedykowany Konfiguracja odzyskiwania po awarii.<br /> Konfigurowanie odzyskiwania po awarii Multipurpose. | |
| Host automatyczny tryb failover: Skalowalny w poziomie (z lub bez w stanie wstrzymania)<br /> w tym 1 + 1 | Możliwa biorąc aktywną rolę w stanie wstrzymania.<br /> HANA kontrolki przełącznika roli. | Dedykowany Konfiguracja odzyskiwania po awarii.<br /> Konfigurowanie odzyskiwania po awarii Multipurpose.<br /> Synchronizacja odzyskiwania po awarii przy użyciu funkcji replikacji magazynu. | Zestawy platformy HANA są dołączone do wszystkich węzłów.<br /> Odzyskiwanie po awarii lokacji musi mieć taką samą liczbę węzłów. |
| Replikacji systemu HANA | Możliwa podstawowej lub dodatkowej konfiguracji.<br /> Pomocniczy przenosi podstawową rolą w przypadku pracy awaryjnej.<br /> Replikacji systemu HANA i system operacyjny sterujący trybu failover. | Dedykowany Konfiguracja odzyskiwania po awarii.<br /> Konfigurowanie odzyskiwania po awarii Multipurpose.<br /> Synchronizacja odzyskiwania po awarii przy użyciu funkcji replikacji magazynu.<br /> Odzyskiwanie po awarii za pomocą replikacji systemu HANA nie jest jeszcze możliwe bez składników innych firm. | Osobny zestaw woluminów dysków, które są dołączone do każdego węzła.<br /> Tylko dysku woluminy repliki pomocniczej w lokacji produkcyjnej są replikowane do lokalizacji odzyskiwania po awarii.<br /> Jeden zestaw woluminów jest wymagany w lokacji odzyskiwania po awarii. | 

Dedykowanej konfiguracji odzyskiwania po awarii jest, gdzie jednostka duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii nie jest używany do uruchamiania innych obciążeń lub system nieprodukcyjnych. Jednostka jest w stanie pasywnym i jest wdrażana tylko wtedy, gdy tryb failover po awarii jest wykonywany. Jednak ta konfiguracja nie jest preferowanych przez dla wielu klientów.

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) na naukę szczegółów sieci ethernet i układ pamięci masowej dla architektury.

> [!NOTE]
> [SAP HANA MCOD wdrożeń](https://launchpad.support.sap.com/#/notes/1681092) (wiele wystąpień HANA w jednej jednostce) nałożenie scenariusze pracy o wysokiej dostępności i odzyskiwania po awarii metody wymienione w tabeli. Wyjątkiem jest użycie replikacji systemu HANA z klastrem automatycznej pracy awaryjnej, w oparciu o program Pacemaker. Takiej sytuacji obsługuje tylko jedno wystąpienie oprogramowania HANA na jednostkę. Aby uzyskać [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) wdrożeń, tylko systemem magazynu o wysokiej dostępności i odzyskiwania po awarii metody działają, jeśli więcej niż jednej dzierżawy zostanie wdrożona. Za pomocą jednej dzierżawy wdrożone wszystkie metody wymienione są prawidłowe.  

Multipurpose konfiguracji odzyskiwania po awarii jest, gdzie jednostka duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii jest uruchamiany obciążeń nieprodukcyjnych. W razie awarii zamykania systemu nieprodukcyjnych instalowanie zestawów replikowany magazyn woluminów (dodatkowe), a następnie uruchom wystąpienie oprogramowania HANA w środowisku produkcyjnym. Większość klientów, którzy korzystają z funkcji odzyskiwania po awarii dużych wystąpień HANA używać tej konfiguracji. 


Więcej informacji na temat oprogramowania SAP HANA wysokiej dostępności można znaleźć w następujących artykułach SAP: 

- [Oficjalny dokument dotyczący programu SAP HANA wysoką dostępność](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Przewodnik administratora programu SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy wideo na replikacji systemu SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Obsługa Uwaga #1999880 — często zadawane pytania dotyczące replikacji systemu SAP HANA SAP](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Uwaga pomocy technicznej 2165547 # — SAP HANA tworzenie kopii zapasowej i przywracania w środowisku replikacji systemu HANA SAP](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Uwaga pomocy technicznej 1984882 # — za pomocą replikacji systemu SAP HANA dla wymiany sprzętu z co najmniej/żadnych przestojów](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Zagadnienia dotyczące sieci na potrzeby odzyskiwania po awarii przy użyciu dużych wystąpień HANA

Aby móc korzystać z funkcji odzyskiwania po awarii w dużych wystąpień HANA, należy zaprojektować połączenie sieciowe z dwóch regionach platformy Azure. Konieczne jest połączenie obwodu ExpressRoute systemu Azure ze środowiska lokalnego w główny region platformy Azure i inne połączenie obwodu ze środowiska lokalnego do regionu odzyskiwania po awarii. Ta miara dotyczy sytuacji, w którym występuje problem w regionie platformy Azure, w tym miejscu rozwiązania Microsoft Enterprise krawędzi routera (MSEE).

Jako druga Metryka używana możesz połączyć wszystkie sieci wirtualne platformy Azure, które łączą z platformą SAP HANA na platformie Azure (duże wystąpienia) w jednym regionie z obwodem usługi ExpressRoute, która łączy się z dużymi wystąpieniami platformy HANA w innym regionie. Dzięki temu *krzyżowe połączyć*, usług działających na platformie Azure sieci wirtualnej w regionie 1 podłączeniem jednostki duże wystąpienie oprogramowania HANA w regionie 2 i odwrotnie. Ta miara obsługuje przypadek, w którym tylko jeden lokalizacje MSEE, który nawiązuje połączenie z lokalizacji lokalnej z platformą Azure przejdzie do trybu offline.

Poniższa ilustracja przedstawia odporne na błędy konfiguracji w przypadku odzyskiwania po awarii:

![Optymalną konfigurację odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Inne wymagania przy użyciu dużych wystąpień HANA replikacji magazynu do odzyskiwania po awarii

Oprócz poprzedniego wymaganiami Instalator odzyskiwania po awarii przy użyciu dużych wystąpień HANA musisz mieć:

- Kolejność platformy SAP HANA na platformie Azure (duże wystąpienia) jednostek SKU w taki sam rozmiar jak jednostki SKU w środowisku produkcyjnym i wdróż je w regionie odzyskiwania po awarii. W bieżącym wdrożeń klienta te wystąpienia są używane do uruchamiania wystąpień HANA nieprodukcyjnych. Te konfiguracje są określane jako *multipurpose konfiguracji odzyskiwania po awarii*.   
- Kolejność dodatkowy magazyn w lokacji odzyskiwania po awarii dla każdego środowiska SAP HANA na platformie Azure (duże wystąpienia) jednostek SKU, które mają zostać odzyskane w lokacji odzyskiwania po awarii. Kupowania dodatkowego miejsca do magazynowania pozwala przydzielić odpowiedniej wielkości magazynu. Można alokować woluminy, które są celem replikacji magazynu z w środowisku produkcyjnym region platformy Azure do odzyskiwania po awarii w regionie platformy Azure.
- W przypadku, gdy skonfigurowano replikacji systemu HANA na maszynie podstawowej i Konfigurowanie replikacji magazynu opartego na lokacji odzyskiwania po awarii, musi zakupić dodatkowy magazyn w lokacji odzyskiwania po awarii więc podstawowych i dodatkowych węzłów danych pobiera replikowane do lokacji odzyskiwania po awarii.

  **Następne kroki**
- Zapoznaj się [kopii zapasowej i przywracania](hana-backup-restore.md).













