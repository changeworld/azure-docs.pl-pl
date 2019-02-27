---
title: Mapowanie sieci wirtualnych między dwoma regionami platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Usługa Azure Site Recovery koordynuje replikację, tryb failover i odzyskiwania maszyn wirtualnych i serwerów fizycznych. Więcej informacji na temat trybu failover na platformie Azure lub dodatkowego centrum danych.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: b52bdd5948676c09c433cbda249248ce26ce8a48
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866032"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurowanie mapowania sieci i adresowania IP dla sieci wirtualnych

W tym artykule opisano sposób mapowania dwóch wystąpień z sieciami wirtualnymi platformy Azure (Vnet) znajduje się w różnych regionach platformy Azure oraz konfigurowanie adresów IP między sieciami. Mapowanie sieci zapewnia, że zreplikowanej maszyny Wirtualnej jest tworzony w lokalizacji docelowej usługi Azure region jest tworzony w sieci wirtualnej, która jest mapowana do sieci wirtualnej źródłowej maszyny Wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed mapowaniem sieci powinny mieć [sieciami wirtualnymi platformy Azure](../virtual-network/virtual-networks-overview.md) w źródle i określania elementów docelowych regionów platformy Azure. 

## <a name="set-up-network-mapping"></a>Konfigurowanie mapowania sieci

Mapowania sieci w następujący sposób:

1. W **infrastruktura usługi Site Recovery**, kliknij przycisk **+ mapowanie sieci**.

    ![ Tworzenie mapowania sieci](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. W **Dodawanie mapowania sieci**, wybierz źródło i lokalizacje docelowe. W naszym przykładzie źródło maszyny Wirtualnej jest uruchomiona w regionie Azja Wschodnia i są replikowane do regionu Azja południowo-wschodnia.

    ![Wybierz źródło i cel](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Teraz Utwórz mapowanie sieci w przeciwny katalogu. W naszym przykładzie źródło będzie teraz Azja południowo-wschodnia, a lokalizacją docelową będzie Azja Wschodnia.

    ![Dodaj okienko mapowania sieci — wybierz lokalizacje źródłowe i docelowe dla sieci docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapuj sieci po włączeniu replikacji

Jeśli nie zostały przygotowane, mapowanie sieci, przed rozpoczęciem konfigurowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, można określić obiekt docelowy sieci, gdy zostanie [Skonfiguruj i Włącz replikację](azure-to-azure-how-to-enable-replication.md). Po wykonaniu tej następujące konsekwencje:

- Oparte na docelowym, którą wybierzesz, Usługa Site Recovery automatycznie tworzy mapowania sieci ze źródła do docelowego regionu, a także z docelowej do regionu źródłowego.
- Domyślnie usługa Site Recovery tworzy sieć w regionie docelowym, który jest identyczny ze źródłową siecią. Usługa Site Recovery dodaje **— asr** jako sufiks do nazwy źródłowej sieci. Można dostosować sieci docelowej.
- Jeśli już wystąpiła mapowanie sieci, nie można zmienić docelowej sieci wirtualnej, po włączeniu replikacji. Aby zmienić docelowej sieci wirtualnej, musisz zmodyfikować istniejące mapowanie sieci.
- Jeśli zmodyfikujesz mapowania sieci z regionu, A do regionu B, upewnij się, również zmodyfikować mapowania sieci z regionu B do regionu A.]

## <a name="specify-a-subnet"></a>Określ podsieć

Podsieć docelowa wybrania maszyny Wirtualnej na podstawie nazwy podsieci źródłowej maszyny Wirtualnej.

- Jeśli podsieć z taką samą nazwę jak podsieci maszyny Wirtualnej źródłowego jest dostępny w sieci docelowej, podsieci jest ustawiona dla docelowej maszyny Wirtualnej.
- Jeśli podsieć o takiej samej nazwie nie istnieje w sieci docelowej, podsieci pierwszy w kolejności alfabetycznej jest ustawiony jako podsieci docelowej.
- Można modyfikować w **obliczenia i sieć** ustawień dla maszyny Wirtualnej.

    ![Obliczenia i sieć obliczenia okno właściwości](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Konfigurowanie adresowania IP dla docelowych maszyn wirtualnych

Adres IP dla każdego interfejsu Sieciowego docelowej maszyny wirtualnej są skonfigurowane w następujący sposób:

- **DHCP**: Jeśli karta sieciowa źródłowej maszyny Wirtualnej korzysta z protokołu DHCP, kart interfejsu Sieciowego docelowej maszyny Wirtualnej jest również ustawiona na korzystania z usługi DHCP.
- **Statyczny adres IP**: Jeśli karta sieciowa źródłowej maszyny Wirtualnej używa statycznego adresu IP, docelowej karty Sieciowej maszyny Wirtualnej również użyje statyczny adres IP.


## <a name="ip-address-assignment-during-failover"></a>Przypisywanie adresów IP podczas trybu failover

**Źródłowe i docelowe podsieci** | **Szczegóły**
--- | ---
Tą samą przestrzenią adresów | Adres IP źródłowej maszyny Wirtualnej karty Sieciowej jest ustawiony jako element docelowy adres IP karty Sieciowej maszyny Wirtualnej.<br/><br/> Jeśli adres nie jest dostępna, następny dostępny adres IP jest ustawiany jako element docelowy.
Różnymi przestrzeniami adresowymi<br/><br/> Następnym dostępnym adresem IP w podsieci docelowej jest ustawiany jako element docelowy adres karty Sieciowej maszyny Wirtualnej.



## <a name="ip-address-assignment-during-test-failover"></a>Przypisywanie adresów IP podczas testowania trybu failover

**Sieć docelowa** | **Szczegóły**
--- | ---
Docelowa sieć jest siecią wirtualną w tryb failover | -Docelowy adres IP jest statyczna, ale nie jako ten sam adres IP zarezerwowane dla trybu failover.<br/><br/>  -Przypisanego adresu jest następnym dostępnym adresem od końca zakresu podsieci.<br/><br/> Na przykład: Jeśli źródłowy adres IP jest 10.0.0.19 i trybu failover sieć używa zakresu 10.0.0.0/24, dalej adresu IP przypisanego do docelowej maszyny Wirtualnej jest 10.0.0.254.
Sieć docelowa nie jest trybem failover sieci wirtualnej | -Docelowy adres IP będzie statycznych przy użyciu tego samego adresu IP dla trybu failover.<br/><br/>  -Jeśli ten sam adres IP jest już przypisany, adres IP jest kolejny dostępny w ramach każdego zakresu podsieci.<br/><br/> Na przykład: Jeśli źródło statyczny adres IP jest 10.0.0.19 i trybu failover znajduje się w sieci, która nie jest sieć trybu failover, to przy użyciu zakresu 10.0.0.0/24, statyczny adres IP docelowego będzie 10.0.0.0.19, jeśli jest dostępny, a w przeciwnym razie będzie 10.0.0.254.

- Przełączenie w tryb failover w sieci wirtualnej jest sieci docelowej, która została wybrana, podczas konfigurowania odzyskiwania po awarii.
- Firma Microsoft zaleca, zawsze używać innych produkcyjnego środowiska sieciowego do testowania trybu failover.
- Możesz zmodyfikować docelowy adres IP w **obliczenia i sieć** ustawienia maszyny wirtualnej.


## <a name="next-steps"></a>Kolejne kroki

- Przegląd [sieć wskazówki](site-recovery-azure-to-azure-networking-guidance.md) do odzyskiwania po awarii maszyny Wirtualnej platformy Azure.
- [Dowiedz się więcej](site-recovery-retain-ip-azure-vm-failover.md) o zachowaniu adresy IP po włączeniu trybu failover.

Jeśli wybrana sieć docelowa sieć wirtualna trybu failover"i 2nd punkt powiedzieć"Jeśli sieć docelowa wybrana jest inna niż sieć trybu failover, ale ma tego samego zakresu podsieci co sieć wirtualną w tryb failover"