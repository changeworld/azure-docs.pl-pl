---
title: Projektowanie pierwszej bazy danych SQL na platformie Azure — C# | Microsoft Docs
description: Dowiedz się, jak zaprojektować swoją pierwszą bazę danych SQL na platformie Azure i połączyć się z nią za pomocą programu w języku C# i narzędzia ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727169"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Samouczek: Projektowanie bazy danych SQL na platformie Azure i nawiązywanie połączenia za pomocą języka C&#x23; i narzędzia ADO.NET

Usługa Azure SQL Database to relacyjna baza danych oferowana jako usługa (DBaaS, database-as-a service) na platformie Microsoft Cloud (Azure). Z tego samouczka dowiesz się, jak przy użyciu witryny Azure Portal i narzędzia ADO.NET w programie Visual Studio wykonać następujące czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych w witrynie Azure Portal
> * Skonfigurowanie reguły zapory na poziomie serwera w witrynie Azure Portal
> * Nawiązywanie połączenia z bazą danych przy użyciu narzędzia ADO.NET i programu Visual Studio
> * Tworzenie tabel za pomocą narzędzia ADO.NET
> * Wstawianie, aktualizowanie i usuwanie danych za pomocą narzędzia ADO.NET
> * Wykonywanie zapytań względem danych za pomocą narzędzia ADO.NET

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Instalacja [programu Visual Studio 2017](https://www.visualstudio.com/downloads/)

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowe zadania dotyczące baz danych, takie jak tworzenie bazy danych i tabel, łączenie się z bazą danych, ładowanie danych i uruchamianie zapytań. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie bazy danych
> * Konfigurowanie reguły zapory
> * Nawiązywanie połączenia z bazą danych za pomocą [programu Visual Studio i języka C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Tworzenie tabel
> * Wstawianie, aktualizowanie i usuwanie danych oraz wykonywanie zapytań o dane

Przejdź do następnego samouczka, aby dowiedzieć się więcej o migracji danych.

> [!div class="nextstepaction"]
> [Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie offline przy użyciu usługi DMS](../dms/tutorial-sql-server-to-azure-sql.md)
