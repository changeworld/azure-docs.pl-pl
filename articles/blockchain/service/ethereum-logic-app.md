---
title: Użyj łącznika Ethereum Blockchain z aplikacjami Azure Logic Apps — usługa Azure Blockchain
description: Użyj łącznika Ethereum Blockchain z usługą Azure Logic Apps, aby wyzwolić funkcje inteligentnego kontraktu i reagować na zdarzenia inteligentnego kontraktu.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: chrisseg
ms.openlocfilehash: 4a9acfd6098ed45fd92c7e3047b5d1446eeddbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325218"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Korzystanie ze łącznika Łańcucha bloków Ethereum z aplikacjami Azure Logic Apps

Użyj [łącznika Łańcucha bloków Ethereum](https://docs.microsoft.com/connectors/blockchainethereum/) z [usługą Azure Logic Apps,](https://docs.microsoft.com/azure/logic-apps/) aby wykonywać akcje inteligentnego kontraktu i reagować na zdarzenia inteligentnego kontraktu. Załóżmy na przykład, że chcesz utworzyć mikrousługę opartą na rest, która zwraca informacje z księgi łańcucha bloków. Za pomocą aplikacji logiki, można zaakceptować żądania HTTP, które kwerendy informacji przechowywanych w księdze łańcucha bloków.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ opcjonalny warunek wstępny [Przewodnika Szybki start: łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą programu Visual Studio Code.](connect-vscode.md) Przewodnik po przewodnikach umożliwia zainstalowanie [zestawu Azure Blockchain Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) i skonfigurowanie środowiska programistycznego łańcucha bloków.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Usługa Azure Logic Apps ułatwia planowanie i automatyzację procesów biznesowych i przepływów pracy, gdy trzeba zintegrować systemy i usługi. Najpierw należy utworzyć logikę, która używa łącznika Ethereum Blockchain.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.
1. W obszarze **Tworzenie aplikacji logiki**podaj szczegółowe informacje o tym, gdzie utworzyć aplikację logiki. Po zakończeniu wybierz pozycję **Utwórz**.

    Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki, zobacz [Tworzenie zautomatyzowanych przepływów pracy za pomocą usługi Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po wdrożeniu aplikacji przez platformę Azure wybierz zasób aplikacji logiki.
1. W Projektancie aplikacji logiki w obszarze **Szablony**wybierz pozycję **Pusta aplikacja logiki**.

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy.

Złącze Ethereum Blockchain ma jeden wyzwalacz i kilka akcji. Który wyzwalacz lub akcja, którego używasz, zależy od scenariusza.

Jeśli przepływ pracy:

* Wyzwala, gdy wystąpi zdarzenie w łańcuchu bloków, [Użyj wyzwalacza zdarzenia](#use-the-event-trigger).
* Wysyła zapytania lub wdraża inteligentny kontrakt, [Użyj akcji](#use-actions).
* Postępuje zgodnie ze typowym [scenariuszem Generowanie przepływu pracy przy użyciu zestawu dewelopera](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Użyj wyzwalacza zdarzenia

Użyj wyzwalaczy zdarzeń Ethereum Blockchain, gdy chcesz, aby aplikacja logiki działała po wystąpieniu zdarzenia inteligentnego kontraktu. Na przykład chcesz wysłać wiadomość e-mail, gdy wywoływana jest funkcja inteligentnego kontraktu.

1. W projektancie aplikacji logiki wybierz łącznik Ethereum Blockchain.
1. Na karcie **Wyzwalacze** wybierz pozycję **Po wystąpieniu zdarzenia inteligentnego kontraktu**.
1. Zmień lub [utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure Blockchain.
1. Wprowadź szczegółowe informacje o umowie inteligentnej, którą chcesz sprawdzić pod kątem zdarzeń.

    ![Projektant aplikacji logiki z właściwościami wyzwalacza zdarzeń](./media/ethereum-logic-app/event-properties.png)

    | Właściwość | Opis |
    |----------|-------------|
    | **Umowa ABI** | Interfejs binarny aplikacji kontraktowej (ABI) definiuje interfejsy inteligentnego kontraktu. Aby uzyskać więcej informacji, zobacz [Get the contract ABI](#get-the-contract-abi). |
    | **Inteligentny adres umowy** | Adres umowy to inteligentny adres docelowy kontraktu w łańcuchu bloków Ethereum. Aby uzyskać więcej informacji, zobacz [Get the contract address](#get-the-contract-address). |
    | **Nazwa wydarzenia** | Wybierz zdarzenie inteligentnego kontraktu do sprawdzenia. Zdarzenie wyzwala aplikację logiki. |
    | **Interwał** i **częstotliwość** | Wybierz, jak często chcesz sprawdzić, czy wydarzenie. |

1. Wybierz **pozycję Zapisz**.

Aby ukończyć aplikację logiki, można dodać nowy krok, który wykonuje akcję na podstawie wyzwalacza zdarzenia Ethereum Blockchain. Na przykład wyślij wiadomość e-mail.

## <a name="use-actions"></a>Korzystanie z akcji

Użyj akcji Ethereum Blockchain, gdy chcesz, aby aplikacja logiki wykonała akcję w księdze blockchain. Na przykład chcesz utworzyć mikrousługę opartą na rest, która wywołuje funkcję inteligentnego kontraktu, gdy żądanie HTTP jest składane do aplikacji logiki.

Akcje łącznika wymagają wyzwalacza. Akcji łącznika łańcucha bloków Ethereum jako następny krok po wyzwalaczu, taki jak wyzwalacz żądania HTTP dla mikrousług.

1. W Projektancie aplikacji logiki wybierz pozycję **Nowy krok** po wyzwoleniu.
1. Wybierz złącze Ethereum Blockchain.
1. Na karcie **Akcje** wybierz jedną z dostępnych akcji.

    ![Projektant aplikacji logiki z właściwościami akcji](./media/ethereum-logic-app/action-properties.png)

1. Zmień lub [utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure Blockchain.
1. W zależności od wybranej akcji podaj następujące szczegóły dotyczące funkcji inteligentnego kontraktu.

    | Właściwość | Opis |
    |----------|-------------|
    | **Umowa ABI** | ABI umowy definiuje interfejsy inteligentnego kontraktu. Aby uzyskać więcej informacji, zobacz [Get the contract ABI](#get-the-contract-abi). |
    | **Kod bajtowy kontraktu** | Skompilowany inteligentny kod bajtowy kontraktu. Aby uzyskać więcej informacji, zobacz [Get the contract bytecode](#get-the-contract-bytecode). |
    | **Inteligentny adres umowy** | Adres umowy to inteligentny adres docelowy kontraktu w łańcuchu bloków Ethereum. Aby uzyskać więcej informacji, zobacz [Get the contract address](#get-the-contract-address). |
    | **Nazwa funkcji inteligentnego kontraktu** | Wybierz nazwę funkcji inteligentnego kontraktu dla akcji. Lista jest wypełniana ze szczegółów w umowie ABI. |

    Po wybraniu nazwy funkcji inteligentnego kontraktu mogą zostać wyświetlone wymagane pola dla parametrów funkcji. Wprowadź wartości lub zawartość dynamiczną wymaganą dla scenariusza.

Teraz możesz korzystać z aplikacji logiki. Po wyzwoleniu zdarzenia aplikacji logiki uruchamia się akcja Ethereum Blockchain. Na przykład wyzwalacz żądania HTTP uruchamia akcję łańcucha bloków Ethereum, aby zbadać wartość stanu inteligentnego kontraktu. Ta kwerenda powoduje odpowiedź HTTP, która zwraca wartość.

## <a name="generate-a-workflow"></a>Generowanie przepływu pracy

Zestaw Azure Blockchain Development Kit dla rozszerzenia kodu programu Visual Studio Ethereum może generować przepływy pracy aplikacji logiki dla typowych scenariuszy. Dostępne są cztery scenariusze:

* Publikowanie danych w wystąpieniu bazy danych SQL usługi Azure
* Publikowanie zdarzeń w wystąpieniu usługi Azure Event Grid lub usługi Azure Service Bus
* Publikowanie raportów
* Mikrousługa oparta na restiezacie

 Zestaw Azure Blockchain Development Kit używa trufli, aby uprościć tworzenie łańcucha bloków. Aby wygenerować aplikację logiczną opartą na inteligentnym kontrakcie, potrzebujesz rozwiązania trufli dla inteligentnego kontraktu. Potrzebujesz również połączenia z siecią konsorcjum usługi Azure Blockchain Service. Aby uzyskać więcej informacji, zobacz [Łączenie się z siecią szybkiego startu sieci usługi Azure Blockchain Service za pomocą kodu programu Visual Studio.](connect-vscode.md)

Na przykład następujące kroki generują aplikację logiki opartej na rest na podstawie umowy inteligentnej programu Szybki start **HelloBlockchain:**

1. Na pasku bocznym eksploratora kodu programu Visual Studio rozwiń folder **kontraktów** w rozwiązaniu.
1. Kliknij prawym przyciskiem myszy **HelloBlockchain.sol** i wybierz polecenie **Generuj mikrousług dla inteligentnych kontraktów** z menu.

    ![Okienko Kod programu Visual Studio z wyborem Generuj mikrousługi dla kontraktów inteligentnych](./media/ethereum-logic-app/generate-logic-app.png)

1. W palecie poleceń wybierz pozycję **Aplikacja logiki**.
1. Wprowadź **adres umowy**. Aby uzyskać więcej informacji, zobacz [Get the contract address](#get-the-contract-address).
1. Wybierz subskrypcję platformy Azure i grupę zasobów dla aplikacji logiki.

    Konfiguracja aplikacji logiki i pliki kodu są generowane w **katalogu generatedLogicApp.**

1. Wyświetl **wygenerowany katalogLogicApp/HelloBlockchain.** Istnieje plik JSON aplikacji logiki dla każdej funkcji inteligentnego kontraktu, zdarzenia i właściwości.
1. Otwórz **generatedLogicApp/HelloBlockchain/Service/property. Plik RequestMessage.logicapp.json** i skopiuj zawartość.

    ![Plik JSON z kodem do skopiowania](./media/ethereum-logic-app/requestmessage.png)

1. W aplikacji logiki wybierz widok **kodu aplikacji Logika**. Zastąp istniejący JSON wygenerowaną aplikacją logiki JSON.

    ![Widok kodu aplikacji logiki z nowym kodem aplikacji zastąpione](./media/ethereum-logic-app/code-view.png)

1. Wybierz **projektanta,** aby przełączyć się do widoku projektanta.
1. Aplikacja logiki zawiera podstawowe kroki dla scenariusza. Musisz jednak zaktualizować szczegóły konfiguracji łącznika Ethereum Blockchain.
1. Wybierz krok **Połączenia** i zmień lub [utwórz połączenie interfejsu API z usługą](#create-an-api-connection) Azure Blockchain.

    ![Widok projektanta z wyborem Połączenia](./media/ethereum-logic-app/microservice-logic-app.png)

1. Teraz możesz korzystać z aplikacji logiki. Aby przetestować mikrousługę opartą na rest, wystaw żądanie HTTP POST do adresu URL żądania aplikacji logiki. Skopiuj zawartość **adresu URL HTTP POST** z kroku Po **odebraniu żądania HTTP.**

    ![Okienko Projektant aplikacji logiki z adresem URL POST HTTP](./media/ethereum-logic-app/post-url.png)

1. Użyj cURL, aby utworzyć żądanie HTTP POST. Zastąp * \<adres URL\> * wpisu HTTP tekst zastępczy adresem URL z poprzedniego kroku.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Polecenie cURL zwraca odpowiedź z aplikacji logiki. W takim przypadku odpowiedź jest dane wyjściowe z **RequestMessage** funkcji inteligentnego kontraktu.

    ![Dane wyjściowe kodu z funkcji inteligentnego kontraktu RequestMessage](./media/ethereum-logic-app/curl.png)

Aby uzyskać więcej informacji na temat korzystania z zestawu deweloperskiego, zobacz [stronę wiki Azure Blockchain Development Kit for Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Tworzenie połączenia interfejsu API

Połączenie interfejsu API z łańcuchem bloków jest wymagane dla złącza Ethereum Blockchain. Łącznika interfejsu API można użyć dla wielu aplikacji logiki. Niektóre właściwości są wymagane, a inne zależą od scenariusza.

> [!IMPORTANT]
> Klucz prywatny lub adres konta i hasło są wymagane do tworzenia transakcji w łańcuchu bloków. Potrzebna jest tylko jedna forma uwierzytelniania. Nie musisz podawać zarówno klucza prywatnego, jak i szczegółów konta. Wykonywanie zapytań o kontrakty nie wymaga transakcji. Jeśli używasz akcji, które kwerendy stanu umowy, klucz prywatny lub adres konta i hasło nie są wymagane.

Aby ułatwić konfigurowanie połączenia z członkiem usługi Azure Blockchain, poniższa lista zawiera możliwe właściwości, które mogą być potrzebne w zależności od scenariusza.

| Właściwość | Opis |
|----------|-------------|
|**Nazwa połączenia** | Nazwa połączenia interfejsu API. Wymagany. |
|**Punkt końcowy RPC Ethereum** | Adres HTTP węzła transakcji usługi Azure Blockchain Service. Wymagany. Aby uzyskać więcej informacji, zobacz [Pobierz punkt końcowy RPC](#get-the-rpc-endpoint). |
|**Klucz prywatny** | Klucz prywatny konta Ethereum. Klucz prywatny lub adres konta i hasło są wymagane dla transakcji. Aby uzyskać więcej informacji, zobacz [Pobierz klucz prywatny](#get-the-private-key). |
|**Adres konta** | Adres konta członkowskiego usługi Azure Blockchain Service. Klucz prywatny lub adres konta i hasło są wymagane dla transakcji. Aby uzyskać więcej informacji, zobacz [Get the account address](#get-the-account-address). |
|**Hasło do konta** | Hasło konta jest ustawiane podczas tworzenia członka. Aby uzyskać informacje na temat resetowania hasła, zobacz [konto Ethereum](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Pobierz punkt końcowy RPC

Adres punktu końcowego RPC usługi Blockchain jest wymagany do nawiązania połączenia z siecią łańcucha bloków. Adres punktu końcowego można uzyskać przy użyciu zestawu Azure Blockchain Development Kit dla Ethereum lub witryny Azure portal.

**Aby użyć zestawu deweloperskiego:**

1. W obszarze **Usługa Azure Blockchain** w programie Visual Studio Code kliknij prawym przyciskiem myszy konsorcjum.
1. Wybierz **pozycję Kopiuj adres punktu końcowego RPC**.

    ![Okienko Kod programu Visual Studio przedstawiające konsorcjum z wyborem adresu punktu końcowego kopiowania RPC](./media/ethereum-logic-app/devkit-rpc.png)

    Punkt końcowy RPC jest kopiowany do schowka.

**Aby korzystać z witryny Azure portal:**

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do swojego członka usługi Azure Blockchain Service. Wybierz węzły transakcji i **domyślne łącze** węzła transakcji.

    ![Strona Węzły transakcji z zaznaczeniem (węzeł domyślny)](./media/ethereum-logic-app/transaction-nodes.png)

1. Wybierz **pozycję Parametry połączenia Klawisze** > **dostępu**.
1. Skopiuj adres punktu końcowego z **protokołu HTTPS (klucz dostępu 1)** lub **HTTPS (klucz dostępu 2)**.

    ![Portal Azure z kluczami dostępu do ciągu połączenia](./media/ethereum-logic-app/connection-string.png)

    Punkt końcowy RPC to adres URL HTTPS, który zawiera adres i klucz dostępu węzła transakcji członkowskiego usługi Azure Blockchain Service.

## <a name="get-the-private-key"></a>Pobierz klucz prywatny

Klucza prywatnego konta Ethereum można użyć do uwierzytelnienia podczas wysyłania transakcji do łańcucha bloków. Klucze publiczne i prywatne twojego konta Ethereum są generowane z 12-słownego mnemoni. Zestaw Azure Blockchain Development Kit for Ethereum generuje mnemonię podczas łączenia się z członkiem konsorcjum usługi Azure Blockchain Service. Adres punktu końcowego można uzyskać przy użyciu rozszerzenia zestawu deweloperskiego.

1. W programie Visual Studio Code otwórz paletę poleceń (F1).
1. Wybierz **narzędzie Azure Blockchain: Pobieranie klucza prywatnego**.
1. Wybierz mnemonic zapisane podczas łączenia się z członkiem konsorcjum.

    ![Paleta poleceń z opcją wyboru mnemonicznej](./media/ethereum-logic-app/private-key.png)

    Klucz prywatny zostanie skopiowany do schowka.

## <a name="get-the-account-address"></a>Uzyskaj adres konta

Możesz użyć konta członkowskiego i hasła do uwierzytelniania podczas wysyłania transakcji do łańcucha bloków. Hasło jest ustawiane podczas tworzenia elementu członkowskiego.

1. W witrynie Azure portal przejdź do strony przeglądu usługi Azure Blockchain Service.
1. Skopiuj adres **konta członka.**

    ![Strona przeglądowa z adresem konta członkowskiego](./media/ethereum-logic-app/member-account.png)

Aby uzyskać więcej informacji na temat adresu konta i hasła, zobacz [konto Ethereum](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Uzyskaj kontrakt ABI

ABI umowy definiuje interfejsy inteligentnego kontraktu. Opisano w nim sposób interakcji z inteligentnym kontraktem. Kontrakt można uzyskać przy użyciu zestawu Azure Blockchain Development Kit for Ethereum. Można również uzyskać go z pliku metadanych umowy utworzonego przez kompilator Solidity.

**Aby użyć zestawu deweloperskiego:**

Jeśli do utworzenia inteligentnego kontraktu użyto zestawu deweloperskiego lub trufli, możesz użyć rozszerzenia, aby skopiować kontrakt ABI do schowka.

1. W okienku eksploratora kodu programu Visual Studio rozwiń folder **kompilacji/kontraktów** projektu Solidity.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Wybierz **pozycję Kopiuj umowę ABI**.

    ![Okienko Kod programu Visual Studio z zaznaczeniem ABI kopiowania umowy](./media/ethereum-logic-app/abi-devkit.png)

    Umowa ABI jest kopiowana do schowka.

**Aby użyć pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu zawarty w folderze **kompilacji/kontraktów** projektu Solidity. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Znajdź **sekcję abi** w pliku JSON.
1. Skopiuj **tablicę Abi** JSON.

    ![Kod ABI w pliku metadanych kontraktu](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Pobierz kod bajtowy umowy

Bajtowy kod kontraktu jest skompilowanym inteligentnym kontraktem wykonywanym przez maszynę wirtualną Ethereum. Bajtowy kod umowy można uzyskać przy użyciu zestawu Azure Blockchain Development Kit for Ethereum. Można również uzyskać go z kompilatora solidity.

**Aby użyć zestawu deweloperskiego:**

Jeśli do utworzenia inteligentnego kontraktu użyto zestawu deweloperskiego lub trufli, można użyć rozszerzenia do skopiowania kodu bajtowego kontraktu do schowka.

1. W okienku eksploratora kodu programu Visual Studio rozwiń folder **kompilacji/kontraktów** projektu Solidity.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Wybierz **opcję Kopiuj kod bajtowy kontraktu**.

    ![Okienko Kod programu Visual Studio z wyborem kopiuj kod bajtowy kontraktu](./media/ethereum-logic-app/bytecode-devkit.png)

    Bajtowy kod kontraktu jest kopiowany do schowka.

**Aby użyć pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu zawarty w folderze **kompilacji/kontraktów** projektu Solidity. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Znajdź element **kodu bajtowego** w pliku JSON.
1. Skopiuj wartość **kodu bajtowego.**

    ![Okienko Kod programu Visual Studio z kodem bajtowym w metadanych](./media/ethereum-logic-app/bytecode-metadata.png)

**Aby użyć kompilatora solidity:**

Użyj polecenia, `solc --bin <smart contract>.sol` aby wygenerować kod bajtowy kontraktu.

## <a name="get-the-contract-address"></a>Uzyskaj adres umowy

Adres umowy to inteligentny adres docelowy kontraktu w łańcuchu bloków Ethereum. Ten adres służy do wysyłania transakcji lub kwerendy stanu inteligentnego kontraktu. Adres kontraktu można uzyskać z danych wyjściowych migracji trufli lub pliku metadanych kontraktu.

**Aby użyć danych wyjściowych migracji trufli:**

Trufla wyświetla adres kontraktu po wdrożeniu inteligentnego kontraktu. Skopiuj **adres umowy** z danych wyjściowych.

![Dane wyjściowe migracji trufli z adresem kontraktu w programie Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Aby użyć pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu zawarty w folderze **kompilacji/kontraktów** projektu Solidity. Nazwa pliku jest nazwą inteligentnego kontraktu, po której następuje rozszerzenie **.json.**
1. Znajdź sekcję **sieci** w pliku JSON.
1. Sieci prywatne są identyfikowane za pomocą identyfikatora sieci całkowitej. Znajdź wartość adresu w sekcji sieci.
1. Skopiuj wartość **adresu.**

![Metadane z wartością adresu w programie Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Następne kroki

Obejrzyj typowe scenariusze w klipie wideo [Robienie więcej za pomocą aplikacji logiki](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
