---
title: Jak skonfigurować wystąpienie zarządzane
description: Dowiedz się, jak skonfigurować wystąpienie zarządzane usługi Azure SQL Database i zarządzać nimi.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: b56375388f6293d27bcd2f2548d8b20205a92b15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638031"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Jak używać wystąpienia zarządzanego w bazie danych SQL usługi Azure

W tym artykule można znaleźć różne przewodniki, skrypty i wyjaśnienia, które mogą pomóc w zarządzaniu i konfigurowaniu wystąpienia zarządzanego.

## <a name="migration"></a>Migracja

- [Migrowanie do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md) — dowiedz się więcej o zalecanym procesie migracji i narzędziach migracji do wystąpienia zarządzanego.

- [Migrowanie certyfikatu TDE do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md) — jeśli baza danych programu SQL Server jest chroniona za pomocą przezroczystego szyfrowania danych (TDE), należy przeprowadzić migrację certyfikatu, którego wystąpienie zarządzane może użyć do odszyfrowania kopii zapasowej, którą chcesz przywrócić na platformie Azure.

## <a name="network-configuration"></a>Konfiguracja sieci

- [Określ rozmiar podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) — wystąpienie zarządzane jest umieszczane w podsieci dedykaty, której nie można zwymiarować po dodaniu zasobów wewnątrz. W związku z tym należy obliczyć, jaki zakres adresów IP będzie wymagany dla podsieci w zależności od liczby i typów wystąpień, które chcesz wdrożyć w podsieci.
- [Utwórz nową sieć wirtualną i podsieć dla wystąpienia zarządzanego](sql-database-managed-instance-create-vnet-subnet.md) — sieć wirtualna i podsieć platformy Azure, w których chcesz wdrożyć wystąpienia zarządzane, muszą być skonfigurowane zgodnie z [wymaganiami sieciowymi opisanymi w tym miejscu.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) W tym przewodniku można znaleźć najprostszy sposób, aby utworzyć nową sieć wirtualną i podsieć poprawnie skonfigurowane dla wystąpień zarządzanych.
- [Skonfiguruj istniejącą sieć wirtualną i podsieć dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md) — jeśli chcesz skonfigurować istniejącą sieć wirtualną i podsieć do wdrażania wystąpienia zarządzanych wewnątrz, w tym miejscu można znaleźć skrypt, który sprawdza [wymagania sieciowe](sql-database-managed-instance-connectivity-architecture.md#network-requirements) i konfiguruje podsieć zgodnie z wymaganiami.
- [Skonfiguruj niestandardowy system DNS](sql-database-managed-instance-custom-dns.md) — należy skonfigurować niestandardowy system DNS, jeśli chcesz uzyskać dostęp do zasobów zewnętrznych w domenach niestandardowych z wystąpienia zarządzanego za pośrednictwem połączonego serwera profilów poczty bazy danych.
- [Konfiguracja sieci synchronizacji](sql-database-managed-instance-sync-network-configuration.md) — może się zdarzyć, że mimo [zintegrowanej aplikacji z siecią wirtualną platformy Azure,](../app-service/web-sites-integrate-with-vnet.md)można&#39;t ustanowić połączenie z wystąpieniem zarządzanym. Jedną z rzeczy, które można spróbować jest odświeżenie konfiguracji sieci dla planu usług.
- [Znajdź adres IP punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md) — wystąpienie zarządzane używa publicznego punktu końcowego do celów zarządzania. Adres IP punktu końcowego zarządzania można określić za pomocą skryptu opisanego w tym miejscu.
- [Sprawdź wbudowaną ochronę zapory](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) — wystąpienie zarządzane jest chronione za pomocą wbudowanej zapory, która umożliwia ruch tylko na niezbędnych portach. Możesz sprawdzić i zweryfikować wbudowane reguły zapory za pomocą skryptu opisanego w tym przewodniku.
- [Połącz aplikacje](sql-database-managed-instance-connect-app.md) — wystąpienie zarządzane jest umieszczane we własnej prywatnej sieci wirtualnej platformy Azure z prywatnym adresem IP. Dowiedz się więcej o różnych wzorcach łączenia aplikacji z wystąpieniem zarządzanym.

## <a name="feature-configuration"></a>Konfiguracja funkcji

- [Replikacja transakcyjna](replication-with-sql-database-managed-instance.md) umożliwia replikowanie danych między wystąpieniami zarządzanymi lub z lokalnego programu SQL Server do wystąpienia zarządzanego i odwrotnie. Więcej informacji na temat używania i konfigurowania replikacji transakcji można znaleźć w tym przewodniku.
- [Skonfiguruj wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) — [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) to wbudowana funkcja usługi Azure SQL Database, która wykrywa różne potencjalne ataki, takie jak iniekcja SQL lub dostęp z podejrzanych lokalizacji. W tym przewodniku dowiesz się, jak włączyć i skonfigurować [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla wystąpienia zarządzanego.
- [Tworzenie alertów](sql-database-managed-instance-alerts.md) umożliwia konfigurowanie alertów na monitorowanych metrykach, takich jak wykorzystanie procesora CPU, zużycie miejsca do magazynowania, usługi We/Wy i inne dla wystąpienia zarządzanego. W tym przewodniku dowiesz się, jak włączyć i skonfigurować alerty dla wystąpienia zarządzanego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [poradnikach obsługi pojedynczych baz danych](sql-database-howto-single-database.md)
