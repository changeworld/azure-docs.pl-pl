---
title: Omówienie tworzenia usługi Azure Blockchain
description: Wprowadzenie do opracowywania rozwiązań w usłudze Azure Blockchain.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348377"
---
# <a name="azure-blockchain-service-development-overview"></a>Omówienie tworzenia usługi Azure Blockchain

Dzięki usłudze Azure Blockchain Service możesz tworzyć sieci blockchain konsorcjum, aby umożliwić scenariusze przedsiębiorstwa, takie jak śledzenie zasobów, token cyfrowy, lojalność i nagroda, finansowanie łańcucha dostaw i pochodzenie. W poniższych sekcjach przedstawiono program rozwoju usługi Azure Blockchain w celu wdrożenia rozwiązań sieci blockchain dla przedsiębiorstw.

## <a name="connecting-to-azure-blockchain-service"></a>Łączenie się z usługą Azure Blockchain

Istnieją różne typy klientów dla sieci blockchain, w tym pełne węzły, węzły świetlne i klienci zdalni. Usługa Azure Blockchain Service tworzy sieć łańcucha bloków, która zawiera węzły. Możesz użyć różnych klientów jako bramy do usługi Azure Blockchain Service do tworzenia łańcucha bloków. Usługa Azure Blockchain service oferuje uwierzytelnianie podstawowe lub klucz dostępu jako punkt końcowy rozwoju. Poniżej przedstawiono popularnych klientów, których można używać connect.

### <a name="visual-studio-code"></a>Visual Studio Code

Można połączyć się z członkami konsorcjum przy użyciu rozszerzenia kodu programu Visual Studio programu Azure Blockchain Development Kit. Po połączeniu z konsorcjum można kompilować, tworzyć i wdrażać inteligentne kontrakty dla członka konsorcjum usługi Azure Blockchain Service.

Aby opracować zaawansowane rozwiązania blockchain dla przedsiębiorstw, potrzebne są ramy rozwoju, aby połączyć się z różnymi sieciami blockchain i zarządzać inteligentnymi cyklami życia umów. Większość projektów współdziała z co najmniej dwoma węzłami łańcucha bloków. Deweloperzy używają lokalnego łańcucha bloków podczas tworzenia. Gdy aplikacja jest gotowa do testowania lub wydania, deweloper wdraża do sieci blockchain. Na przykład głównej publicznej sieci Ethereum lub usługi Azure Blockchain. Zestaw Azure Blockchain Development Kit for Ethereum w programie Visual Studio Code używa trufli. Trufla to popularna struktura rozwoju łańcucha bloków do pisania, kompilowania, wdrażania i testowania zdecentralizowanych aplikacji w łańcuchach bloków Ethereum. Truffle można również myśleć jako o platformie, która próbuje bezproblemowo zintegrować inteligentne tworzenie kontraktów i tworzenie tradycyjnych stron internetowych.

Aby uzyskać więcej informacji, zobacz [Szybki start: Łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą kodu programu Visual Studio.](connect-vscode.md)

### <a name="metamask"></a>MetaMask (MetaMask)

MetaMask to portfel oparty na przeglądarce (klient zdalny), klient RPC i podstawowy eksplorator kontraktów. W przeciwieństwie do innych portfeli przeglądarki, MetaMask wstrzykuje web3 instancji w kontekście javascript przeglądarki, działając jako klient RPC, który łączy się z różnymi łańcuchami bloków Ethereum *(mainnet*, *Ropsten testnet*, *Testnet Kovan*, lokalny węzeł RPC, itp.). Możesz łatwo skonfigurować niestandardowe RPC, aby połączyć się z usługą Azure Blockchain Service i rozpocząć tworzenie łańcucha bloków za pomocą Remix.

Aby uzyskać więcej informacji, zobacz [Szybki start: Łączenie i wdrażanie inteligentnego kontraktu za pomocą metamasku](connect-metamask.md)

### <a name="geth"></a>Geth ( Geth )

Geth to interfejs wiersza polecenia do uruchamiania pełnego węzła Ethereum zaimplementowanego w Go. Nie trzeba uruchamiać pełnego węzła, ale można uruchomić jego interaktywną konsolę, która zapewnia środowisko wykonawcze JavaScript narażając interfejs API JavaScript do interakcji z usługą Azure Blockchain Service.

Aby uzyskać więcej informacji, zobacz [Szybki start: Dołącz do węzła transakcji usługi Azure Blockchain.](connect-geth.md)

## <a name="ethereum-quorum-private-transactions"></a>Transakcje prywatne ethereum Kworum

Kworum jest protoko-dystrybucyjnym protokołem księgi opartej na Ethereum z transakcją oraz prywatnością umowy i nowymi mechanizmami konsensusu. Najważniejsze ulepszenia w go-ethereum obejmują:

* **Prywatność** — kworum obsługuje transakcje prywatne i kontrakty prywatne za pośrednictwem separacji stanu publicznego i prywatnego oraz wykorzystuje szyfrowaną wymianę wiadomości typu peer-to-peer w celu ukierunkowanego przesyłania danych prywatnych do uczestników sieci.
* **Alternatywne mechanizmy konsensusu** - dowód pracy lub konsensus proof-of-stake nie jest potrzebny dla sieci zezwoloneją. Kworum oferuje wiele mechanizmów konsensusu, które są przeznaczone dla łańcuchów konsorcjum, takich jak RAFT i IBFT.Usługa Azure Blockchain service korzysta z mechanizmu konsensusu IBFT.
* **Uprawnienia dla elementów równorzędnych** — uprawnienia węzłów i elementów równorzędnych przy użyciu inteligentnych kontraktów zapewniają, że tylko znane strony mogą dołączyć do sieci.
* **Wyższa wydajność** — kworum oferuje wyższą wydajność niż publiczny Geth.

## <a name="block-explorers"></a>Eksploratorzy bloków

Eksploratorzy bloków to przeglądarki łańcucha bloków online, które wyświetlają pojedynczą zawartość bloku, dane adresów transakcji i historię. Podstawowe informacje o blokach są dostępne za pośrednictwem usługi Azure Monitor w usłudze Azure Blockchain Service. Jednak jeśli potrzebujesz więcej informacji szczegółowych podczas tworzenia, eksploratorów bloków może być przydatne.  Następujące eksploratory bloków współpracują z usługą Azure Blockchain Service:

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) z Web3 Labs
* [BlockScout (BlokScout)](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Możesz również utworzyć własny eksplorator bloków przy użyciu Menedżera danych Blockchain i usługi Azure Cosmos DB, zobacz [Samouczek: Wysyłanie danych do usługi Azure Cosmos DB za pomocą menedżera danych blockchain.](data-manager-cosmosdb.md)

## <a name="tps-measurement"></a>Pomiar TPS

Ponieważ blockchain jest używany w większej liczbie scenariuszy korporacyjnych, szybkość transakcji na sekundę (TPS) jest ważna, aby uniknąć wąskich gardeł i nieefektywności systemu. Wysokie stopy transakcyjne mogą być trudne do utrzymania w zdecentralizowanym łańcuchu bloków. Dokładny pomiar TPS może mieć wpływ na różne czynniki, takie jak wątek serwera, rozmiar kolejki transakcji, opóźnienie sieci i zabezpieczenia. Jeśli chcesz zmierzyć prędkość TPS podczas rozwoju, popularnym narzędziem open-source jest [ChainHammer.](https://github.com/drandreaskrueger/chainhammer)

## <a name="next-steps"></a>Następne kroki

Wypróbuj szybki start przy użyciu zestawu Azure Blockchain Development Kit for Ethereum w celu dołączenia do konsorcjum w usłudze Azure Blockchain.

> [!div class="nextstepaction"]
> [Łączenie się z usługą Azure Blockchain Service za pomocą kodu programu Visual Studio](connect-vscode.md)