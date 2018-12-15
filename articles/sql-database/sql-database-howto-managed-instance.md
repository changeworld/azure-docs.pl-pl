---
title: Jak skonfigurować wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i zarządzać nimi wystąpienia zarządzanego Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440492"
---
# <a name="how-to-use-managed-instance"></a>Jak używać wystąpienia zarządzanego

W tej sekcji można znaleźć różne przewodniki, skrypty i wyjaśnienia, które mogą ułatwić zarządzanie i konfigurowanie usługi Azure SQL Database — wystąpienie zarządzane.

## <a name="migration"></a>Migracja

- [Migrowanie do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md) — Dowiedz się więcej na temat procesu migracji zalecane i narzędzia do migracji do wystąpienia zarządzanego.

- [Migrowanie funkcji TDE certyfikatu do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md) — Jeśli bazy danych programu SQL Server jest chroniony za pomocą technologii transparent data encryption (TDE), należy przeprowadzić migrację certyfikatu, który jest wystąpienie zarządzane usługi można użyć do odszyfrowania kopii zapasowej, który chcesz przywrócić na platformie Azure.

## <a name="network-configuration"></a>Konfiguracja sieci

- [Należy określić rozmiar podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) — wystąpienie zarządzane jest umieszczany w przeznacza podsieci, której nie można zmienić rozmiaru po dodaniu zasobów w obrębie. W związku z tym należy obliczyć, zakres adresów IP adresów jest wymagana dla tej podsieci, w zależności od liczby i typów wystąpień, które mają zostać wdrożone w podsieci.
- [Tworzenie nowej sieci wirtualnej i podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-create-vnet-subnet.md) — sieci wirtualnej platformy Azure i podsieć, w której chcesz wdrożyć wystąpień zarządzanych muszą być skonfigurowane zgodnie z opisem w [wymagania opisane w tym miejscu sieciowego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). W tym przewodniku można znaleźć Najprostszym sposobem tworzenia nowej sieci wirtualnej i podsieci, poprawnie skonfigurowany na potrzeby wystąpienia zarządzane przez usługę.
- [Konfigurowanie istniejącej sieci wirtualnej i podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md) — Jeśli chcesz skonfigurować do wdrożenia wystąpienia zarządzanego wewnątrz istniejącej sieci wirtualnej i podsieci, w tym miejscu można znaleźć skryptu, który sprawdza, czy [wymagania dotyczące sieciowej](sql-database-managed-instance-connectivity-architecture.md#network-requirements) i Marka konfiguruje podsieci zgodnie z wymaganiami.
- [Konfigurowanie niestandardowych pozycji DNS](sql-database-managed-instance-custom-dns.md) — należy skonfigurować niestandardowe DNS, jeśli chcesz uzyskać dostęp do zasobów zewnętrznych dla domen niestandardowych z wystąpienia zarządzanego za pośrednictwem połączonego serwera profile poczty bazy danych.
- [Synchronizacja konfiguracji sieci](sql-database-managed-instance-sync-network-configuration.md) — może się zdarzyć, że chociaż możesz [zintegrować aplikację z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), możesz&#39;t ustanowić połączenia z wystąpieniem zarządzanym. Jest jedyną operacją, której możesz spróbować odświeżyć konfiguracji sieci dla planu usługi.
- [Znajdź adres IP punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md) — wystąpienie zarządzane usługi używa publicznego punktu końcowego, wyłącznie do celów zarządzania —. Można określić adres IP punktu końcowego zarządzania za pomocą skryptu, opisane w tym miejscu.
- [Sprawdzanie ochrony wbudowanej zapory](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) — wystąpienie zarządzane jest chroniony za pomocą wbudowanej zapory zezwalającą na ruch tylko na wymagane porty. Można sprawdzić i sprawdź reguły zapory wbudowanych, za pomocą skryptu, opisane w tym przewodniku.
- [Łączenie aplikacji](sql-database-managed-instance-connect-app.md) — wystąpienie zarządzane jest umieszczany w własnej prywatnej sieci wirtualnej platformy Azure za pomocą prywatnego adresu IP. Więcej informacji na temat różnych wzorców do łączenia aplikacji z wystąpieniem zarządzanym.

## <a name="feature-configuration"></a>Konfiguracja funkcji

- [Replikacja transakcyjna](replication-with-sql-database-managed-instance.md) umożliwia replikowanie danych między wystąpienia zarządzane przez usługę lub z lokalnego programu SQL Server do wystąpienia zarządzanego i na odwrót. Więcej informacji można znaleźć sposób używania i konfigurowania replikacji transakcji, w tym przewodniku.
- [Konfigurowanie wykrywania zagrożeń](sql-database-managed-instance-threat-detection.md) — [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) to wbudowana funkcja usługi Azure SQL Database, który wykryje różne wypadek potencjalnych ataków, takich jak wstrzykiwanie kodu SQL lub dostępu z podejrzanych lokalizacji. W tym przewodniku możesz dowiedzieć się, jak włączyć i skonfigurować [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla wystąpienia zarządzanego.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [instrukcje przeprowadza w pojedynczej bazy danych](sql-database-howto-single-database.md)