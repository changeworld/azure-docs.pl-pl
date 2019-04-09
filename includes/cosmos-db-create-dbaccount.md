---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5d57d7e18befba175a5a8a825494ce512644b5a2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291669"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz kolejno pozycje **Utwórz zasób** > **Bazy danych** > **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Na **Tworzenie konta usługi Azure Cosmos DB** wpisz podstawowe ustawienia dla nowego konta usługi Azure Cosmos. 
 
    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, która ma być używana dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz **Utwórz nową**, następnie wprowadź unikatową nazwę dla nowej grupy zasobów. |
    | Nazwa konta|Wprowadź unikatową nazwę|Wprowadź nazwę, aby zidentyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanego identyfikatora w celu utworzenia identyfikatora URI, użyj unikatowego identyfikatora.<br><br>Identyfikator może zawierać tylko małe litery, cyfry i znaki łącznika (-). Należy się od 3 31 znaków.|
    | Interfejs API|Core (SQL)|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB oferuje pięć interfejsów API: Rdzeń (SQL) i bazy danych MongoDB dla danych dokumentu, Gremlin danych programu graph, Azure Table i bazy danych Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>Wybierz **Core (SQL)** do tworzenia zapytań i bazę danych dokumentów przy użyciu składni SQL. <br><br>[Dowiedz się więcej o interfejsie API SQL](../articles/cosmos-db/documentdb-introduction.md).|
    | Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji, która jest najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|
   
   ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

1. Wybierz pozycję **Przegląd + utwórz**. Możesz pominąć **sieci** i **tagi** sekcje. 

1. Przejrzyj ustawienia konta, a następnie wybierz **Utwórz**. Trwa kilka minut, aby utworzyć konto. Poczekaj na stronie portalu wyświetlić **swoje wdrożenie jest ukończone**. 

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Wybierz **przejdź do zasobu** aby przejść do strony konta usługi Azure Cosmos DB. 

    ![Na stronie konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
