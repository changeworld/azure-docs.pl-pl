---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 0acc989f558c5de4e8f52ba2c77f2842f8d5d70c
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75752302"
---
1. W nowym oknie zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**, wybierz pozycję **bazy danych**, a następnie w obszarze **Azure Cosmos DB**wybierz pozycję **Utwórz**.
   
   ![Zrzut ekranu przedstawiający Azure Portal, wyróżnianie więcej usług i Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź ustawienia nowego konta usługi Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź taką samą unikatową nazwę, która została podana jako identyfikator|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nazwę nowej grupy zasobów dla swojego konta. Dla uproszczenia użyj takiej samej nazwy jak identyfikator. 
    Nazwa konta|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB. Ponieważ adres *documents.azure.com* jest dołączany do podanego identyfikatora w celu utworzenia identyfikatora URI, użyj unikatowego identyfikatora.<br><br>Identyfikator może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.
    API|Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB|Interfejs API określa typ konta do utworzenia. Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych programu Graph, Azure Cosmos DB interfejsu API MongoDB dla baz danych dokumentów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>Wybierz pozycję **MongoDB**, ponieważ w tym przewodniku Szybki start tworzysz tabelę, która współdziała z bazą danych MongoDB.|
    Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

    Wybierz pozycję **Przeglądanie+tworzenie**. Możesz pominąć sekcje **Sieć** i **Tagi**. 

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż Portal wyświetli **gratulacje! Twoje konto usługi Cosmos z funkcją zgodności protokołu telekomunikacyjnych dla MongoDB jest gotowe** .

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
