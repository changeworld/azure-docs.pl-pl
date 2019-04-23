---
title: Zabezpieczanie usługi Azure SQL Database wystąpienia zarządzanego publiczne punkty końcowe — wystąpienie zarządzane | Dokumentacja firmy Microsoft
description: Bezpiecznie używać publicznych punktów końcowych na platformie Azure za pomocą wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014020"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Za pomocą usługi Azure SQL Database managed wystąpienie bezpiecznie z publicznym punktem końcowym

Usługa Azure SQL Database, wystąpienia zarządzanego może zostać włączona w celu zapewnienia łączności z użytkownikami za pośrednictwem [publicznym punktem końcowym](../virtual-network/virtual-network-service-endpoints-overview.md). Ten artykuł zawiera wskazówki, jak zabezpieczyć tę konfigurację.

## <a name="scenarios"></a>Scenariusze

Wystąpienie zarządzane zapewnia prywatnych punktów końcowych, aby włączyć łączność z wewnątrz sieci wirtualnej. Jest to opcja domyślna do zapewnienia maksymalnej izolacji. Jednak istnieją scenariusze, w których wymagane jest połączenie z publicznym punktem końcowym:

- Integracja z wieloma dzierżawami tylko oferty rozwiązań PaaS.
- Większą przepływność wymiany danych niż przy użyciu sieci VPN.
- Zasady firmowe ze Stanów Zjednoczonych zabraniają PaaS wewnątrz sieci firmowej.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Wdrażanie wystąpienia zarządzanego dla publicznego dostępu do punktu końcowego

Chociaż nie jest to konieczne, wspólnego modelu wdrożenia wystąpienia zarządzanego przy użyciu dostępu publicznego punktu końcowego jest do utworzenia wystąpienia w dedykowanym izolowanej sieci wirtualnej. W tej konfiguracji sieci wirtualnej jest używany tylko w przypadku izolacji klaster wirtualny. Nie jest odpowiednie w przypadku wystąpienia zarządzanego przestrzeń adresów IP nakłada się na przestrzeń adresów IP sieci firmowej.

## <a name="securing-data-in-motion"></a>Zabezpieczanie danych w ruchu

Ruch związany z wystąpienia zarządzanego danych są zawsze szyfrowane, jeśli sterownik klienta obsługuje szyfrowanie. Dane pomiędzy wystąpienia zarządzanego i innych maszyn wirtualnych platformy Azure lub usług platformy Azure nigdy nie opuszcza sieci szkieletowej platformy Azure. W przypadku wystąpienia zarządzanego do środowiska lokalnego połączenia sieciowego, zaleca się używać Expressroute za pomocą komunikacji równorzędnej firmy Microsoft. Expressroute może pomóc uniknąć przenoszenia danych za pośrednictwem publicznej sieci Internet (wystąpienie zarządzane łączności prywatnych, tylko prywatnej komunikacji równorzędnej może być używane).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Zablokowanie ruchu przychodzącego i wychodzącego łączności

Na poniższym diagramie przedstawiono konfiguracji zabezpieczeń.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Wystąpienia zarządzanego istnieją [dedykowany adres publiczny punkt końcowy](sql-database-managed-instance-find-management-endpoint-ip-address.md). Ten adres IP powinna być równa w regułach sieciowej grupy zabezpieczeń i zapory dla ruchu wychodzącego po stronie klienta ograniczyć łączności wychodzącej.

Aby upewnić się, że ruch do wystąpienia zarządzanego pochodzi z zaufanego źródła, zaleca się połączyć ze źródeł za pomocą dobrze znanych adresów IP. Ograniczanie dostępu do wystąpienia zarządzanego publiczny punkt końcowy na porcie 3342 przy użyciu sieciowej grupy zabezpieczeń.

Gdy klienci potrzebne do nawiązania połączenia z siecią lokalną, upewnij się, że źródłowy adres jest tłumaczona na dobrze znany zbiór adresów IP. Jeśli nie, można osiągnąć (na przykład jest to typowy scenariusz pracowników mobilnych), zaleca się używać [Point-to-site VPN połączenia i prywatnych punktów końcowych](sql-database-managed-instance-configure-p2s.md).

Jeśli połączenia są uruchomione na platformie Azure, zaleca się że ruchu sieciowego pochodzi z dobrze znanych przypisane [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (na przykład maszyny wirtualne). W celu ułatwienia zarządzania adresów VIP, klienci mogą należy wziąć pod uwagę przy użyciu [publiczny prefiks adresu IP](../virtual-network/public-ip-address-prefix.md).