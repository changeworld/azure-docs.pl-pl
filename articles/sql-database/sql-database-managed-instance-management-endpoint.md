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
ms.date: 12/04/2018
ms.openlocfilehash: fc04422f5e32a77fff42dc07e2441e84c9b15866
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271882"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Odnajdywanie punktu końcowego zarządzania wystąpienia zarządzanego Azure SQL Database

Azure SQL Database Managed Instance [klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md) zawiera punkt końcowy zarządzania, używane przez firmę Microsoft do zarządzania wystąpienia zarządzanego. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory sieciowej poziomu i wzajemne certyfikat weryfikacji na poziomie aplikacji.

Gdy połączenia były inicjowane z wewnątrz wystąpienia zarządzanego (kopia zapasowa, dziennik inspekcji) wygląda na to, że ruch pochodzący z punktu końcowego zarządzania publicznego adresu IP. Można ograniczyć dostęp usług publicznych z wystąpieniem zarządzanym przez ustawienie reguły zapory zezwalające na tylko za pomocą adresu IP wystąpienia zarządzanego.

> [!NOTE]
> To nie ma zastosowania do ustawiania reguły zapory dla usług platformy Azure, które znajdują się w tym samym regionie, co wystąpienie zarządzane usługi platformy Azure ma optymalizacji dla ruchu, który przechodzi między usługami, które są zlokalizowana.

W tym artykule wyjaśniono, jak można pobrać zarządzania punktu końcowego publicznego adresu IP oraz jak można sprawdzić jego ochronę za pomocą wbudowanych zapory.

## <a name="finding-the-management-endpoint-public-ip-address"></a>Znajdowanie punktu końcowego na publiczny adres IP zarządzania

Załóżmy, że to wystąpienie zarządzane usługi hosta `mi-demo.xxxxxx.database.windows.net`. Uruchom `nslookup` przy użyciu nazwy hosta.

![Rozpoznawanie nazwy hosta wewnętrznego](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Teraz zrobić innego `nslookup` usuwania nazwy wyróżnione `.vnet.` segmentu. Przedstawiamy publiczny adres IP w wyniku wykonania tego polecenia.

![Rozpoznawanie publiczny adres IP](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-the-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego

Wystąpienie zarządzane [reguły zabezpieczeń ruchu przychodzącego obowiązkowe](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) wymaga portów zarządzania 9000, 9003, 1438, 1440, 1452 być otwarte z **dowolnego źródła** na grupy zabezpieczeń sieci (NSG), chroniące zarządzane Wystąpienie. Mimo że te porty zostały otwarte na poziomie sieciowych grup zabezpieczeń, są chronione na poziomie sieci przez zapory wbudowanych.

Aby sprawdzić, czy te porty, należy użyć dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak użyć jednej z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wystąpienia zarządzane przez usługę i łączność, zobacz [architektura łączności usługi Azure SQL bazy danych zarządzane wystąpienia](sql-database-managed-instance-connectivity-architecture.md).