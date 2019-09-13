---
title: Podłącz maskę do sieci usługi Azure łańcucha bloków Service
description: Nawiązywanie połączenia z siecią usługi Azure łańcucha bloków przy użyciu funkcji dbmask i wdrażanie kontraktu inteligentnego.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 5b46c5b2e8f613d351442fdf3c8ae5ee2198f2da
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933986"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Szybki start: Łączenie i wdrażanie kontraktu inteligentnego przy użyciu klasy dbmask

W tym przewodniku szybki start użyjemy do łączenia się z siecią usługi Azure łańcucha bloków i używania Remix do wdrażania kontraktu inteligentnego. Dbmask to rozszerzenie przeglądarki umożliwiające zarządzanie portfelem eteru i wykonywanie akcji inteligentnych kontraktu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Pełny [Przewodnik Szybki Start: Utwórz składową łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Zainstaluj [rozszerzenie przeglądarki Xmlmask](https://metamask.io)
* Generowanie [portfela](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) z maską

## <a name="get-endpoint-address"></a>Pobierz adres punktu końcowego

Do nawiązania połączenia z siecią łańcucha bloków potrzebny jest adres punktu końcowego usługi Azure łańcucha bloków. Adres punktu końcowego i klucze dostępu można znaleźć w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-metamask/transaction-nodes.png)

1. Wybierz **Parametry połączenia > klucze dostępu**.
1. Skopiuj adres punktu końcowego z **protokołu HTTPS (dostęp do klucza 1)** . Potrzebujesz adresu dla następnej sekcji.

    ![Parametry połączenia](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Łączenie z maską

1. Otwórz rozszerzenie przeglądarki dbmask i zaloguj się.
1. Z listy rozwijanej sieć wybierz pozycję **niestandardowe wywołanie RPC**.

    ![Niestandardowe wywołanie RPC](./media/connect-metamask/custom-rpc.png)

1. W obszarze **nowy > sieciowej nowy adres URL usługi RPC**wprowadź adres punktu końcowego skopiowany z poprzedniej sekcji.
1. Wybierz pozycję **Zapisz**.

    Jeśli połączenie zakończyło się pomyślnie, Sieć prywatna zostanie wyświetlona na liście rozwijanej sieć.

    ![Nowa sieć](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Wdrażanie kontraktu inteligentnego

Remix to środowisko programistyczne oparte na przeglądarce. Korzystając z funkcji dbmasking i remix, możesz wdrażać i podejmować działania dotyczące inteligentnych umów.

1. W przeglądarce przejdź do `https://remix.ethereum.org`.
1. Wybierz pozycję **Uruchom**. 

    Maska usuwania ustawia swoje **środowisko** do **dodania Web3** i **konta** do sieci.

    ![Karta przebieg](./media/connect-metamask/injected-web3.png)

1. Wybierz pozycję **Utwórz nowy plik**.

    Nazwij nowy plik `simple.sol`.

    ![Utwórz plik](./media/connect-metamask/create-file.png)

    Kliknij przycisk **OK**.

1. W edytorze Remix wklej następujący **prosty kod kontraktu inteligentnego** .

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

    **Prosta umowa** deklaruje zmienną stanu o nazwie **Saldo**. Zdefiniowano dwie funkcje. Funkcja **Dodaj** dodaje liczbę do **zrównoważenia**. Funkcja **Get** zwraca wartość **Saldo**.

1. Aby skompilować kontrakt, wybierz **kompilację, > rozpocząć Kompilowanie**. Jeśli to się powiedzie, zostanie wyświetlony zielony prostokąt z nazwą kontraktu.

    ![Kompilacji](./media/connect-metamask/compile.png)

1. Aby wykonać kontrakt, wybierz kartę **uruchomienie** . Wybierz kontrakt **prosty** , a następnie **Wdróż**.

    ![Niestandardowe wywołanie RPC](./media/connect-metamask/deploy.png)

1. Zostanie wyświetlone powiadomienie o niewystarczającej masce do wykonania transakcji.

    W przypadku sieci publicznej łańcucha bloków należy ponieść Eter, aby płacić za koszt transakcji. Ponieważ jest to sieć prywatna w konsorcjum, można ustawić cenę gazu na zero.

1.  Wybierz pozycję **opłata za gaz > edytuj > zaawansowane**, ustaw **cenę gazu** na 0.

    ![Cena gazu](./media/connect-metamask/gas-price.png)

    Wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Potwierdź** , aby wdrożyć kontrakt inteligentny do łańcucha bloków.
1. W sekcji **wdrożone kontrakty** rozwiń pozycję **prosty** kontrakt.

    ![Wdrożony kontrakt](./media/connect-metamask/deployed-contract.png)

    Istnieją dwie akcje, które umożliwiają **Dodawanie** i **pobieranie** mapy do funkcji zdefiniowanych w kontrakcie.

1. Aby wykonać operację **Dodaj** transakcję na łańcucha bloków, wprowadź liczbę, która ma zostać dodana, a następnie wybierz pozycję **Dodaj**.
1. Podobnie jak w przypadku wdrożenia kontraktu, wyświetlane jest powiadomienie o niewystarczającej masce do wykonania transakcji.

    Ponieważ jest to sieć prywatna w ramach konsorcjum, można ustawić cenę gazu na zero.

1.  Wybierz pozycję **opłata za gaz > edytuj > zaawansowane**, ustaw **cenę gazu** na 0, a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Potwierdź** , aby wykonać transakcję w łańcucha bloków.
1. Wybierz pozycję **Pobierz** akcję. Jest to wywołanie do zapytania o dane węzła. Transakcja nie jest wymagana.
1. W okienku debugowanie elementu Remix można zobaczyć szczegóły dotyczące transakcji w łańcucha bloków.

    ![Historia debugowania](./media/connect-metamask/debug.png)

    Możesz zobaczyć **proste** tworzenie kontraktu, transakcję dla **prostego. Dodaj**i Wywołaj metodę **Simple. Get**.

1. Możesz również wyświetlić historię transakcji w ramach maski. Otwórz rozszerzenie przeglądarki z maską.
1. W sekcji **historia** można zobaczyć dziennik wdrożonego kontraktu i transakcji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto rozszerzenia Browser masking do nawiązania połączenia z węzłem transakcji usługi Azure łańcucha bloków, wdrożenia kontraktu inteligentnego i wysłania transakcji do łańcucha bloków. Skorzystaj z następnego samouczka, aby użyć zestawu Azure łańcucha bloków Development Kit dla Ethereum i Truffle do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Używanie Visual Studio Code do tworzenia, kompilowania i wdrażania kontraktów inteligentnych](send-transaction.md)