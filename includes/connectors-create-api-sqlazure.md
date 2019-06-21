---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183484"
---
* Jeśli używasz usługi Azure SQL Database, wykonaj kroki opisane w temacie [nawiązywanie połączenia z bazą danych SQL Azure](#connect-azure-sql-db). 

* Jeśli używasz programu SQL Server, wykonaj kroki opisane w temacie [Połącz z serwerem SQL](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Nawiązać połączenie z bazą danych Azure SQL

1. Gdy SQL wyzwalacza lub akcji wyświetli monit o podanie informacji o połączeniu, wykonaj następujące kroki:

   1. Utwórz nazwę połączenia.

   2. Wybierz swój serwer SQL, a następnie wybierz swoją bazę danych. 

      Lista baz danych pojawia się tylko po wybraniu programu SQL server.
 
   3. Podaj nazwę użytkownika i hasło dla serwera.

      Można znaleźć te informacje w witrynie Azure portal w obszarze właściwości bazy danych SQL lub w ciągu połączenia: 
      
      "Nazwa użytkownika = <*nazwa_użytkownika*>"
      <br>
      "Hasło = <*yourPassword*>"

   Ten przykład przedstawia informacje o połączeniu dla wyzwalacza, ale następujące kroki działają zbyt dla akcji.

   ![Utwórz połączenie z bazą danych SQL Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Gwiazdki (*) wskazują wymaganymi wartościami.

   | Właściwość | Wartość | Szczegóły | 
   |----------|-------|---------| 
   | Nazwa połączenia | <*my-sql-connection*> | Nazwa połączenia | 
   | Nazwa serwera SQL | <*my-sql-server*> | Nazwa serwera SQL |
   | Nazwa bazy danych SQL | <*my-sql-database*>  | Nazwa bazy danych SQL | 
   | Nazwa użytkownika | <*my-sql-username*> | Nazwa użytkownika do uzyskiwania dostępu do bazy danych |
   | Hasło | <*moje hasło sql*> | Hasło do uzyskiwania dostępu do bazy danych | 
   |||| 

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

3. Po utworzeniu połączenia, kontynuuj [wyzwalacza SQL Dodaj](#add-sql-trigger) lub [SQL Dodaj akcję](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

Aby można było wybrać bramę, upewnij się, że już [Konfigurowanie Twoja brama data gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). W ten sposób, brama pojawia się na liście bram podczas tworzenia połączenia.

1. Gdy SQL wyzwalacza lub akcji wyświetli monit o podanie informacji o połączeniu, wykonaj następujące kroki:

   1. Wyzwalacz lub akcję, wybierz **Połącz za pośrednictwem lokalnej bramy danych** tak, aby wyświetlane opcje programu SQL server.

   2. Utwórz nazwę połączenia.

   3. Podaj adres swojego serwera SQL server, a następnie podaj nazwę dla bazy danych.
   
      Te informacje można znaleźć w ciągu połączenia: 
      
      * "Server=<*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. Podaj nazwę użytkownika i hasło dla serwera.

      Te informacje można znaleźć w ciągu połączenia: 
      
      * "Nazwa użytkownika = <*nazwa_użytkownika*>"
      * "Hasło = <*yourPassword*>"

   5. Jeśli program SQL server korzysta z uwierzytelniania podstawowe lub Windows, wybierz typ uwierzytelniania.

   6. Wybierz nazwę dla bramy danych lokalnych, która została wcześniej utworzona.
   
      Jeśli brama nie pojawia się na liście, sprawdź, że poprawnie [skonfigurowania bramy](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Ten przykład przedstawia informacje o połączeniu dla wyzwalacza, ale następujące kroki działają zbyt dla akcji.

   ![Tworzenie połączenia programu SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Gwiazdki (*) wskazują wymaganymi wartościami.

   | Właściwość | Wartość | Szczegóły | 
   |----------|-------|---------| 
   | Nawiązywanie połączenia za pośrednictwem bramy lokalnej | Wybierz tę opcję, najpierw ustawień programu SQL Server. | | 
   | Nazwa połączenia | <*my-sql-connection*> | Nazwa połączenia | 
   | Nazwa serwera SQL | <*my-sql-server*> | Nazwa serwera SQL |
   | Nazwa bazy danych SQL | <*my-sql-database*>  | Nazwa bazy danych SQL |
   | Nazwa użytkownika | <*my-sql-username*> | Nazwa użytkownika do uzyskiwania dostępu do bazy danych |
   | Hasło | <*moje hasło sql*> | Hasło do uzyskiwania dostępu do bazy danych | 
   | Typ uwierzytelniania | Windows lub Basic | Opcjonalnie: Typ uwierzytelniania używany przez program SQL server | 
   | Bramy | <*my-data-gateway*> | Nazwa dla lokalnej bramy danych | 
   |||| 

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 

3. Po utworzeniu połączenia, kontynuuj [wyzwalacza SQL Dodaj](#add-sql-trigger) lub [SQL Dodaj akcję](#add-sql-action).
