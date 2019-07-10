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
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67182922"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W okienku nawigacji po lewej stronie wybierz **Utwórz zasób**. Wybierz **baz danych** , a następnie wybierz **usługi Azure Cosmos DB**.
   
   ![Zrzut ekranu witryny Azure Portal wyróżnionymi poleceniami więcej usług i usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Na **Tworzenie konta usługi Azure Cosmos DB** strony, wprowadź ustawienia dla nowego konta usługi Azure Cosmos DB:
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subscription|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|Tworzenie nowego elementu<br><br>Następnie wprowadź taką samą unikatową nazwę, która została podana jako identyfikator|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nazwę nowej grupy zasobów dla swojego konta. Dla uproszczenia użyj takiej samej nazwy jak identyfikator. 
    Nazwa konta|Wprowadź unikatową nazwę|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB.<br><br>Identyfikator może zawierać tylko małe litery, cyfry i znaki łącznika (-). Należy się od 3 do 31 znaków.
    interfejs API|Tabela platformy Azure|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB oferuje pięć interfejsów API: Core(SQL) dla baz danych dokumentów, Gremlin dla baz danych wykresów, MongoDB dla baz danych dokumentów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>Wybierz **Azure Table** ponieważ w tym przewodniku Szybki Start tworzysz tabelę, która współdziała z interfejsem API tabel. <br><br>[Dowiedz się więcej na temat interfejsu API tabel](../articles/cosmos-db/table-introduction.md).|
    Location|Wybierz region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji, która jest najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

    Możesz pozostawić **nadmiarowość geograficzna** i **zapisuje w wielu regionach** opcje wartościami domyślnymi (**wyłączyć**) aby uniknąć dodatkowych opłat jednostek RU. Możesz pominąć **sieci** i **tagi** sekcje.

5. Wybierz pozycję **Przeglądanie+tworzenie**. Po zakończeniu walidacji wybierz **Utwórz** do utworzenia konta. 
 
   ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Trwa kilka minut, aby utworzyć konto. Zobaczysz komunikat informujący, **wdrożenia jest w toku**. Poczekaj, aż wdrożenia zakończyć, a następnie wybierz **przejdź do zasobu**.

    ![W okienku powiadomienia z portalu Azure](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
