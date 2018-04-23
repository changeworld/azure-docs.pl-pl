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
ms.date: 02/28/2018
ms.openlocfilehash: 2db18b014606799bdf5707c4c19f363bbc323e5c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
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

## <a name="how-do-i-manage-a-server"></a>Jak zarządzać serwerem?
Bazy danych Azure dla serwerów PostgreSQL można zarządzać za pomocą [portalu Azure](https://portal.azure.com) lub [interfejsu wiersza polecenia Azure](/cli/azure/postgres).

## <a name="server-parameters"></a>Parametry serwera
Parametry serwera PostgreSQL określić konfigurację serwera. W bazie danych Azure PostgreSQL lista parametrów można wyświetlić i edytować za pomocą portalu Azure lub interfejsu wiersza polecenia Azure. 

Jako usługa zarządzana Postgres, można konfigurować parametry w bazie danych Azure PostgreSQL są podzbiorem parametrów w lokalnym wystąpieniu Postgres (Aby uzyskać więcej informacji o parametrach Postgres, zobacz [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Bazy danych Azure, PostgreSQL serwera jest włączone w wartości domyślne dla każdego parametru po utworzeniu. Parametry, które wymagają serwera Uruchom lub dostępu administratora, aby zmiany zaczęły obowiązywać nie może zostać skonfigurowany przez użytkownika.


## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [bazy danych Azure omówienie PostgreSQL](overview.md).
- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](concepts-pricing-tiers.md).
- Aby uzyskać informacje na połączenie z usługą, zobacz [biblioteki połączeń dla bazy danych Azure dla PostgreSQL](concepts-connection-libraries.md).
- Wyświetl i Edytuj parametry serwera za pomocą [portalu Azure](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia Azure](howto-configure-server-parameters-using-cli.md).
