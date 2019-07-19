---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68002675"
---
Teraz można użyć narzędzia Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1. Kliknij **Eksplorator danych** > **nowy kontener**. 
    
    Obszar **Dodaj kontener** jest wyświetlany po prawej stronie, może być konieczne przewinięcie w prawo w celu wyświetlenia go.

    ![Azure Portal Eksplorator danych, Dodaj blok kontenera](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na stronie **Dodawanie kontenera** wprowadź ustawienia dla nowego kontenera.

    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|Zadania|Wprowadź *Zadania* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków /, \\, #, ? ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Elementy|Wprowadź *elementy* jako nazwę nowego kontenera. Identyfikatory kontenerów mają takie same wymagania dotyczące znaków jak nazwy baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule jest stosowany */Category* jako klucz partycji. Ustawienie klucza partycji umożliwia usłudze Azure Cosmos DB skalowanie kolekcji w celu spełnienia wymagań dotyczących magazynu i przepływności Twojej aplikacji. Ogólnie rzecz biorąc, dobry klucz partycji to taki, który ma szeroki zakres unikatowych wartości oraz umożliwia równomierną dystrybucję woluminu żądań i magazynu w obciążeniu. [Dowiedz się więcej na temat partycjonowania.](../articles/cosmos-db/partitioning-overview.md)|
    
    Oprócz powyższych ustawień można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc unikatowe Zasady kluczy podczas tworzenia kontenera, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](../articles/cosmos-db/unique-keys.md).
    
    Kliknij przycisk **OK**. Eksplorator danych wyświetla nową bazę danych i kontener.

