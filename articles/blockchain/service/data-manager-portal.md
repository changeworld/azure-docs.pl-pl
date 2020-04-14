---
title: Konfigurowanie menedżera danych blockchain przy użyciu witryny Azure portal — usługa Azure Blockchain Service
description: Twórz i zarządzaj Menedżerem danych Blockchain dla usługi Azure Blockchain za pomocą witryny Azure portal.
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 08f5a4a807087afce13dd4a6e96c0e9dd0a36103
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260602"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Konfigurowanie menedżera danych łańcucha bloków przy użyciu witryny Azure Portal

Skonfiguruj menedżera danych blockchain dla usługi Azure Blockchain, aby przechwytywać dane łańcucha bloków i wysyłać je do tematu usługi Azure Event Grid.

Aby skonfigurować wystąpienie menedżera danych blockchain, należy:

* Tworzenie wystąpienia menedżera danych blockchain dla węzła transakcji usługi Azure Blockchain
* Dodawanie aplikacji blockchain

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [przewodnik Szybki start: Utwórz członka łańcucha bloków za pomocą portalu Azure](create-member.md) lub [przewodnika Szybki start: utwórz członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure.](create-member-cli.md) Warstwa *Standard* Standard usługi Azure Blockchain Service jest zalecana podczas korzystania z Menedżera danych blockchain.
* Tworzenie [tematu siatki zdarzeń](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Dowiedz się więcej o [programach obsługi zdarzeń w usłudze Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Utwórz wystąpienie

Wystąpienie menedżera danych blockchain łączy i monitoruje węzeł transakcji usługi Azure Blockchain Service. Tylko użytkownicy z dostępem do węzła transakcji mogą utworzyć połączenie. Wystąpienie przechwytuje wszystkie nieprzetworzone dane transakcji bloku i nieprzetworzonego z węzła transakcji. Blockchain Data Manager publikuje komunikat **RawBlockAndTransactionMsg,** który jest nadzbiorem informacji zwróconych z web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) i [zapytań getTransaction.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)

Połączenie wychodzące wysyła dane łańcucha bloków do usługi Azure Event Grid. Podczas tworzenia wystąpienia można skonfigurować pojedyncze połączenie wychodzące. Blockchain Data Manager obsługuje wiele połączeń wychodzących w temacie siatki zdarzeń dla danego wystąpienia Menedżera danych Blockchain. Możesz wysyłać dane łańcucha bloków do jednego miejsca docelowego lub wysyłać dane łańcucha bloków do wielu miejsc docelowych. Aby dodać inne miejsce docelowe, wystarczy dodać dodatkowe połączenia wychodzące do wystąpienia.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do członka usługi Azure Blockchain, którego chcesz połączyć z Menedżerem danych blockchain. Wybierz **Blockchain Data Manager**.
1. Wybierz pozycję **Dodaj**.

    ![Dodaj Menedżera danych blockchain](./media/data-manager-portal/add-instance.png)

    Wprowadź następujące wartości:

    Ustawienie | Opis
    --------|------------
    Nazwa | Wprowadź unikatową nazwę połączonego Menedżera danych Blockchain. Nazwa Blockchain Data Manager może zawierać małe litery i cyfry i ma maksymalną długość 20 znaków.
    Węzeł transakcji | Wybierz węzeł transakcji. Wymienione są tylko węzły transakcji, do których masz dostęp do odczytu.
    Nazwa połączenia | Wprowadź unikatową nazwę połączenia wychodzącego, na którym wysyłane są dane transakcji łańcucha bloków.
    Punkt końcowy siatki zdarzeń | Wybierz temat siatki zdarzeń w tej samej subskrypcji co wystąpienie Menedżera danych blockchain.

1. Kliknij przycisk **OK**.

    Utworzenie wystąpienia menedżera danych blockchain zajmuje mniej niż minutę. Po wdrożeniu wystąpienia jest ono uruchamiane automatycznie. Uruchomione wystąpienie Menedżera danych blockchain przechwytuje zdarzenia łańcucha bloków z węzła transakcji i wysyła dane do połączeń wychodzących.

    Nowe wystąpienie pojawia się na liście wystąpień menedżera danych blockchain dla członka usługi Azure Blockchain Service.

    ![Lista wystąpień członków danych blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Dodaj aplikację blockchain

Jeśli dodasz aplikację blockchain, Blockchain Data Manager dekoduje zdarzenia i stan właściwości dla aplikacji. W przeciwnym razie wysyłane są tylko nieprzetworzone dane transakcji bloku i nieprzetworzonego. Blockchain Data Manager odnajduje również adresy umów po wdrożeniu umowy. Do wystąpienia Menedżera danych Blockchain można dodać wiele aplikacji blockchain.

> [!IMPORTANT]
> Obecnie aplikacje łańcucha bloków, które deklarują [typy tablic](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) solidity lub [typy mapowania,](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) nie są w pełni obsługiwane. Właściwości zadeklarowane jako typy tablic lub mapowania nie zostaną zdekodowane w *komunikatach ContractPropertiesMsg* lub *DecodedContractEventsMsg.*

Blockchain Data Manager wymaga inteligentnego kontraktu ABI i wdrożonego pliku kodu bajtowego, aby dodać aplikację.

### <a name="get-contract-abi-and-bytecode"></a>Pobierz ABI kontraktu i kod bajtowy

ABI umowy definiuje interfejsy inteligentnego kontraktu. Opisano w nim sposób interakcji z inteligentnym kontraktem. Możesz użyć [rozszerzenia Azure Blockchain Development Kit for Ethereum,](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) aby skopiować umowę ABI do schowka.

1. W okienku eksploratora kodu programu Visual Studio rozwiń folder **kompilacji/kontraktów** projektu Solidity.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Wybierz **pozycję Kopiuj umowę ABI**.

    ![Okienko Kod programu Visual Studio z zaznaczeniem ABI kopiowania umowy](./media/data-manager-portal/abi-devkit.png)

    Umowa ABI jest kopiowana do schowka.

1. Zapisz **tablicę abi** jako plik JSON. Na przykład *abi.json*. Plik jest używany w późniejszym kroku.

Blockchain Data Manager wymaga wdrożonego kodu bajtowego dla inteligentnego kontraktu. Wdrożony kod bajtowy różni się od kodu bajtowego inteligentnego kontraktu. Użyj rozszerzenia zestawu deweloperskiego platformy Azure blockchain, aby skopiować kod bajtowy do schowka.

1. W okienku eksploratora kodu programu Visual Studio rozwiń folder **kompilacji/kontraktów** projektu Solidity.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Wybierz **opcję Kopiuj kod bajtowy transakcji**.

    ![Okienko Kod programu Visual Studio z wyborem kod bajtowy transakcji kopiowania](./media/data-manager-portal/bytecode-devkit.png)

    Kod bajtowy jest kopiowany do schowka.

1. Zapisz wartość **kodu bajtowego** jako plik JSON. Na przykład *bytecode.json*. Plik jest używany w późniejszym kroku.

W poniższym przykładzie przedstawiono pliki *abi.json* i *bytecode.json* otwarte w edytorze kodu VS. Pliki powinny wyglądać podobnie.

![Przykład plików abi.json i bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Tworzenie adresu URL ABI i kodu bajtowego kontraktu

Blockchain Data Manager wymaga, aby pliki ABI i bajtcode umowy były dostępne za pomocą adresu URL podczas dodawania aplikacji. Za pomocą konta usługi Azure Storage można podać adres URL dostępny dla osób prywatnych.

#### <a name="create-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Przekazywanie plików kontraktu

1. Utwórz nowy kontener dla konta magazynu. Wybierz **kontenery > kontenera**.

    ![Tworzenie kontenera konta magazynu](./media/data-manager-portal/create-container.png)

    | Pole | Opis |
    |-------|-------------|
    | Nazwa  | Nazwij kontener. Na przykład *smartcontract* |
    | Poziom dostępu publicznego | Wybierz *pozycję Prywatne (bez dostępu anonimowego)* |

1. Wybierz przycisk **OK**, aby utworzyć kontener.
1. Wybierz kontener, a następnie wybierz pozycję **Przekaż**.
1. Wybierz oba pliki JSON utworzone w sekcji [Pobierz ABI kontraktu i kod bajtowy.](#get-contract-abi-and-bytecode)

    ![Przekazywanie obiektu blob](./media/data-manager-portal/upload-blobs.png)

    Wybierz **pozycję Przekaż**.

#### <a name="generate-url"></a>Generowanie adresu URL

Dla każdego obiektu blob wygeneruj podpis dostępu współdzielonego.

1. Wybierz obiekt blob ABI JSON.
1. Wybierz **pozycję Generuj SYGNATURĘ**
1. Ustaw żądany wygaśnięcie podpisu dostępu, a następnie wybierz pozycję **Generuj token i adres URL sygnatury dostępu współdzielonego**obiektu blob .

    ![Generowanie tokenu Sygnatury dostępu Współ](./media/data-manager-portal/generate-sas.png)

1. Skopiuj **adres URL sygnatury dostępu Współdzielonego** obiektu Blob i zapisz go w następnej sekcji.
1. Powtórz kroki [Generowanie adresu URL](#generate-url) dla obiektu blob JSON kodu bajtowego.

### <a name="add-application-to-instance"></a>Dodawanie aplikacji do wystąpienia

1. Wybierz wystąpienie Menedżera danych blockchain z listy wystąpień.
1. Wybierz **aplikacje Blockchain**.
1. Wybierz pozycję **Dodaj**.

    ![Dodawanie aplikacji blockchain](./media/data-manager-portal/add-application.png)

    Wprowadź nazwę aplikacji blockchain i inteligentnych adresów URL ABI i bajtcode.

    Ustawienie | Opis
    --------|------------
    Nazwa | Wprowadź unikatową nazwę aplikacji blockchain do śledzenia.
    Umowa ABI | Ścieżka adresu URL do pliku ABI kontraktu. Aby uzyskać więcej informacji, zobacz [Tworzenie umowy ABI i adres URL kodu bajtowego](#create-contract-abi-and-bytecode-url).
    Kod bajtowy kontraktu | Ścieżka adresu URL do pliku kodu bajtowego. Aby uzyskać więcej informacji, zobacz [Tworzenie umowy ABI i adres URL kodu bajtowego](#create-contract-abi-and-bytecode-url).

1. Kliknij przycisk **OK**.

    Po utworzeniu aplikacji aplikacja pojawia się na liście aplikacji blockchain.

    ![Lista aplikacji blockchain](./media/data-manager-portal/artifact-list.png)

Możesz usunąć konto usługi Azure Storage lub użyć go do skonfigurowania większej liczby aplikacji blockchain. Jeśli chcesz usunąć konto usługi Azure Storage, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

## <a name="stop-instance"></a>Zatrzymaj wystąpienie

Zatrzymaj wystąpienie Menedżera łańcucha bloków, gdy chcesz zatrzymać przechwytywanie zdarzeń łańcucha bloków i wysyłanie danych do połączeń wychodzących. Po zatrzymaniu wystąpienia nie są naliczane żadne opłaty za Menedżera danych blockchain. Aby uzyskać więcej informacji, zobacz [cennik](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Przejdź do **przeglądu** i wybierz pozycję **Zatrzymaj**.

    ![Zatrzymaj wystąpienie](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj następny samouczek tworzenia eksploratora komunikatów transakcji łańcucha bloków przy użyciu menedżera danych blockchain i usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Wysyłanie danych do usługi Azure Cosmos DB za pomocą menedżera danych łańcucha bloków](data-manager-cosmosdb.md)