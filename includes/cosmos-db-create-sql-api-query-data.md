---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927349"
---
Możesz użyć zapytań w Eksplorator danych, aby pobrać i filtrować dane.

1. W górnej części karty **elementy** w Eksplorator danych zapoznaj się z domyślną `SELECT * FROM c`kwerendy. To zapytanie pobiera i wyświetla wszystkie dokumenty w kolekcji w kolejności identyfikatorów. 
   
   ![Domyślne zapytanie w Eksploratorze danych to „SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Aby zmienić zapytanie, wybierz pozycję **Edytuj filtr**, Zastąp domyślne zapytanie `ORDER BY c._ts DESC`, a następnie wybierz pozycję **Zastosuj filtr**.
   
   ![Zmień zapytanie domyślne przez dodanie ORDER BY c._ts DESC i kliknij pozycję Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Zmodyfikowane zapytanie wyświetla dokumenty w kolejności malejącej na podstawie ich sygnatury czasowej, dlatego teraz drugi dokument jest wyświetlany na liście jako pierwszy. 
   
   ![Zmieniono zapytanie na ORDER BY c. _ts DESC i kliknąć przycisk Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Jeśli znasz składnię SQL, możesz wprowadzić dowolne obsługiwane [zapytania SQL](../articles/cosmos-db/sql-api-sql-query.md) w polu predykatu zapytania. Za pomocą Eksplorator danych można także tworzyć procedury składowane, UDF i wyzwalacze dla logiki biznesowej po stronie serwera. 

Eksplorator danych zapewnia łatwy Azure Portal dostęp do wszystkich wbudowanych funkcji dostępu do danych programistycznych dostępnych w interfejsach API. Portal umożliwia również skalowanie przepływności, uzyskiwanie kluczy i parametrów połączenia oraz Przeglądanie metryk i umowy SLA dla konta Azure Cosmos DB. 

