---
title: Omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL
description: Zawiera omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74481668"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co to jest usługa Azure Database for PostgreSQL?
Usługa Azure Database for PostgreSQL to relacyjne usługi bazy danych w chmurze firmy Microsoft utworzonej dla deweloperów. Jest on oparty na wersji społeczności open-source [PostgreSQL](https://www.postgresql.org/) aparatu bazy danych i jest dostępny w dwóch opcjach wdrażania: Single Server i Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL — pojedynczy serwer
Opcja wdrażania na jednym serwerze zapewnia:

- Wbudowana [wysoka dostępność](concepts-high-availability.md) bez dodatkowych kosztów (99,99% SLA)
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- [Skala pionowa](concepts-pricing-tiers.md) w razie potrzeby w ciągu kilku sekund
- [Monitorowanie i ostrzeganie](concepts-monitoring.md) w celu oceny serwera
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej
- [Zabezpieczone w celu ochrony](concepts-security.md) poufnych danych w spoczynku i w ruchu
- [Automatyczne tworzenie kopii zapasowych i przywracanie punktu w czasie](concepts-business-continuity.md) przez maksymalnie 35 dni


Wszystkie powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Pozwalają one skupić się na szybkim rozwoju aplikacji i przyspieszeniu czasu na rynku, zamiast spędzać cenny czas i zasoby do zarządzania maszynami wirtualnymi i infrastrukturą. Możesz nadal rozwijać swoją aplikację za pomocą narzędzi open source i wybranej platformy, bez konieczności uczenia się nowych umiejętności.

Opcja wdrażania na jednym serwerze oferuje trzy warstwy cenowe: podstawowe, ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Każda warstwa oferuje inne możliwości w zakresie zasobów, aby wspierać obciążenia bazy danych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz  [Warstwy cenowe](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Usługa Azure Database for PostgreSQL — hiperskala (Citus)
Opcja Hiperskala (Citus) skaluje zapytania w poziomie na wielu komputerach przy użyciu dzielenia na fragmenty. Jego aparat zapytań równoległościają przychodzące zapytania SQL na tych serwerach dla szybszych odpowiedzi na dużych zestawów danych. Obsługuje aplikacje, które wymagają większej skali i wydajności, zazwyczaj obciążeń, które zbliżają się — lub już przekraczają — 100 GB danych.

Opcja wdrażania w skali hiperskali (Citus) zapewnia:

- Skalowanie w poziomie na wielu komputerach przy użyciu dzielenia na fragmenty
- Równoległość zapytań na tych serwerach w celu uzyskania szybszych odpowiedzi na duże zestawy danych
- Doskonała obsługa aplikacji wielodostępnych, analiza operacyjna w czasie rzeczywistym i wysokie obciążenia transakcyjne o wysokiej przepływności

Aplikacje stworzone dla PostgreSQL mogą uruchamiać zapytania rozproszone na hiperskali (Citus) ze [standardowymi bibliotekami połączeń](./concepts-connection-libraries.md) i minimalnymi zmianami.

## <a name="contacts"></a>Kontakty
W przypadku jakichkolwiek pytań lub sugestii dotyczących pracy z usługą Azure Database for PostgreSQL należy wysłać wiadomość e-mail do usługi Azure Database for PostgreSQL Team[ @Ask (Azure DB for PostgreSQL).](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) Ten adres dotyczy pytań ogólnych, a nie biletów pomocy technicznej.

Ponadto należy odpowiednio rozważyć te punkty kontaktowe:
- Aby skontaktować się z pomocą techniczną platformy Azure lub rozwiązać problem z [kontem, złóż bilet w witrynie Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Następne kroki
- Zobacz [stronę cennik dla](https://azure.microsoft.com/pricing/details/postgresql/) porównania kosztów i kalkulatorów.
- Rozpocznij od utworzenia pierwszej bazy danych usługi Azure database dla [pojedynczego serwera](./quickstart-create-server-database-portal.md) postgreSQL lub [hiperskali (Citus)](./quickstart-create-hyperscale-portal.md)
- Skompiluj pierwszą aplikację w języku Python, PHP, Ruby, C\#, Java, Node.js: [Biblioteki połączeń](./concepts-connection-libraries.md)
