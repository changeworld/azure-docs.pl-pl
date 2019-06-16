---
title: Podstawowe różnice dla SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)
description: W tym temacie opisano podstawowe różnice między usługami Azure SQL Database Machine Learning (przy użyciu języka R) i SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 92785015a1ce122b8301b56fa62d122c8d95180c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725057"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Podstawowe różnice między usługami Machine Learning w usłudze Azure SQL Database (wersja zapoznawcza) i programu SQL Server

Funkcje usługi Azure SQL Database Machine Learning Services (przy użyciu języka R) (wersja zapoznawcza) jest podobny do [programu SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej przedstawiono niektóre podstawowe różnice.

> [!IMPORTANT]
> SQL Database usługi Azure Machine Learning jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Obsługa języków

Program SQL Server zapewnia obsługę języków R i Python przy użyciu [strukturę rozszerzalności](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nie obsługuje obu językach. Podstawowe różnice są następujące:

- R jest jedynym obsługiwanym językiem w bazie danych SQL. W tej chwili język Python nie jest obsługiwany.
- Wersja języka R jest 3.4.4.
- Nie ma potrzeby konfigurowania `external scripts enabled` za pośrednictwem `sp_configure`. Po wprowadzeniu [zarejestrowali](sql-database-machine-learning-services-overview.md#signup), usługi machine learning jest włączona dla usługi SQL database.

## <a name="package-management"></a>Zarządzanie pakietami

Zarządzanie pakietami języka R i instalacji pracy różnic między bazą danych SQL i programu SQL Server. Te różnice są następujące:

- Pakiety R są instalowane za pośrednictwem [sqlmlutils](https://github.com/Microsoft/sqlmlutils) lub [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakiety nie można wykonać połączenia sieciowego ruchu wychodzącego. To ograniczenie jest podobny do [domyślne reguły zapory dla usługi Machine Learning](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) w programie SQL Server, ale nie można zmienić w usłudze SQL Database.
- Nie jest obsługiwane dla pakietów, które są zależne od zewnętrznego środowiska uruchomieniowe (na przykład dla środowiska Java) lub muszą mieć dostęp do interfejsów API systemu operacyjnego w celu instalacji lub użycia.

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe do ograniczania zasobów języka R za pośrednictwem [zarządcy zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) i pule zasobów zewnętrznych.

Okresie publicznej wersji zapoznawczej zasoby języka R są ustawione na maksymalnie 20% zasobów bazy danych SQL i zależą od w warstwie usługi, które wybierzesz. Aby uzyskać więcej informacji, zobacz [usługi Azure SQL Database, zakup modeli](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Błąd braku pamięci

Jeśli jest dostępny dla języka R za mało pamięci, zostanie wyświetlony komunikat o błędzie. Typowe komunikaty o błędach są:

- Nie można nawiązać komunikacji ze środowiskiem uruchomieniowym na potrzeby skryptu "R" identyfikator żądania: ***. Sprawdź wymagania środowiska uruchomieniowego "R"
- "R" Wystąpił błąd skryptu podczas wykonywania "sp_execute_external_script" HRESULT 0x80004004. ... Zewnętrzny błąd skryptu: "... Nie można przydzielić pamięci (0 Mb) w funkcji języka C "R_AllocStringBuffer" "
- Wystąpił zewnętrzny błąd skryptu: Błąd: nie można przydzielić wektor rozmiar.

Użycie zależy od ilości pamięci jest używany w Twoje skrypty języka R oraz liczby zapytania równolegle wykonywane. Jeśli zostanie wyświetlony powyższe błędy, możesz skalować bazy danych na wyższą warstwę usługi, aby rozwiązać ten problem.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz Omówienie [usług Azure SQL Database Machine Learning (wersja zapoznawcza) języka r](sql-database-machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza), zobacz [przewodnika Szybki Start](sql-database-connect-query-r.md).
- Aby rozpocząć korzystanie z niektórych prostych skryptów języka R, zobacz [tworzenia i uruchamiania prostych skryptów języka R w usłudze Azure SQL Database usług Machine Learning (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).
