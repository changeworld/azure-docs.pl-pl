---
title: Wystąpienie zarządzane w sposób konfigurowania usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i zarządzać nimi wystąpienia zarządzanego usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 886f06e8640891ac09d1e4624335a7bfebcd3def
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60340795"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Jak używać wystąpienia zarządzanego usługi Azure SQL Database

W tym artykule można znaleźć różne przewodniki, skrypty i wyjaśnienia, które mogą ułatwić zarządzanie i konfigurowanie usługi wystąpienia zarządzanego.

## <a name="migration"></a>Migracja

- [Migrowanie do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md) — Dowiedz się więcej na temat procesu zalecane migracji i narzędzia do migracji do wystąpienia zarządzanego.

- [Migrowanie funkcji TDE certyfikatu do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md) — Jeśli bazy danych programu SQL Server jest chroniony za pomocą technologii transparent data encryption (TDE), należy przeprowadzić migrację certyfikatu, który wystąpienia zarządzanego, można użyć do odszyfrowania kopii zapasowej, który chcesz przywrócić na platformie Azure.

## <a name="network-configuration"></a>Konfiguracja sieci

- [Należy określić rozmiar podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) — wystąpienie zarządzane jest umieszczany w przeznacza podsieci, której nie można zmienić rozmiaru po dodaniu zasobów w obrębie. W związku z tym należy obliczyć, zakres adresów IP adresów jest wymagana dla tej podsieci, w zależności od liczby i typów wystąpień, które mają zostać wdrożone w podsieci.
- [Tworzenie nowej sieci wirtualnej i podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-create-vnet-subnet.md) — sieci wirtualnej platformy Azure i podsieć, w której chcesz wdrożyć wystąpień zarządzanych muszą być skonfigurowane zgodnie z opisem w [wymagania opisane w tym miejscu sieciowego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). W tym przewodniku można znaleźć Najprostszym sposobem tworzenia nowej sieci wirtualnej i podsieci, poprawnie skonfigurowany na potrzeby wystąpienia zarządzanego.
- [Konfigurowanie istniejącej sieci wirtualnej i podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md) — Jeśli chcesz skonfigurować wdrażanie wystąpień zarządzanych w istniejącej sieci wirtualnej i podsieci, w tym miejscu można znaleźć skryptu, który sprawdza, czy [wymagania dotyczące sieciowej](sql-database-managed-instance-connectivity-architecture.md#network-requirements) i Utwórz konfiguruje podsieci zgodnie z wymaganiami.
- [Konfigurowanie niestandardowych pozycji DNS](sql-database-managed-instance-custom-dns.md) — należy skonfigurować niestandardowe DNS, jeśli chcesz uzyskać dostęp do zasobów zewnętrznych dla domen niestandardowych z wystąpienia zarządzanego za pośrednictwem połączonego serwera profilów poczty bazy danych.
- [Synchronizacja konfiguracji sieci](sql-database-managed-instance-sync-network-configuration.md) — może się zdarzyć, że chociaż możesz [zintegrować aplikację z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), możesz&#39;t nawiązać połączenie z wystąpieniem zarządzanym. Jest jedyną operacją, której możesz spróbować odświeżyć konfiguracji sieci dla planu usługi.
- [Znajdź adres IP punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md) — wystąpienie zarządzane używa publicznego punktu końcowego dla celów zarządzania. Można określić adres IP punktu końcowego zarządzania za pomocą skryptu, opisane w tym miejscu.
- [Sprawdzanie ochrony wbudowanej zapory](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) — wystąpienie zarządzane jest chroniony za pomocą wbudowanej zapory zezwalającą na ruch tylko na wymagane porty. Można sprawdzić i sprawdź reguły zapory wbudowanych, za pomocą skryptu, opisane w tym przewodniku.
- [Łączenie aplikacji](sql-database-managed-instance-connect-app.md) — wystąpienie zarządzane jest umieszczany w własnej prywatnej sieci wirtualnej platformy Azure za pomocą prywatnego adresu IP. Więcej informacji na temat różnych wzorców do łączenia aplikacji z Twojego wystąpienia zarządzanego.

## <a name="feature-configuration"></a>Konfiguracja funkcji

- [Replikacja transakcyjna](replication-with-sql-database-managed-instance.md) umożliwia replikowanie danych między wystąpieniami zarządzanych lub z lokalnego programu SQL Server do wystąpienia zarządzanego i na odwrót. Więcej informacji można znaleźć sposób używania i konfigurowania replikacji transakcji, w tym przewodniku.
- [Konfigurowanie wykrywania zagrożeń](sql-database-managed-instance-threat-detection.md) — [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) to wbudowana funkcja usługi Azure SQL Database, który wykryje różne wypadek potencjalnych ataków, takich jak wstrzykiwanie kodu SQL lub dostępu z podejrzanych lokalizacji. W tym przewodniku możesz dowiedzieć się, jak włączyć i skonfigurować [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla wystąpienia zarządzanego.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [instrukcje prowadzi dla pojedynczych baz danych](sql-database-howto-single-database.md)