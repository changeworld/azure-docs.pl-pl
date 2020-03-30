---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a6cfafd41d1632d99b7ca03bce2316ec679d7579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061844"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**.
   
   ![Tworzenie zasobu w witrynie Azure portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stronie **Nowy** wybierz pozycję **Bazy danych Usługi** > **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź ustawienia nowego konta usługi Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź tę samą nazwę co Nazwa konta|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nową nazwę grupy zasobów dla swojego konta. Dla uproszczenia należy użyć tej samej nazwy co nazwa konta usługi Azure Cosmos DB. 
    Nazwa konta|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB. Identyfikator URI twojego konta zostanie *gremlin.azure.com* dołączona do unikatowej nazwy konta.<br><br>Nazwa konta może używać tylko małych liter, cyfr i łączników (-) i musi mieć od 3 do 31 znaków.
    interfejs API|Gremlin (graf)|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB udostępnia pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych wykresów, MongoDB dla baz danych dokumentów, Tabela azure i Cassandra. Należy utworzyć osobne konto dla każdego interfejsu API. <br><br>Wybierz **gremlin (wykres),** ponieważ w tym przewodniku Szybki start tworzysz tabelę, która działa z interfejsem API Gremlin. <br><br>[Dowiedz się więcej o interfejsie API Gremlin](../articles/cosmos-db/graph-introduction.md).|
    Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

    Wybierz pozycję **Przeglądanie+tworzenie**. Możesz pominąć sekcje **Sieć** i **Tagi**. 

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż portal wyświetli **gratulacje! Twoja strona usługi Azure Cosmos DB została utworzona.**
   
   ![Strona utworzenia konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

