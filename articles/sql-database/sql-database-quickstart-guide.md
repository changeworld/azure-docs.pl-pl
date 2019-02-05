---
title: Szybki start — pojedyncze bazy danych w usłudze Azure SQL Database | Microsoft Docs
description: Dowiedz się, jak szybko rozpocząć pracę z pojedynczymi bazami danych w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464820"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Wprowadzenie do pojedynczych baz danych w usłudze Azure SQL Database

[Pojedyncza baza danych](sql-database-single-index.yml) w usłudze Azure SQL Database to w pełni zarządzana baza danych jako usługa (DbaaS), która jest doskonałym aparatem magazynu dla nowoczesnych aplikacji zaprojektowanych pod kątem chmury. W tej sekcji dowiesz się, jak szybko skonfigurować i utworzyć usługę SQL Database.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

W tej sekcji możesz zobaczyć przegląd dostępnych artykułów, które ułatwiają szybkie rozpoczęcie pracy z pojedynczymi bazami danych. Najprostszym sposobem utworzenia pierwszej usługi SQL Database jest użycie [witryny Azure Portal](sql-database-get-started-portal.md), w której można skonfigurować wymagane parametry.
Po utworzeniu należy [zabezpieczyć bazę danych, konfigurując reguły zapory](sql-database-get-started-portal-firewall.md). 

Jeśli w programie SQL Server masz istniejącą bazę danych, którą chcesz zmigrować na platformę Azure, zainstaluj narzędzie [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), które przeanalizuje bazy danych w programie SQL Server i znajdzie wszelkie problemy mogące blokować migrację do pojedynczej bazy danych. Jeśli nie znajdziesz żadnego problemu, możesz wyeksportować bazę danych jako plik `.bacpac` i [zaimportować ją za pomocą witryny Azure Portal lub programu SqlPackage](sql-database-import.md).

Te przewodniki Szybki start umożliwiają szybkie skonfigurowanie, utworzenie i zaimportowanie baz danych do chmury platformy Azure.

## <a name="automating-management-operations"></a>Automatyzowanie operacji zarządzania

Witryna Azure Portal umożliwia łatwe utworzenie i zmodyfikowanie pojedynczej bazy danych. Możesz też użyć programu [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md) do tworzenia baz danych.
Istnieje także możliwość aktualizowania pojedynczej bazy danych i skalowania zasobów za pomocą programu [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrowanie do pojedynczej bazy danych z minimalnym przestojem

Artykuły w tych przewodnikach Szybki start umożliwiają szybkie utworzenie lub zaimportowanie bazy danych na platformę Azure za pomocą pliku `.bacpac`. Pliki `.bacpac` i `.dacpack` są zaprojektowane pod kątem szybkiego przenoszenia baz danych między różnymi wersjami programu SQL Server i usługi Azure SQL Database (pojedyncza baza danych, elastyczna pula lub wystąpienie zarządzane) lub implementowania ciągłej integracji w potoku infrastruktury DevOps. Jednak ta metoda nie jest przeznaczona do migracji produkcyjnych baz danych z minimalnym przestojem, ponieważ trzeba czekać na wyeksportowanie źródłowej bazy danych jako pliku `.bacpac`, a następnie zaimportować ją do usługi Azure SQL Database, co powoduje przestój aplikacji, szczególnie jeśli baza danych jest duża. Jeśli przenosisz produkcyjną bazę danych, prawdopodobnie potrzebujesz lepszego sposobu migracji, który gwarantuje minimalny przestój. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) to usługa umożliwiająca zmigrowanie bazy danych z minimalnym przestojem. Dzięki niej można szybko przełączyć aplikację ze źródłowej do docelowej bazy danych z możliwie najkrótszym przestojem.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [ogólną listę funkcji obsługiwanych w usłudze Azure SQL Database](sql-database-features.md). 
* Dowiedz się, jak [lepiej zabezpieczyć bazę danych](sql-database-security-tutorial.md). 
* Bardziej zaawansowane samouczki znajdują się w [sekcji instrukcji](sql-database-howto-single-database.md). 
* Jest dostępnych więcej przykładowych skryptów dla programu [PowerShell](sql-database-powershell-samples.md) i [interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md). 
* Dowiedz się więcej o [interfejsie API zarządzania](sql-database-single-databases-manage.md) służącym do konfigurowania baz danych. 
