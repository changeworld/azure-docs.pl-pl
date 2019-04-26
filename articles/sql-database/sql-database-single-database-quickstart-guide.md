---
title: Szybki start — pojedyncze bazy danych w usłudze Azure SQL Database | Microsoft Docs
description: Dowiedz się, jak szybko rozpocząć pracę z pojedynczymi bazami danych w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0dc9d18ea4dae6ea0f45d1a91392eb1994e6e80e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331693"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Wprowadzenie do pojedynczych baz danych w usłudze Azure SQL Database

[Pojedyncza baza danych](sql-database-single-index.yml) to w pełni zarządzana baza danych jako usługa (DbaaS), która jest doskonałym aparatem magazynu dla nowoczesnych aplikacji zaprojektowanych pod kątem chmury. W tej sekcji dowiesz się, jak szybko skonfigurować i utworzyć pojedynczą bazę danych w usłudze SQL Database.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

W tej sekcji możesz zobaczyć przegląd dostępnych artykułów, które ułatwiają szybkie rozpoczęcie pracy z pojedynczymi bazami danych. Następujące przewodniki Szybki start umożliwiają szybkie tworzenie pojedynczej bazy danych, konfigurowanie reguły zapory serwera bazy danych, a następnie importowanie bazy danych do nowej pojedynczej bazy danych przy użyciu pliku `.bacpac`:

- [Tworzenie pojedynczej bazy danych za pomocą witryny Azure Portal](sql-database-single-database-get-started.md).
- Po utworzeniu bazy danych należy [ją zabezpieczyć, konfigurując reguły zapory](sql-database-server-level-firewall-rule.md).
- Jeśli w programie SQL Server masz istniejącą bazę danych, którą chcesz zmigrować na platformę Azure, zainstaluj narzędzie [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), które przeanalizuje Twoje bazy danych w programie SQL Server i znajdzie wszelkie problemy mogące blokować migrację do opcji wdrożenia pojedynczej bazy danych. Jeśli nie znajdziesz żadnego problemu, możesz wyeksportować bazę danych jako plik `.bacpac` i [zaimportować ją za pomocą witryny Azure Portal lub programu SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatyzowanie operacji zarządzania

Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby tworzyć, konfigurować i skalować bazę danych.

- [Tworzenie i konfigurowanie pojedynczej bazy danych za pomocą programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Tworzenie i konfigurowanie pojedynczej bazy danych za pomocą interfejsu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md)
- [Aktualizowanie pojedynczej bazy danych i skalowanie zasobów za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Aktualizowanie pojedynczej bazy danych i skalowanie zasobów za pomocą interfejsu wiersza polecenia platformy Azure](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrowanie do pojedynczej bazy danych z minimalnym przestojem

Informacje w tych przewodnikach Szybki start umożliwiają szybkie utworzenie lub zaimportowanie bazy danych na platformę Azure za pomocą pliku `.bacpac`. Pliki `.bacpac` i `.dacpac` są zaprojektowane pod kątem szybkiego przenoszenia baz danych między różnymi wersjami programu SQL Server i opcjami wdrażania w ramach usługi Azure SQL Database lub implementowania ciągłej integracji w potoku infrastruktury DevOps. Jednak ta metoda nie jest przeznaczona do migracji produkcyjnych baz danych z minimalnym przestojem, ponieważ należy zaprzestać dodawać nowe dane, poczekać na wyeksportowanie źródłowej bazy danych do pliku `.bacpac`, a następnie poczekać na zakończenie importu do bazy danych Azure SQL Database. Wszystkie te czynniki powodują przestój aplikacji, szczególnie w przypadku dużych baz danych. Aby przenieść produkcyjną bazę danych, potrzebujesz lepszego sposobu migracji, który gwarantuje minimalny przestój. Aby przeprowadzić migrację bazy danych z minimalnym przestojem, użyj usługi [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json). Usługa DMS realizuje to przez przyrostowe wypychanie zmian wprowadzanych w źródłowej bazie danych do przywracanej pojedynczej baza danych. Dzięki niej można szybko przełączyć aplikację ze źródłowej do docelowej bazy danych z możliwie najkrótszym przestojem.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj [ogólną listę funkcji obsługiwanych w usłudze Azure SQL Database](sql-database-features.md).
- Dowiedz się, jak [lepiej zabezpieczyć bazę danych](sql-database-security-tutorial.md).
- Więcej instrukcji zaawansowanych można znaleźć w temacie dotyczącym [sposobu używania pojedynczej bazy danych w usłudze Azure SQL Database](sql-database-howto-single-database.md).
- Jest dostępnych więcej przykładowych skryptów dla programu [PowerShell](sql-database-powershell-samples.md) i [interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md).
- Dowiedz się więcej o [interfejsie API zarządzania](sql-database-single-databases-manage.md) służącym do konfigurowania baz danych.
