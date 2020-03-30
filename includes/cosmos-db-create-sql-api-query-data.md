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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927349"
---
Za pomocą kwerend w Eksploratorze danych można pobierać i filtrować dane.

1. U góry karty **Elementy** w Eksploratorze `SELECT * FROM c`danych przejrzyj kwerendę domyślną . Ta kwerenda pobiera i wyświetla wszystkie dokumenty w kolekcji w kolejności identyfikatora. 
   
   ![Domyślne zapytanie w Eksploratorze danych to „SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Aby zmienić kwerendę, wybierz pozycję Edytuj `ORDER BY c._ts DESC` **filtr,** zastąp kwerendę domyślną , a następnie wybierz pozycję Zastosuj **filtr**.
   
   ![Zmień zapytanie domyślne przez dodanie ORDER BY c._ts DESC i kliknij pozycję Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Zmodyfikowana kwerenda wyświetla dokumenty w kolejności malejącej na podstawie ich sygnatury czasowej, więc teraz drugi dokument jest wyświetlany jako pierwszy. 
   
   ![Zmieniono kwerendę na KOLEJNOŚĆ według _ts DESC i kliknij przycisk Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Jeśli znasz składnię SQL, możesz wprowadzić wszystkie obsługiwane [kwerendy SQL](../articles/cosmos-db/sql-api-sql-query.md) w polu predykatu kwerendy. Za pomocą Eksploratora danych można również tworzyć procedury przechowywane, pliki UDF i wyzwalacze dla logiki biznesowej po stronie serwera. 

Eksplorator danych zapewnia łatwy dostęp do witryny Azure portal do wszystkich wbudowanych funkcji dostępu do danych programowych dostępnych w interfejsach API. Portal służy również do skalowania przepływności, uzyskania kluczy i ciągów połączeń oraz przeglądania metryk i SLA dla konta usługi Azure Cosmos DB. 

