---
title: Mapowanie sieci wirtualnych między dwoma regionami platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Usługa Azure Site Recovery koordynuje replikację, tryb failover i odzyskiwania maszyn wirtualnych i serwerów fizycznych. Więcej informacji na temat trybu failover na platformie Azure lub dodatkowego centrum danych.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 7b7f9c079a1fc9d74fed4cc4d94d37f336ca5dc7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916744"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mapowanie sieci wirtualnych w różnych regionach platformy Azure


W tym artykule opisano sposób mapowania dwa wystąpienia usługi Azure Virtual Network znajduje się w różnych regionach platformy Azure ze sobą. Mapowanie sieci zapewnia, że po utworzeniu replikowanej maszyny wirtualnej w regionie platformy Azure w lokalizacji docelowej maszyny wirtualnej jest tworzony także na sieć wirtualną, która jest mapowana do sieci wirtualnej w źródłowej maszyny wirtualnej.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed mapowaniem sieci, upewnij się, że utworzono [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) zarówno w regionie źródłowym, jak i docelowego regionu platformy Azure.

## <a name="map-virtual-networks"></a>Mapowanie sieci wirtualnych

Aby zamapować siecią wirtualną platformy Azure, który znajduje się w jednym regionie platformy Azure (Sieć źródłowa) do sieci wirtualnej, który znajduje się w innym regionie (sieci docelowej), maszyn wirtualnych platformy Azure, przejdź do **infrastruktura usługi Site Recovery**  >  **Mapowania sieci**. Tworzenie mapowania sieci.

![Okno mapowania sieci — Tworzenie mapowania sieci](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


W poniższym przykładzie maszyna wirtualna jest uruchomiona w regionie Azja Wschodnia. Maszyna wirtualna jest replikowana do regionu Azja południowo-wschodnia.

Aby utworzyć mapowanie sieci z regionu Azja Wschodnia, w regionie Azja południowo-wschodnia, wybierz lokalizacji sieciowej, źródło i lokalizację sieci docelowej. Następnie wybierz przycisk **OK**.

![Dodaj okno mapowanie sieci — wybierz lokalizacje źródłowa i docelowa Sieć źródłowa](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Powtórz poprzedni proces tworzenia mapowania sieci z regionu Azja południowo-wschodnia, w regionie Azja Wschodnia.

![Dodaj okienko mapowania sieci — wybierz lokalizacje źródłowe i docelowe dla sieci docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mapowanie sieci, po włączeniu replikacji

Podczas replikowania maszyny wirtualnej między regionami platformy Azure do innego regionu po raz pierwszy, jeśli nie istnieje żadne mapowanie sieci, można ustawić sieci docelowej po skonfigurowaniu replikacji. Zależnie od tego ustawienia, usługi Azure Site Recovery tworzy mapowania sieci z regionu źródłowego do regionu docelowego i region docelowy region źródła.   

![Skonfiguruj ustawienia w okienku — Wybieranie lokalizacji docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Domyślnie usługa Site Recovery tworzy sieć w regionie docelowym, który jest identyczny ze źródłową siecią. Usługa Site Recovery tworzy sieć, dodając **— asr** jako sufiks do nazwy źródłowej sieci. Aby wybrać sieć, która została już utworzona, zaznacz **Dostosuj**.

![Dostosowywanie okienka ustawień target - Nazwa docelowej grupy zasobów zestawu i nazwa wirtualnej sieci docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Jeśli już wystąpiła mapowanie sieci, nie można zmienić docelowej sieci wirtualnej, po włączeniu replikacji. W takim przypadku można zmienić docelowa sieć wirtualna, należy zmodyfikować istniejące mapowanie sieci.  

![Dostosowywanie docelowy okienka ustawień - Ustaw nazwę grupy zasobów docelowych](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Modyfikowanie okienka mapowania sieci — zmodyfikuj istniejącą nazwę docelowej sieci wirtualnej](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Jeśli zmodyfikujesz mapowania sieci z regionu, A do regionu B, upewnij się, również zmodyfikować mapowania sieci z regionu B do regionu A.
>
>


## <a name="subnet-selection"></a>Wybór podsieci
Podsieci docelowej maszyny wirtualnej jest zaznaczone, na podstawie nazwy podsieci źródłowej maszyny wirtualnej. Jeśli podsieć, która ma taką samą nazwę jak źródłowa maszyna wirtualna jest dostępny w sieci docelowej, podsieci jest ustawiona dla docelowej maszyny wirtualnej. Jeśli podsieć o takiej samej nazwie nie istnieje w sieci docelowej, alfabetycznie pierwszej podsieci jest ustawiony jako podsieci docelowej.

Aby zmodyfikować podsieci, przejdź do **obliczenia i sieć** ustawień dla maszyny wirtualnej.

![Obliczenia i sieć obliczenia okno właściwości](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Adres IP

Adres IP dla każdego interfejsu sieciowego docelowej maszyny wirtualnej jest ustawiona, zgodnie z opisem w poniższych sekcjach.

### <a name="dhcp"></a>DHCP
Jeśli do interfejsu sieciowego źródłowej maszyny wirtualnej korzysta z protokołu DHCP, do interfejsu sieciowego docelowej maszyny wirtualnej jest również ustawiona na korzystania z usługi DHCP.

### <a name="static-ip-address"></a>Statyczny adres IP
Jeśli do interfejsu sieciowego źródłowej maszyny wirtualnej używa statycznego adresu IP, do interfejsu sieciowego docelowej maszyny wirtualnej jest również ustawiona na używanie statycznego adresu IP. W poniższych sekcjach opisano, jak statyczny adres IP jest ustawiony.

#### <a name="same-address-space"></a>Tą samą przestrzenią adresów

Jeśli podsieć źródłowa i docelowa podsieć ma tą samą przestrzenią adresów, adres IP interfejsu sieciowego źródłowej maszyny wirtualnej jest ustawiony jako docelowy adres IP. Jeśli ten sam adres IP nie jest dostępna, następny dostępny adres IP jest ustawiony jako docelowy adres IP.

#### <a name="different-address-spaces"></a>Różne przestrzenie adresowe

Jeśli podsieć źródłowa i docelowa podsieć ma różne przestrzenie adresowe, następnym dostępnym adresem IP w podsieci docelowej jest ustawiony jako docelowy adres IP.

Aby zmodyfikować docelowy adres IP dla każdego interfejsu sieciowego, przejdź do **obliczenia i sieć** ustawień dla maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [wskazówki dotyczące replikowania maszyn wirtualnych platformy Azure networking](site-recovery-azure-to-azure-networking-guidance.md).
