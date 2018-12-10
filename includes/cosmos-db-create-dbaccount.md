---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a144433e1ff6c0f7ca4e0ed72704d339150f2d56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643428"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Wybierz kolejno pozycje **Utwórz zasób** > **Bazy danych** > **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Na stronie **Nowe konto** wprowadź ustawienia nowego konta usługi Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    ID|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB. Ponieważ adres *documents.azure.com* jest dołączany do podanego identyfikatora w celu utworzenia identyfikatora URI, użyj unikatowego identyfikatora.<br><br>Identyfikator może zawierać tylko małe litery, cyfry i znaki łącznika (-). Identyfikator musi mieć od 3 do 50 znaków.
    Interfejs API|SQL|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB oferuje pięć interfejsów API: SQL dla baz danych dokumentów, Gremlin dla baz danych wykresów, MongoDB dla baz danych dokumentów, interfejs API tabel i interfejs API Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>Wybierz pozycję **SQL**, ponieważ w tym artykule tworzysz bazę danych dokumentów i wykonujesz zapytanie przy użyciu składni języka SQL. <br><br>[Dowiedz się więcej o interfejsie API SQL](../articles/cosmos-db/documentdb-introduction.md).|
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź taką samą unikatową nazwę, która została podana jako identyfikator|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nazwę nowej grupy zasobów dla swojego konta. Dla uproszczenia użyj takiej samej nazwy jak identyfikator. 
    Lokalizacja|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.
    Włącz nadmiarowość geograficzną| Pozostaw puste | Ta opcja powoduje utworzenie replikowanej wersji bazy danych w drugim (sparowanym) regionie. Pozostaw to pole wyboru puste. 

    Wybierz pozycję **Utwórz**.

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż w portalu zostanie wyświetlona strona **Gratulacje! Konto usługi Azure Cosmos DB zostało utworzone**.

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

