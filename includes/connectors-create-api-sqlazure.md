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
ms.openlocfilehash: 013e230aa7e096f6a90ed7cf9e93a44fbdeb3bd6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678270"
---
* Jeśli używasz bazy danych SQL Azure, wykonaj kroki opisane w obszarze [Połącz z bazą danych SQL Azure](#connect-azure-sql-db). 

* Jeśli używasz programu SQL Server, wykonaj kroki opisane w temacie [Połącz z serwerem SQL](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Połącz z bazą danych Azure SQL

1. Podczas tego wyzwalacza SQL lub akcji wyświetla monit o podanie informacji o połączeniu, wykonaj następujące czynności:

   1. Utwórz nazwę dla połączenia.

   2. Wybierz program SQL server, a następnie wybierz bazę danych. 

      Listy baz danych jest wyświetlana tylko po wybraniu serwera SQL.
 
   3. Podaj swoją nazwę użytkownika i hasło dla serwera.

      Można znaleźć te informacje w portalu Azure w obszarze właściwości bazy danych SQL lub w ciągu połączenia: 
      
      "Nazwa użytkownika = <*nazwa_użytkownika*>"
      <br>
      "Hasło = <*yourPassword*>"

   Ten przykład przedstawia informacje o połączeniu dla wyzwalacza, ale te kroki pracy akcji zbyt.

   ![Utwórz połączenie z bazą danych SQL Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Gwiazdki (*) wskazuje wymagane wartości.

   | Właściwość | Wartość | Szczegóły | 
   |----------|-------|---------| 
   | Nazwa połączenia | <*Moje połączenia sql*> | Nazwa połączenia | 
   | Nazwa serwera SQL | <*Mój serwer sql*> | Nazwa serwera SQL |
   | Nazwa bazy danych SQL | <*Moje--bazy danych sql*>  | Nazwa bazy danych SQL | 
   | Nazwa użytkownika | <*Moje username sql*> | Nazwa użytkownika do uzyskiwania dostępu do bazy danych |
   | Hasło | <*moje hasło sql*> | Hasło do uzyskiwania dostępu do bazy danych | 
   |||| 

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

3. Po utworzeniu połączenia kontynuować [SQL dodać wyzwalacza](#add-sql-trigger) lub [akcji Dodaj SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

Aby można było wybrać bramy, upewnij się, że zostały już [Konfigurowanie bramy danych](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). W ten sposób brama zostanie wyświetlony na liście bram podczas tworzenia połączenia.

1. Podczas tego wyzwalacza SQL lub akcji wyświetla monit o podanie informacji o połączeniu, wykonaj następujące czynności:

   1. Wyzwalacz lub akcji, wybierz **Połącz za pośrednictwem bramy danych lokalnych** będą wyświetlane opcje serwera SQL.

   2. Utwórz nazwę dla połączenia.

   3. Podaj adres serwera SQL, a następnie podaj nazwę dla bazy danych.
   
      Te informacje można znaleźć w ciągu połączenia: 
      
      * "Serwer = <*yourServerAddress*>"
      * "Baza danych = <*yourDatabaseName*>"

   4. Podaj swoją nazwę użytkownika i hasło dla serwera.

      Te informacje można znaleźć w ciągu połączenia: 
      
      * "Nazwa użytkownika = <*nazwa_użytkownika*>"
      * "Hasło = <*yourPassword*>"

   5. Jeśli program SQL server korzysta z systemu Windows lub uwierzytelniania podstawowego, wybierz typ uwierzytelniania.

   6. Wybierz nazwę utworzonego wcześniej bramy danych lokalnych.
   
      Jeśli brama nie jest wyświetlane na liście, sprawdź, że poprawnie [Konfigurowanie bramy](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Ten przykład przedstawia informacje o połączeniu dla wyzwalacza, ale te kroki pracy akcji zbyt.

   ![Utwórz połączenie z serwerem SQL](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Gwiazdki (*) wskazuje wymagane wartości.

   | Właściwość | Wartość | Szczegóły | 
   |----------|-------|---------| 
   | Łączenie za pośrednictwem bramy lokalnej | Wybierz tę opcję, najpierw dla ustawień programu SQL Server. | | 
   | Nazwa połączenia | <*Moje połączenia sql*> | Nazwa połączenia | 
   | Nazwa serwera SQL | <*Mój serwer sql*> | Nazwa serwera SQL |
   | Nazwa bazy danych SQL | <*Moje--bazy danych sql*>  | Nazwa bazy danych SQL |
   | Nazwa użytkownika | <*Moje username sql*> | Nazwa użytkownika do uzyskiwania dostępu do bazy danych |
   | Hasło | <*moje hasło sql*> | Hasło do uzyskiwania dostępu do bazy danych | 
   | Typ uwierzytelnienia | Systemu Windows lub Basic | Opcjonalnie: Typ uwierzytelniania używany przez program SQL server | 
   | Bramy | <*Moje bramy danych*> | Nazwa bramy danych lokalnych | 
   |||| 

2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 

3. Po utworzeniu połączenia kontynuować [SQL dodać wyzwalacza](#add-sql-trigger) lub [akcji Dodaj SQL](#add-sql-action).
