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
ms.openlocfilehash: cf77eaa07d45222cecf0450fb33fe62e556bcd9e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38729003"
---
Teraz możesz użyć narzędzia Eksplorator danych w witrynie Azure Portal, aby utworzyć bazę danych i kolekcję. 

1. Kliknij przycisk **Eksplorator danych** > **Nowa kolekcja**. 
    
    Obszar **Dodaj kolekcję** jest wyświetlany po prawej stronie i konieczne może być przewinięcie w prawo w celu wyświetlenia go.

    ![Eksplorator danych w witrynie Azure Portal, blok Dodaj kolekcję](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodaj kolekcję** wprowadź ustawienia dla nowej kolekcji.

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    Identyfikator bazy danych|Zadania|Wprowadź *Zadania* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków /, \\, #, ? ani mieć spacji na końcu.
    Identyfikator kolekcji|Items|Wprowadź *Elementy* jako nazwę nowej kolekcji. W przypadku identyfikatorów kolekcji obowiązują takie same wymagania dotyczące znaków, jak dla nazw baz danych.
    Pojemność magazynu| Stała (10 GB)|Użyj wartości domyślnej **Stała (10 GB)**. Ta wartość to pojemność magazynu bazy danych.
    Przepływność|400 RU|Zmień przepływność na 400 jednostek żądania na sekundę (RU/s). Pojemność magazynu musi być ustawiona na **Stałe (10 GB)**, aby możliwe było ustawienie przepływności na 400 RU/s. Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę. 
    
    Oprócz powyższych ustawień, można opcjonalnie dodać **unikatowe klucze** dla kolekcji. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc zasady unikatowych kluczy podczas tworzenia kolekcji, można zapewnić unikatowość co najmniej jednej wartości na każdy klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](../articles/cosmos-db/unique-keys.md).
    
    Kliknij przycisk **OK**.

    W Eksploratorze danych zostanie wyświetlona nowa baza danych i kolekcja.

    ![Eksplorator danych w witrynie Azure Portal z widoczną nową bazą danych i kolekcją](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)