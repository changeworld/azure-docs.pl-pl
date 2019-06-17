---
title: Temat mapowania sieci na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V, (z programem VMM) do platformy Azure z usługą Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania mapowania sieci na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V (zarządzane w chmurach programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: d2f7f83654f397cc6b93adbbebc25193155bcedb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399373"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Przygotowywanie mapowania sieci na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure


Ten artykuł ułatwia zrozumienie i przygotowanie do mapowania sieci podczas replikowania maszyn wirtualnych z funkcją Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do platformy Azure lub lokacji dodatkowej przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Przygotowywanie mapowania sieci na potrzeby replikacji do platformy Azure

W przypadku replikowania do platformy Azure, mapowanie sieci działa między sieciami maszyny Wirtualnej na źródłowym serwerze programu VMM i docelowych, sieciami wirtualnymi platformy Azure. Mapowanie wykonuje następujące czynności:
-  **Połączenia sieciowego**— gwarantuje, czy replikowane maszyny wirtualne platformy Azure są podłączone do mapowanej sieci. Wszystkie maszyny, które w trybie Failover w tej samej sieci można połączyć ze sobą, nawet wtedy, gdy ich przełączone w tryb failover w planach odzyskiwania inny.
- **Brama sieci**— Jeśli brama sieci została skonfigurowana w docelowej sieci platformy Azure, maszyny wirtualne można połączyć się maszynami wirtualnymi w środowisku lokalnym.

Mapowanie sieci działa w następujący sposób:

- Źródłowej sieci maszyny Wirtualnej VMM są mapowane na sieć wirtualną platformy Azure.
- Po przełączeniu w tryb failover maszyn wirtualnych platformy Azure w źródłowej sieci będzie połączony z zamapowanego docelowa sieć wirtualna.
- Nowe maszyny wirtualne dodane do źródłowej sieci maszyny Wirtualnej są podłączone do mapowanej sieci platformy Azure, podczas replikacji.
- Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę, jak podsieć, w której znajduje się źródłowa maszyna wirtualna, replika maszyny wirtualnej jest łączona z tą docelową podsiecią po przejściu do trybu failover.
- Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna jest łączona z pierwszą podsiecią w sieci.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Przygotowywanie mapowania sieci na potrzeby replikacji do lokacji dodatkowej

Jeśli przeprowadzasz replikację do lokacji dodatkowej, mapowanie sieci działa między sieciami maszyny Wirtualnej na źródłowym serwerze programu VMM i sieci maszyn wirtualnych na docelowym serwerze VMM. Mapowanie wykonuje następujące czynności:

- **Połączenia sieciowego**— nawiązuje połączenie z maszynami wirtualnymi z odpowiednimi sieciami po włączeniu trybu failover. Maszyna wirtualna repliki zostaną podłączone do sieci docelowej, która jest mapowana na Sieć źródłowa.
- **Optymalne umieszczanie maszyny Wirtualnej**— optymalnie umieszcza replik maszyn wirtualnych na serwerach hostów funkcji Hyper-V. Maszyny wirtualne repliki są umieszczane na hostach, które mogą uzyskiwać dostęp do zamapowaną sieć maszyny Wirtualnej.
- **Nie mapowania sieci**— Jeśli nie skonfigurujesz mapowania sieci, maszyn wirtualnych repliki nie zostanie połączona z żadną siecią maszyny Wirtualnej po włączeniu trybu failover.

Mapowanie sieci działa w następujący sposób:

- Można skonfigurować mapowanie sieci między sieciami maszyny Wirtualnej na dwóch serwerach programu VMM lub na jednym serwerze programu VMM, jeśli dwie lokacje są zarządzane przez ten sam serwer.
- Podczas mapowania jest poprawnie skonfigurowana replikacja jest włączona, w lokalizacji podstawowej maszyny Wirtualnej zostaną podłączone do sieci i jego repliką w lokalizacji docelowej będą podłączone do jego mapowanej sieci.
- Po wybraniu opcji sieć maszyny Wirtualnej docelową podczas mapowania sieci w usłudze Site Recovery źródła chmurach programu VMM, które używają źródłowej sieci maszyny Wirtualnej zostanie wyświetlony, wraz z dostępnych sieci maszyn wirtualnych w chmurach docelowych, które są używane do ochrony.
- Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, na którym znajduje się źródłowa maszyna wirtualna, następnie repliki maszyny Wirtualnej zostanie podłączona do tej docelowej podsieci po pracy awaryjnej. Jeśli nie ma żadnych docelowa podsieć o takiej samej nazwie, maszyna wirtualna będzie połączony z pierwszej podsieci w sieci.

## <a name="example"></a>Przykład

Oto przykład, aby zilustrować ten mechanizm. Weźmy organizacji z dwóch lokalizacjach w Nowym Jorku i Chicago.

**Lokalizacja** | **Serwer VMM** | **Sieci maszyn wirtualnych** | **Mapowany do**
---|---|---|---
Nowy Jork | VMM-NewYork| VMNetwork1-NewYork | Mapowany do VMNetwork1 Chicago
 |  | VMNetwork2-NewYork | Niezamapowane
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapowany do VMNetwork1 NewYork
 | | VMNetwork2-Chicago | Niezamapowane

W tym przykładzie:

- Gdy replika maszyny Wirtualnej jest tworzona dla każdej maszyny Wirtualnej, która jest połączona VMNetwork1 NewYork, zostaną podłączone do VMNetwork1 Chicago.
- Gdy replika maszyny Wirtualnej jest tworzona dla VMNetwork2 NewYork lub VMNetwork2 Chicago, nie jest połączona z żadną siecią.

Oto, jak chmur programu VMM są zainstalowane w naszej przykładowej organizacji oraz sieci logicznych skojarzonych z chmurami.

### <a name="cloud-protection-settings"></a>Ustawienia ochrony chmury

**Chronioną chmurą** | **Ochrona chmury** | **Sieć logiczna (Nowy Jork)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Nie dotyczy</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>Nie dotyczy</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Ustawienia sieci logiczne i maszyn wirtualnych

**Lokalizacja** | **Sieć logiczna** | **Skojarzona żadna sieć maszyny Wirtualnej**
---|---|---
Nowy Jork | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Ustawienia sieci docelowej

Na podstawie tych ustawień, po wybraniu sieci docelowej maszyny Wirtualnej, w poniższej tabeli przedstawiono opcje, które będą dostępne.

**Select** | **Chronioną chmurą** | **Ochrona chmury** | **Dostępne sieci docelowej**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Dostępne
 | GoldCloud1 | GoldCloud2 | Dostępne
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Niedostępne
 | GoldCloud1 | GoldCloud2 | Dostępne


Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, na którym znajduje się źródłowa maszyna wirtualna, replika maszyny wirtualnej będzie można dołączenie do tej docelowej podsieci po włączeniu trybu failover. Jeśli nie istnieje docelowa podsieć o takiej samej nazwie, maszyna wirtualna zostanie podłączona do pierwszej podsieci w sieci.


### <a name="failback-behavior"></a>Zachowanie podczas powrotu po awarii

Aby zobaczyć, co się dzieje w przypadku powrotu po awarii (replikacja odwrotna), załóżmy, że VMNetwork1 NewYork jest mapowany do VMNetwork1 Chicago, z następującymi ustawieniami.


**VM** | **Połączone z siecią maszyny Wirtualnej**
---|---
Maszyna wirtualna 1 | VMNetwork1-Network
VM2 (repliki maszyny VM1) | VMNetwork1-Chicago

Przy użyciu tych ustawień Omówmy się, co się dzieje w kilka możliwych scenariuszy.

**Scenariusz** | **Wynik**
---|---
Brak zmian właściwości sieci maszyny Wirtualnej 2 po włączeniu trybu failover. | 1 maszyna wirtualna pozostaje połączone ze źródłową siecią.
Właściwości sieci maszyny Wirtualnej 2 zostaną zmienione po włączeniu trybu failover i jest odłączony. | 1 maszyna wirtualna jest odłączony.
Właściwości sieci maszyny Wirtualnej 2 zostały zmienione po włączeniu trybu failover, a jest połączona z VMNetwork2 Chicago. | Jeśli nie jest zamapowany VMNetwork2 Chicago, 1 maszyna wirtualna zostanie odłączony.
Mapowanie sieci VMNetwork1 Chicago zostanie zmieniony. | 1 maszyny Wirtualnej zostaną podłączone do sieci, teraz są mapowane do VMNetwork1 Chicago.



## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o](hyper-v-vmm-networking.md) adresów IP po włączeniu trybu failover do lokacji dodatkowej programu VMM.
- [Dowiedz się więcej o](concepts-on-premises-to-azure-networking.md) adresów IP po włączeniu trybu failover na platformie Azure.
