---
title: Łączenie metamask z siecią usługi Azure Blockchain
description: Połącz się z siecią usługi Azure Blockchain service przy użyciu metamask i wdrożyć inteligentny kontrakt.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205108"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Szybki start: łączenie i wdrażanie inteligentnego kontraktu za pomocą metamasku

W tym przewodniku Szybki start użyjesz MetaMask, aby połączyć się z siecią usługi Azure Blockchain i użyć remix do wdrożenia inteligentnego kontraktu. Metamask to rozszerzenie przeglądarki do zarządzania portfelem Ether i wykonywania inteligentnych działań kontraktowych.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [szybki start: Tworzenie członka łańcucha bloków przy użyciu portalu Azure](create-member.md) lub [przewodnika Szybki start: tworzenie członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Zainstaluj [rozszerzenie przeglądarki MetaMask](https://metamask.io)
* Generowanie [portfela](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) MetaMask

## <a name="get-endpoint-address"></a>Uzyskaj adres punktu końcowego

Aby połączyć się z siecią łańcucha bloków, potrzebujesz adresu punktu końcowego usługi Azure Blockchain Service. Adres punktu końcowego i klucze dostępu znajdują się w witrynie Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do członka usługi Azure Blockchain Service.
1. Wybierz węzły transakcji i **domyślne łącze** węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-metamask/transaction-nodes.png)

1. Wybierz **pozycję Parametry połączenia > klawiszy programu Access**.
1. Skopiuj adres punktu końcowego z **protokołu HTTPS (klucz programu Access 1)**.

    ![Parametry połączenia](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Połącz metamask

1. Otwórz rozszerzenie przeglądarki MetaMask i zaloguj się.
1. Z listy rozwijanej sieci wybierz pozycję **Niestandardowe rpc**.

    ![Niestandardowe RPC](./media/connect-metamask/custom-rpc.png)

1. W **programie New Network > New RPC URL**wklej adres końcowy skopiowany powyżej.
1. Wybierz **pozycję Zapisz**.

    Jeśli połączenie zakończy się pomyślnie, sieć prywatna jest wyświetlana z listy rozwijanej sieci.

    ![Nowa sieć](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Wdrażanie inteligentnego kontraktu

Remix to oparte na przeglądarce środowisko programistyczne Solidity. Korzystając z MetaMask i Remix razem, można wdrożyć i podjąć działania na inteligentnych kontraktów.

1. W przeglądarce przejdź do `https://remix.ethereum.org`.
1. Wybierz **pozycję Nowy plik** na karcie Narzędzia **główne** w obszarze **Plik**.

    Nazwij `simple.sol`nowy plik .

    ![Utwórz plik](./media/connect-metamask/create-file.png)

    Kliknij przycisk **OK**.
1. W edytorze Remix wklej następujący **prosty kod inteligentnego kontraktu.**

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

    Umowa **prosta** deklaruje zmienną stanu o nazwie **saldo**. Istnieją dwie funkcje zdefiniowane. Funkcja **dodawania** dodaje liczbę do **równowagi**. Funkcja **get** zwraca wartość **salda**.
1. Aby skompilować kontrakt, najpierw wybierz okienko kompilatora solidarności, a następnie wybierz **compile simple.sol**.

    ![Skompilować](./media/connect-metamask/compile.png)

1. Wybierz okienko **Wdrażanie & Uruchom,** a następnie ustaw **środowisko** na **Wstrzyk w sieci Web3,** aby połączyć się za pośrednictwem metamaski z członkiem łańcucha bloków.

    ![Karta Uruchom](./media/connect-metamask/injected-web3.png)

1. Wybierz **prosty** kontrakt, a następnie **deploy**.

    ![Wdrożenie](./media/connect-metamask/deploy.png)


1. Powiadomienie MetaMask ostrzega o niewystarczających środkach do wykonania transakcji.

    W przypadku publicznej sieci blockchain potrzebny jest Ether do opłacenia kosztów transakcji. Ponieważ jest to sieć prywatna w konsorcjum, można ustawić cenę gazu na zero.

1.  Wybierz **opcję Opłata gazowa > Edytuj > Zaawansowane**, ustaw cenę **gazu** na 0.

    ![Cena gazu](./media/connect-metamask/gas-price.png)

    Wybierz **pozycję Zapisz**.

1. Wybierz **pozycję Potwierdź,** aby wdrożyć inteligentny kontrakt w łańcuchu bloków.
1. W sekcji **Wdrożone kontrakty** rozwiń **prosty** kontrakt.

    ![Wdrożony kontrakt](./media/connect-metamask/deployed-contract.png)

    Dwie akcje, **dodaj** i **pobierz**, mapuj do funkcji zdefiniowanych w umowie.

1. Aby wykonać transakcję **dodawania** w łańcuchu bloków, wprowadź liczbę do dodania, a następnie wybierz **dodaj**. Możesz otrzymać komunikat o awarii szacowania gazu od Remix: "Wysyłasz transakcję do prywatnego łańcucha bloków, który nie wymaga gazu". Wybierz **pozycję Wyślij transakcję,** aby wymusić transakcję.
1. Podobnie jak podczas wdrażania umowy, powiadomienie MetaMask ostrzega o niewystarczających środków do wykonania transakcji.

    Ponieważ jest to sieć prywatna w konsorcjum, możemy ustawić cenę gazu na zero.

1. Wybierz **opcję Opłata gazowa > Edytuj > Zaawansowane,** ustaw **cenę gazu** na 0 i wybierz **zapisz**.
1. Wybierz **pozycję Potwierdź,** aby wysłać transakcję do łańcucha bloków.
1. Wybierz **pozycję Pobierz** akcję. Jest to wywołanie danych węzła kwerendy. Transakcja nie jest potrzebna.

Okienko debugowania Remix pokazuje szczegóły dotyczące transakcji w łańcuchu bloków:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Aby wyświetlić historię transakcji w metamasku, otwórz rozszerzenie przeglądarki MetaMask i poszukaj w sekcji **Historia** dziennika wdrożonego kontraktu i transakcji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto rozszerzenia przeglądarki MetaMask, aby połączyć się z węzłem transakcji usługi Azure Blockchain, wdrożyć umowę inteligentną i wysłać transakcję do łańcucha bloków. Wypróbuj następny samouczek, aby użyć zestawu Azure Blockchain Development Kit dla Ethereum i Trufli do tworzenia, tworzenia, wdrażania, wdrażania i wykonywania funkcji inteligentnego kontraktu za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Tworzenie, tworzenie i wdrażanie inteligentnych kontraktów w usłudze Azure Blockchain Service](send-transaction.md)