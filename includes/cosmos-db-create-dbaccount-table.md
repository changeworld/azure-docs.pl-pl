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
ms.openlocfilehash: 0a9f2aaa4bc6422d4e8a86373b5e578c5bd65b4c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38726139"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie kliknij pozycję **Utwórz zasób**, kliknij pozycję **Bazy danych**, a następnie w obszarze **Azure Cosmos DB** kliknij pozycję **Utwórz**. 
   
   ![Zrzut ekranu przedstawiający witrynę Azure Portal z wyróżnionymi poleceniami Więcej usług i Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Na stronie **Nowe konto** wprowadź ustawienia nowego konta usługi Azure Cosmos DB. 
 
    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    ID|*Wprowadź unikatową nazwę*|Wprowadź unikatową nazwę do identyfikacji tego konta usługi Azure Cosmos DB. Ponieważ adres *documents.azure.com* jest dołączany do podanego identyfikatora w celu utworzenia identyfikatora URI, użyty identyfikator powinien być unikatowy, ale rozpoznawalny.<br><br>Identyfikator może zawierać tylko małe litery, cyfry i znaki łącznika (-) oraz musi zawierać od 3 do 50 znaków.
    Interfejs API|Tabela platformy Azure|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB oferuje pięć interfejsów API odpowiadających potrzebom aplikacji: SQL (baza danych dokumentów), Gremlin (baza danych wykresów), MongoDB (baza danych dokumentów), tabela platformy Azure i Cassandra. Każdy z tych interfejsów wymaga obecnie oddzielnego konta.<br><br>Wybierz pozycję **Tabela platformy Azure**, ponieważ w podręczniku Szybki start tworzysz tabelę, która współdziała z interfejsem API tabeli.<br><br>[Dowiedz się więcej o interfejsie API tabeli](../articles/cosmos-db/table-introduction.md) |
    Subskrypcja|*Wprowadź taką samą unikatową nazwę, która została podana powyżej jako identyfikator*|Wybierz subskrypcję platformy Azure, która ma być używana dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>*Następnie wprowadź taką samą unikatową nazwę, która została podana powyżej jako identyfikator*|Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów dla konta. Dla uproszczenia można użyć takiej samej nazwy jak identyfikator.
    Lokalizacja|*Wybierz region najbliżej Twoich użytkowników*|Wybierz lokalizację geograficzną, w której będzie hostowane Twoje konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.
    Włącz nadmiarowość geograficzną| Pozostaw puste | Spowoduje to utworzenie replikowanej wersji bazy danych w drugim (sparowanym) regionie. Pozostaw to pole puste.  
    Przypnij do pulpitu nawigacyjnego | Wybierz pozycję | Zaznacz to pole, aby nowe konto bazy danych zostało dodane do pulpitu nawigacyjnego portalu w celu ułatwienia dostępu.

    Następnie kliknij pozycję **Utwórz**.

    ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

4. Tworzenie konta potrwa kilka minut. Poczekaj, aż w portalu zostanie wyświetlona strona **Gratulacje! Konto usługi Azure Cosmos DB zostało utworzone**.

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
