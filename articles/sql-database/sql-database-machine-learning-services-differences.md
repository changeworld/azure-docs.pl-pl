---
title: Podstawowe różnice dla usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database (wersja zapoznawcza) — omówienie
description: W tym temacie opisano podstawowe różnice między usługami Azure SQL Database Machine Learning (przy użyciu języka R) i SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: adc303e9b375aaa6f37e9e79ea5434675c75523d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824139"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Podstawowe różnice między usługami Machine Learning w usłudze Azure SQL Database i programu SQL Server

Funkcje usług Machine Learning Services (z językiem R) w usłudze Azure SQL Database są podobne do usług [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej przedstawiono niektóre podstawowe różnice między tymi.

## <a name="language-support"></a>Obsługa języków

Program SQL Server zapewnia obsługę języków R i Python przy użyciu [strukturę rozszerzalności](https://docs.microsoft.com/en-us/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nie obsługuje obu językach. Podstawowe różnice są następujące:

- R jest jedynym obsługiwanym językiem w bazie danych SQL. W tej chwili język Python nie jest obsługiwany.
- Wersja języka R jest 3.4.4.
- Nie ma potrzeby konfigurowania `external scripts enabled` za pośrednictwem `sp_configure`. Po wprowadzeniu [zarejestrowali](sql-database-machine-learning-services-overview.md#signup), usługi machine learning jest włączona dla usługi SQL database.

## <a name="package-management"></a>Zarządzanie pakietami

Zarządzanie pakietami języka R i instalacji pracy różnic między bazą danych SQL i programu SQL Server. Te różnice są następujące:

- Pakiety R są instalowane za pośrednictwem [sqlmlutils](https://github.com/Microsoft/sqlmlutils) lub [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakiety nie można wykonać połączenia sieciowego ruchu wychodzącego. To ograniczenie jest podobny do [domyślne reguły zapory dla usługi Machine Learning](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) w programie SQL Server, ale nie można zmienić w usłudze SQL Database.
- Nie jest obsługiwane dla pakietów, które są zależne od zewnętrznego środowiska uruchomieniowe (na przykład dla środowiska Java) lub muszą mieć dostęp do interfejsów API systemu operacyjnego w celu instalacji lub użycia.

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe do ograniczania zasobów języka R za pośrednictwem [zarządcy zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) i pule zasobów zewnętrznych. Zasoby języka R są procent zasobów bazy danych SQL i zależne w warstwie usługi, które wybierzesz. Aby uzyskać więcej informacji, zobacz [usługi Azure SQL Database, zakup modeli](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Izolacja na potrzeby zabezpieczeń

W usłudze Azure SQL Database SQL platformy Abstraction Layer (SQLPAL) zapewnia izolację procesów zewnętrznych. Izolacja zapewnia dodatkową warstwę zabezpieczeń do uruchamiania skryptów języka R.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [programu SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics) dokumentacji, aby uzyskać informacje ogólne
- Aby dowiedzieć się, jak używać usługi Machine Learning (przy użyciu języka R) w usłudze Azure SQL Database, zobacz [przewodnika Szybki Start](sql-database-connect-query-r.md).
- Dowiedz się więcej dzięki [samouczki języka SQL, R Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)