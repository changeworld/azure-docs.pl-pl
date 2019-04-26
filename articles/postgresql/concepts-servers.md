---
title: Pojęcia serwera w usłudze Azure Database for PostgreSQL
description: Ten artykuł zawiera zagadnienia i wytyczne dotyczące konfigurowania i zarządzanie usługą Azure Database for postgresql w warstwie serwerów.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: a5b6db9f4bfbe9a11119270e9236580b41fae8c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422103"
---
# <a name="azure-database-for-postgresql-servers"></a>Serwery usługi Azure Database for PostgreSQL
Ten artykuł zawiera zagadnienia i wytyczne dotyczące pracy z usługą Azure Database dla serwerów MySQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co to jest usługi Azure Database for postgresql w warstwie serwera?
Azure Database for postgresql w warstwie serwera to centralny punkt administracyjny dla wielu baz danych. Jest tego samego serwera PostgreSQL konstrukcja można zapoznać się z w środowisku lokalnym. W szczególności usługa PostgreSQL jest zarządzany, zapewnia gwarancje wydajności, uwidacznia dostępu i funkcje na poziomie serwera.

Azure Database for postgresql w warstwie serwera:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Zapewnia przestrzeń nazw dla baz danych.
- To kontener z silną semantyką okresu istnienia — usuwanie serwera, a następnie usuwane zawartych baz danych.
- Rozmieszcza zasoby w regionie.
- Udostępnia punkt końcowy połączenia dla serwera i dostępu do bazy danych 
- Określa zakres dla zasad zarządzania, które są stosowane do jego baz danych: logowania, zapory, użytkowników, ról, konfiguracji itp.
- Jest dostępna w wielu wersji. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje bazy danych postgresql w warstwie](concepts-supported-versions.md).
- Jest otwarty przez użytkowników. Aby uzyskać więcej informacji, zobacz [rozszerzenia PostgreSQL](concepts-extensions.md).

W ramach usługi Azure Database dla serwera PostgreSQL można utworzyć jeden lub wiele baz danych. Można wybrać opcję utworzenia jednej bazy danych na serwer w celu wykorzystania wszystkich zasobów. Można też utworzyć wiele baz danych, współdzielących zasoby. Cennik jest strukturą poszczególnych serwerów, na podstawie konfiguracji ceny warstwy, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [warstw cenowych](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Jak połączyć i uwierzytelniania usługi Azure Database for postgresql w warstwie serwera?
Następujące elementy zapewnić bezpieczny dostęp do bazy danych:

|||
|:--|:--|
| **Uwierzytelnianie i autoryzacja** | Usługa Azure Database for postgresql w warstwie serwera obsługuje uwierzytelnianie za pomocą natywnego PostgreSQL. Można połączyć i uwierzytelniania z serwerem przy użyciu identyfikatora logowania administratora serwera. |
| **Protokół** | Usługa obsługuje oparta na komunikatach protokół używany przez PostgreSQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP, a także za pośrednictwem gniazd domeny systemu Unix. |
| **Zapora** | Aby lepiej chronić swoje dane, reguła zapory uniemożliwia dostęp do serwera i jego baz danych do momentu określenia komputerów, które mają uprawnienia. Zobacz [— Azure Database for reguły zapory serwera PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Zarządzanie serwerem
Można zarządzać — Azure Database dla serwerów MySQL za pomocą [witryny Azure portal](https://portal.azure.com) lub [wiersza polecenia platformy Azure](/cli/azure/postgres).

Podczas tworzenia serwera, skonfigurowaniu poświadczeń dla użytkownika administracyjnego. Użytkownika administratora jest najwyższym użytkownika uprawnień, do którego masz na serwerze. Należy on do azure_pg_admin roli. Ta rola nie ma uprawnień administratora pełnego. 

Atrybut PostgreSQL administratora jest przypisywany do azure_superuser, która należy do zarządzanej usługi. Nie masz dostępu do tej roli.

Azure Database for postgresql w warstwie serwera zawiera domyślne bazy danych: 
- **postgres** — domyślna baza danych, można połączyć serwerowi po utworzeniu.
- **azure_maintenance** — ta baza danych jest używany do oddzielania procesów, które zapewniają usługę zarządzaną od działań użytkownika. Nie masz dostępu do tej bazy danych.
- **azure_sys** -Store kwerendy bazy danych. Ta baza danych nie są gromadzone dane, gdy Store zapytań jest wyłączony; to ustawienie domyślne. Aby uzyskać więcej informacji, zobacz [Przegląd Query Store](concepts-query-store.md).


## <a name="server-parameters"></a>Parametry serwera
Parametry serwera postgresql w warstwie określają konfigurację serwera. W usłudze Azure Database for PostgreSQL listę parametrów można wyświetlać i edytować za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. 

Jako usługa zarządzana dla programu Postgres konfigurowalne parametry w usłudze Azure Database for PostgreSQL są podzbiorem parametrów w lokalnym wystąpieniu Postgres (Aby uzyskać więcej informacji na temat parametrów Postgres, zobacz [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Usługi Azure Database for postgresql w warstwie serwera jest włączona z wartościami domyślnymi dla każdego parametru podczas ich tworzenia. Niektóre parametry, które wymagają serwera, uruchom ponownie, lub nie można skonfigurować dostęp administratora do zmiany zostały wprowadzone przez użytkownika.


## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [— Azure Database for postgresql — omówienie](overview.md).
- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](concepts-pricing-tiers.md).
- Aby uzyskać informacje na temat łączenia się z usługą, zobacz [biblioteki połączeń dla usługi Azure Database for postgresql w warstwie](concepts-connection-libraries.md).
- Wyświetlanie i edytowanie parametrów serwera za pomocą [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
