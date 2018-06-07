---
title: Pojęcia dotyczące serwera bazy danych Azure PostgreSQL
description: Ten artykuł zawiera zagadnienia i zalecenia dotyczące konfigurowania i zarządzania dla serwerów PostgreSQL bazy danych Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/22/2018
ms.openlocfilehash: f877f6df51cd7aed29260331d27d5c96f0584afc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640017"
---
# <a name="azure-database-for-postgresql-servers"></a>Serwery usługi Azure Database for PostgreSQL
Ten artykuł zawiera zagadnienia i wskazówki dotyczące pracy z bazą danych Azure PostgreSQL serwerów.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co to jest Azure bazy danych serwera PostgreSQL?
Baza danych Azure dla serwera PostgreSQL jest centralny punkt administracyjny dla wielu baz danych. Jest tym samym konstrukcja PostgreSQL serwera można zapoznać się z na świecie lokalnych. W szczególności usługa PostgreSQL jest zarządzany, zapewnia gwarancje wydajności, udostępnia dostępu i funkcji na poziomie serwera.

Azure bazy danych programu PostgreSQL serwera:

- Zostanie utworzone w subskrypcji platformy Azure.
- Jest zasobem nadrzędnej dla baz danych.
- Obejmuje przestrzeń nazw dla baz danych.
- To kontener z semantyki silne istnienia — usuwanie serwera i usuwa zawartych baz danych.
- Collocates zasoby w regionie.
- Udostępnia punkt końcowy połączenia dla dostępu do serwera i bazy danych (. postgresql.database.azure.com).
- Określa zakres dla zasad zarządzania, które są stosowane do jej baz danych: logowania, zapory, użytkowników, ról, konfiguracje itd.
- Jest dostępna w różnych wersjach. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje bazy danych PostgreSQL](concepts-supported-versions.md).
- Jest otwarty przez użytkowników. Aby uzyskać więcej informacji, zobacz [PostgreSQL rozszerzenia](concepts-extensions.md).

W ramach Azure bazy danych programu PostgreSQL serwera można utworzyć jedną lub wiele baz danych. Można wybrać opcję utworzenia jednej bazy danych na serwer w celu wykorzystania wszystkich zasobów. Można też utworzyć wiele baz danych, współdzielących zasoby. Cennik jest strukturalnych dla poszczególnych serwerów, na podstawie konfiguracji warstwa cenowa, vCores i magazyn (GB). Aby uzyskać więcej informacji, zobacz [warstw cenowych](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Jak połączyć i uwierzytelniania z bazą danych PostgreSQL serwera Azure?
Następujące elementy zapewnić bezpieczny dostęp do bazy danych:

|||
|:--|:--|
| **Uwierzytelnianie i autoryzacja** | Azure bazy danych dla serwera PostgreSQL obsługuje natywnych uwierzytelnianie PostgreSQL. Możesz łączyć i uwierzytelniać się na serwerze z nazwą logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparta na komunikatach używany przez PostgreSQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP i za pośrednictwem gniazda domeny systemu Unix. |
| **Zapora** | Aby chronić dane, regułę zapory zapobiega dostęp do serwera i jego baz danych, do momentu określenia komputery, które ma uprawnienia. Zobacz [bazą danych Azure dla reguł zapory serwera PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Zarządzanie serwerem
Bazy danych Azure dla serwerów PostgreSQL można zarządzać za pomocą [portalu Azure](https://portal.azure.com) lub [interfejsu wiersza polecenia Azure](/cli/azure/postgres).

Podczas tworzenia serwera, skonfigurowaniu poświadczeń dla użytkownika administracyjnego. Administrator jest najwyższy użytkownika uprawnień, które masz na serwerze. Należy azure_pg_admin roli. Ta rola nie ma uprawnień administratora pełnego. 

Atrybut administratora PostgreSQL jest przypisany do azure_superuser, który należy do zarządzanych usług. Nie masz dostępu do tej roli.

Baza danych Azure dla serwera PostgreSQL ma dwie domyślne bazy danych: 
- **postgres** -utworzeniu domyślnej bazy danych, możesz połączyć się jeden raz z serwerem.
- **azure_maintenance** — ta baza danych jest używany do rozdzielania procesów, które dostarcza zarządzanej usługi z akcji użytkownika. Nie masz dostępu do tej bazy danych.


## <a name="server-parameters"></a>Parametry serwera
Parametry serwera PostgreSQL określić konfigurację serwera. W bazie danych Azure PostgreSQL lista parametrów można wyświetlić i edytować za pomocą portalu Azure lub interfejsu wiersza polecenia Azure. 

Jako usługa zarządzana Postgres, można konfigurować parametry w bazie danych Azure PostgreSQL są podzbiorem parametrów w lokalnym wystąpieniu Postgres (Aby uzyskać więcej informacji o parametrach Postgres, zobacz [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Bazy danych Azure, PostgreSQL serwera jest włączone w wartości domyślne dla każdego parametru po utworzeniu. Niektóre parametry, które wymagają serwera Uruchom lub dostępu administratora, aby zmiany zaczęły obowiązywać nie może zostać skonfigurowany przez użytkownika.


## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [bazy danych Azure omówienie PostgreSQL](overview.md).
- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](concepts-pricing-tiers.md).
- Aby uzyskać informacje na połączenie z usługą, zobacz [biblioteki połączeń dla bazy danych Azure dla PostgreSQL](concepts-connection-libraries.md).
- Wyświetl i Edytuj parametry serwera za pomocą [portalu Azure](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia Azure](howto-configure-server-parameters-using-cli.md).
