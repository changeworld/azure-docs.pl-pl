---
title: Serwery — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Ten artykuł zawiera zagadnienia i wskazówki dotyczące konfigurowania i zarządzania usługą Azure Database dla postgreSQL — single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768167"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL — pojedynczy serwer
Ten artykuł zawiera zagadnienia i wskazówki dotyczące pracy z usługą Azure Database dla postgreSQL — single server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co to jest serwer usługi Azure Database for PostgreSQL?
Serwer w usłudze Azure Database for PostgreSQL — opcja wdrażania pojedynczego serwera jest centralnym punktem administracyjnym dla wielu baz danych. Jest to ta sama konstrukcja serwera PostgreSQL, którą możesz znać w środowisku lokalnym. W szczególności usługa PostgreSQL jest zarządzana, zapewnia gwarancje wydajności, udostępnia dostęp i funkcje na poziomie serwera.

Usługa Azure Database dla serwera PostgreSQL:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Udostępnia obszar nazw dla baz danych.
- Jest kontenerem z silną semantyką okresu istnienia — usuń serwer i usuwa zawarte bazy danych.
- Kolokuje zasoby w regionie.
- Zapewnia punkt końcowy połączenia dla dostępu do serwera i bazy danych 
- Udostępnia zakres zasad zarządzania, które mają zastosowanie do jego baz danych: login, firewall, użytkownicy, role, konfiguracje itp.
- Jest dostępny w wielu wersjach. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje bazy danych PostgreSQL](concepts-supported-versions.md).
- Jest rozszerzalny przez użytkowników. Aby uzyskać więcej informacji, zobacz [PostgreSQL rozszerzenia](concepts-extensions.md).

W ramach usługi Azure Database for PostgreSQL serwera można utworzyć jedną lub wiele baz danych. Można wybrać opcję utworzenia jednej bazy danych na serwer w celu wykorzystania wszystkich zasobów. Można też utworzyć wiele baz danych, współdzielących zasoby. Cennik jest skonstruowany na serwer, na podstawie konfiguracji warstwy cenowej, vCores i magazynu (GB). Aby uzyskać więcej informacji, zobacz [Warstwy cenowe](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Jak połączyć się i uwierzytelnić na serwerze Usługi Azure Database dla postgreSql?
Następujące elementy pomagają zapewnić bezpieczny dostęp do bazy danych:

|||
|:--|:--|
| **Uwierzytelnianie i autoryzacja** | Serwer usługi Azure Database for PostgreSQL obsługuje natywne uwierzytelnianie bazy danych PostgreSQL. Aby nawiązać połączenie z serwerem i przeprowadzić uwierzytelnianie na serwerze, można użyć identyfikatora logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na wiadomościach używany przez PostgreSQL. |
| **TCP/IP** | Protokół jest obsługiwany przez protokół TCP/IP i za pomocą gniazd domen uniksowych. |
| **Zapora** | Aby chronić dane, reguła zapory uniemożliwia dostęp do serwera i jego baz danych, dopóki nie określisz, które komputery mają uprawnienia. Zobacz [Usługa Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Zarządzanie serwerem
Usługę Azure Database dla serwerów PostgreSQL można zarządzać za pomocą [witryny Azure portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure.](/cli/azure/postgres)

Podczas tworzenia serwera, można skonfigurować poświadczenia dla użytkownika administratora. Użytkownik administratora jest najwyższym uprawnieniem użytkownika, jaki masz na serwerze. Należy do roli azure_pg_admin. Ta rola nie ma pełnych uprawnień superużytnika. 

Atrybut superużytnik PostgreSQL jest przypisany do azure_superuser, który należy do usługi zarządzanej. Nie masz dostępu do tej roli.

Serwer usługi Azure Database for PostgreSQL ma domyślne bazy danych: 
- **postgres** - domyślna baza danych, z którą można się połączyć po utworzeniu serwera.
- **azure_maintenance** — ta baza danych jest używana do oddzielania procesów, które zapewniają usługę zarządzaną od akcji użytkownika. Nie masz dostępu do tej bazy danych.
- **azure_sys** — baza danych magazynu zapytań. Ta baza danych nie gromadzi danych, gdy magazyn zapytań jest wyłączony; jest to ustawienie domyślne. Aby uzyskać więcej informacji, zobacz [omówienie Magazynu zapytań](concepts-query-store.md).


## <a name="server-parameters"></a>Parametry serwera
Parametry serwera PostgreSQL określają konfigurację serwera. W usłudze Azure Database for PostgreSQL listę parametrów można wyświetlać i edytować za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. 

Jako usługa zarządzana dla postgresu konfigurowalne parametry w usłudze Azure Database for PostgreSQL są podzbiorem parametrów w lokalnym wystąpieniu postgresu (aby uzyskać więcej informacji na temat parametrów postgres, zobacz [dokumentację PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Twój serwer usługi Azure Database for PostgreSQL jest włączony z wartościami domyślnymi dla każdego parametru podczas tworzenia. Niektóre parametry, które wymagają ponownego uruchomienia serwera lub dostępu administratora dla zmian, które mają zostać wprowadzone, nie mogą być skonfigurowane przez użytkownika.


## <a name="next-steps"></a>Następne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [Azure Database for PostgreSQL Overview](overview.md).
- Aby uzyskać informacje o określonych przydziałach zasobów i ograniczeniach opartych na **warstwie usług,** zobacz [Warstwy usług](concepts-pricing-tiers.md).
- Aby uzyskać informacje na temat łączenia się z usługą, zobacz [Biblioteki połączeń dla usługi Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Wyświetlanie i edytowanie parametrów serwera za pośrednictwem [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
