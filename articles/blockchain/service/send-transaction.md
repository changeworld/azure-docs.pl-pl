---
title: Samouczek tworzenia, kompilowania, & wdrażania inteligentnych kontraktów — usługa Azure łańcucha bloków
description: Samouczek dotyczący sposobu korzystania z rozszerzenia Azure łańcucha bloków Development Kit for Ethereum w programie Visual Studio Code do tworzenia, kompilowania i wdrażania inteligentnego kontraktu w usłudze Azure łańcucha bloków Service.
ms.date: 11/20/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 2d2cb174656f5ed8f13d4463d416455ebb3f9ec9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325168"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Samouczek: Tworzenie, kompilowanie i wdrażanie inteligentnych kontraktów w usłudze Azure łańcucha bloków Service

W tym samouczku Użyj rozszerzenia Azure łańcucha bloków Development Kit for Ethereum w Visual Studio Code, aby utworzyć, skompilować i wdrożyć inteligentny kontrakt w usłudze Azure łańcucha bloków Service. Możesz również użyć Truffle do wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

Używasz zestawu Azure łańcucha bloków Development Kit dla Ethereum:

> [!div class="checklist"]
> * Tworzenie kontraktu inteligentnego
> * Wdrażanie kontraktu inteligentnego
> * Wykonywanie funkcji kontraktu inteligentnego za pośrednictwem transakcji
> * Kwerenda stanu kontraktu

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: użyj Visual Studio Code, aby nawiązać połączenie z siecią Azure łańcucha bloków Service Consortium](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js 10.15. x lub nowszy](https://nodejs.org/download)
* [Git 2.10. x lub nowszy](https://git-scm.com)
* [2.7.15 Python](https://www.python.org/downloads/release/python-2715/) Dodaj język Python. exe do ścieżki. Wersja 2.7.15 języka Python w ścieżce jest wymagana w przypadku zestawu Azure łańcucha bloków Development Kit.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfejs wiersza polecenia ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

W systemie Windows zainstalowany C++ kompilator jest wymagany dla modułu Node-GYP. Możesz użyć narzędzi MSBuild:

* Jeśli zainstalowano program Visual Studio 2017, należy skonfigurować npm do używania narzędzi MSBuild z poleceniem `npm config set msvs_version 2017 -g`
* Jeśli zainstalowano program Visual Studio 2019, Ustaw ścieżkę Microsoft Build Tools for npm. Na przykład: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* W przeciwnym razie zainstaluj autonomiczne narzędzia programu VS Build przy użyciu `npm install --global windows-build-tools` w powłoce poleceń *administratora* z podwyższonym poziomem uprawnień.

Aby uzyskać więcej informacji na temat węzła Node-GYP, zobacz [repozytorium Node-GYP w witrynie GitHub](https://github.com/node-gyp).

## <a name="create-a-smart-contract"></a>Tworzenie kontraktu inteligentnego

Zestaw Azure łańcucha bloków Development Kit dla Ethereum używa szablonów projektów i narzędzi Truffle, aby pomóc w tworzeniu i wdrażaniu umów. Przed rozpoczęciem Wypełnij wymagania wstępne [przewodnika Szybki Start: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md). Przewodnik Szybki Start przeprowadzi Cię przez proces instalowania i konfigurowania zestawu Azure łańcucha bloków Development Kit dla Ethereum.

1. Z palety poleceń VS Code wybierz pozycję **Azure łańcucha bloków: nowy projekt o stałej**wypełniania.
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
1. Wybierz sieć Azure łańcucha bloków Consortium w palecie poleceń. Sieć konsorcjum łańcucha bloków została dodana do pliku konfiguracji Truffle projektu podczas tworzenia projektu.
1. Wybierz **Generuj**polecenie. Wybierz nazwę pliku i Zapisz plik w folderze projektu. Na przykład `myblockchainmember.env`. Ten plik jest używany do generowania klucza prywatnego Ethereum dla członka łańcucha bloków.

Usługa Azure łańcucha bloków Development Kit używa Truffle do wykonania skryptu migracji w celu wdrożenia umów w usłudze łańcucha bloków.

![Pomyślnie wdrożono kontrakt](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Wywoływanie funkcji kontraktu

Funkcja **SendRequest** kontraktu **HelloBlockchain** zmienia zmienną stanu **RequestMessage** . Zmiana stanu sieci łańcucha bloków odbywa się za pośrednictwem transakcji. Możesz utworzyć skrypt, aby wykonać funkcję **SendRequest** za pośrednictwem transakcji.

1. Utwórz nowy plik w katalogu głównym projektu Truffle i nadaj mu nazwę `sendrequest.js`. Dodaj następujący kod JavaScript Web3 do pliku.

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

    Zastąp \<łańcucha bloków Network\> nazwą sieci łańcucha bloków zdefiniowanej w **Truffle-config. js**.

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
1. Aby wdrożyć, kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Wdróż kontrakty** z menu. Po wyświetleniu monitu wybierz Sieć Azure łańcucha bloków Consortium w palecie poleceń.
1. Następnie utwórz skrypt, używając programu w celu wywołania funkcji **GetMessage** . Utwórz nowy plik w katalogu głównym projektu Truffle i nadaj mu nazwę `getmessage.js`. Dodaj następujący kod JavaScript Web3 do pliku.

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

    Zastąp \<łańcucha bloków Network\> nazwą sieci łańcucha bloków zdefiniowanej w **Truffle-config. js**.

Skrypt wysyła zapytanie do inteligentnego kontraktu przez wywołanie funkcji GetMessage. Zwracana jest bieżąca wartość zmiennej stanu **RequestMessage** .

![Dane wyjściowe skryptu](./media/send-transaction/execute-get.png)

Zwróć uwagę na to, że wartość nie jest **Hello, łańcucha bloków!** . Zamiast tego zwracana wartość jest symbolem zastępczym. W przypadku zmiany i wdrożenia kontraktu zmieniony kontrakt zostanie wdrożony pod nowym adresem, a zmienne stanu są przypisywane wartości w konstruktorze kontraktu inteligentnego. Skrypt migracji Truffle Sample **2_deploy_contracts. js** wdraża inteligentny kontrakt i przekazuje wartość symbolu zastępczego jako argument. Konstruktor ustawia zmienną stanu **RequestMessage** na wartość symbolu zastępczego, co oznacza, że jest zwracana.

1. Aby ustawić zmienną stanu **RequestMessage** i wykonać zapytanie dotyczące wartości, ponownie uruchom skrypty **SendRequest. js** i **GetMessage. js** .

    ![Dane wyjściowe skryptu](./media/send-transaction/execute-set-get.png)

    **SendRequest. js** ustawia zmienną stanu **RequestMessage** na **Hello, łańcucha bloków!** a **GetMessage. js** wysyła zapytanie do kontraktu o wartość zmiennej stanu **RequestMessage** i zwraca **Hello, łańcucha bloków!** .

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby nie będą już potrzebne, można je usunąć, usuwając grupę zasobów `myResourceGroup` utworzoną w ramach przewodnika Szybki Start dotyczącego *tworzenia elementu członkowskiego łańcucha bloków* .

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono Tworzenie przykładowego projektu z jednolitością przy użyciu zestawu Azure łańcucha bloków Development Kit. Skompilowano i wdrożono inteligentny kontrakt o nazwie funkcja za pośrednictwem transakcji w sieci łańcucha bloków Consortium hostowanej w usłudze Azure łańcucha bloków Service.

> [!div class="nextstepaction"]
> [Opracowywanie aplikacji łańcucha bloków przy użyciu usługi Azure łańcucha bloków Service](develop.md)
