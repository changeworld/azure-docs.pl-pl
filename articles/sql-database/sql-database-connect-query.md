---
title: Przewodniki Szybki start dotyczące nawiązywania połączeń z usługą Azure SQL Database i wykonywania zapytań | Microsoft Docs
description: Przewodniki Szybki start z informacjami na temat nawiązywania połączeń z usługą Azure SQL Database i wykonywania zapytań.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186015"
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Przewodniki Szybki start dotyczące nawiązywania połączeń z usługą Azure SQL Database i wykonywania zapytań

Poniższy dokument zawiera linki do przykładów związanych z platformą Azure przedstawiających nawiązywanie połączeń z usługą Azure SQL Database i wykonywanie zapytań. Zawiera także pewne rekomendacje dotyczące protokołu TLS.

## <a name="quickstarts"></a>Przewodniki Szybki start

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|W tym przewodniku Szybki start pokazano, jak używać narzędzia SSMS w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane z bazy danych.|
|[SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|W tym samouczku Szybki start pokazano, jak używać programu SQL Operations Studio (w wersji zapoznawczej) w celu nawiązania połączenia z usługą Azure SQL Database, a następnie utworzyć bazę danych TutorialDB używaną w samouczkach programu SQL Operations Studio (w wersji zapoznawczej) za pomocą instrukcji Transact-SQL (T-SQL).|
|[Azure Portal](sql-database-connect-query-portal.md)|W tym przewodniku Szybki start pokazano, jak używać edytora zapytań w celu nawiązywania połączenia z bazą danych SQL, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane oraz wstawiać, aktualizować i usuwać dane z bazy danych.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|W tym przewodniku Szybki start pokazano, jak używać edytora Visual Studio Code w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane z bazy danych.|
|[Korzystanie z programu .NET przy użyciu programu Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|W tym przewodniku Szybki start pokazano, jak używać programu .NET Framework w celu utworzenia — za pomocą programu Visual Studio — programu w języku C# służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|W tym przewodniku Szybki start pokazano, jak używać platformy .NET Core w systemach operacyjnych Windows/Linux/macOS w celu utworzenia programu w języku C# służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[Go](sql-database-connect-query-go.md)|W tym przewodniku Szybki start przedstawiono sposób nawiązywania połączeń z bazą danych Azure SQL Database za pomocą języka Go. Ponadto przedstawiono instrukcje języka Transact-SQL umożliwiające wykonywanie zapytań i modyfikowanie danych.|
|[Java](sql-database-connect-query-java.md)|W tym przewodniku Szybki start pokazano, jak używać języka Java w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[Node.js](sql-database-connect-query-nodejs.md)|W tym przewodniku Szybki start pokazano, jak używać narzędzia Node.js w celu utworzenia programu służącego do nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[PHP](sql-database-connect-query-php.md)|W tym przewodniku Szybki start pokazano, jak używać języka PHP w celu utworzenia programu służącego do nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[Python](sql-database-connect-query-python.md)|W tym przewodniku Szybki start pokazano, jak używać języka Python w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane. |
|[Ruby](sql-database-connect-query-ruby.md)|W tym przewodniku Szybki start pokazano, jak używać języka Ruby w celu utworzenia programu służącego do nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Zagadnienia dotyczące protokołu TLS na potrzeby łączności z usługą SQL Database
Protokół TLS (Transport Layer Security) jest używany przez wszystkie sterowniki dostarczane lub obsługiwane przez firmę Microsoft na potrzeby nawiązywania połączenia z usługą Azure SQL Database. Nie jest konieczna żadna specjalna konfiguracja. W przypadku wszystkich połączeń z programem SQL Server lub z usługą Azure SQL Database zalecamy, aby wszystkie aplikacje ustawiały następujące konfiguracje albo ich odpowiedniki:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Niektóre systemy używają innych, lecz równoważnych słów kluczowych dla tych słów kluczowych konfiguracji. Te konfiguracje zapewniają, że sterownik klienta weryfikuje tożsamość certyfikatu TLS otrzymanego z serwera.

Zalecamy również wyłączenie protokołu TLS 1.1 i 1.0 na kliencie, jeśli wymagana jest zgodność ze standardem Payment Card Industry Data Security Standard (PCI DSS).

Sterowniki firm innych niż Microsoft mogą nie używać domyślnie protokołu TLS. Może to mieć znaczenie podczas nawiązywania połączenia z usługą Azure SQL Database. Aplikacje ze sterownikami osadzonymi mogą nie pozwalać na kontrolowanie tych ustawień połączenia. Zaleca się zbadanie zabezpieczeń takich sterowników i aplikacji przed ich użyciem w systemach, które wchodzą w interakcję z danymi poufnymi.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat architektury łączności, zobacz artykuł [Azure SQL Database Connectivity Architecture (Architektura łączności usługi Azure SQL Database)](sql-database-connectivity-architecture.md).