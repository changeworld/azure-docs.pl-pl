---
title: Szybki Start — tworzenie zasobów Azure Cosmos DB z poziomu Azure Portal
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia bazy danych, kontenera i elementów usługi Azure Cosmos przy użyciu Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/02/2019
ms.openlocfilehash: 58fa891503342ac0ec9d41e9c458a71d18e58161
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445600"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Szybki Start: Tworzenie konta, bazy danych, kontenera i elementów usługi Azure Cosmos w Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Za pomocą Azure Cosmos DB można szybko tworzyć i wykonywać zapytania dotyczące baz danych typu klucz/wartość, baz danych dokumentów i baz danych grafów, a wszystkie korzyści wynikające z dystrybucji globalnej i możliwości skalowania poziomego w ramach rdzenia Azure Cosmos DB. 

W tym przewodniku szybki start pokazano, jak używać Azure Portal do tworzenia Azure Cosmos DB konta [interfejsu API SQL](sql-api-introduction.md) , tworzenia bazy danych dokumentów i kontenera oraz dodawania danych do kontenera. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne Azure Cosmos DB konto wersji próbnej
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Dodawanie bazy danych i kontenera 

Możesz użyć Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1.  Wybierz pozycję **Eksplorator danych** w lewym obszarze nawigacji na stronie konta Azure Cosmos DB, a następnie wybierz pozycję **nowy kontener**. 
    
    Może być konieczne przewinięcie w prawo, aby wyświetlić okno **Dodawanie kontenera** .
    
    ![Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  W okienku **Dodaj kontener** wprowadź ustawienia dla nowego kontenera.
    
    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|ToDoList|Wprowadź *todolist* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Items|Wprowadź *elementy* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule jest stosowany */Category* jako klucz partycji.|

    
    Nie dodawaj **unikatowych kluczy** dla tego przykładu. Klucze unikatowe umożliwiają dodanie warstwy integralności danych do bazy danych, zapewniając unikatowość jednej lub więcej wartości na klucz partycji. Aby uzyskać więcej informacji, zobacz [unikalne klucze w Azure Cosmos DB](unique-keys.md).
    
1.  Kliknij przycisk **OK**. W Eksplorator danych zostanie wyświetlona nowa baza danych i kontener, który został utworzony.

## <a name="add-data-to-your-database"></a>Dodawanie danych do bazy danych

Dodaj dane do nowej bazy danych przy użyciu Eksplorator danych.

1. W **Eksplorator danych**rozwiń bazę danych **todolist** i rozwiń kontener **elementów** . Następnie wybierz pozycję **elementy**, a następnie wybierz pozycję **nowy element**. 
   
   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Dodaj następującą strukturę do dokumentu po prawej stronie okienka **dokumenty** :

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Wybierz pozycję **Zapisz**.
   
   ![Kopiuj w danych JSON i wybierz pozycję Zapisz w Eksplorator danych w Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Ponownie wybierz pozycję **Nowy dokument** i Utwórz i Zapisz inny dokument z unikatowym `id`oraz wszystkie inne właściwości i wartości. Dokumenty mogą mieć dowolną strukturę, ponieważ Azure Cosmos DB nie nakłada żadnych schematów na dane.

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia bazy danych i kontenera przy użyciu Eksplorator danych. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)