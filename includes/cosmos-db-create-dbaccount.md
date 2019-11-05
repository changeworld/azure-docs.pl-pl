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
ms.openlocfilehash: 93652273dda16e93bdcf9123d7d4a9b3fc956fe9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466186"
---
1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby utworzyć konto Azure Cosmos DB. Wyszukaj i wybierz **Azure Cosmos DB**.

   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Wybierz pozycję **Dodaj**.
1. Na stronie **Tworzenie konta Azure Cosmos DB** wprowadź podstawowe ustawienia dla nowego konta usługi Azure Cosmos. 

    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, która ma być używana dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
    |Nazwa konta|Unikatowa nazwa|Wprowadź nazwę, aby zidentyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanego identyfikatora w celu utworzenia identyfikatora URI, użyj unikatowego identyfikatora.<br><br>Identyfikator może zawierać tylko małe litery, cyfry i znaki łącznika (-). Długość musi należeć do zakresu 3-31 znaków.|
    |Interfejs API|Typ konta do utworzenia|Wybierz pozycję **rdzeń (SQL)** , aby utworzyć bazę danych dokumentów i zapytanie przy użyciu składni SQL. <br><br>Interfejs API określa typ konta do utworzenia. Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) i MongoDB dla danych dokumentu, Gremlin dla danych grafów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>[Dowiedz się więcej o interfejsie API SQL](../articles/cosmos-db/documentdb-introduction.md).|
    |Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|

   ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Wybierz pozycję **Przegląd + utwórz**. Możesz pominąć sekcje **sieci** i **Tagi** .

1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta może potrwać kilka minut. Poczekaj, aż Strona portalu **zostanie**wyświetlona. 

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony konta Azure Cosmos DB. 

    ![Strona konta Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
