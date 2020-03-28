---
title: Użyj menedżera danych blockchain, aby zaktualizować usługę Azure Cosmos DB — usługa Azure Blockchain
description: Używanie menedżera danych blockchain dla usługi Azure Blockchain service do wysyłania danych łańcucha bloków do usługi Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533185"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Samouczek: Wysyłanie danych do usługi Azure Cosmos DB za pomocą menedżera danych blockchain

W tym samouczku używasz Menedżera danych Blockchain dla usługi Azure Blockchain do rejestrowania danych transakcji łańcucha bloków w usłudze Azure Cosmos DB. Blockchain Data Manager przechwytuje, przekształca i dostarcza dane księgi blockchain do tematów usługi Azure Event Grid. Z usługi Azure Event Grid używasz łącznika aplikacji logiki platformy Azure do tworzenia dokumentów w bazie danych usługi Azure Cosmos DB. Po zakończeniu samouczka można eksplorować dane transakcji łańcucha bloków w Eksploratorze danych usługi Azure Cosmos DB.

[![Szczegóły transakcji blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie wystąpienia Menedżera danych blockchain
> * Dodawanie aplikacji blockchain do dekodowania właściwości i zdarzeń transakcji
> * Tworzenie konta i bazy danych usługi Azure Cosmos DB w celu przechowywania danych transakcji
> * Tworzenie aplikacji logiki platformy Azure w celu połączenia tematu siatki zdarzeń platformy Azure z usługą Azure Cosmos DB
> * Wysyłanie transakcji do księgi łańcucha bloków
> * Wyświetlanie zdekodowanych danych transakcji w usłudze Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [szybki start: Tworzenie członka łańcucha bloków przy użyciu portalu Azure](create-member.md) lub [przewodnika Szybki start: tworzenie członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Ukończ [szybki start: Łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą programu Visual Studio Code.](connect-vscode.md) Przewodnik po przewodnikach umożliwia zainstalowanie [zestawu Azure Blockchain Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) i skonfigurowanie środowiska programistycznego łańcucha bloków.
* Kompletny [samouczek: Tworzenie, tworzenie i wdrażanie inteligentnych kontraktów za pomocą programu Visual Studio Code umożliwia tworzenie, tworzenie i wdrażanie inteligentnych kontraktów.](send-transaction.md) Samouczek przechodzi przez tworzenie przykładowego kontraktu inteligentnego.
* Tworzenie [tematu siatki zdarzeń](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Dowiedz się więcej o [programach obsługi zdarzeń w usłudze Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Utwórz wystąpienie

Wystąpienie menedżera danych blockchain łączy i monitoruje węzeł transakcji usługi Azure Blockchain Service. Wystąpienie przechwytuje wszystkie nieprzetworzone dane transakcji bloku i nieprzetworzonego z węzła transakcji. Połączenie wychodzące wysyła dane łańcucha bloków do usługi Azure Event Grid. Podczas tworzenia wystąpienia można skonfigurować pojedyncze połączenie wychodzące.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do członka usługi Azure Blockchain service utworzonego w trybie [wstępnym Przewodnika Szybki start: Utwórz członka łańcucha bloków za pomocą portalu Azure.](create-member.md) Wybierz **Blockchain Data Manager**.
1. Wybierz pozycję **Dodaj**.

    ![Dodaj Menedżera danych blockchain](./media/data-manager-cosmosdb/add-instance.png)

    Wprowadź następujące wartości:

    Ustawienie | Przykład | Opis
    --------|---------|------------
    Nazwa | mywatcher ( mywatcher ) | Wprowadź unikatową nazwę połączonego Menedżera danych Blockchain.
    Węzeł transakcji | myblockchainmember | Wybierz domyślny węzeł transakcji członka usługi Azure Blockchain, który został utworzony w warsztacie wstępnym.
    Nazwa połączenia | cosmosdb | Wprowadź unikatową nazwę połączenia wychodzącego, na którym wysyłane są dane transakcji łańcucha bloków.
    Punkt końcowy siatki zdarzeń | myTopic ( myTopic ) | Wybierz temat siatki zdarzeń utworzony w warunek wstępny. Uwaga: Wystąpienie Menedżera danych blockchain i temat siatki zdarzeń muszą znajdować się w tej samej subskrypcji.

1. Kliknij przycisk **OK**.

    Utworzenie wystąpienia menedżera danych blockchain zajmuje mniej niż minutę. Po wdrożeniu wystąpienia jest ono uruchamiane automatycznie. Uruchomione wystąpienie Menedżera danych blockchain przechwytuje zdarzenia łańcucha bloków z węzła transakcji i wysyła dane do siatki zdarzeń.

## <a name="add-application"></a>Dodawanie aplikacji

Dodaj aplikację **blockchain helloblockchain,** aby Blockchain Data Manager dekodował stan zdarzenia i właściwości. Blockchain Data Manager wymaga inteligentnego kontraktu ABI i pliku kodu bajtowego, aby dodać aplikację.

### <a name="get-contract-abi-and-bytecode"></a>Uzyskaj umowy ABI i kod bajtowy

ABI umowy definiuje interfejsy inteligentnego kontraktu. Opisano w nim sposób interakcji z inteligentnym kontraktem. Możesz użyć [rozszerzenia Azure Blockchain Development Kit for Ethereum,](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) aby skopiować umowę ABI do schowka.

1. W okienku Eksploratora kodu programu Visual Studio rozwiń folder **kompilacji/kontraktów** projektu **helloblockchain** Solidity utworzonego w [samouczku wstępnym: tworzenie, tworzenie i wdrażanie inteligentnych kontraktów za pomocą programu Visual Studio Code.](send-transaction.md)
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Wybierz **pozycję Kopiuj umowę ABI**.

    ![Okienko Kod programu Visual Studio z zaznaczeniem ABI kopiowania umowy](./media/data-manager-cosmosdb/abi-devkit.png)

    Umowa ABI jest kopiowana do schowka.

1. Zapisz **tablicę abi** jako plik JSON. Na przykład *abi.json*. Plik jest używany w późniejszym kroku.

Blockchain Data Manager wymaga wdrożonego kodu bajtowego dla inteligentnego kontraktu. Wdrożony kod bajtowy różni się od kodu bajtowego inteligentnego kontraktu. Użyj rozszerzenia zestawu deweloperskiego platformy Azure blockchain, aby skopiować kod bajtowy do schowka.

1. W okienku eksploratora kodu programu Visual Studio rozwiń folder **kompilacji/kontraktów** projektu Solidity.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Wybierz **opcję Kopiuj kod bajtowy transakcji**.

    ![Okienko Kod programu Visual Studio z wyborem kod bajtowy transakcji kopiowania](./media/data-manager-cosmosdb/bytecode-devkit.png)

    Kod bajtowy jest kopiowany do schowka.

1. Zapisz wartość **kodu bajtowego** jako plik JSON. Na przykład *bytecode.json*. Plik jest używany w późniejszym kroku.

W poniższym przykładzie przedstawiono pliki *abi.json* i *bytecode.json* otwarte w edytorze kodu VS. Pliki powinny wyglądać podobnie.

![Przykład plików abi.json i bytecode.json](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Tworzenie adresu URL ABI i kodu bajtowego kontraktu

Blockchain Data Manager wymaga, aby pliki ABI i bajtcode umowy były dostępne za pomocą adresu URL podczas dodawania aplikacji. Za pomocą konta usługi Azure Storage można podać adres URL dostępny dla osób prywatnych.

#### <a name="create-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Przekazywanie plików kontraktu

1. Utwórz nowy kontener dla konta magazynu. Wybierz **kontenery > kontenera**.

    ![Tworzenie kontenera konta magazynu](./media/data-manager-cosmosdb/create-container.png)

    | Ustawienie | Opis |
    |---------|-------------|
    | Nazwa  | Nazwij kontener. Na przykład *smartcontract* |
    | Poziom dostępu publicznego | Wybierz *pozycję Prywatne (bez dostępu anonimowego)* |

1. Wybierz przycisk **OK**, aby utworzyć kontener.
1. Wybierz kontener, a następnie wybierz pozycję **Przekaż**.
1. Wybierz oba pliki JSON utworzone w sekcji [Pobierz ABI kontraktu i kod bajtowy.](#get-contract-abi-and-bytecode)

    ![Przekazywanie obiektu blob](./media/data-manager-cosmosdb/upload-blobs.png)

    Wybierz **pozycję Przekaż**.

#### <a name="generate-url"></a>Generowanie adresu URL

Dla każdego obiektu blob wygeneruj podpis dostępu współdzielonego.

1. Wybierz obiekt blob ABI JSON.
1. Wybierz **pozycję Generuj SYGNATURĘ**
1. Ustaw żądany wygaśnięcie podpisu dostępu, a następnie wybierz pozycję **Generuj token i adres URL sygnatury dostępu współdzielonego**obiektu blob .

    ![Generowanie tokenu Sygnatury dostępu Współ](./media/data-manager-cosmosdb/generate-sas.png)

1. Skopiuj **adres URL sygnatury dostępu Współdzielonego** obiektu Blob i zapisz go w następnej sekcji.
1. Powtórz kroki [Generowanie adresu URL](#generate-url) dla obiektu blob JSON kodu bajtowego.

### <a name="add-helloblockchain-application-to-instance"></a>Dodawanie aplikacji helloblockchain do wystąpienia

1. Wybierz wystąpienie Menedżera danych blockchain z listy wystąpień.
1. Wybierz **aplikacje Blockchain**.
1. Wybierz pozycję **Dodaj**.

    ![Dodawanie aplikacji blockchain](./media/data-manager-cosmosdb/add-application.png)

    Wprowadź nazwę aplikacji blockchain i inteligentnych adresów URL ABI i bajtcode.

    Ustawienie | Opis
    --------|------------
    Nazwa | Wprowadź unikatową nazwę aplikacji blockchain do śledzenia.
    Umowa ABI | Ścieżka adresu URL do pliku ABI kontraktu. Aby uzyskać więcej informacji, zobacz [Tworzenie umowy ABI i adres URL kodu bajtowego](#create-contract-abi-and-bytecode-url).
    Kod bajtowy kontraktu | Ścieżka adresu URL do pliku kodu bajtowego. Aby uzyskać więcej informacji, zobacz [Tworzenie umowy ABI i adres URL kodu bajtowego](#create-contract-abi-and-bytecode-url).

1. Kliknij przycisk **OK**.

    Po utworzeniu aplikacji aplikacja pojawia się na liście aplikacji blockchain.

    ![Lista aplikacji blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Możesz usunąć konto usługi Azure Storage lub użyć go do skonfigurowania większej liczby aplikacji blockchain. Jeśli chcesz usunąć konto usługi Azure Storage, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

## <a name="create-azure-cosmos-db"></a>Tworzenie bazy danych usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Dodawanie bazy danych i kontenera

Eksploratora danych w witrynie Azure portal można utworzyć bazę danych i kontener.

1. Wybierz **Eksploratora danych** z lewej strony nawigacji na stronie konta usługi Azure Cosmos DB, a następnie wybierz pozycję **Nowy kontener**.
1. W okienku **Dodawanie kontenera** wprowadź ustawienia nowego kontenera.

    ![Dodawanie ustawień kontenera](./media/data-manager-cosmosdb/add-container.png)

    | Ustawienie | Opis
    |---------|-------------|
    | Identyfikator bazy danych | Wprowadź **dane łańcucha bloków** jako nazwę nowej bazy danych. |
    | Przepływność | Pozostaw przepływność na **400** jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.|
    | Identyfikator kontenera | Wprowadź **wiadomości** jako nazwę nowego kontenera. |
    | Klucz partycji | Użyj **/MessageType** jako klucz partycji. |

1. Kliknij przycisk **OK**. Eksplorator danych wyświetla nową bazę danych i utworzony kontener.

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

Usługa Azure Logic Apps ułatwia planowanie i automatyzację procesów biznesowych i przepływów pracy, gdy trzeba zintegrować systemy i usługi. Za pomocą aplikacji logiki można połączyć usługę Event Grid z usługą Azure Cosmos DB.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.
1. Podaj szczegółowe informacje o tym, gdzie utworzyć aplikację logiki. Po zakończeniu wybierz pozycję **Utwórz**.

    Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki, zobacz [Tworzenie zautomatyzowanych przepływów pracy za pomocą usługi Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po wdrożeniu aplikacji przez platformę Azure wybierz zasób aplikacji logiki.
1. W Projektancie aplikacji logiki w obszarze **Szablony**wybierz pozycję **Pusta aplikacja logiki**.

### <a name="add-event-grid-trigger"></a>Dodaj wyzwalacz siatki zdarzeń

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy. Użyj wyzwalacza usługi Azure Event Grid, aby wysłać dane transakcji łańcucha bloków z usługi Event Grid do usługi Cosmos DB.

1. W Projektancie aplikacji logiki wyszukaj i wybierz łącznik **usługi Azure Event Grid.**
1. Na karcie **Wyzwalacze** wybierz pozycję **Kiedy wystąpi zdarzenie zasobu**.
1. Utwórz połączenie interfejsu API z tematem siatki zdarzeń.

    ![Ustawienia wyzwalacza siatki zdarzeń](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Ustawienie | Opis
    |---------|-------------|
    | Subskrypcja | Wybierz subskrypcję zawierającą temat siatki zdarzeń. |
    | Typ zasobu | Wybierz **pozycję Microsoft.EventGrid.Topics**. |
    | Nazwa zasobu | Wybierz nazwę tematu siatki zdarzeń, w którym Blockchain Data Manager wysyła wiadomości danych transakcji. |

### <a name="add-cosmos-db-action"></a>Dodaj akcję usługi Cosmos DB

Dodaj akcję, aby utworzyć dokument w usłudze Cosmos DB dla każdej transakcji. Użyj typu komunikatu transakcji jako klucza partycji do kategoryzowania wiadomości.

1. Wybierz pozycję **Nowy krok**.
1. Na **Wybierz akcję**, wyszukaj usługę Azure **Cosmos DB**.
1. Wybierz **pozycję Azure Cosmos DB > Actions > Create or update document**.
1. Utwórz połączenie interfejsu API z bazą danych usługi Cosmos DB.

    ![Ustawienia połączenia usługi Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Ustawienie | Opis
    |---------|-------------|
    | Nazwa połączenia | Wybierz subskrypcję zawierającą temat siatki zdarzeń. |
    | Konto DocumentDB | Wybierz konto DocumentDB utworzone w sekcji [Tworzenie konta usługi Azure Cosmos DB.](#create-azure-cosmos-db) |

1. Wprowadź **identyfikator bazy danych** i **identyfikator kolekcji** dla usługi Azure Cosmos DB utworzonej wcześniej w sekcji [Dodaj bazę danych i kontener.](#add-a-database-and-container)

1. Wybierz ustawienie **Dokument.** W wyskakującym okienku *Dodaj zawartość dynamiczną* wybierz pozycję Wyrażenie i skopiuj i wklej następujące wyrażenie: **Expression**

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Wyrażenie pobiera część danych wiadomości i ustawia identyfikator do wartości sygnatury czasowej.

1. Wybierz **pozycję Dodaj nowy parametr** i wybierz pozycję Wartość **klucza partycji**.
1. Ustaw **wartość klucza partycji** na `"@{triggerBody()['data']['MessageType']}"`. Wartość musi być otoczona podwójnymi cudzysłowami.

    ![Projektant aplikacji logiki z ustawieniami usługi Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    Wartość ustawia klucz partycji na typ komunikatu transakcji.

1. Wybierz **pozycję Zapisz**.

Aplikacja logiki monitoruje temat siatki zdarzeń. Gdy nowy komunikat transakcji jest wysyłany z Menedżera danych blockchain, aplikacja logiki tworzy dokument w usłudze Cosmos DB.

## <a name="send-a-transaction"></a>Wysyłanie transakcji

Następnie wyślij transakcję do księgi blockchain, aby przetestować to, co utworzyłeś. Użyj funkcji **SendRequest** umowy **HelloBlockchain** utworzonej w [samouczku wstępnym: Tworzenie, tworzenie i wdrażanie inteligentnych kontraktów za pomocą programu Visual Studio Code umożliwia tworzenie, tworzenie i wdrażanie inteligentnych kontraktów.](send-transaction.md)

1. Użyj strony interakcji inteligentnego kontraktu zestawu Azure Blockchain Development Kit, aby wywołać funkcję **SendRequest.** Kliknij prawym przyciskiem myszy **helloblockchain.sol** i wybierz polecenie **Pokaż stronę interakcji inteligentnego kontraktu** z menu.

    ![Z menu wybierz polecenie Pokaż stronę inteligentnej interakcji z kontraktem](./media/data-manager-cosmosdb/contract-interaction.png)

1. Wybierz akcję umowy **SendRequest** i wprowadź **Hello, Blockchain!** dla parametru **requestMessage.** Wybierz **opcję Wykonaj,** aby wywołać funkcję **SendRequest** za pośrednictwem transakcji.

    ![Wykonaj akcję SendRequest](./media/data-manager-cosmosdb/sendrequest-action.png)

Funkcja SendRequest ustawia pola **RequestMessage** i **State.** Bieżący stan **requestmessage** jest argument, który przeszedł **Hello, Blockchain**. Wartość pola **Stan** pozostaje **Żądanie**.

## <a name="view-transaction-data"></a>Wyświetlanie danych transakcji

Po połączeniu menedżera danych blockchain z usługą Azure Cosmos DB można wyświetlić komunikaty transakcji łańcucha bloków w Eksploratorze danych usługi Cosmos DB.

1. Przejdź do widoku Eksploratora danych usługi Cosmos DB. Na **przykład, cosmosdb-blockchain > Data Explorer > dane blockchain > Wiadomości > Items**.

    ![Eksplorator danych bazy danych usługi Cosmos](./media/data-manager-cosmosdb/data-explorer.png)

    Eksplorator danych wyświetla listę komunikatów danych łańcucha bloków, które zostały utworzone w bazie danych usługi Cosmos DB.

1. Przejrzyj wiadomości, wybierając identyfikator elementu i znajdź wiadomość z pasującym haszem transakcji.

    [![Szczegóły transakcji blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Nieprzetworzony komunikat transakcji zawiera szczegółowe informacje o transakcji. Jednak informacje o właściwościach są szyfrowane.

    Od momentu dodania inteligentnego kontraktu HelloBlockchain do wystąpienia Menedżera danych Blockchain jest również wysyłany typ **komunikatu ContractProperties** zawierający zdekodowane informacje o właściwościach.

1. Znajdź komunikat **ContractProperties** dla transakcji. Powinna to być następna wiadomość na liście.

    [![Szczegóły transakcji blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    Tablica **DecodedProperties** zawiera właściwości transakcji.

Gratulacje! Pomyślnie utworzono eksploratora komunikatów transakcji przy użyciu menedżera danych blockchain i usługi Azure Cosmos DB.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można usunąć zasoby i grupy zasobów używane w tym samouczku. Aby usunąć grupę zasobów:

1. W witrynie Azure Portal przejdź do **grupy zasobów** w lewym okienku nawigacji i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o integracji z księgami blockchain.

> [!div class="nextstepaction"]
> [Korzystanie ze łącznika Łańcucha bloków Ethereum z aplikacjami azure logiki](ethereum-logic-app.md)
