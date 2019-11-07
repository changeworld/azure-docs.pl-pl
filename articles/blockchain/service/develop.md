---
title: Omówienie tworzenia usług łańcucha bloków platformy Azure
description: Wprowadzenie do tworzenia rozwiązań w usłudze Azure łańcucha bloków Service.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a9444847fb75bdf01cabba98057605afbe03c9fc
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577445"
---
# <a name="azure-blockchain-service-development-overview"></a>Omówienie tworzenia usług łańcucha bloków platformy Azure

Za pomocą usługi Azure łańcucha bloków można tworzyć sieci łańcucha bloków konsorcjum, aby umożliwić korzystanie z scenariuszy korporacyjnych, takich jak śledzenie zasobów, token cyfrowy, lojalność, wynagrodzenie i pochodzenie łańcucha dostaw. Ten artykuł zawiera wprowadzenie do programowania usługi Azure łańcucha bloków i kluczowych tematów dotyczących wdrażania łańcucha bloków dla przedsiębiorstw.

## <a name="client-connection-to-azure-blockchain-service"></a>Połączenie klienckie z usługą Azure łańcucha bloków

Istnieją różne typy klientów dla sieci łańcucha bloków, w tym pełne węzły, węzły lekkie i klienci zdalni. Usługa Azure łańcucha bloków tworzy sieć łańcucha bloków, która zawiera węzły. Możesz użyć różnych klientów jako bramy do usługi Azure łańcucha bloków Service do tworzenia łańcucha bloków. Usługa Azure łańcucha bloków oferuje podstawowe uwierzytelnianie lub klucz dostępu jako punkt końcowy projektowania. Poniżej znajdują się popularne Klienci, których można użyć do nawiązania połączenia.

### <a name="metamask"></a>Maska

Funkcja maskowania to oparty na przeglądarce portfel (Klient zdalny), klient RPC i podstawowy Eksplorator umów. W przeciwieństwie do innych portfeli przeglądarki, usuwanie wystąpienia web3 jest wprowadzane do kontekstu JavaScript przeglądarki, działającego jako klient RPC, który łączy się z wieloma Ethereum blockchains (*mainnet*, *Ropsten testnet*, *KOVAN testnet*, lokalny węzeł RPC, itp.). Możesz łatwo skonfigurować niestandardowe zdalne wywoływanie procedur, aby nawiązać połączenie z usługą Azure łańcucha bloków i rozpocząć programowanie łańcucha bloków przy użyciu Remix.

### <a name="geth"></a>Geth

Geth jest interfejs wiersza polecenia do uruchamiania pełnego węzła Ethereum zaimplementowanego w programie go. Nie musisz uruchamiać pełnego węzła, ale można uruchomić jego interaktywną konsolę, która zapewnia środowisko uruchomieniowe języka JavaScript, które udostępnia interfejs API języka JavaScript do interakcji z usługą Azure łańcucha bloków.

## <a name="development-framework-configuration"></a>Konfiguracja struktury programistycznej

Aby opracowywać zaawansowane rozwiązania dla przedsiębiorstw łańcucha bloków, platforma programistyczna jest wymagana do łączenia się z różnymi sieciami łańcucha bloków, zarządzania cyklem życia kontraktu inteligentnego, automatyzowania testowania, wdrażania inteligentnego kontraktu ze skryptami i wyposażania interaktywnej konsoli.

Truffle to popularna platforma programistyczna łańcucha bloków do pisania, kompilowania, wdrażania i testowania zdecentralizowanych aplikacji na Ethereum blockchains. Możesz również traktować Truffle jako platformę, która próbuje bezproblemowo zintegrować inteligentne projektowanie kontraktu i tradycyjne programowanie w sieci Web.

Nawet najmniejszy projekt współdziała z co najmniej dwoma węzłami łańcucha bloków: jeden na komputerze dewelopera, a drugi reprezentuje sieć, w której deweloper wdraża swoją aplikację. Na przykład główna publiczna sieć Ethereum lub usługa Azure łańcucha bloków. Truffle zapewnia system do zarządzania artefaktami kompilacji i wdrażania dla każdej sieci i robi to w sposób, który upraszcza wdrażanie aplikacji końcowej. Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z Truffle w celu nawiązania połączenia z siecią usługi Azure łańcucha bloków](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Prywatna transakcja kworum Ethereum

Kworum to oparty na systemie Ethereum protokół księgi rozproszonej z transakcją oraz prywatnością umowy i nowymi konsensusami. Najważniejsze ulepszenia w porównaniu do funkcji go-Ethereum obejmują:

* Prywatność — kworum obsługuje transakcje prywatne i prywatne w ramach separacji Stanów publicznych i prywatnych oraz wykorzystuje wymiany komunikatów typu peer-to-peer do bezpośredniego transferu danych prywatnych do uczestników sieci.
* Alternatywne mechanizmy z Konsensusem — bez konieczności wykonywania czynności związanych z zawodami lub potwierdzeniem zgodności w sieci z uprawnieniami. Kworum oferuje wiele mechanizmów o consensusu, które są przeznaczone dla łańcuchów konsorcjum, takich jak TRATWy i IBFT.  Usługi Azure łańcucha bloków Services wykorzystują mechanizm IBFT o konsensusie.

* Uprawnienia elementu równorzędnego — węzeł i równorzędne uprawnienia przy użyciu inteligentnych kontraktów, dzięki czemu tylko znane strony mogą dołączyć do sieci
* Wyższa wydajność — kworum zapewnia wyższą wydajność niż Geth publiczny

Zapoznaj się z [samouczkiem: Wyślij transakcję przy użyciu usługi Azure łańcucha bloków Service](send-transaction.md) , aby zapoznać się z przykładem transakcji prywatnej.

## <a name="block-explorers"></a>Eksplorator bloków

Eksploratory bloków są w trybie online łańcucha bloków przeglądarki, które wyświetlają poszczególne zawartość bloku, dane adresów transakcji i historię. Podstawowe informacje o bloku są dostępne za pomocą Azure Monitor w usłudze Azure łańcucha bloków, jednak jeśli potrzebne są bardziej szczegółowe informacje podczas opracowywania, mogą być przydatne Eksploratory blokowe.  Istnieją popularne Eksploratora bloków Open Source, których można użyć. Poniżej znajduje się lista bloków Eksploratora, które współpracują z usługą Azure łańcucha bloków:

* [Epirus Azure łańcucha bloków Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) z programu Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Pomiar TPS

Ponieważ łańcucha bloków jest używany w większej liczbie scenariuszy dla przedsiębiorstw, ważne jest, aby uniknąć wąskich gardeł i niewydajności systemu. Wysokie stawki transakcji mogą być trudne do utrzymania w zdecentralizowanej łańcucha bloków. Do dokładnej miary TPS mogą mieć wpływ różne czynniki, takie jak wątek serwera, rozmiar kolejki transakcji, opóźnienie sieci i zabezpieczenia. Jeśli potrzebujesz mierzyć szybkość TPS podczas opracowywania, popularne narzędzie typu Open Source to [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Następne kroki

[Szybki Start: używanie Truffle do nawiązywania połączenia z siecią usługi Azure łańcucha bloków Service](connect-truffle.md)
