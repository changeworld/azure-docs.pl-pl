---
title: Mapowanie sieci wirtualnych między dwoma regionami w usłudze Azure Site Recovery
description: Dowiedz się więcej o mapowaniu sieci wirtualnych między dwoma regionami platformy Azure w celu odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258085"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurowanie mapowania sieci i adresowania IP dla sieci wirtualnych

W tym artykule opisano sposób mapowania dwóch wystąpień sieci wirtualnych platformy Azure (VNets) znajdujących się w różnych regionach platformy Azure oraz konfigurowania adresowania IP między sieciami. Mapowanie sieci zapewnia domyślne zachowanie wyboru sieci docelowej na podstawie sieci źródłowej w momencie włączania replikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed mapą sieci, należy mieć [sieci wirtualne platformy Azure](../virtual-network/virtual-networks-overview.md) w regionach źródłowych i docelowych platformy Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Ręczne konfigurowanie mapowania sieci (opcjonalnie)

Mapuj sieci w następujący sposób:

1. W **programie Site Recovery Infrastructure**kliknij pozycję **+Mapowanie sieci**.

    ![ Tworzenie mapowania sieci](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. W **obszarze Dodaj mapowanie sieci**wybierz lokalizacje źródłowe i docelowe. W naszym przykładzie źródłowa maszyna wirtualna jest uruchomiona w regionie Azji Wschodniej i replikuje się do regionu Azji Południowo-Wschodniej.

    ![Wybierz źródło i cel](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Teraz utwórz mapowanie sieci w przeciwnym kierunku. W naszym przykładzie źródłem będzie teraz Azja Południowo-Wschodnia, a celem będzie Azja Wschodnia.

    ![Dodawanie okienka mapowania sieci — wybieranie lokalizacji źródłowych i docelowych dla sieci docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapowanie sieci po włączeniu replikacji

Jeśli mapowanie sieci nie było przygotowane przed skonfigurowaniem odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, można określić sieć docelową podczas [konfigurowania i włączania replikacji](azure-to-azure-how-to-enable-replication.md). Po wykonaniu tej czynności dzieje się tak:

- Na podstawie wybranego obiektu docelowego usługa Site Recovery automatycznie tworzy mapowania sieciowe ze źródła do regionu docelowego oraz od obiektu docelowego do regionu źródłowego.
- Domyślnie usługa Site Recovery tworzy sieć w regionie docelowym, która jest identyczna z siecią źródłową. Usługa Site Recovery dodaje **-asr** jako sufiks do nazwy sieci źródłowej. Sieć docelową można dostosować.
- Jeśli mapowanie sieci zostało już zajdzie w przypadku sieci źródłowej, zamapowana sieć docelowa będzie zawsze domyślna w momencie włączania replikacji dla większej liczby maszyn wirtualnych. Docelową sieć wirtualną można zmienić, wybierając inne dostępne opcje z listy rozwijanej. 
- Aby zmienić domyślną docelową sieć wirtualną dla nowych replikacji, należy zmodyfikować istniejące mapowanie sieci.
- Jeśli chcesz zmodyfikować mapowanie sieci z regionu A do regionu B, upewnij się, że najpierw usuniesz mapowanie sieci z regionu B do regionu A. Po usunięciu mapowania wstecznego zmodyfikuj mapowanie sieci z regionu A do regionu B, a następnie utwórz odpowiednie mapowanie wsteczne.

>[!NOTE]
>* Modyfikowanie mapowania sieci zmienia tylko wartości domyślne dla nowych replikacji maszyn wirtualnych. Nie ma to wpływu na wybór docelowej sieci wirtualnej dla istniejących replikacji. 
>* Jeśli chcesz zmodyfikować sieć docelową dla istniejącej replikacji, przejdź do ustawień obliczeniowych i sieciowych z replikowanego elementu.

## <a name="specify-a-subnet"></a>Określanie podsieci

Podsieć docelowej maszyny Wirtualnej jest wybierana na podstawie nazwy podsieci źródłowej maszyny Wirtualnej.

- Jeśli podsieć o takiej samej nazwie jak źródłowsza podsieć maszyny Wirtualnej jest dostępna w sieci docelowej, ta podsieć jest ustawiona dla docelowej maszyny Wirtualnej.
- Jeśli podsieć o tej samej nazwie nie istnieje w sieci docelowej, pierwsza podsieć w kolejności alfabetycznej jest ustawiona jako podsieć docelowa.
- Podsieci docelowej można zmodyfikować w **ustawieniach obliczeń i sieci** dla maszyny Wirtualnej.

    ![Okno Właściwości obliczeniowe i sieciowe](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Konfigurowanie adresowania IP dla docelowych maszyn wirtualnych

Adres IP dla każdej karty sieciowej na docelowej maszynie wirtualnej jest skonfigurowany w następujący sposób:

- **DHCP**: Jeśli karta sieciowa źródłowej maszyny Wirtualnej korzysta z usługi DHCP, karta sieciowa docelowej maszyny Wirtualnej jest również ustawiona na użycie usługi DHCP.
- **Statyczny adres IP:** Jeśli karta sieciowa źródłowej maszyny Wirtualnej używa statycznego adresowania IP, docelowa karta wirtualna będzie również używać statycznego adresu IP.


## <a name="ip-address-assignment-during-failover"></a>Przypisywanie adresów IP podczas przełączania do trybu failover

**Podsieci źródłowe i docelowe** | **Szczegóły**
--- | ---
Ta sama przestrzeń adresowa | Adres IP źródłowej karty sieciowej maszyny Wirtualnej jest ustawiany jako docelowy adres IP karty wirtualnej.<br/><br/> Jeśli adres nie jest dostępny, następny dostępny adres IP jest ustawiany jako miejsce docelowe.
Inna przestrzeń adresowa | Następny dostępny adres IP w podsieci docelowej jest ustawiany jako docelowy adres karty wirtualnej.



## <a name="ip-address-assignment-during-test-failover"></a>Przypisywanie adresu IP podczas pracy awaryjnej testu

**Sieć docelowa** | **Szczegóły**
--- | ---
Sieć docelowa jest siecią wirtualną trybu failover | - Docelowy adres IP będzie statyczny z tym samym adresem IP. <br/><br/>  - Jeśli ten sam adres IP jest już przypisany, adres IP jest kolejnym dostępnym na końcu zakresu podsieci. Na przykład: Jeśli źródłowy adres IP to 10.0.0.19, a sieć trybu failover używa zakresu 10.0.0.0/24, następny adres IP przypisany do docelowej maszyny Wirtualnej to 10.0.0.254.
Sieć docelowa nie jest siecią wirtualną trybu failover | - Docelowy adres IP będzie statyczny z tym samym adresem IP.<br/><br/>  - Jeśli ten sam adres IP jest już przypisany, adres IP jest kolejnym dostępnym na końcu zakresu podsieci.<br/><br/> Na przykład: Jeśli źródłowy statyczny adres IP to 10.0.0.19, a praca awaryjna znajduje się w sieci, która nie jest siecią trybu failover, z zakresem 10.0.0.0/24, docelowy statyczny adres IP będzie miał 10.0.0.0.19, jeśli jest dostępny, a w przeciwnym razie będzie to 10.0.0.254.

- Sieć wirtualna trybu failover to sieć docelowa, którą można wybrać podczas konfigurowania odzyskiwania po awarii.
- Zaleca się, aby zawsze używać sieci nieprodukcyjnej do testowania pracy awaryjnej.
- Docelowy adres IP można zmodyfikować w ustawieniach **obliczeniowych i sieciowych** maszyny Wirtualnej.


## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wskazówki dotyczące sieci](site-recovery-azure-to-azure-networking-guidance.md) odzyskiwania po awarii maszyny Wirtualnej platformy Azure.
- [Dowiedz się więcej](site-recovery-retain-ip-azure-vm-failover.md) o zachowywaniu adresów IP po przemijeniu awaryjnym.
