---
title: Wyślij transakcji za pomocą usługi Azure Service łańcucha bloków
description: Samouczek dotyczący sposobu użycia usługi Azure Service łańcucha bloków wdrażanie inteligentne kontraktu i wysyłania prywatnego transakcji.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9037c7b5498a5e0a37b05e5ee09891bf8066393d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417485"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Samouczek: Wyślij transakcji za pomocą usługi Azure Service łańcucha bloków

W tym samouczku będziesz tworzyć węzły transakcji do testowania, ochrony prywatności kontraktu i transakcji.  Użyjesz Truffle utworzyć lokalne Środowisko deweloperskie i wdrożyć inteligentne kontraktu i wysłać prywatnej transakcji.

Omawiane tematy:

> [!div class="checklist"]
> * Dodaj węzły transakcji
> * Użyj Truffle, aby wdrożyć inteligentne kontraktu
> * Wyślij transakcji
> * Sprawdź poprawność zachowania transakcji

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Pełne [utworzyć element członkowski łańcucha bloków w witrynie Azure portal](create-member.md)
* Pełne [Szybki Start: Nawiązywanie połączenia z sieci konsorcjum za pomocą Truffle](connect-truffle.md)
* Zainstaluj [Truffle](https://github.com/trufflesuite/truffle). Truffle wymaga zainstalowania narzędzi kilka tym [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Zainstaluj [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python jest wymagany dla sieci Web 3.
* Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/Download)
* Zainstaluj [rozszerzenia programu Visual Studio Code trwałość](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Utwórz węzły transakcji

Domyślnie masz jeden węzeł transakcji. Zamierzamy dodać dwa więcej. Jednym z węzłów bierze udział w transakcji prywatnych. Druga jest niedostępna w prywatnej transakcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure blockchain zobaczyliśmy, a następnie wybierz pozycję **węzłów transakcji > Dodaj**.
1. Określ ustawienia dla nowego węzła transakcji o nazwie `alpha`.

    ![Utwórz węzeł transakcji](./media/send-transaction/create-node.png)

    | Ustawienie | Wartość | Opis |
    |---------|-------|-------------|
    | Name (Nazwa) | `alpha` | Nazwa węzła transakcji. Nazwa jest używana do utworzenia adresu DNS dla punktu końcowego węzła transakcji. Na przykład `alpha-mymanagedledger.blockchain.azure.com`. |
    | Hasło | Silne hasło | Hasło jest używane do dostępu do punktu końcowego węzła transakcji z uwierzytelnianiem podstawowym.

1. Wybierz pozycję **Utwórz**.

    Aprowizacja nowego węzła transakcji trwa około 10 minut.

1. Powtórz kroki od 2 do 4, aby dodać węzeł transakcji o nazwie `beta`.

Możesz kontynuować samouczek, natomiast węzły są aprowizowane. Po zakończeniu inicjowania obsługi administracyjnej, będziesz mieć trzy węzły transakcji.

## <a name="open-truffle-console"></a>Otwórz konsolę Truffle

1. Otwórz wiersz polecenia środowiska Node.js lub powłoki.
1. Zmień swoją ścieżkę do katalogu projektu Truffle z wymagań wstępnych [Szybki Start: Nawiązywanie połączenia z sieci konsorcjum za pomocą Truffle](connect-truffle.md). Na przykład:

    ```bash
    cd truffledemo
    ```

1. Uruchom konsolę opracowywanie interakcyjne Truffle firmy.

    ``` bash
    truffle develop
    ```

    Truffle tworzy łańcuch bloków rozwoju lokalnego i dostarcza interakcyjną konsolę.

## <a name="create-ethereum-account"></a>Utwórz konto Ethereum

Sieci Web 3 umożliwia łączenie z węzłem transakcji domyślne i Utwórz konto Ethereum. Parametry połączenia w sieci Web 3 może pobrać z witryny Azure portal.

1. W witrynie Azure portal przejdź do węzła transakcji domyślne i wybierz **węzłów transakcji > przykładowego kodu > sieci Web 3**.
1. Skopiuj kod JavaScript z **HTTPS (klucz dostępu 1)** ![sieci Web 3 przykładowy kod](./media/send-transaction/web3-code.png)

1. Wklej kod JavaScript sieci Web 3 domyślnego węzła transakcji do konsoli opracowywanie interakcyjne Truffle. Kod tworzy obiekt sieci Web 3, który jest połączony Twój węzeł transakcji usługi Azure Service łańcucha bloków.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Może wywoływać metody dla obiektu sieci Web 3 do interakcji z Twój węzeł transakcji.

1. W węźle transakcji domyślne, należy utworzyć nowe konto. Zastąp parametr hasła silne hasło.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Upewnij się, notatki zwrócony adres konta i hasło. Należy Ethereum adres konta i hasło w następnej sekcji.

1. Zamknij środowisko programistyczne Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurowanie projektu Truffle

Aby skonfigurować projekt Truffle, należy niektóre informacje o węźle transakcji w witrynie Azure portal.

### <a name="transaction-node-public-key"></a>Klucz publiczny węzła transakcji

Każdy węzeł transakcji ma klucz prywatny. Klucz publiczny umożliwia wysyłanie transakcji prywatnej do węzła. Wysyłanie transakcji z węzła transakcji domyślne do *alfa* węzła transakcji, należy *alfa* węzła transakcji klucza publicznego.

Z listy węzłów transakcji, można uzyskać klucz publiczny. Skopiuj klucz publiczny dla węzła alfa i Zapisz wartość w dalszej części tego samouczka.

![Lista węzłów transakcji](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Adresy punktów końcowych węzła transakcji

1. W witrynie Azure portal przejdź do węzła każdej transakcji, a następnie wybierz pozycję **węzłów transakcji > Parametry połączenia**.
1. Skopiuj i Zapisz adres URL punktu końcowego z **HTTPS (klucz dostępu 1)** dla każdego węzła transakcji. Adresy punktów końcowych plik konfiguracyjny inteligentne umowy będą potrzebne w dalszej części tego samouczka.

    ![Adres punktu końcowego transakcji](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Edytuj plik konfiguracji

1. Uruchamianie programu Visual Studio Code i otworzyć Truffle projekt katalogu folderu za pomocą **Plik > Otwórz Folder** menu.
1. Otwórz plik konfiguracyjny Truffle `truffle-config.js`.
1. Zastąp zawartość pliku następujące informacje o konfiguracji. Dodaj zmienne uwzględniające adresy punktów końcowych i informacje o koncie. Sekcje nawiasu ostrego należy zastąpić wartościami, które zostały zebrane z poprzedniej sekcji.

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
          gas: 0,
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        }
      }
    }
    ```

1. Czy zapisać zmiany `truffle-config.js`.

## <a name="create-smart-contract"></a>Tworzenie inteligentnych kontraktu

1. W **umów** folderu, Utwórz nowy plik o nazwie `SimpleStorage.sol`. Dodaj następujący kod.

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
    
1. W **migracje** folderu, Utwórz nowy plik o nazwie `2_deploy_simplestorage.js`. Dodaj następujący kod.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Zastąp wartości w nawiasy ostre.

    | Wartość | Opis
    |-------|-------------
    | \<alpha node public key\> | Klucz publiczny alfa węzła
    | \<Ethereum account address\> | Adres konta Ethereum utworzone w węźle transakcji domyślne

    W tym przykładzie wartość początkową **storeData** ma wartość 42.

    **privateFor** definiuje węzły, na których jest dostępna Umowa. W tym przykładzie węzeł transakcji domyślne konto można rzutować prywatnej transakcji **alfa** węzła. Możesz dodać klucze publiczne dla wszystkich uczestników transakcji prywatnych. Jeśli nie podasz **privateFor:** i **z:** , transakcje inteligentne kontraktu były publiczne i mogą być widoczne dla wszystkich członków konsorcjum.

1. Zapisz wszystkie pliki, wybierając **Plik > Zapisz wszystko**.

## <a name="deploy-smart-contract"></a>Wdrażanie inteligentne kontraktu

Wdrażanie przy użyciu Truffle `SimpleStorage.sol` do domyślnej transakcji węzła sieci.

```bash
truffle migrate --network defaultnode
```

Truffle najpierw kompiluje i wdraża **SimpleStorage** inteligentne kontraktu.

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

## <a name="validate-contract-privacy"></a>Weryfikowanie zamówienia zachowania

Ze względu na umowy poufność informacji użytkowników, wartości umowy może być odpytywany tylko z węzłów, firma Microsoft jest zadeklarowana w **privateFor**. W tym przykładzie firma Microsoft kwerendy domyślnego węzła transakcji, ponieważ konto istnieje w tym węźle. 

1. Za pomocą konsoli Truffle łączenie z węzłem transakcji domyślne.

    ```bash
    truffle console --network defaultnode
    ```

1. W konsoli Truffle wykonaj kod zwracający wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Jeśli podczas badania domyślnego węzła transakcji zakończy się pomyślnie, jest zwracana wartość 42. Na przykład:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Zakończ działanie konsoli Truffle.

    ```bash
    .exit
    ```

Ponieważ firma Microsoft zadeklarowana **alfa** węzła klucz publiczny w **privateFor**, firma Microsoft może zbadać **alfa** węzła.

1. Za pomocą konsoli Truffle nawiązać **alfa** węzła.

    ```bash
    truffle console --network alpha
    ```

1. W konsoli Truffle wykonaj kod zwracający wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Jeśli podczas badania **alfa** node zakończy się pomyślnie, zostanie zwrócona wartość 42. Na przykład:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Zakończ działanie konsoli Truffle.

    ```bash
    .exit
    ```

Ponieważ firma Microsoft nie zadeklarował **beta** węzła klucz publiczny w **privateFor**, firma Microsoft nie będzie można wykonać zapytania o **beta** węzła ze względu na zachowania kontraktu.

1. Za pomocą konsoli Truffle nawiązać **beta** węzła.

    ```bash
    truffle console --network beta
    ```

1. Wykonywanie kodu, który zwraca wartość instancji kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Podczas badania **beta** node zakończy się niepowodzeniem, ponieważ kontrakt jest prywatny. Na przykład:

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

1. Zakończ działanie konsoli Truffle.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Wyślij transakcji

1. Utwórz plik o nazwie `sampletx.js`. Zapisz go w katalogu głównym projektu.
1. Poniższy skrypt ustawia kontrakt **storedData** wartość zmiennej do 65. Dodaj kod do nowego pliku.

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

    Zastąp wartości w nawiasy kątowe, a następnie zapisz plik.

    | Wartość | Opis
    |-------|-------------
    | \<alpha node public key\> | Klucz publiczny alfa węzła
    | \<Ethereum account address\> | Utworzone w węźle transakcji domyślny adres konta Ethereum.

    **privateFor** definiuje węzły, których transakcji jest dostępna. W tym przykładzie węzeł transakcji domyślne konto można rzutować prywatnej transakcji **alfa** węzła. Należy dodać klucze publiczne dla wszystkich uczestników transakcji prywatnych.

1. Użyj Truffle można wykonać skryptu dla domyślnego węzła transakcji.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. W konsoli Truffle wykonaj kod zwracający wartość wystąpienia kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Jeśli transakcja zakończyła się pomyślnie, jest zwracana wartość 65. Na przykład:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Zakończ działanie konsoli Truffle.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Sprawdź poprawność zachowania transakcji

Ze względu na transakcji poufność informacji użytkowników, można wykonać tylko transakcji w węzłach, firma Microsoft jest zadeklarowana w **privateFor**. W tym przykładzie można wykonać transakcji, ponieważ firma Microsoft zadeklarowana **alfa** węzła klucz publiczny w **privateFor**. 

1. Wykonywanie transakcji w za pomocą Truffle **alfa** węzła.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Wykonywanie kodu, który zwraca wartość instancji kontraktu.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Jeśli transakcja zakończyła się pomyślnie, jest zwracana wartość 65. Na przykład:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Zakończ działanie konsoli Truffle.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, możesz usunąć zasoby, usuwając `myResourceGroup` grupę zasobów utworzoną przez usługę Azure Service łańcucha bloków.

Aby usunąć grupę zasobów:

1. W witrynie Azure portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usunięcie, wpisując nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodano dwa węzły transakcji, aby zademonstrować zachowania kontraktu i transakcji. Węzeł domyślne są używane do wdrażania prywatnej kontraktu inteligentne. Możesz przetestować zachowania podczas badania wartości umowy i wykonywania transakcji z łańcucha bloków.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków przy użyciu usługi Azure Service łańcucha bloków](develop.md)
