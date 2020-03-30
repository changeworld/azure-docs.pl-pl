---
title: Szybki start — tworzenie zasobów usługi Azure Cosmos DB z witryny Azure portal
description: W tym przewodniku Szybki start pokazano, jak utworzyć bazę danych, kontener i elementy usługi Azure Cosmos przy użyciu witryny Azure portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: bc7e77cc498958b2f8f0c5b2d5ab2d59db97a235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240408"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Szybki start: tworzenie konta, bazy danych, kontenerów i elementów usługi Azure Cosmos z witryny Azure portal

> [!div class="op_single_selector"]
> * [Portal Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Usługi Azure Cosmos DB można używać do szybkiego tworzenia i wykonywania zapytań o bazy danych klucz/wartość, bazy danych dokumentów i baz danych wykresów, z których wszystkie korzystają z możliwości dystrybucji globalnej i skali poziomej w rdzeniu usługi Azure Cosmos DB. 

Ten przewodnik Szybki start pokazuje, jak używać portalu Azure portal do tworzenia konta [interfejsu API SQL](sql-api-introduction.md) usługi Azure Cosmos DB, tworzenia bazy danych dokumentów i kontenera oraz dodawania danych do kontenera. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne konto próbne usługi Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Dodawanie bazy danych i kontenera 

Eksploratora danych w witrynie Azure portal można utworzyć bazę danych i kontener. 

1.  Wybierz **Eksploratora danych** z lewej strony nawigacji na stronie konta usługi Azure Cosmos DB, a następnie wybierz pozycję **Nowy kontener**. 
    
    Może być konieczne przewinięcie w prawo, aby wyświetlić okno **Dodaj kontener.**
    
    ![Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  W okienku **Dodawanie kontenera** wprowadź ustawienia nowego kontenera.
    
    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|ToDoList|Wprowadź *Listę DoDon jako* nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **przepływność bazy danych aprowizuj,** umożliwia udostępnianie przepływności aprowizowanej do bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w oszczędzaniu kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Items|Wprowadź *elementy* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| Przykład opisany w tym artykule używa */category* jako klucz partycji.|

    
    Nie **dodawaj unikatowych kluczy** w tym przykładzie. Unikatowe klucze umożliwiają dodanie warstwy integralności danych do bazy danych, zapewniając unikatowość jednej lub więcej wartości na klucz partycji. Aby uzyskać więcej informacji, zobacz [Unikatowe klucze w usłudze Azure Cosmos DB](unique-keys.md).
    
1.  Kliknij przycisk **OK**. Eksplorator danych wyświetla nową bazę danych i utworzony kontener.

## <a name="add-data-to-your-database"></a>Dodawanie danych do bazy danych

Dodawanie danych do nowej bazy danych za pomocą Eksploratora danych.

1. W **Eksploratorze danych**rozwiń bazę danych **ToDoList** i rozwiń kontener **Elementy.** Następnie wybierz pozycję **Elementy**, a następnie wybierz pozycję **Nowy element**. 
   
   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Dodaj następującą strukturę do dokumentu po prawej stronie okienka **Dokumenty:**

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Wybierz **pozycję Zapisz**.
   
   ![Kopiowanie w json danych i wybierz Zapisz w Eksploratorze danych w witrynie Azure portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Ponownie wybierz **pozycję Nowy dokument,** a następnie `id`utwórz i zapisz inny dokument z unikatowym i dowolnymi innymi właściwościami i wartościami. Dokumenty mogą mieć dowolną strukturę, ponieważ usługa Azure Cosmos DB nie nakłada żadnego schematu na dane.

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Jeśli chcesz usunąć tylko bazy danych i użyć konta usługi Azure Cosmos w przyszłości, można usunąć bazę danych z następujących kroków:

* Masz do swojego konta usługi Azure Cosmos.
* Otwórz **Eksploratora danych**, kliknij prawym przyciskiem myszy bazę danych, którą chcesz usunąć, a następnie wybierz pozycję **Usuń bazę danych**.
* Wprowadź nazwę identyfikatora/bazy danych bazy danych, aby potwierdzić operację usuwania. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos DB, utworzyć bazę danych i kontener przy użyciu Eksploratora danych. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
