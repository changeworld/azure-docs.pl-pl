---
title: Omówienie scenariusza odzyskiwania po awarii programu Oracle w środowisku platformy Azure | Dokumentacja firmy Microsoft
description: Scenariusza odzyskiwania po awarii dla bazy danych Oracle 12c w środowisku platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.openlocfilehash: bb319c4ba9bbfba584803b35a0db0763fcf97b86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657865"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Odzyskiwanie po awarii dla bazy danych programu Oracle Database 12c w środowisku platformy Azure

## <a name="assumptions"></a>Wartości domyślne

- Należy dobrze poznać zasady środowiska platformy Azure i Oracle Data Guard projektu.


## <a name="goals"></a>Cele
- Projektowanie topologii i konfiguracji, które spełniają wymagania odzyskiwanie po awarii.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenariusz 1: Lokacji głównej i odzyskiwania po awarii lokacji na platformie Azure

Klient ma Oracle bazy danych zestawu w lokacji głównej. Lokacja A DR znajduje się w innym regionie. Klient używa Oracle Data Guard Szybkie odzyskiwanie między tymi lokacjami. Lokacja główna ma również pomocniczej bazy danych raportowania i innych celów. 

### <a name="topology"></a>Topologia

Poniżej przedstawiono podsumowanie konfiguracji platformy Azure:

- Dwie witryny (lokacji głównej i lokacji odzyskiwania po awarii)
- Dwie sieci wirtualne
- Dwóch baz danych Oracle Data Guard (podstawowe i stan gotowości)
- Dwóch baz danych Oracle Golden bramy lub Data Guard (tylko w lokacji głównej)
- Dwie usługi aplikacji, podstawowego i jeden w lokacji odzyskiwania po awarii
- *Zestawu dostępności* używany dla usługi bazy danych i aplikacji w lokacji głównej
- Jeden jumpbox w każdej lokacji, który ogranicza dostęp do sieci prywatnej i zezwala na logowanie przez administratora
- Jumpbox, usługa aplikacji, bazy danych i Brama sieci VPN w różnych podsieciach
- Grupa NSG wymuszone w aplikacji i podsieci bazy danych

![Zrzut ekranu przedstawiający stronę topologii DR](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenariusz 2: Lokalne lokacji głównej i lokacji odzyskiwania po awarii na platformie Azure

Klient ma Konfiguracja bazy danych Oracle lokalnymi (lokacji głównej). Lokacja A DR jest na platformie Azure. Oracle Data Guard służy do szybkiego odzyskania między tymi lokacjami. Lokacja główna ma również pomocniczej bazy danych raportowania i innych celów. 

Istnieją dwa podejścia do tej instalacji.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Podejście 1: Bezpośrednich połączeń między lokalną i platformą Azure, wymagających otwartych portów TCP w zaporze 

Nie zaleca się bezpośredniego połączenia, ponieważ udostępniają porty TCP, aby publicznie.

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Jeden odzyskiwania po awarii lokacji 
- Jedną sieć wirtualną
- Co baza danych Oracle przy użyciu funkcji Guard danych (aktywny)
- Usługa jedną aplikację w witrynie DR
- Jeden jumpbox, który ogranicza dostęp do sieci prywatnej i zezwala na logowanie przez administratora
- Jumpbox, usługa aplikacji, bazy danych i Brama sieci VPN w różnych podsieciach
- Grupa NSG wymuszone w aplikacji i podsieci bazy danych
- Grupa NSG zasad/Reguła zezwalająca na port wejściowy TCP 1521 (lub port zdefiniowane przez użytkownika)
- NSG/regułę ograniczyć tylko IP adres/adresów lokalnych (bazy danych lub aplikacji), dostęp do sieci wirtualnej

![Zrzut ekranu przedstawiający stronę topologii DR](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Podejście 2: VPN lokacja lokacja
Sieć VPN lokacja lokacja jest lepszym rozwiązaniem. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN, zobacz [tworzenie sieci wirtualnej za pomocą połączenia sieci VPN typu lokacja-lokacja za pomocą interfejsu wiersza polecenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Jeden odzyskiwania po awarii lokacji 
- Jedną sieć wirtualną 
- Co baza danych Oracle przy użyciu funkcji Guard danych (aktywny)
- Usługa jedną aplikację w witrynie DR
- Jeden jumpbox, który ogranicza dostęp do sieci prywatnej i zezwala na logowanie przez administratora
- Jumpbox, usługa aplikacji, bazy danych i Brama sieci VPN są w różnych podsieciach
- Grupa NSG wymuszone w aplikacji i podsieci bazy danych
- Połączenia sieci VPN lokacja lokacja między lokalną i platformą Azure

![Zrzut ekranu przedstawiający stronę topologii DR](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Dodatkowe materiały

- [Projektowania i implementacji bazy danych programu Oracle na platformie Azure](oracle-design.md)
- [Skonfiguruj Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie bramy Golden Oracle](configure-oracle-golden-gate.md)
- [Oracle kopii zapasowych i odzyskiwania](oracle-backup-recovery.md)


## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie maszyn wirtualnych wysokiej dostępności](../../linux/create-cli-complete.md)
- [Eksploruj przykłady Azure CLI wdrożenia maszyny Wirtualnej](../../linux/cli-samples.md)
