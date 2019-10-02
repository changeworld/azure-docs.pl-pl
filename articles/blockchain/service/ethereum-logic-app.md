---
title: Jak używać łącznika Ethereum łańcucha bloków z Azure Logic Apps
description: Jak używać łącznika łańcucha bloków Ethereum z Azure Logic Apps, aby wyzwalać funkcje kontraktu inteligentnego i odpowiadać na zdarzenia inteligentnego kontraktu.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720401"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Jak używać łącznika Ethereum łańcucha bloków z Azure Logic Apps

Użyj [łącznika łańcucha bloków Ethereum](https://docs.microsoft.com/connectors/blockchainethereum/) z [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) , aby wykonywać inteligentne akcje kontraktowe i odpowiadać na zdarzenia inteligentnego kontraktu. Na przykład chcesz utworzyć mikrousługi oparte na protokole REST, która zwraca informacje z księgi łańcucha bloków. Za pomocą aplikacji logiki można akceptować żądania HTTP, które wysyłają zapytania do informacji przechowywanych w księdze łańcucha bloków.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj opcjonalne wymagania wstępne [szybkiego startu: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md). Przewodnik Szybki Start przeprowadzi Cię przez instalację [zestawu Azure łańcucha bloków Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) i skonfigurowanie środowiska deweloperskiego łańcucha bloków.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Azure Logic Apps ułatwiają planowanie i automatyzowanie procesów i przepływów pracy, gdy trzeba zintegrować systemy i usługi. Najpierw należy utworzyć logikę korzystającą z łącznika Ethereum łańcucha bloków.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób > integrację > aplikacji logiki**.
1. W obszarze **Tworzenie aplikacji logiki**Podaj szczegóły dotyczące sposobu tworzenia aplikacji logiki. Po zakończeniu wybierz pozycję **Utwórz**.

    Aby uzyskać więcej informacji na temat tworzenia Azure Logic Apps, zobacz [tworzenie Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po wdrożeniu aplikacji przez platformę Azure wybierz zasób aplikacji logiki.
1. W projektancie Logic Apps w obszarze **Szablony**wybierz pozycję **pusta aplikacja logiki**.

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy.

Łącznik Ethereum łańcucha bloków ma jeden wyzwalacz i kilka akcji. Używany wyzwalacz lub akcja zależy od danego scenariusza.

Wybierz jedną z następujących sekcji, jeśli przepływ pracy:

* Wyzwalane, gdy wystąpi zdarzenie w łańcucha bloków, [Użyj wyzwalacza zdarzenia](#use-the-event-trigger).
* Wykonuje zapytania lub wdraża inteligentne kontraktu, [Użyj akcji](#use-actions).
* W ramach typowego scenariusza [Wygeneruj przepływ pracy przy użyciu zestawu deweloperów](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Korzystanie z wyzwalacza zdarzeń

Użyj wyzwalaczy zdarzeń Ethereum łańcucha bloków, jeśli chcesz, aby aplikacja logiki działała po wystąpieniu zdarzenia w ramach kontraktu inteligentnego. Na przykład, chcesz wysłać wiadomość e-mail, gdy wywoływana jest funkcja kontraktu inteligentnego.

1. W Projektancie aplikacji logiki wybierz łącznik Ethereum łańcucha bloków.
1. Na karcie **wyzwalacze** wybierz, **kiedy występuje wydarzenie inteligentne kontraktu**.
1. Zmień lub [Utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure łańcucha bloków.
1. Wprowadź szczegóły dotyczące kontraktu inteligentnego, który chcesz sprawdzić pod kątem zdarzeń.

    ![Właściwości wyzwalacza zdarzenia](./media/ethereum-logic-app/event-properties.png)

    | Właściwość | Opis |
    |----------|-------------|
    | **ABI kontraktu** | Interfejs binarny aplikacji kontraktu (ABI) definiuje inteligentne interfejsy kontraktu. Jak [uzyskać ABI kontraktu](#get-contract-abi). |
    | **Adres kontraktu inteligentnego** | Adres kontraktu jest adresem docelowym kontraktu inteligentnego na Ethereum łańcucha bloków. Jak [uzyskać adres kontraktu](#get-contract-address). |
    | **Nazwa zdarzenia** | Wybierz inteligentne zdarzenie kontraktu do sprawdzenia. Zdarzenie wyzwala aplikację logiki. |
    | **Interwał** i **częstotliwość** | Wybierz, jak często chcesz sprawdzać zdarzenie. |

1. Wybierz pozycję **Zapisz**.

Aby ukończyć aplikację logiki, można dodać nowy krok wykonujący akcję na podstawie wyzwalacza zdarzenia Ethereum łańcucha bloków. Na przykład Wyślij wiadomość e-mail.

## <a name="use-actions"></a>Użyj akcji

Użyj akcji Ethereum łańcucha bloków, jeśli chcesz, aby aplikacja logiki wykonywała akcję w księdze łańcucha bloków. Na przykład chcesz utworzyć mikrousługi opartej na protokole REST, która wywoła funkcję kontraktu inteligentnego, gdy żądanie HTTP zostanie wysłane do aplikacji logiki.

Akcje łącznika wymagają wyzwalacza. Możesz użyć akcji łącznika Ethereum łańcucha bloków, jak w następnym kroku po wyzwalaczu. Na przykład wyzwalacz żądania HTTP dla microsservice.

1. W Projektancie aplikacji logiki wybierz pozycję **nowy krok** po wyzwalaczu.
1. Wybierz łącznik Ethereum łańcucha bloków.
1. Na karcie **Akcje** wybierz jedną z dostępnych akcji.

    ![Właściwości akcji](./media/ethereum-logic-app/action-properties.png)

1. Zmień lub [Utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure łańcucha bloków.
1. W zależności od wybranej akcji podaj następujące informacje o funkcji kontraktu inteligentnego.

    | Właściwość | Opis |
    |----------|-------------|
    | **ABI kontraktu** | Interfejs binarny aplikacji kontraktu (ABI) definiuje inteligentne interfejsy kontraktu. Jak [uzyskać ABI kontraktu](#get-contract-abi). |
    | **Kod bajtowy kontraktu** | Skompilowany kod bajtowy w postaci skompilowanej inteligentnej. Jak [uzyskać kod bajtowy kontraktu](#get-contract-bytecode). |
    | **Adres kontraktu inteligentnego** | Adres kontraktu jest adresem docelowym kontraktu inteligentnego na Ethereum łańcucha bloków. Jak [uzyskać adres kontraktu](#get-contract-address). |
    | **Nazwa funkcji kontraktu inteligentnego** | Wybierz nazwę funkcji kontraktu inteligentnego dla akcji. Lista zostanie wypełniona ze szczegółowych informacji w ABI kontraktu. |

    Po wybraniu nazwy funkcji kontraktu inteligentnego mogą być widoczne pola wymagane dla parametrów funkcji. Wprowadź wartości lub zawartość dynamiczną wymaganą w danym scenariuszu.

Możesz teraz korzystać z aplikacji logiki. Po wyzwoleniu zdarzenia aplikacji logiki zostanie uruchomione działanie Ethereum łańcucha bloków. Na przykład wyzwalacz żądania HTTP uruchamia akcję Ethereum łańcucha bloków, aby wykonać zapytanie o wartość stanu kontraktu inteligentnego w wyniku odpowiedzi HTTP, która zwraca wartość.

## <a name="generate-a-workflow"></a>Generowanie przepływu pracy

Zestaw Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum Visual Studio Code może generować przepływy pracy aplikacji logiki dla typowych scenariuszy. Dostępne są cztery scenariusze:

* Publikowanie danych w Azure SQL Database
* Publikowanie zdarzeń w Azure Event Grid lub Azure Service Bus
* Publikowanie raportów
* Mikrousługa oparta na protokole REST

 Usługa Azure łańcucha bloków Development Kit używa Truffle, aby uprościć programowanie łańcucha bloków. Aby wygenerować aplikację logiki na podstawie kontraktu inteligentnego, musisz mieć rozwiązanie Truffle dla kontraktu inteligentnego. Potrzebne jest również połączenie z siecią konsorcjum usługi Azure łańcucha bloków. Aby uzyskać więcej informacji, zobacz [używanie Visual Studio Code do nawiązywania połączenia z siecią Azure łańcucha bloków Service Consortium — szybki start](connect-vscode.md).

Na przykład następujące kroki generują aplikację logiki mikrousług opartą na protokole REST na podstawie **HelloBlockchainego** przewodnika Szybki Start:

1. Na pasku bocznym Eksploratora VS Code rozwiń folder **kontrakty** w rozwiązaniu.
1. Kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Generuj mikrousługi dla inteligentnych kontraktów** z menu.

    ![Generowanie aplikacji logiki](./media/ethereum-logic-app/generate-logic-app.png)

1. W palecie poleceń wybierz pozycję **aplikacja logiki**.
1. Wprowadź **adres kontraktu**. Aby uzyskać więcej informacji, zobacz [jak uzyskać adres kontraktu](#get-contract-address).
1. Wybierz subskrypcję platformy Azure i grupę zasobów dla aplikacji logiki.

    Pliki konfiguracji i kodu aplikacji logiki są generowane w katalogu **generatedLogicApp** .

1. Wyświetl katalog **generatedLogicApp/HelloBlockchain** . Istnieje plik JSON aplikacji logiki dla każdej funkcji, zdarzenia i właściwości kontraktu inteligentnego.
1. Otwórz **generatedLogicApp/HelloBlockchain/Service/Property. Plik RequestMessage. logicapp. JSON** i skopiuj zawartość.

    ![JSON dla właściwości RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. W aplikacji logiki wybierz pozycję **Widok kodu aplikacji logiki**. Zamień istniejący plik JSON na wygenerowany kod JSON aplikacji logiki.

    ![Zamień konfigurację aplikacji logiki w widoku kodu](./media/ethereum-logic-app/code-view.png)

1. Wybierz pozycję **Projektant** , aby przełączyć się do widoku projektanta.
1. Aplikacja logiki zawiera podstawowe kroki dla scenariusza. Należy jednak zaktualizować szczegóły konfiguracji łącznika usługi Ethereum łańcucha bloków.
1. Wybierz krok **połączenia** i Zmień lub [Utwórz połączenie interfejsu API](#create-an-api-connection) z usługą Azure łańcucha bloków.

    ![Aplikacja logiki mikrousług](./media/ethereum-logic-app/microservice-logic-app.png)

1. Możesz teraz korzystać z aplikacji logiki. Aby przetestować mikrousługi oparte na protokole REST, wydaj żądanie HTTP POST na adres URL żądania aplikacji logiki. Skopiuj **adres URL post protokołu HTTP** z **elementu po odebraniu żądania HTTP** .

    ![ADRES URL POST PROTOKOŁU HTTP](./media/ethereum-logic-app/post-url.png)

1. Użyj Zwinięciea, aby utworzyć żądanie HTTP POST. Zastąp tekst zastępczy **\<HTTP post URL @ no__t-2** adresem URL z poprzedniego kroku.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Polecenie zwinięcie zwraca odpowiedź z aplikacji logiki. W tym przypadku dane wyjściowe z funkcji **RequestMessage** Smart Contract.

    ![Dane wyjściowe właściwości RequestMessage](./media/ethereum-logic-app/curl.png)

Aby uzyskać więcej informacji o korzystaniu z zestawu Development Kit, zobacz [Azure łańcucha bloków Development Kit for Ethereum wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) .

## <a name="create-an-api-connection"></a>Tworzenie połączenia interfejsu API

Do łącznika łańcucha bloków Ethereum jest wymagane połączenie interfejsu API z łańcucha bloków. Łącznika interfejsu API można użyć dla wielu aplikacji logiki. Niektóre właściwości są wymagane, a inne zależą od danego scenariusza.

> [!IMPORTANT]
> Do tworzenia transakcji na łańcucha bloków jest wymagany klucz prywatny lub konto i hasło. Wymagana jest tylko jedna forma uwierzytelniania. Nie musisz podawać zarówno klucza prywatnego, jak i szczegółów konta. Wykonywanie zapytań dotyczących kontraktów nie wymaga transakcji. Jeśli używasz akcji, które wykonują zapytania o stan kontraktu, klucz prywatny lub adres konta i hasło nie są wymagane.

Aby skonfigurować połączenie z członkiem usługi Azure łańcucha bloków, Poniższa lista jest możliwymi właściwościami, które mogą być potrzebne w zależności od danego scenariusza.

| Właściwość | Opis |
|----------|-------------|
|**Nazwa połączenia** | Nazwa połączenia interfejsu API. Wymagany. |
|**Punkt końcowy RPC Ethereum** | Adres HTTP węzła transakcji usługi Azure łańcucha bloków. Wymagany. Jak [uzyskać punkt końcowy RPC](#get-rpc-endpoint). |
|**Klucz prywatny** | Klucz prywatny konta Ethereum. Do transakcji są wymagane klucze prywatne lub adresy i hasła. Jak [uzyskać klucz prywatny](#get-private-key). |
|**Adres konta** | Adres konta członka usługi Azure łańcucha bloków. Do transakcji są wymagane klucze prywatne lub adresy i hasła. Jak [uzyskać adres konta](#get-account-address). |
|**Hasło konta** | Hasło konta jest ustawiane podczas tworzenia elementu członkowskiego. Aby uzyskać informacje na temat resetowania hasła, zobacz [Ethereum Account (konto](consortium.md#ethereum-account)).|

## <a name="get-rpc-endpoint"></a>Pobierz punkt końcowy RPC

Adres punktu końcowego RPC usługi Azure łańcucha bloków Service jest wymagany do nawiązania połączenia z siecią łańcucha bloków. Adres punktu końcowego można uzyskać przy użyciu zestawu Azure łańcucha bloków Development Kit dla Ethereum lub Azure Portal.

**Korzystanie z zestawu Development Kit:**

1. W obszarze **usługa Azure łańcucha bloków** w Visual Studio Code kliknij prawym przyciskiem myszy konsorcjum.
1. Wybierz pozycję **Kopiuj punkt końcowy usługi RPC**.

    ![Kopiuj punkt końcowy wywołania RPC](./media/ethereum-logic-app/devkit-rpc.png)

    Punkt końcowy RPC jest kopiowany do Schowka.

**Używanie Azure Portal:**

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/ethereum-logic-app/transaction-nodes.png)

1. Wybierz **Parametry połączenia > klucze dostępu**.
1. Skopiuj adres punktu końcowego z **protokołu HTTPS (Access Key 1)** lub Access Key 2.

    ![Parametry połączenia](./media/ethereum-logic-app/connection-string.png)

    Punkt końcowy RPC to adres URL HTTPS obejmujący adres i klucz dostępu węzła transakcji elementu członkowskiego usługi Azure łańcucha bloków.

## <a name="get-private-key"></a>Pobierz klucz prywatny

Klucz prywatny konta Ethereum może służyć do uwierzytelniania podczas wysyłania transakcji do łańcucha bloków. Klucze publiczne i prywatne konta Ethereum są generowane na podstawie 12 wyrazów. Zestaw Azure łańcucha bloków Development Kit dla Ethereum generuje element w przypadku łączenia się z członkiem konsorcjum usługi Azure łańcucha bloków. Adres punktu końcowego można uzyskać przy użyciu rozszerzenia Development Kit.

1. W Visual Studio Code Otwórz paletę poleceń (F1).
1. Wybierz pozycję **Azure łańcucha bloków: Pobierz klucz prywatny**.
1. Wybierz opcję zapisywany podczas nawiązywania połączenia z członkiem konsorcjum.

    ![Wybierz opcję](./media/ethereum-logic-app/private-key.png)

    Klucz prywatny jest kopiowany do Schowka.

## <a name="get-account-address"></a>Pobierz adres konta

Przy wysyłaniu transakcji do łańcucha bloków można użyć konta i hasła elementu członkowskiego. Hasło jest ustawiane podczas tworzenia elementu członkowskiego.

1. W Azure Portal przejdź do strony Przegląd usługi Azure łańcucha bloków.
1. Skopiuj adres **konta elementu członkowskiego** .

    ![Kopiuj konto elementu członkowskiego](./media/ethereum-logic-app/member-account.png)

Aby uzyskać więcej informacji na temat adresu i hasła konta, zobacz [Ethereum Account (konto](consortium.md#ethereum-account)).

## <a name="get-contract-abi"></a>Pobierz ABI kontraktu

Interfejs binarny aplikacji kontraktu (ABI) definiuje inteligentne interfejsy kontraktu. Opisuje sposób korzystania z kontraktu inteligentnego. Możesz uzyskać ABI kontraktu przy użyciu zestawu Azure łańcucha bloków Development Kit dla Ethereum lub z pliku metadanych kontraktu kompilatora o trwałość.

**Korzystanie z zestawu Development Kit:**

Jeśli używasz zestawu Development Kit lub Truffle do kompilowania kontraktu inteligentnego, możesz użyć rozszerzenia, aby skopiować ABI kontraktu do Schowka.

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz **Kopiuj kontrakt ABI**.

    ![Kopiuj kontrakt ABI przy użyciu DevKit](./media/ethereum-logic-app/abi-devkit.png)

    ABI kontraktu jest kopiowany do Schowka.

**Przy użyciu pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu znajdujący się w folderze **kompilacja/kontrakty** w projekcie o trwałym stanie. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Znajdź sekcję **ABI** w pliku JSON.
1. Skopiuj tablicę JSON **ABI** .

    ![Sekcja kontraktu ABI w metadanych](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Pobierz kod bajtowy kontraktu

Kod bajtowy kontraktu jest skompilowanym inteligentnym kontraktem wykonywanym przez maszynę wirtualną Ethereum. Kod bajtowy kontraktu można uzyskać przy użyciu zestawu Azure łańcucha bloków Development Kit dla Ethereum lub z kompilatora kryjącego.

**Korzystanie z zestawu Development Kit:**

Jeśli używasz zestawu Development Kit lub Truffle do kompilowania kontraktu inteligentnego, możesz użyć rozszerzenia, aby skopiować kod bajtowy kontraktu do Schowka.

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz **Kopiuj kod bajtowy kontraktu**.

    ![Kopiuj kod bajtowy kontraktu przy użyciu DevKit](./media/ethereum-logic-app/bytecode-devkit.png)

    Kod bajtowy kontraktu jest kopiowany do Schowka.

**Przy użyciu pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu znajdujący się w folderze **kompilacja/kontrakty** w projekcie o trwałym stanie. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Znajdź element **kodu bajtowego** w pliku JSON.
1. Skopiuj wartość **kod bajtowy** .

    ![Kopiuj kod bajtowy przy użyciu metadanych](./media/ethereum-logic-app/bytecode-metadata.png)

**Korzystanie z kompilatora kryjącego:**

Użyj polecenia `solc --bin <smart contract>.sol`, aby wygenerować kod bajtowy kontraktu.

## <a name="get-contract-address"></a>Pobierz adres kontraktu

Adres kontraktu jest adresem docelowym kontraktu inteligentnego na Ethereum łańcucha bloków. Ten adres jest używany do wysyłania transakcji lub stanu zapytania dla kontraktu inteligentnego. Adres kontraktu można uzyskać z danych wyjściowych migracji Truffle lub pliku metadanych kontraktu.

**Korzystanie z Truffle migracji danych wyjściowych:**

Truffle wyświetla adres kontraktu po wdrożeniu kontraktu inteligentnego. Skopiuj **adres kontraktu** z danych wyjściowych.

![Adres kontraktu z danych wyjściowych Truffle](./media/ethereum-logic-app/contract-address-truffle.png)

**Przy użyciu pliku metadanych kontraktu:**

1. Otwórz plik metadanych kontraktu znajdujący się w folderze **kompilacja/kontrakty** w projekcie o trwałym stanie. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Znajdź sekcję **Networks** w pliku JSON.
1. Sieci prywatne są identyfikowane przez identyfikator sieci liczb całkowitych. Znajdź wartość adresu w sekcji Sieć.
1. Skopiuj wartość **adresu** .

![Adres kontraktu z metadanych](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Następne kroki

Obejrzyj typowe [scenariusze łączące łańcucha bloków przy użyciu Azure Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
