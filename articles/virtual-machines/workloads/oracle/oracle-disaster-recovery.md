---
title: Omówienie scenariusza odzyskiwania po awarii oprogramowania Oracle w środowisku platformy Azure | Dokumentacja firmy Microsoft
description: Scenariusz odzyskiwania po awarii dla bazy danych Oracle database 12c w środowisku platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: db0b9887b80f13938045a5d11fb09ed0a43efc19
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706968"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Odzyskiwanie po awarii dla bazy danych Oracle database 12c w środowisku platformy Azure

## <a name="assumptions"></a>Założenia

- Masz zrozumienie projektu środowiska Oracle Data Guard i środowisk platformy Azure.


## <a name="goals"></a>Cele
- Projektowanie topologii i konfiguracji, które spełniają wymagania odzyskiwania po awarii.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenariusz 1: Podstawowej i lokacji odzyskiwania po awarii na platformie Azure

Klient ma Oracle bazy danych zestaw w lokacji głównej. Element odzyskiwania po awarii, lokacja znajduje się w innym regionie. Klient używa środowiska Oracle Data Guard dla Szybkie odzyskiwanie między tymi lokacjami. Lokacja główna ma również dodatkowej bazy danych raportowania i do innych celów. 

### <a name="topology"></a>Topologia

Poniżej przedstawiono podsumowanie konfiguracji platformy Azure:

- Dwie witryny (lokacji głównej i lokacji odzyskiwania po awarii)
- Dwie sieci wirtualne
- Dwóch baz danych Oracle Data Guard (podstawowego i w gotowości)
- Dwóch baz danych Oracle Golden jednorazowych lub Data Guard (tylko w lokacji głównej)
- Dwie usługi aplikacji, jedną podstawową i jeden w lokacji odzyskiwania po awarii
- *Zestaw dostępności* używanej usługi bazy danych i aplikacji w lokacji głównej
- Jeden serwer przesiadkowy w każdej lokacji, która ogranicza dostęp do sieci prywatnej i może składać się logowania przez administratora
- Serwer przesiadkowy, usługi aplikacji, bazy danych i bramy sieci VPN w różnych podsieciach
- Sieciowa grupa zabezpieczeń, wymusić na aplikacji i podsieci bazy danych

![Zrzut ekranu przedstawiający stronę topologii odzyskiwania po awarii](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenariusz 2: Lokacja główna w środowisku lokalnym i lokacji odzyskiwania po awarii na platformie Azure

Klient ma Konfiguracja bazy danych Oracle lokalną (lokacja główna). Element odzyskiwania po awarii lokacji jest na platformie Azure. Środowiska Oracle Data Guard jest używana do szybkiego odzyskiwania między tymi lokacjami. Lokacja główna ma również dodatkowej bazy danych raportowania i do innych celów. 

Dostępne są dwie opcje tej instalacji.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Podejście 1: Bezpośrednich połączeń między wystąpieniami w środowisku lokalnym i platformą Azure, wymaga otwartych portów TCP w zaporze 

Nie zalecamy bezpośrednie połączenia, ponieważ ich udostępnianie portów TCP dla użytkowników zewnętrznych

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Odzyskiwania po awarii w jednej lokacji 
- Jedna sieć wirtualna
- Co baza danych Oracle przy użyciu funkcji Guard danych (aktywny)
- Usługa aplikacji w lokacji odzyskiwania po awarii
- Jeden serwer przesiadkowy, ogranicza dostęp do sieci prywatnej, co może składać się logowania przez administratora
- Serwer przesiadkowy, usługi aplikacji, bazy danych i bramy sieci VPN w różnych podsieciach
- Sieciowa grupa zabezpieczeń, wymusić na aplikacji i podsieci bazy danych
- Sieciowa grupa zabezpieczeń zasad/reguły zezwalającej na przychodzące TCP port 1521 bazy danych (lub zdefiniowany przez użytkownika port)
- Regułę sieciowej grupy zabezpieczeń zasad/ograniczyć tylko adres IP adres/adresów lokalnych (bazy danych lub aplikacji), dostęp do sieci wirtualnej

![Zrzut ekranu przedstawiający stronę topologii odzyskiwania po awarii](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Podejście 2: Sieci VPN typu lokacja lokacja
Sieci VPN typu lokacja lokacja jest lepszym rozwiązaniem. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN, zobacz [tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Odzyskiwania po awarii w jednej lokacji 
- Jedna sieć wirtualna 
- Co baza danych Oracle przy użyciu funkcji Guard danych (aktywny)
- Usługa aplikacji w lokacji odzyskiwania po awarii
- Jeden serwer przesiadkowy, ogranicza dostęp do sieci prywatnej, co może składać się logowania przez administratora
- Serwer przesiadkowy, usługi aplikacji, bazy danych i bramy sieci VPN są w różnych podsieciach
- Sieciowa grupa zabezpieczeń, wymusić na aplikacji i podsieci bazy danych
- Połączenia sieci VPN lokacja lokacja między lokalną i platformą Azure

![Zrzut ekranu przedstawiający stronę topologii odzyskiwania po awarii](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Materiały uzupełniające

- [Projektowanie i implementację z bazą danych Oracle na platformie Azure](oracle-design.md)
- [Konfigurowanie środowiska Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie bramy Golden Oracle](configure-oracle-golden-gate.md)
- [Oracle kopii zapasowych i odzyskiwania](oracle-backup-recovery.md)


## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Poznaj przykłady interfejsu wiersza polecenia Azure wdrażania maszyn wirtualnych](../../linux/cli-samples.md)
