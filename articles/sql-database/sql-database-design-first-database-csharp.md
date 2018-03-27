---
title: Projektowanie pierwszej bazy danych SQL na platformie Azure — C# | Microsoft Docs
description: Dowiedz się, jak zaprojektować swoją pierwszą bazę danych SQL na platformie Azure i połączyć się z nią za pomocą programu w języku C# i narzędzia ADO.NET.
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.date: 03/15/2018
ms.openlocfilehash: 3b6f260983e3c826bf558f0fe6d1a0fa6ae6b3af
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Projektowanie bazy danych SQL na platformie Azure i nawiązywanie połączenia za pomocą języka C&#x23; i narzędzia ADO.NET

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

Zainstalowany program [Visual Studio Community 2017, Visual Studio Professional 2017 lub Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowe zadania dotyczące bazy danych, takie jak tworzenie bazy danych i tabel, ładowanie danych i tworzenie zapytań oraz przywracanie bazy danych do wcześniejszego punktu w czasie. W tym samouczku omówiono:
> [!div class="checklist"]
> * Tworzenie bazy danych
> * Konfigurowanie reguły zapory
> * Nawiązywanie połączenia z bazą danych za pomocą [programu Visual Studio i języka C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Tworzenie tabel
> * Wstawianie, aktualizowanie i usuwanie danych
> * Zapytania o dane

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat migracji danych.

> [!div class="nextstepaction"]
>[Migracja bazy danych SQL Server do usługi Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

