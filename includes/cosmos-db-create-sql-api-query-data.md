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
ms.openlocfilehash: 9971b16da42cdf1de0464857291c74a947535735
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287089"
---
Zapytania w Eksploratorze danych służy do pobrania i filtrowania danych.

1. W górnej części **dokumenty** kartę w Eksploratorze danych, przejrzyj zapytanie domyślne `SELECT * FROM c`. To zapytanie pobiera i wyświetla wszystkie dokumenty w kolekcji według Identyfikatora. 
   
   ![Domyślne zapytanie w Eksploratorze danych to „SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Aby zmienić kwerendę, zaznacz **Edytuj filtr**, Zastąp domyślne zapytanie za pomocą `ORDER BY c._ts DESC`, a następnie wybierz pozycję **Zastosuj filtr**.
   
   ![Zmień zapytanie domyślne przez dodanie ORDER BY c._ts DESC i kliknij pozycję Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Wyświetla zmodyfikowane zapytanie, dokumenty w kolejności malejącej według ich sygnatury czasowej, więc drugi dokument jest wymienione jako pierwsze. 
   
   ![Zmieniono zapytanie, aby ORDER BY c._ts DESC i kliknięcie przycisku Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Jeśli znasz składnię SQL, możesz wprowadzić dowolne obsługiwane [zapytania SQL](../articles/cosmos-db/sql-api-sql-query.md) w pola predykatu zapytania. Eksplorator danych umożliwia również tworzenie procedur składowanych, funkcji zdefiniowanych przez użytkownika i Wyzwalacze dla logiki biznesowej po stronie serwera. 

Eksplorator danych udostępnia proste Azure portal wszystkie wbudowane programowe funkcje dostępu do danych w interfejsach API. Możesz także użyć portalu skalowanie przepływności, klucze i parametry połączenia i przejrzeć metryki i umowy SLA dla swojego konta usługi Azure Cosmos DB. 

