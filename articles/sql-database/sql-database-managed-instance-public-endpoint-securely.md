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
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470309"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Bezpieczne korzystanie z wystąpienia zarządzanego usługi Azure SQL Database z publicznymi punktami końcowymi

Usługa Azure SQL Database, wystąpienia zarządzanego może udostępniać łączności z użytkownikami za pośrednictwem [publiczne punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md). W tym artykule wyjaśniono, jak zabezpieczyć tę konfigurację.

## <a name="scenarios"></a>Scenariusze

Zarządzanego wystąpienia bazy danych SQL Database zapewnia prywatnych punktów końcowych, aby zezwolić na połączenie z wewnątrz sieci wirtualnej. Jest to opcja domyślna do zapewnienia maksymalnej izolacji. Jednak istnieją scenariusze, w którym należy podać połączenia publicznego punktu końcowego:

- Wystąpienie zarządzane należy zintegrować z wielu-tenant tylko oferty (PaaS) platformy jako usługi.
- Potrzebujesz większej przepływności wymiany danych niż jest możliwe, podczas korzystania z sieci VPN.
- Zasady firmowe ze Stanów Zjednoczonych zabraniają PaaS wewnątrz sieci firmowej.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Wdrażanie wystąpienia zarządzanego dostępu publicznego punktu końcowego

Chociaż nie jest to konieczne, wspólnego modelu wdrożenia wystąpienia zarządzanego przy użyciu dostępu publicznego punktu końcowego jest do utworzenia wystąpienia w dedykowanym izolowanej sieci wirtualnej. W tej konfiguracji sieci wirtualnej jest używany tylko w przypadku izolacji klaster wirtualny. Nie ma znaczenia, jeśli przestrzeń adresów IP dla wystąpienia zarządzanego nakłada się na przestrzeń adresów IP w sieci firmowej.

## <a name="secure-data-in-motion"></a>Zabezpieczanie danych w ruchu

Ruch związany z wystąpienia zarządzanego danych są zawsze szyfrowane, jeśli sterownik klienta obsługuje szyfrowanie. Dane przesyłane między wystąpienia zarządzanego i innych maszyn wirtualnych platformy Azure lub usług platformy Azure nigdy nie opuszcza sieci szkieletowej platformy Azure. Jeśli istnieje połączenie między wystąpienia zarządzanego i sieci lokalnej, zalecane jest usługa Azure ExpressRoute za pomocą komunikacji równorzędnej firmy Microsoft. Usługa ExpressRoute ułatwia Tobie należy unikać przenoszenia danych za pośrednictwem publicznej sieci internet. Wystąpienie zarządzane łączności prywatnych tylko prywatnej komunikacji równorzędnej może służyć.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Zablokować połączenia przychodzące i wychodzące

Na poniższym diagramie przedstawiono konfiguracje zabezpieczeń:

![Konfiguracje zabezpieczeń blokowania dla ruchu przychodzącego i wychodzącego łączności](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Wystąpienia zarządzanego istnieją [dedykowany adres publiczny punkt końcowy](sql-database-managed-instance-find-management-endpoint-ip-address.md). W zapory dla ruchu wychodzącego po stronie klienta i reguły sieciowej grupy zabezpieczeń należy ustawić ten adres IP publicznego punktu końcowego, aby ograniczyć łączności wychodzącej.

Aby upewnić się, że ruch do wystąpienia zarządzanego pochodzi z zaufanego źródła, zaleca się podłączania ze źródeł za pomocą dobrze znanych adresów IP. Aby ograniczyć dostęp do wystąpienia zarządzanego publiczny punkt końcowy na porcie 3342, należy użyć sieciowej grupy zabezpieczeń.

Gdy klienci potrzebne do nawiązania połączenia z siecią lokalną, upewnij się, że źródłowy adres jest tłumaczona na dobrze znany zbiór adresów IP. Jeśli nie możesz zrobić, aby (na przykład jest to typowy scenariusz pracowników mobilnych), zalecane jest użycie [połączeń VPN typu punkt lokacja i prywatnych punktów końcowych](sql-database-managed-instance-configure-p2s.md).

Jeśli połączenia są uruchomione na platformie Azure, zaleca się że pochodzą ruchu dobrze znanym przypisane [wirtualnego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md) (na przykład maszyna wirtualna). Aby Zarządzanie wirtualnymi adresami IP (VIP), łatwiej, warto użyć [prefiksy publicznych adresów IP](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak skonfigurować publiczny punkt końcowy zarządzania wystąpień: [Konfigurowanie publicznego punktu końcowego](sql-database-managed-instance-public-endpoint-configure.md)
