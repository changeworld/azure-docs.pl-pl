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
ms.openlocfilehash: 45fa6a332697cf298b2446212701025007682357
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754273"
---
Można teraz użyć narzędzia Eksplorator danych w witrynie Azure portal, aby utworzyć bazę danych i kontener. 

1. Wybierz **Eksplorator danych** > **nowy kontener**. 
    
    **Dodaj kontener** obszar jest wyświetlany po prawej, konieczne może być przewinięcie w prawo w celu wyświetlenia go.

    ![Dodaj kontener w okienku Eksplorator danych witryny Azure portal](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. W **Dodaj kontener** strony, wprowadź ustawienia dla nowego kontenera.

    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|Zadania|Wprowadź *ToDoList* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź **Aprowizowanie przepływności bazy danych** opcji umożliwia udostępnianie przepływnością aprowizowaną do bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również zmniejszyć koszty. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Elementy|Wprowadź *elementów* jako nazwa dla nowego kontenera. Identyfikatory kontenerów mają te same wymagania znaków jako nazwy bazy danych.|
    |**Klucz partycji**| /category| W przykładzie opisanych w tym artykule użyto */category* jako klucza partycji.|
    
    Oprócz powyższych ustawień, można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc zasady unikatowych kluczy podczas tworzenia kontenera, możesz zapewnić unikatowość co najmniej jedną wartość na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](../articles/cosmos-db/unique-keys.md).
    
    Kliknij przycisk **OK**. W Eksploratorze danych zostanie wyświetlona, nową bazę danych i kontener.