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
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
Teraz można użyć zapytań w Eksploratorze danych w celu pobrania i filtrowania danych.

1. Domyślnie zapytanie jest ustawione na `SELECT * FROM c`. To domyślne zapytanie pobiera i wyświetla wszystkie dokumenty w kolekcji. 

    ![Domyślne zapytanie w Eksploratorze danych to „SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. Pozostań na karcie **Dokumenty**, zmień zapytanie, klikając przycisk **Edytuj filtr**, dodając wartość `ORDER BY c._ts DESC` do pola predykatu zapytania, a następnie klikając pozycję **Zastosuj filtr**.

    ![Zmień zapytanie domyślne przez dodanie ORDER BY c._ts DESC i kliknij pozycję Zastosuj filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

To zmodyfikowane zapytanie wyświetla dokumenty w kolejności malejącej według ich sygnatury czasowej, więc drugi dokument jest wyświetlany na początku listy. Jeśli znasz składnię SQL, w tym polu możesz wprowadzić dowolne obsługiwane [zapytanie SQL](../articles/cosmos-db/sql-api-sql-query.md). 

Na tym kończy się praca w Eksploratorze danych. Przed przejściem do pracy z kodem należy zapamiętać, że Eksplorator danych umożliwia również tworzenie procedur składowanych, funkcji definiowanych przez użytkownika (UDF) i wyzwalaczy w celu wykonania logiki biznesowej po stronie serwera oraz skalowania przepływności. Eksplorator danych udostępnia wszystkie wbudowane programowe procedury dostępu do danych w interfejsach API, ale umożliwia łatwy dostęp do danych za pośrednictwem witryny Azure Portal.