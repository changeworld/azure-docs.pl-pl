---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77061718"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**.
   
   ![Tworzenie zasobu w witrynie Azure portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stronie **Nowy** wybierz pozycję **Bazy danych Usługi** > **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź ustawienia nowego konta usługi Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź tę samą nazwę co Nazwa konta|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nową nazwę grupy zasobów dla swojego konta. Dla uproszczenia należy użyć tej samej nazwy co nazwa konta usługi Azure Cosmos DB. 
    Nazwa konta|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB. Identyfikator URI twojego konta zostanie *mongo.cosmos.azure.com* dołączona do unikatowej nazwy konta.<br><br>Nazwa konta może używać tylko małych liter, cyfr i łączników (-) i musi mieć od 3 do 31 znaków.
    interfejs API|Usługa Azure Cosmos DB dla interfejsu API bazy danych Mongo|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB udostępnia pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych wykresów, usługi Azure Cosmos DB dla interfejsu API bazy danych Mongo dla baz danych dokumentów, tabelę platformy Azure i kasandrę. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>Wybierz **usługę Azure Cosmos DB for Mongo DB API,** ponieważ w tym przewodniku Szybki start tworzysz kolekcję, która współpracuje z mongodb.<br><br>[Dowiedz się więcej o usłudze Azure Cosmos DB for MongoDB API](../articles/cosmos-db/mongodb-introduction.md).|
    Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|

    Wybierz pozycję **Przeglądanie+tworzenie**. Możesz pominąć sekcje **Sieć** i **Tagi**. 

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż portal wyświetli **gratulacje! Twoje konto interfejsu API usługi Azure Cosmos DB for Mongo DB jest gotowe** na stronie.

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
