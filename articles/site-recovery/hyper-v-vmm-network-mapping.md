---
title: Mapowanie sieci funkcji Hyper-V (z programem VMM) z odzyskiwaniem witryny — informacje
description: W tym artykule opisano sposób konfigurowania mapowania sieci do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V (zarządzanych w chmurach programu VMM) na platformie Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082555"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Przygotowywanie mapowania sieci dla odzyskiwania po awarii maszyny Wirtualnej funkcji Hyper V na platformie Azure


Ten artykuł ułatwia zrozumienie i przygotowanie do mapowania sieci podczas replikowania maszyn wirtualnych funkcji Hyper-V w chmurach Menedżera maszyn wirtualnych (VMM) w centrum systemu na platformie Azure lub w lokacji dodatkowej przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Przygotowywanie mapowania sieci dla replikacji na platformę Azure

Podczas replikowania na platformę Azure mapowanie sieci między sieciami maszyn wirtualnych na źródłowym serwerze programu VMM i docelowe sieci wirtualne platformy Azure. Mapowanie wykonuje następujące czynności:
-  **Połączenie sieciowe**— zapewnia, że replikowane maszyny wirtualne platformy Azure są połączone z mapowaną siecią. Wszystkie maszyny, które działają awaryjnie w tej samej sieci, mogą łączyć się ze sobą, nawet jeśli zostały przejęte awaryjnie w różnych planach odzyskiwania.
- **Brama sieciowa**— jeśli brama sieciowa jest skonfigurowana w docelowej sieci platformy Azure, maszyny wirtualne mogą łączyć się z innymi lokalnymi maszynami wirtualnymi.

Mapowanie sieci działa w następujący sposób:

- Mapujesz źródłową sieć maszyn wirtualnych vmm na sieć wirtualną platformy Azure.
- Po przejściu awaryjnym maszyny wirtualne platformy Azure w sieci źródłowej zostaną połączone z mapowane docelowej sieci wirtualnej.
- Nowe maszyny wirtualne dodane do źródłowej sieci maszyn wirtualnych są połączone z mapowanymi sieciami platformy Azure po wystąpieniu replikacji.
- Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę, jak podsieć, w której znajduje się źródłowa maszyna wirtualna, replika maszyny wirtualnej jest łączona z tą docelową podsiecią po przejściu do trybu failover.
- Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna jest łączona z pierwszą podsiecią w sieci.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Przygotowywanie mapowania sieci dla replikacji do lokacji dodatkowej

Podczas replikowania do lokacji dodatkowej mapowanie sieci między sieciami maszyn wirtualnych na źródłowym serwerze programu VMM i sieciami maszyn wirtualnych na docelowym serwerze programu VMM. Mapowanie wykonuje następujące czynności:

- **Połączenie sieciowe**— łączy maszyny wirtualne z odpowiednimi sieciami po przełączeniu w błąd. Maszyna wirtualna repliki zostanie podłączona do sieci docelowej, która jest mapowana do sieci źródłowej.
- **Optymalne rozmieszczenie maszyn wirtualnych**— optymalnie umieszcza repliki maszyn wirtualnych na serwerach hosta funkcji Hyper-V. Maszyny wirtualne repliki są umieszczane na hostach, które mogą uzyskiwać dostęp do mapowanych sieci maszyn wirtualnych.
- **Brak mapowania sieci**— jeśli nie skonfigurujesz mapowania sieci, repliki maszyn wirtualnych nie będą połączone z żadnymi sieciami maszyn wirtualnych po przełączeniu awaryjnym.

Mapowanie sieci działa w następujący sposób:

- Mapowanie sieci można skonfigurować między sieciami maszyn wirtualnych na dwóch serwerach programu VMM lub na jednym serwerze programu VMM, jeśli dwie lokacje są zarządzane przez ten sam serwer.
- Gdy mapowanie jest poprawnie skonfigurowane i replikacja jest włączona, maszyna wirtualna w lokalizacji podstawowej zostanie połączona z siecią, a jej replika w lokalizacji docelowej zostanie połączona z zamapowanymi sieciami.
- Po wybraniu docelowej sieci maszyn wirtualnych podczas mapowania sieci w uczynku usługi Site Recovery zostaną wyświetlone chmury źródłowe programu VMM korzystające ze źródłowej sieci maszyn wirtualnych wraz z dostępnymi docelowymi sieciami maszyn wirtualnych w chmurach docelowych, które są używane do ochrony.
- Jeśli sieć docelowa ma wiele podsieci, a jedna z tych podsieci ma taką samą nazwę jak podsieć, na której znajduje się źródłocza maszyna wirtualna, replika maszyny wirtualnej zostanie połączona z tą podsiecią docelową po przełączeniu awaryjnym. Jeśli nie ma podsieci docelowej o pasującej nazwie, maszyna wirtualna zostanie połączona z pierwszą podsiecią w sieci.

## <a name="example"></a>Przykład

Oto przykład ilustrowania tego mechanizmu. Weźmy organizację z dwiema lokalizacjami w Nowym Jorku i Chicago.

**Lokalizacja** | **Serwer VMM** | **Sieci maszyn wirtualnych** | **Zamapowane na**
---|---|---|---
Nowy Jork | VMM-Nowy Jork| VMNetwork1-NewYork | Mapowane do VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Nie mapowane
Chicago | VMM-Chicago| VMNetwork1-Chicago | Zamapowane na VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Nie mapowane

W tym przykładzie:

- Gdy replika maszyny Wirtualnej jest tworzony dla dowolnej maszyny Wirtualnej, która jest podłączona do VMNetwork1-NewYork, zostanie połączony z VMNetwork1-Chicago.
- Gdy replika maszyny Wirtualnej jest tworzony dla VMNetwork2-NewYork lub VMNetwork2-Chicago, nie będzie podłączony do żadnej sieci.

Oto jak skonfigurowano chmury programu VMM w naszej przykładowej organizacji oraz sieci logiczne skojarzone z chmurami.

### <a name="cloud-protection-settings"></a>Ustawienia ochrony w chmurze

**Chmura chroniona** | **Ochrona chmury** | **Sieć logiczna (Nowy Jork)**  
---|---|---
GoldCloud1 (własnach) | Usługa GoldCloud2 |
SilverCloud1 (1)| SilverCloud2 (SilverCloud2) |
Usługa GoldCloud2 | <p>Nie dotyczy</p><p></p> | <p>LogicznaNetwork1-NewYork</p><p>LogiczneNetwork1-Chicago</p>
SilverCloud2 (SilverCloud2) | <p>Nie dotyczy</p><p></p> | <p>LogicznaNetwork1-NewYork</p><p>LogiczneNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Ustawienia sieci logiczne i wirtualne

**Lokalizacja** | **Sieć logiczna** | **Skojarzona sieć maszyn wirtualnych**
---|---|---
Nowy Jork | LogicznaNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogiczneNetwork1-Chicago | VMNetwork1-Chicago
 | LogiczneNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Docelowe ustawienia sieciowe

Na podstawie tych ustawień po wybraniu docelowej sieci maszyn wirtualnych w poniższej tabeli przedstawiono opcje, które będą dostępne.

**Wybierz** | **Chmura chroniona** | **Ochrona chmury** | **Dostępna sieć docelowa**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 (1) | SilverCloud2 (SilverCloud2) | Dostępne
 | GoldCloud1 (własnach) | Usługa GoldCloud2 | Dostępne
VMNetwork2-Chicago | SilverCloud1 (1) | SilverCloud2 (SilverCloud2) | Niedostępne
 | GoldCloud1 (własnach) | Usługa GoldCloud2 | Dostępne


Jeśli sieć docelowa ma wiele podsieci, a jedna z tych podsieci ma taką samą nazwę jak podsieć, na której znajduje się źródłocza maszyna wirtualna, maszyna wirtualna repliki zostanie połączona z tą podsiecią docelową po przełączeniu awaryjnym. Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna zostanie podłączona do pierwszej podsieci w sieci.


### <a name="failback-behavior"></a>Zachowanie powrotu po awarii

Aby zobaczyć, co się dzieje w przypadku powrotu po awarii (replikacja odwrotna), załóżmy, że VMNetwork1-NewYork jest mapowany na VMNetwork1-Chicago, z następującymi ustawieniami.


**Vm** | **Połączenie z siecią maszyn wirtualnych**
---|---
Maszyna wirtualna 1 | Sieć VMNetwork1
VM2 (replika VM1) | VMNetwork1-Chicago

Dzięki tym ustawieniom przejrzyjmy, co dzieje się w kilku możliwych scenariuszach.

**Scenariusz** | **Wynik**
---|---
Brak zmian we właściwościach sieci VM-2 po przemijaniu awaryjnym. | Maszyna wirtualna-1 pozostaje podłączona do sieci źródłowej.
Właściwości sieci VM-2 są zmieniane po przełączeniu w błąd i są rozłączane. | Maszyna wirtualna VM-1 jest rozłączona.
Właściwości sieci VM-2 są zmieniane po przełączeniu w pracę awaryjną i jest podłączony do VMNetwork2-Chicago. | Jeśli VMNetwork2-Chicago nie jest mapowany, VM-1 zostanie rozłączony.
Mapowanie sieci VMNetwork1-Chicago zostanie zmienione. | Maszyna wirtualna-1 zostanie podłączona do sieci, która jest teraz mapowana na sieć VMNetwork1-Chicago.



## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o](hyper-v-vmm-networking.md) Adres ip po przewijaniu awaryjnego do dodatkowej lokacji programu VMM.
- [Dowiedz się więcej o](concepts-on-premises-to-azure-networking.md) Adresowanie IP po przewijaniu awaryjnego na platformie Azure.
