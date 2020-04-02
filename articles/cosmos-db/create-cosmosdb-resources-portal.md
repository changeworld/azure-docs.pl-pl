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
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521141"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Szybki start: tworzenie konta, bazy danych, kontenerów i elementów usługi Azure Cosmos z witryny Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Usługi Azure Cosmos DB można używać do szybkiego tworzenia i wykonywania zapytań o bazy danych klucz/wartość, bazy danych dokumentów i baz danych wykresów, z których wszystkie korzystają z możliwości dystrybucji globalnej i skali poziomej w rdzeniu usługi Azure Cosmos DB. 

Ten przewodnik Szybki start pokazuje, jak używać portalu Azure portal do tworzenia konta [interfejsu API SQL](sql-api-introduction.md) usługi Azure Cosmos DB, tworzenia bazy danych dokumentów i kontenera oraz dodawania danych do kontenera. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne konto próbne usługi Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Przejdź do witryny [Azure Portal](https://portal.azure.com/), aby utworzyć konto usługi Azure Cosmos DB. Wyszukaj i wybierz usługę **Azure Cosmos DB**.

   ![Okienko Bazy danych w witrynie Azure Portal](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Wybierz pozycję **Dodaj**.
1. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź podstawowe ustawienia nowego konta usługi Azure Cosmos. 

    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
    |Nazwa konta|Unikatowa nazwa|Wprowadź nazwę, która będzie identyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanej nazwy w celu utworzenia identyfikatora URI, użyj unikatowej nazwy.<br><br>Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.|
    |interfejs API|Typ konta do utworzenia|Wybierz pozycję **Core (SQL)**, aby utworzyć bazę danych dokumentów i wykonać zapytanie przy użyciu składni języka SQL. <br><br>Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB udostępnia pięć interfejsów API: Core (SQL) i MongoDB dla danych dokumentu, Gremlin dla danych wykresu, Tabela platformy Azure i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>[Dowiedz się więcej o interfejsie API SQL](introduction.md).|
    |Zastosuj rabat bezpłatny poziom|Zastosuj lub nie stosuj|Dzięki bezpłatnej warstwie usługi Azure Cosmos DB otrzymasz pierwsze 400 ru/s i 5 GB miejsca na koncie. Dowiedz się więcej o [warstwie bezpłatnej](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|
    |Typ konta|Produkcja lub nieprodukcja|Wybierz **opcję Produkcja,** jeśli konto będzie używane dla obciążenia produkcyjnego. Wybierz **opcję Nieprodukcja,** jeśli konto będzie używane do nieprodukcji, np. Jest to ustawienie tagu zasobów platformy Azure, które umożliwia dostrojenie środowiska portalu, ale nie wpływa na podstawowe konto usługi Azure Cosmos DB. Tę wartość można zmienić w dowolnym momencie.|


> [!NOTE]
> Możesz mieć maksymalnie jedno bezpłatne konto usługi Azure Cosmos DB na subskrypcję platformy Azure i musisz wyrazić zgodę podczas tworzenia konta. Jeśli nie widzisz opcji zastosowania rabatu warstwy bezpłatnej, oznacza to, że inne konto w subskrypcji zostało już włączone z warstwą bezpłatną.
   
   ![Strona nowego konta usługi Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Wybierz pozycję **Przegląd + utwórz**. Sekcje **Sieć** i **Tagi** możesz pominąć.

1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    ![Strona konta usługi Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

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
