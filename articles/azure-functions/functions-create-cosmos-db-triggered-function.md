---
title: Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB | Microsoft Docs
description: Utwórz za pomocą usługi Azure Functions funkcję niewymagającą użycia serwera wywoływaną w przypadku dodania danych do bazy danych w usłudze Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.date: 03/27/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 5ae81824c2f35dd2ad26d64f3a343fecc549d805
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095219"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB

Dowiedz się, jak utworzyć funkcję wyzwalaną w przypadku dodania lub zmiany danych w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji o usłudze Azure Cosmos DB, zobacz [Azure Cosmos DB: Serverless database computing using Azure Functions](..\cosmos-db\serverless-computing-database.md) (Azure Cosmos DB: przetwarzanie danych w bazie danych bez użycia serwera dzięki usłudze Azure Functions).

![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

+ Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Tworzenie wyzwalacza usługi Azure Cosmos DB

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **Funkcja niestandardowa**. Spowoduje to wyświetlenie pełnego zestawu szablonów funkcji.

    ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. W polu wyszukiwania wpisz `cosmos`, a następnie wybierz żądany język dla szablonu wyzwalacza usługi Azure Cosmos DB.

    ![Wybieranie wyzwalacza usługi Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Skonfiguruj nowy wyzwalacz, wprowadzając ustawienia określone w tabeli znajdującej się poniżej obrazu.

    ![Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nazwa** | Domyślne | Użyj domyślnej nazwy funkcji sugerowanej przez szablon. |
    | **Nazwa kolekcji** | Items | Nazwa monitorowanej kolekcji. |
    | **Utwórz kolekcję dzierżaw, jeśli nie istnieje** | Zaznaczone | Kolekcja jeszcze nie istnieje, więc należy ją utworzyć. |
    | **Nazwa bazy danych** | Zadania | Nazwa bazy danych zawierającej monitorowaną kolekcję. |

4. Wybierz pozycję **Nowe** obok etykiety **Połączenie konta usługi Azure Cosmos DB** i wybierz istniejące konto usługi Cosmos DB lub pozycję **+ Utwórz nowe**. 
 
    ![Konfigurowanie połączenia z usługą Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. W przypadku tworzenia nowego konta usługi Azure Cosmos DB wprowadź w obszarze **Nowe konto** ustawienia wymienione w tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Identyfikator** | Nazwa bazy danych | Unikatowy identyfikator bazy danych usługi Azure Cosmos DB  |
    | **Interfejs API** | SQL | W tym temacie jest używany interfejs API SQL.  |
    | **Subskrypcja** | Subskrypcja platformy Azure | Subskrypcja, w ramach której jest tworzone to nowe konto usługi Cosmos DB.  |
    | **Grupa zasobów** | myResourceGroup |  Użyj istniejącej grupy zasobów zawierającej aplikację funkcji. |
    | **Lokalizacja**  | WestEurope | Wybierz lokalizację znajdującą się w pobliżu aplikacji funkcji lub innych aplikacji, które korzystają z przechowywanych dokumentów.  |

6. Kliknij przycisk **OK**, aby utworzyć bazę danych. Tworzenie bazy danych może potrwać kilka minut. Po utworzeniu bazy danych parametry połączenia bazy danych są przechowywane jako ustawienie aplikacji funkcji. Nazwa tego ustawienia aplikacji jest wstawiana w sekcji **Połączenie konta usługi Azure Cosmos DB**. 

7. Kliknij pozycję **Utwórz**, aby utworzyć funkcję wyzwalaną usługi Azure Cosmos DB. Po utworzeniu funkcji zostanie wyświetlony kod funkcji oparty na szablonie.  

    ![Szablon funkcji usługi Azure Cosmos DB w języku C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Ten szablon funkcji zapisuje w dziennikach liczbę dokumentów oraz identyfikator pierwszego dokumentu. 

Następnie należy połączyć się z kontem usługi Azure Cosmos DB i utworzyć w bazie danych kolekcję **Zadania**. 

## <a name="create-the-items-collection"></a>Tworzenie kolekcji Elementy

1. Otwórz drugie wystąpienie witryny [Azure Portal](https://portal.azure.com) w nowej karcie przeglądarki. 

2. Rozwiń pasek ikon po lewej stronie portalu, wpisz ciąg `cosmos` w polu wyszukiwania, a następnie wybierz pozycję **Azure Cosmos DB**.

    ![Wyszukiwanie usługi Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Wybierz konto usługi Azure Cosmos DB, a następnie wybierz pozycję **Eksplorator danych**. 
 
3. W obszarze **Kolekcje** wybierz pozycję **taskDatabase**, a następnie **Nowa kolekcja**.

    ![Tworzenie kolekcji](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. W obszarze **Dodawanie kolekcji** wprowadź ustawienia wymienione w tabeli pod ilustracją. 
 
    ![Definiowanie kolekcji taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Ustawienie|Sugerowana wartość|Opis |
    | ---|---|--- |
    | **Identyfikator bazy danych** | Zadania |Nazwa nowej bazy danych. Musi być zgodna z nazwą zdefiniowaną w powiązaniu funkcji. |
    | **Identyfikator kolekcji** | Items | Nazwa nowej kolekcji. Musi być zgodna z nazwą zdefiniowaną w powiązaniu funkcji.  |
    | **Pojemność magazynu** | Stała (10 GB)|Użyj wartości domyślnej. Ta wartość to pojemność magazynu bazy danych. |
    | **Przepływność** |400 RU| Użyj wartości domyślnej. Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę. |
    | **[Klucz partycji](../cosmos-db/partition-data.md#best-practices-when-choosing-a-partition-key)** | /category|Klucz partycji służący do równomiernego dystrybuowania danych do każdej partycji. Wybór odpowiedniego klucza partycji jest ważny w celu utworzenia wydajnej kolekcji. | 

1. Kliknij przycisk **OK**, aby utworzyć kolekcję **Zadania**. Tworzenie kolekcji może chwilę potrwać.

Po utworzeniu kolekcji wskazanej w powiązaniu funkcji możesz przetestować funkcję, dodając dokumenty do nowej kolekcji.

## <a name="test-the-function"></a>Testowanie funkcji

1. Rozwiń nową kolekcję **taskCollection** w Eksploratorze danych, a następnie wybierz kolejno pozycje **Dokumenty** i **Nowy dokument**.

    ![Tworzenie dokumentu w kolekcji taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Zastąp zawartość nowego dokumentu następującą zawartością, a następnie wybierz pozycję **Zapisz**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Przejdź do pierwszej karty przeglądarki, zawierającej funkcję w portalu. Rozwiń dzienniki funkcji i sprawdź, czy nowy dokument spowodował wyzwolenie funkcji. Zobaczysz, że wartość identyfikatora dokumentu `task1` jest zapisana w dziennikach. 

    ![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Opcjonalnie) Wróć do dokumentu, wprowadź zmianę i kliknij pozycję **Aktualizuj**. Następnie wróć do dzienników funkcji i sprawdź, czy aktualizacja również spowodowała wyzwolenie funkcji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję uruchamianą w przypadku dodania lub zmodyfikowania dokumentu w usłudze Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji na temat wyzwalaczy bazy danych Azure Cosmos DB, zobacz [Azure Cosmos DB bindings for Azure Functions](functions-bindings-cosmosdb.md) (Powiązania bazy danych Azure Cosmos DB na potrzeby usługi Azure Functions).
