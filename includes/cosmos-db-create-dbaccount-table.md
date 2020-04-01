---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212724"
---
1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**.
   
   ![Tworzenie zasobu w witrynie Azure portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Na stronie **Nowy** wybierz pozycję **Bazy danych Usługi** > **Azure Cosmos DB**.
   
   ![Okienko Bazy danych w witrynie Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź ustawienia nowego konta usługi Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|Twoja subskrypcja|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos DB. 
    Grupa zasobów|**Utwórz nowy**, a następnie Nazwa konta|Wybierz pozycję**Utwórz nowy**. Następnie wprowadź nową nazwę grupy zasobów dla swojego konta. Dla uproszczenia należy użyć tej samej nazwy co nazwa konta usługi Azure Cosmos DB. 
    Nazwa konta|Unikatowa nazwa|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB.<br><br>Nazwa konta może używać tylko małych liter, cyfr i łączników (-) i musi mieć od 3 do 31 znaków.
    interfejs API|Tabela|Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB udostępnia pięć interfejsów API: Core (SQL) dla baz danych dokumentów, Gremlin dla baz danych wykresów, MongoDB dla baz danych dokumentów, Tabela azure i Cassandra. Należy utworzyć osobne konto dla każdego interfejsu API. <br><br>Wybierz **tabelę platformy Azure**, ponieważ w tym przewodniku Szybki start tworzysz tabelę, która współpracuje z interfejsem API tabeli. <br><br>[Dowiedz się więcej o interfejsie API tabeli](../articles/cosmos-db/table-introduction.md).|
    Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

    Opcje **Geo-Redundancy** i **Zapisy wieloregionowe** można pozostawić w **trybie Wyłącz,** aby uniknąć dodatkowych opłat, i pominąć sekcje **Sieć** i **Znaczniki.**

5. Wybierz pozycję **Przeglądanie+tworzenie**. Po zakończeniu sprawdzania poprawności wybierz pozycję **Utwórz,** aby utworzyć konto. 
 
   ![Strona nowego konta usługi Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Utworzenie konta trwa kilka minut. Zostanie wyświetlony komunikat informujący o zakończeniu **wdrażania.** Poczekaj na zakończenie wdrożenia, a następnie wybierz pozycję **Przejdź do zasobu**.

    ![Okienko powiadomień portalu Platformy Azure](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

