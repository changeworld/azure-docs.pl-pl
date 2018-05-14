---
title: Mapowanie sieci wirtualne od dwóch regionach platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Usługa Azure Site Recovery koordynuje replikację, tryb failover i odzyskiwania maszyn wirtualnych i serwerów fizycznych. Więcej informacji na temat trybu failover do platformy Azure lub dodatkowego centrum danych.
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
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9294940785deb0834a419de8320286783635d68e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mapowanie sieci wirtualnych w różnych regionach platformy Azure


W tym artykule opisano sposób mapowania sieci wirtualnej Azure znajduje się w różnych regionach platformy Azure ze sobą dwa wystąpienia. Mapowanie sieci zapewnia, że podczas replikowanej maszyny wirtualnej jest tworzony w docelowym regionie Azure, maszyna wirtualna tworzona jest również w sieci wirtualnej, który jest zamapowany do sieci wirtualnej dla źródłowej maszyny wirtualnej.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed mapowanie sieci, upewnij się, że utworzono [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) w regionie źródłowym i docelowym region platformy Azure.

## <a name="map-virtual-networks"></a>Mapowanie sieci wirtualnych

Do mapowania sieci wirtualnej platformy Azure, który znajduje się w jednym regionie Azure (Sieć źródłowa) do sieci wirtualnej, który znajduje się w innym regionie (Sieć docelowa), maszynach wirtualnych platformy Azure, przejdź do **infrastruktura usługi Site Recovery**  >  **Mapowania sieci**. Utwórz mapowania sieci.

![Okno mapowania sieci — Utwórz mapowania sieci](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


W poniższym przykładzie maszyna wirtualna jest uruchomiona w regionie, Azja Wschodnia. Maszyna wirtualna jest replikowana do regionu Azja południowo-wschodnia.

Aby utworzyć mapowanie sieci z regionu Azja Wschodnia, regionu Azja południowo-wschodnia, wybierz lokalizacji źródłowej sieci i lokalizacja sieci docelowej. Następnie wybierz przycisk **OK**.

![Dodaj okno mapowania sieci — wybierz lokalizacja źródłowa i docelowa dla źródłowej sieci](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Powtórz poprzedni proces tworzenia mapowania sieci z regionu Azja południowo-wschodnia dla regionu Azja Wschodnia.

![Dodawanie okienka mapowania sieci — wybierz lokalizacja źródłowa i docelowa dla sieci docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mapowanie sieci po włączeniu replikacji

Podczas replikacji maszyny wirtualnej z jednego regionu Azure do innego regionu po raz pierwszy, jeśli nie istnieje żadne mapowanie sieci, można ustawić sieci docelowej po skonfigurowaniu replikacji. Na podstawie tego ustawienia, usługi Azure Site Recovery tworzy mapowania sieci z regionu źródła do region docelowy i region docelowy do regionu źródła.   

![Skonfiguruj ustawienia okienka — wybierz lokalizację docelową](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Domyślnie usługa Site Recovery tworzy sieć w regionie docelowej, która jest taka sama jak Sieć źródłowa. Usługa Site Recovery tworzy sieci przez dodanie **— funkcja automatycznego odzyskiwania systemu** sufiks nazwy źródłowej sieci. Aby wybrać sieci, która została już utworzona, wybierz **Dostosuj**.

![Dostosowywanie okienka ustawień docelowy — nazwa grupy zasobów docelowych zestawu i nazwa sieci wirtualnej docelowego](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Jeśli już przeprowadzono mapowania sieci, nie można zmienić wirtualnej sieci docelowej po włączeniu replikacji. W takim przypadku można zmienić wirtualnej sieci docelowej, należy zmodyfikować istniejące mapowanie sieci.  

![Dostosowywanie docelowy okienka ustawień - Ustaw nazwę grupy zasobów docelowych](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Okienko mapowania sieci modyfikowania — zmodyfikować istniejącą nazwę docelowej sieci wirtualnej](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Zmodyfikowanie mapowania sieci z regionu A regionu B upewnij się również zmodyfikować mapowania sieci z regionu B regionu A.
>
>


## <a name="subnet-selection"></a>Wybór podsieci
Podsieci docelowej maszyny wirtualnej jest zaznaczone, na podstawie nazwy podsieci źródłowej maszyny wirtualnej. Jeśli podsieć, która ma taką samą nazwę jak źródłowej maszyny wirtualnej jest dostępny w sieci docelowej, podsieci ustawiono dla docelowej maszyny wirtualnej. Jeśli podsieć o takiej samej nazwie nie istnieje w sieci docelowej, alfabetycznie pierwszej podsieci jest ustawiony jako podsieci docelowej.

Aby zmodyfikować podsieci, przejdź do **obliczenia i sieć** ustawień dla maszyny wirtualnej.

![Okno właściwości obliczeń sieci i obliczeń](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Adres IP

Adres IP dla każdego interfejsu w sieci docelowej maszyny wirtualnej jest ustawiona zgodnie z opisem w poniższych sekcjach.

### <a name="dhcp"></a>DHCP
Interfejs sieciowy źródłowej maszyny wirtualnej korzysta z protokołu DHCP, interfejsu sieci docelowej maszyny wirtualnej jest również ustawić do używania protokołu DHCP.

### <a name="static-ip-address"></a>Statyczny adres IP
Interfejs sieciowy źródłowej maszyny wirtualnej używa statycznego adresu IP, interfejsu sieci docelowej maszyny wirtualnej jest również ustawić na używanie statycznego adresu IP. W poniższych sekcjach opisano konfiguracji statycznego adresu IP.

#### <a name="same-address-space"></a>Tą samą przestrzenią adresów

Jeśli podsieć źródłowej i docelowej podsieci z tą samą przestrzenią adresów, adres IP interfejsu sieciowego dla źródłowej maszyny wirtualnej jest ustawiony jako docelowy adres IP. Jeśli ten sam adres IP nie jest dostępna, następnego dostępnego adresu IP jest ustawiony jako docelowy adres IP.

#### <a name="different-address-spaces"></a>Funkcja miejsca do innego adresu

Jeśli podsieci źródłowej i docelowej podsieci ma inny adres spacje, następnego dostępnego adresu IP w podsieci docelowej jest ustawiony jako docelowy adres IP.

Aby zmodyfikować docelowy adres IP dla każdego interfejsu sieciowego, przejdź do **obliczenia i sieć** ustawień dla maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [wskazówki dotyczące replikowanie maszyn wirtualnych platformy Azure networking](site-recovery-azure-to-azure-networking-guidance.md).
