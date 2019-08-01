---
title: Korzystanie z inteligentnych kontraktów w usłudze Azure łańcucha bloków Service
description: Samouczek dotyczący sposobu użycia usługi Azure łańcucha bloków do wdrożenia inteligentnego kontraktu i wykonania funkcji za pośrednictwem transakcji.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705134"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>Samouczek: Korzystanie z inteligentnych kontraktów w usłudze Azure łańcucha bloków Service

W tym samouczku użyjesz usługi Azure łańcucha bloków Development Kit do tworzenia i wdrażania kontraktu inteligentnego, a następnie wykonasz funkcję kontraktu inteligentnego za pośrednictwem transakcji w sieci łańcucha bloków Consortium.

Używasz zestawu Azure łańcucha bloków Development Kit dla Ethereum:

> [!div class="checklist"]
> * Łączenie z członkiem usługi Azure łańcucha bloków Service Consortium łańcucha bloków
> * Tworzenie kontraktu inteligentnego
> * Wdrażanie kontraktu inteligentnego
> * Wykonywanie funkcji kontraktu inteligentnego za pośrednictwem transakcji
> * Kwerenda stanu kontraktu

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Pełny [Przewodnik Szybki Start: Utwórz składową łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Usługa Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Dodaj język Python. exe do ścieżki. Język Python w ścieżce jest wymagany w przypadku usługi Azure łańcucha bloków Development Kit.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfejs wiersza polecenia ganache](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Weryfikowanie środowiska zestawu Azure łańcucha bloków Development Kit

Zestaw Azure łańcucha bloków Development Kit sprawdza wymagania wstępne dotyczące środowiska deweloperskiego. Aby zweryfikować środowisko programistyczne:

Z palety poleceń vs Code wybierz **pozycję Azure łańcucha bloków: Pokaż stronę**powitalną.

Pakiet Azure łańcucha bloków Development Kit uruchamia skrypt walidacji, który trwa około minuty. Możesz wyświetlić dane wyjściowe, wybierając pozycję **terminal > nowym terminalu**. Na pasku menu terminalu wybierz kartę Output ( **dane wyjściowe** ) i pozycję **Azure łańcucha bloków** na liście rozwijanej. Pomyślne sprawdzenie poprawności wygląda tak, jak na poniższym obrazie:

![Prawidłowe środowisko deweloperskie](./media/send-transaction/valid-environment.png)

 Jeśli brakuje wymaganego narzędzia, Nowa karta o nazwie **Azure łańcucha bloków Development Kit — wersja** zapoznawcza zawiera listę wymaganych aplikacji do zainstalowania i linków do pobrania narzędzi.

![Aplikacje wymagane przez zestaw dev Kit](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>Łączenie z członkiem konsorcjum

Można nawiązać połączenie z członkami konsorcjum przy użyciu rozszerzenia Azure łańcucha bloków Development VS Code Kit. Po nawiązaniu połączenia z konsorcjum można skompilować, skompilować i wdrożyć inteligentne kontrakty w składowej konsorcjum usługi Azure łańcucha bloków.

Jeśli nie masz dostępu do elementu członkowskiego konsorcjum usługi Azure łańcucha bloków, Wypełnij wymagania wstępne [: Utwórz składową łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Utwórz członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu](create-member-cli.md)wiersza polecenia platformy Azure.

1. W okienku Eksploratora Visual Studio Code (VS Code) rozwiń rozszerzenie **Azure łańcucha bloków** .
1. Wybierz pozycję **Połącz z konsorcjum**.

   ![Nawiązywanie połączenia z konsorcjum](./media/send-transaction/connect-consortium.png)

    Jeśli zostanie wyświetlony monit o uwierzytelnienie platformy Azure, postępuj zgodnie z monitami, aby przeprowadzić uwierzytelnianie przy użyciu przeglądarki.
1. Wybierz pozycję **Połącz z usługą Azure łańcucha bloków Service Consortium** na liście rozwijanej paleta poleceń.
1. Wybierz subskrypcję i grupę zasobów skojarzoną z elementem członkowskim konsorcjum usługi Azure łańcucha bloków.
1. Wybierz z listy swoją konsorcjum.

Członkowie konsorcjum i łańcucha bloków są wyświetlani na pasku bocznym Eksploratora programu Visual Studio.

![Konsorcjum wyświetlane w Eksploratorze](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>Tworzenie kontraktu inteligentnego

Zestaw Azure łańcucha bloków Development Kit dla Ethereum używa szablonów projektów i narzędzi Truffle, aby pomóc w tworzeniu i wdrażaniu umów.

1. Z palety poleceń vs Code wybierz **pozycję Azure łańcucha bloków: Nowy projekt**o stałej wypełniania.
1. Wybierz pozycję **Utwórz projekt podstawowy**.
1. Utwórz nowy folder o nazwie `HelloBlockchain` i **Wybierz pozycję Nowy projekt ścieżka**.

Zestaw Azure łańcucha bloków Development Kit tworzy i inicjuje nowy projekt o stałej wypełniania. Projekt podstawowy zawiera przykładową **HelloBlockchainą** umowę i wszystkie pliki niezbędne do kompilowania i wdrażania w składowej konsorcjum usługi Azure łańcucha bloków. Utworzenie projektu może potrwać kilka minut. Postęp można monitorować w panelu terminalu VS Code, wybierając dane wyjściowe dla usługi Azure łańcucha bloków.

Struktura projektu wygląda podobnie do poniższego przykładu:

   ![Projekt o stałej mocy](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Tworzenie kontraktu inteligentnego

Inteligentne kontrakty znajdują się w katalogu **kontraktów** projektu. Możesz kompilować inteligentne kontrakty przed wdrożeniem ich w usłudze łańcucha bloków. Użyj polecenia **Kompiluj kontrakty** , aby skompilować wszystkie inteligentne kontrakty w projekcie.

1. Na pasku bocznym Eksploratora VS Code rozwiń folder **kontrakty** w projekcie.
1. Kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Kompiluj kontrakty** z menu.

    ![Kontrakty kompilacji](./media/send-transaction/build-contracts.png)

Usługa Azure łańcucha bloków Development Kit używa Truffle do kompilowania inteligentnych kontraktów.

![Kompiluj dane wyjściowe](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Wdrażanie kontraktu inteligentnego

Truffle używa skryptów migracji do wdrażania kontraktów w sieci Ethereum. Migracje to pliki JavaScript znajdujące się w katalogu **migracji** projektu.

1. Aby wdrożyć kontrakt inteligentny, kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Wdróż kontrakty** z menu.
1. Wybierz sieć Azure łańcucha bloków Consortium w obszarze **from Truffle-config. js**. Sieć konsorcjum łańcucha bloków została dodana do pliku konfiguracji Truffle projektu podczas tworzenia projektu.
1. Wybierz **Generuj**polecenie. Wybierz nazwę pliku i Zapisz plik w folderze projektu. Na przykład `myblockchainmember.env`. Ten plik jest używany do generowania klucza prywatnego Ethereum dla członka łańcucha bloków.

Usługa Azure łańcucha bloków Development Kit używa Truffle do wykonania skryptu migracji w celu wdrożenia umów w usłudze łańcucha bloków.

![Pomyślnie wdrożono kontrakt](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Wywoływanie funkcji kontraktu

Funkcja **SendRequest** kontraktu **HelloBlockchain** zmienia zmienną stanu **RequestMessage** . Zmiana stanu sieci łańcucha bloków odbywa się za pośrednictwem transakcji. Możesz utworzyć skrypt, aby wykonać funkcję **SendRequest** za pośrednictwem transakcji.

1. Utwórz nowy plik w katalogu głównym projektu Truffle i nadaj mu `sendrequest.js`nazwę. Dodaj następujący kod JavaScript Web3 do pliku.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Po utworzeniu projektu w usłudze Azure łańcucha bloków Development Kit zostanie wygenerowany plik konfiguracji Truffle z informacjami o szczegółach punktu końcowego sieci konsorcjum łańcucha bloków. Otwórz **Truffle-config. js** w projekcie. Plik konfiguracji zawiera listę dwóch sieci: jeden o nazwie programowanie i jeden o takiej samej nazwie jak konsorcjum.
1. W okienku terminalu VS Code Użyj Truffle, aby wykonać skrypt w sieci łańcucha bloków konsorcjum. Na pasku menu okienka terminalu wybierz kartę **Terminal** i program **PowerShell** na liście rozwijanej.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Zastąp \<Sieć\> łańcucha bloków nazwą sieci łańcucha bloków zdefiniowanej w **Truffle-config. js**.

Truffle wykonuje skrypt w sieci łańcucha bloków.

![Dane wyjściowe skryptu](./media/send-transaction/execute-transaction.png)

Gdy wykonujesz funkcję kontraktu za pośrednictwem transakcji, transakcja nie zostanie przetworzona do momentu utworzenia bloku. Funkcje przeznaczone do wykonania za pośrednictwem transakcji zwracają identyfikator transakcji zamiast wartości zwracanej.

## <a name="query-contract-state"></a>Kwerenda stanu kontraktu

Funkcje kontraktu inteligentnego mogą zwracać bieżącą wartość zmiennych stanu. Dodajmy funkcję, aby zwrócić wartość zmiennej stanu.

1. W **HelloBlockchain. peruwiański**Dodaj funkcję **GetMessage** do kontraktu inteligentnego **HelloBlockchain** .

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    Funkcja zwraca komunikat przechowywany w zmiennej stanu na podstawie bieżącego stanu kontraktu.

1. Kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Kompiluj kontrakty** z menu, aby skompilować zmiany do kontraktu inteligentnego.
1. Aby wdrożyć, kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Wdróż kontrakty** z menu.
1. Następnie utwórz skrypt, używając programu w celu wywołania funkcji GetMessage. Utwórz nowy plik w katalogu głównym projektu Truffle i nadaj mu `getmessage.js`nazwę. Dodaj następujący kod JavaScript Web3 do pliku.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. W okienku terminalu VS Code Użyj Truffle, aby wykonać skrypt w sieci łańcucha bloków. Na pasku menu okienka terminalu wybierz kartę **Terminal** i program **PowerShell** na liście rozwijanej.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Zastąp \<Sieć\> łańcucha bloków nazwą sieci łańcucha bloków zdefiniowanej w **Truffle-config. js**.

Skrypt wysyła zapytanie do inteligentnego kontraktu przez wywołanie funkcji GetMessage. Zwracana jest bieżąca wartość zmiennej stanu **RequestMessage** .

![Dane wyjściowe skryptu](./media/send-transaction/execute-get.png)

Zwróć uwagę na to, że wartość nie jest **Hello, łańcucha bloków!** . Zamiast tego zwracana wartość jest symbolem zastępczym. Po zmianie i wdrożeniu kontraktu jest pobierany nowy adres kontraktu, a zmienne stanu są przypisywane wartości w konstruktorze kontraktu inteligentnego. Skrypt migracji Truffle Sample **2_deploy_contracts. js** wdraża inteligentny kontrakt i przekazuje wartość symbolu zastępczego jako argument. Konstruktor ustawia zmienną stanu **RequestMessage** na wartość symbolu zastępczego, co oznacza, że jest zwracana.

1. Aby ustawić zmienną stanu **RequestMessage** i wykonać zapytanie dotyczące wartości, ponownie uruchom skrypty **SendRequest. js** i GetMessage **. js** .

    ![Dane wyjściowe skryptu](./media/send-transaction/execute-set-get.png)

    **SendRequest. js** ustawia zmienną stanu **RequestMessage** na **Hello, łańcucha bloków!** a **GetMessage. js** wysyła zapytanie do kontraktu o wartość zmiennej stanu **RequestMessage** i zwraca **Hello, łańcucha bloków!** .

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby nie będą już potrzebne, można je usunąć przez usunięcie `myResourceGroup` grupy zasobów utworzonej w ramach przewodnika Szybki Start dotyczącego *tworzenia elementu członkowskiego łańcucha bloków* .

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono Tworzenie przykładowego projektu z jednolitością przy użyciu zestawu Azure łańcucha bloków Development Kit. Skompilowano i wdrożono inteligentny kontrakt o nazwie funkcja za pośrednictwem transakcji w sieci łańcucha bloków Consortium hostowanej w usłudze Azure łańcucha bloków Service.

> [!div class="nextstepaction"]
> [Opracowywanie aplikacji łańcucha bloków przy użyciu usługi Azure łańcucha bloków Service](develop.md)
