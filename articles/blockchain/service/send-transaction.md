---
title: Wysyłanie transakcji przy użyciu usługi Azure łańcucha bloków Service
description: Samouczek dotyczący sposobu użycia usługi Azure łańcucha bloków do wdrożenia inteligentnego kontraktu i wysłania prywatnej transakcji.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 3cfbbdc5b95d1607738b132980320d2ff7c99788
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698385"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Samouczek: Wysyłanie transakcji przy użyciu usługi Azure łańcucha bloków Service

W ramach tego samouczka utworzysz węzły transakcji do testowania prywatności kontraktu i transakcji.  Użyjesz Truffle, aby utworzyć lokalne środowisko programistyczne i wdrożyć kontrakt inteligentny i wysłać transakcję prywatną.

Omawiane tematy:

> [!div class="checklist"]
> * Dodawanie węzłów transakcji
> * Wdrażanie kontraktu inteligentnego za pomocą Truffle
> * Wyślij transakcję
> * Weryfikuj prywatność transakcji

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md)
* Pełny [Przewodnik Szybki Start: Łączenie się z siecią konsorcjum przy użyciu programu Truffle](connect-truffle.md)
* Zainstaluj [Truffle](https://github.com/trufflesuite/truffle). Truffle wymaga zainstalowania kilku narzędzi, w tym [Node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Zainstaluj środowisko [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Środowisko Python jest niezbędne dla Web3.
* Zainstaluj [Visual Studio Code](https://code.visualstudio.com/Download)
* Zainstaluj [rozszerzenie Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Tworzenie węzłów transakcji

Domyślnie istnieje jeden węzeł transakcji. Będziemy dodawać dwa inne. Jeden z węzłów uczestniczy w transakcji prywatnej. Druga nie jest uwzględniona w transakcji prywatnej.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków i wybierz pozycję **węzły transakcji > Dodaj**.
1. Ukończ ustawienia dla nowego węzła transakcji o nazwie `alpha`.

    ![Utwórz węzeł transakcji](./media/send-transaction/create-node.png)

    | Ustawienie | Wartość | Opis |
    |---------|-------|-------------|
    | Name (Nazwa) | `alpha` | Nazwa węzła transakcji. Nazwa służy do tworzenia adresu DNS dla punktu końcowego węzła transakcji. Na przykład `alpha-mymanagedledger.blockchain.azure.com`. |
    | Hasło | Silne hasło | Hasło służy do uzyskiwania dostępu do punktu końcowego węzła transakcji z uwierzytelnianiem podstawowym.

1. Wybierz pozycję **Utwórz**.

    Inicjowanie obsługi nowego węzła transakcji trwa około 10 minut.

1. Powtórz kroki od 2 do 4, aby dodać węzeł transakcji `beta`o nazwie.

W trakcie aprowizacji węzłów można kontynuować pracę z samouczkiem. Po zakończeniu aprowizacji będziesz mieć trzy węzły transakcji.

## <a name="open-truffle-console"></a>Otwórz konsolę Truffle

1. Otwórz wiersz polecenia środowiska Node. js lub powłokę.
1. Zmień ścieżkę do katalogu projektu Truffle z poziomu wstępnie wymaganego [przewodnika Szybki Start: Użyj Truffle, aby nawiązać połączenie z](connect-truffle.md)siecią konsorcjum. Na przykład

    ```bash
    cd truffledemo
    ```

1. Użyj konsoli Truffle, aby nawiązać połączenie z domyślnym węzłem transakcji.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle nawiązuje połączenie z domyślnym węzłem transakcji i udostępnia konsolę interaktywną.

## <a name="create-ethereum-account"></a>Utwórz konto Ethereum

Użyj Web3, aby nawiązać połączenie z domyślnym węzłem transakcji i utworzyć konto Ethereum. Można wywołać metody obiektu Web3, aby móc korzystać z węzła transakcji.

1. Utwórz nowe konto w domyślnym węźle transakcji. Zastąp parametr password własnym silnym hasłem.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Zanotuj wartość zwróconego adresu konta i hasło. Potrzebujesz adresu i hasła konta Ethereum w następnej sekcji.

1. Zamknij środowisko deweloperskie Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurowanie projektu Truffle

Aby skonfigurować projekt Truffle, potrzebne są pewne informacje o węźle transakcji z Azure Portal.

### <a name="transaction-node-public-key"></a>Klucz publiczny węzła transakcji

Każdy węzeł transakcji ma klucz publiczny. Klucz publiczny umożliwia wysyłanie transakcji prywatnych do węzła. Aby wysłać transakcję z domyślnego węzła transakcji do węzła transakcji *alfa* , należy dysponować kluczem publicznym węzła transakcji *alfa* .

Klucz publiczny można uzyskać z listy węzłów transakcji. Skopiuj klucz publiczny dla węzła alfa i Zapisz wartość w dalszej części tego samouczka.

![Lista węzłów transakcji](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Adresy punktów końcowych węzła transakcji

1. W Azure Portal przejdź do każdego węzła transakcji i wybierz pozycję **węzły transakcji > parametry połączenia**.
1. Skopiuj i Zapisz adres URL punktu końcowego z **protokołu HTTPS (dostęp do klucza 1)** dla każdego węzła transakcji. W dalszej części tego samouczka potrzebujesz adresów punktu końcowego dla pliku konfiguracji kontraktu inteligentnego.

    ![Adres punktu końcowego transakcji](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Edytuj plik konfiguracji

1. Uruchom Visual Studio Code i Otwórz folder katalog projektu Truffle, korzystając z menu **plik > Otwórz folder** .
1. Otwórz plik `truffle-config.js`konfiguracji Truffle.
1. Zastąp zawartość pliku następującymi informacjami o konfiguracji. Dodaj zmienne zawierające adresy punktów końcowych i informacje o koncie. Zamień sekcje z nawiasami ostrymi na wartości zebrane z poprzednich sekcji.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
        }
      },
      compilers: {
        solc: {
          evmVersion: "byzantium"
        }
      }
    }
    ```

1. Zapisz zmiany w `truffle-config.js`.

## <a name="create-smart-contract"></a>Tworzenie kontraktu inteligentnego

1. W folderze **kontrakty** Utwórz nowy plik o nazwie `SimpleStorage.sol`. Dodaj następujący kod.

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. W folderze **migracje** Utwórz nowy plik o nazwie `2_deploy_simplestorage.js`. Dodaj następujący kod.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Zastąp wartości w nawiasach kątowych.

    | Value | Opis
    |-------|-------------
    | \<alpha node public key\> | Klucz publiczny węzła Alpha
    | \<Ethereum account address\> | Adres konta Ethereum utworzony w domyślnym węźle transakcji

    W tym przykładzie wartość początkowa wartości **storeData** jest ustawiona na 42.

    **privateFor** definiuje węzły, do których kontrakt jest dostępny. W tym przykładzie konto domyślnego węzła transakcji może rzutować transakcje prywatne na węzeł **Alpha** . Dodaj klucze publiczne dla wszystkich uczestników transakcji prywatnych. Jeśli nie dołączysz **privateFor:** i **z:** , transakcje kontraktu inteligentnego są publiczne i mogą być widoczne dla wszystkich członków konsorcjum.

1. Zapisz wszystkie pliki, wybierając pozycję **plik > Zapisz wszystko**.

## <a name="deploy-smart-contract"></a>Wdrażanie kontraktu inteligentnego

Użyj Truffle do wdrożenia `SimpleStorage.sol` w domyślnej sieci węzła transakcji.

```bash
truffle migrate --network defaultnode
```

Truffle najpierw kompiluje, a następnie wdraża kontrakt inteligentny **SimpleStorage** .

Przykładowe dane wyjściowe:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Weryfikuj prywatność kontraktu

Ze względu na prywatność umowy wartości kontraktu mogą być wysyłane tylko z węzłów zadeklarowanych w **privateFor**. W tym przykładzie możemy zbadać domyślny węzeł transakcji, ponieważ konto istnieje w tym węźle. 

1. Korzystając z konsoli Truffle, Połącz się z domyślnym węzłem transakcji.

    ```bash
    truffle console --network defaultnode
    ```

1. W konsoli Truffle wykonaj kod, który zwraca wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    W przypadku pomyślnego przeprowadzenia zapytania dotyczącego domyślnego węzła transakcji zwracana jest wartość 42. Przykład:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```

Ponieważ zadeklarowano klucz publiczny węzła **alfa** w **privateFor**, możemy zbadać węzeł **Alpha** .

1. Korzystając z konsoli Truffle, Połącz się z węzłem **Alpha** .

    ```bash
    truffle console --network alpha
    ```

1. W konsoli Truffle wykonaj kod, który zwraca wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    W przypadku pomyślnego przeprowadzenia zapytania względem węzła **alfa** zostanie zwrócona wartość 42. Na przykład:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```

Ponieważ nie deklarujemy klucza publicznego węzła **beta** w programie **privateFor**, nie będziemy mogli wysyłać zapytań do węzła **beta** z powodu prywatności umowy.

1. Korzystając z konsoli Truffle, Połącz się z węzłem **beta** .

    ```bash
    truffle console --network beta
    ```

1. Wykonaj kod, który zwraca wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Wykonywanie zapytania dotyczącego węzła **beta** kończy się niepowodzeniem, ponieważ kontrakt jest prywatny. Na przykład:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Wyślij transakcję

1. Utwórz plik o nazwie `sampletx.js`. Zapisz ją w folderze głównym projektu.
1. Poniższy skrypt ustawia wartość zmiennej **storedData** kontraktu na 65. Dodaj kod do nowego pliku.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Zastąp wartości w nawiasach kątowych, a następnie Zapisz plik.

    | Value | Opis
    |-------|-------------
    | \<alpha node public key\> | Klucz publiczny węzła Alpha
    | \<Ethereum account address\> | Adres konta Ethereum utworzony w domyślnym węźle transakcji.

    **privateFor** definiuje węzły, do których transakcja jest dostępna. W tym przykładzie konto domyślnego węzła transakcji może rzutować transakcje prywatne na węzeł **Alpha** . Należy dodać klucze publiczne dla wszystkich uczestników transakcji prywatnych.

1. Użyj Truffle, aby wykonać skrypt dla domyślnego węzła transakcji.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. W konsoli Truffle wykonaj kod, który zwraca wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Jeśli transakcja zakończyła się pomyślnie, zwracana jest wartość 65. Na przykład:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Weryfikuj prywatność transakcji

Ze względu na prywatność transakcji można wykonywać transakcje tylko na węzłach, które zostały zadeklarowane w **privateFor**. W tym przykładzie możemy wykonywać transakcje od czasu zadeklarowania klucza publicznego węzła **alfa** w **privateFor**. 

1. Użyj Truffle, aby wykonać transakcję w węźle **Alpha** .

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Wykonanie kodu, który zwraca wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Jeśli transakcja zakończyła się pomyślnie, zwracana jest wartość 65. Przykład:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Zamknij konsolę Truffle.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby nie będą już potrzebne, można je usunąć przez usunięcie `myResourceGroup` grupy zasobów utworzonej przez usługę Azure łańcucha bloków.

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano dwa węzły transakcji, aby przedstawić prywatność kontraktu i transakcji. Węzeł domyślny został użyty do wdrożenia prywatnego kontraktu inteligentnego. Przetestowano prywatność, badając wartości kontraktu i wykonując transakcje w łańcucha bloków.

> [!div class="nextstepaction"]
> [Opracowywanie aplikacji łańcucha bloków przy użyciu usługi Azure łańcucha bloków Service](develop.md)
