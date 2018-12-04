---
title: Odnajdywanie punktu końcowego zarządzania wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać wystąpienia zarządzanego Azure SQL Database management punktu końcowego publicznego adresu IP i sprawdź jej ochronę za pomocą wbudowanych zapory
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 99ec559429d66becc20e038e43349f5369afac39
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856259"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Odnajdywanie punktu końcowego zarządzania wystąpienia zarządzanego Azure SQL Database 

## <a name="overview"></a>Przegląd
Wystąpienie zarządzane SQL Azure [klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md) zawiera punkt końcowy zarządzania używane przez firmę Microsoft do zarządzania wystąpienia.  

Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory sieciowej poziomu i wzajemne certyfikat weryfikacji na poziomie aplikacji. 

Po zainicjowaniu połączenia z wewnątrz wystąpienia zarządzanego (CLR, połączonego serwera, kopie zapasowe, dziennik inspekcji, itp.) wygląda na to, że ruch pochodzi z adresu IP publicznego punktu końcowego zarządzania. Można ograniczyć dostęp usług publicznych z wystąpieniem zarządzanym przez ustawienie reguły zapory zezwalające na tylko ten adres IP.

> [!NOTE]
> To nie ma zastosowania do ustawiania reguły zapory dla usług platformy Azure, które znajdują się w tym samym regionie, co wystąpienie zarządzane usługi platformy ma optymalizacji dla ruchu, który przechodzi między usługami, które są zlokalizowana. 

W tym artykule wyjaśniono, jak można pobrać zarządzania punktu końcowego publicznego adresu IP oraz jak można sprawdzić jego ochronę za pomocą wbudowanych zapory.

## <a name="finding-management-endpoint-public-ip-address"></a>Znajdowanie punktu końcowego publiczny adres IP zarządzania
Załóżmy, że MI host to _mi demo.xxxxxx.database.windows.net_. Uruchom _nslookup_ przy użyciu nazwy hosta.

![Rozpoznawanie nazwy hosta wewnętrznego](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Teraz zrobić innego _nslookup_ wyróżnione nazwy z removed_.vnet._segment. Przedstawiamy publiczny adres IP w wyniku wykonania tego polecenia.

![Rozpoznawanie publiczny adres IP](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-managed-instance-built-in-firewall"></a>Sprawdzanie, czy Zapora wbudowane wystąpienia zarządzanego
Wystąpienie zarządzane [reguły zabezpieczeń ruchu przychodzącego obowiązkowe](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) wymaga portów zarządzania 9000, 9003, 1438, 1440, 1452 należy otworzyć z dowolnego źródła na sieciową grupę zabezpieczeń, która chroni wystąpienia zarządzanego. Mimo że te porty zostały otwarte na poziomie sieciowych grup zabezpieczeń, są chronione na poziomie sieci przez zapory wbudowanych.

Aby sprawdzić, czy te porty, możesz użyć dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak użyć jednej z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)
