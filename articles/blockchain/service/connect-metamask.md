---
title: MetaMask nawiązać połączenie z siecią Azure Blockchain Service
description: Łączenie z siecią Azure Blockchain Service przy użyciu MetaMask i wdróż inteligentne kontraktu.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: db029cee6edcd14d29c83964e5bf75aa45077e7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026900"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Szybki start: Umożliwia MetaMask łącz się i wdrażaj inteligentne kontraktu

W tym przewodniku Szybki Start użyjesz MetaMask połączenie z siecią Azure Blockchain Service i użyj Remix, aby wdrożyć inteligentne kontraktu. Metamask to rozszerzenie przeglądarki do zarządzania portfela Ether i wykonywać akcje inteligentne kontraktu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie elementu członkowskiego temat aplikacji Azure Blockchain](create-member.md)
* Zainstaluj [MetaMask rozszerzenia przeglądarki](https://metamask.io)
* Generowanie MetaMask [portfela](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Pobieranie adresu punktu końcowego

Potrzebujesz adresu punktu końcowego usługi Azure Service łańcuch bloków do połączenia z siecią łańcucha bloków. Adres i kluczy dostępu punktu końcowego można znaleźć w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure Service łańcucha bloków. Wybierz **węzłów transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz węzeł transakcji domyślne](./media/connect-metamask/transaction-nodes.png)

1. Wybierz **parametry połączenia > klucze dostępu**.
1. Kopiuj adres punktu końcowego z **HTTPS (klucz dostępu 1)**. Potrzebujesz adresu do następnej sekcji.

    ![Parametry połączenia](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Połącz MetaMask

1. Otwórz MetaMask rozszerzenia przeglądarki i zaloguj się.
1. Na liście rozwijanej sieci wybierz **RPC niestandardowe**.

    ![Niestandardowe RPC](./media/connect-metamask/custom-rpc.png)

1. W **nową sieć > adres URL nowego RPC**, wprowadź adres punktu końcowego skopiowane z poprzedniej sekcji.
1. Wybierz pozycję **Zapisz**.

    Jeśli połączenie zakończyło się pomyślnie, sieci prywatnej jest wyświetlany na liście rozwijanej sieci.

    ![Nową sieć](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Wdrażanie inteligentne kontraktu

Remix to środowisko programistyczne, przeglądarki trwałość. Przy użyciu MetaMask i Remix razem, należy wdrożyć i podejmuj działania na kontraktów inteligentnych.

1. W przeglądarce przejdź do `https://remix.ethereum.org`.
1. Wybierz pozycję **Uruchom**. 

    Zestawy MetaMask swoje **środowiska** do **wprowadzony sieci Web 3** i **konta** do sieci.

    ![Karta Uruchamianie](./media/connect-metamask/injected-web3.png)

1. Wybierz **Utwórz nowy plik**.

    Nadaj nowemu plikowi `simple.sol`.

    ![Utwórz plik](./media/connect-metamask/create-file.png)

    Kliknij przycisk **OK**.

1. W edytorze Remix Wklej następujące **proste kontraktu inteligentne** kodu.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    **Proste kontraktu** deklaruje zmienną stanu o nazwie **Saldo**. Istnieją dwie funkcje zdefiniowane. **Dodaj** funkcja dodaje numer **Saldo**. **Uzyskać** funkcja zwraca wartość **Saldo**.

1. Aby skompilować kontrakt, wybierz **kompilacji > Uruchom, aby skompilować**. Jeśli to się powiedzie, jest wyświetlane zielone pole z nazwą kontraktu.

    ![Kompilowanie](./media/connect-metamask/compile.png)

1. Do wykonania kontrakt, wybierz **Uruchom** kartę. Wybierz **proste** kontraktu następnie **Wdróż**.

    ![Niestandardowe RPC](./media/connect-metamask/deploy.png)

1. Wyświetlane jest powiadomienie MetaMask wysyłać alerty funduszy niewystarczające do wykonania transakcji.

    Dla sieci publicznego łańcucha bloków będziesz potrzebować Ether płacić za koszty transakcji. Ponieważ jest to w konsorcjum sieci prywatnej, można ustawić cenę gazu do zera.

1.  Wybierz **opłata gaz > Edytuj > Zaawansowane**ustaw **cenę gazu** na 0.

    ![Cena gazu](./media/connect-metamask/gas-price.png)

    Wybierz pozycję **Zapisz**.

1. Wybierz **Potwierdź** do wdrożenia inteligentne kontraktu łańcucha bloków.
1. W **wdrożone umów** sekcji, a następnie rozwiń **proste** kontraktu.

    ![Wdrożone kontraktu](./media/connect-metamask/deployed-contract.png)

    Istnieją dwie akcje **Dodaj** i **uzyskać** mapowania funkcji zdefiniowanych w kontrakcie.

1. Aby wykonać **Dodaj** transakcji z łańcucha bloków, wprowadź liczbę, aby dodać, a następnie wybierz pozycję **Dodaj**.
1. Podobnie jak podczas wdrażania kontrakt, MetaMask wyświetlane jest powiadomienie wysyłać alerty funduszy niewystarczające do wykonania transakcji.

    Ponieważ jest to w konsorcjum sieci prywatnej, możemy ustawić cenę gazu na zero.

1.  Wybierz **opłata gaz > Edytuj > Zaawansowane**ustaw **cenę gazu** na 0, a następnie wybierz **Zapisz**.
1. Wybierz **Potwierdź** do wykonania transakcji z łańcucha bloków.
1. Wybierz **uzyskać** akcji. To wywołanie zapytania o dane węzła. Transakcja nie jest wymagane.
1. W okienku debugowanie Remix można wyświetlić szczegółowe informacje o transakcji na łańcucha bloków.

    ![Debugowanie historii](./media/connect-metamask/debug.png)

    Możesz zobaczyć **proste** tworzenia transakcji dla kontraktu **simple.add**i wywołanie **simple.get**.

1. Widać również historii transakcji w MetaMask. Otwórz MetaMask rozszerzenia przeglądarki.
1. W **historii** sekcji, możesz zobaczyć dziennik transakcji i wdrożone kontraktu.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start MetaMask rozszerzenia przeglądarki jest używane do łączenia się z węzłem transakcji usługi Azure Service łańcucha bloków, wdrażanie inteligentne kontraktu i wysyłać transakcji łańcucha bloków. Spróbuj następnego samouczka, aby wdrożyć i wysłać transakcji za pomocą Truffle.

> [!div class="nextstepaction"]
> [Wyślij transakcji](send-transaction.md)