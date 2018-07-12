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
ms.openlocfilehash: 37d7b1d44c2a4b2f3cd2fd3ac881b106d5056279
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38726130"
---
Teraz możesz użyć narzędzia Eksplorator danych w witrynie Azure Portal, aby utworzyć bazę danych i tabelę. 

1. Kliknij pozycję **Eksplorator danych** > **Nowa tabela**. 
    
    Obszar **Dodaj tabelę** zostanie wyświetlony po prawej stronie. Aby go zobaczyć, być może trzeba będzie przewinąć w prawo.

    ![Eksplorator danych w witrynie Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodawanie tabeli** wprowadź ustawienia dla nowej tabeli.

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    Identyfikator tabeli|sample-table|Identyfikator nowej tabeli. W przypadku nazw tabel obowiązują takie same wymagania dotyczące znaków, jak dla identyfikatorów baz danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/ \ # ?` ani mieć spacji na końcu.
    Pojemność magazynu| Stała (10 GB)|Użyj wartości domyślnej **Stała (10 GB)**. Ta wartość to pojemność magazynu bazy danych.
    Przepływność|400 jednostek żądania|Zmień przepływność na 400 jednostek żądania na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.

    Kliknij przycisk **OK**.

    W Eksploratorze danych zostanie wyświetlona nowa baza danych i tabela.

    ![Eksplorator danych w witrynie Azure Portal z widoczną nową bazą danych i kolekcją](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)