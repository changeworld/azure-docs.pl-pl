---
title: Omówienie programowania w usłudze Azure Service łańcucha bloków
description: Wprowadzenie na temat projektowania rozwiązań w usłudze Azure Service łańcucha bloków.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027903"
---
# <a name="azure-blockchain-service-development-overview"></a>Omówienie programowania w usłudze Azure Service łańcucha bloków

Za pomocą usługi Azure Service łańcucha bloków można utworzyć konsorcjum sieci łańcucha bloków w celu obsługi takich scenariuszy przedsiębiorstwa, jak śledzenie zasobów, cyfrowe tokenów, lojalność i nagradzania, łańcuch dostaw finansowych i pochodzenia. Ten artykuł stanowi wprowadzenie do omówienie programowania w usłudze Azure Service łańcucha bloków i kluczowe tematy, aby zaimplementować łańcucha bloków w przedsiębiorstwie.

## <a name="client-connection-to-azure-blockchain-service"></a>Połączenie klienta do usługi Azure Service łańcucha bloków

Istnieją różne typy klientów sieci łańcucha bloków, łącznie z pełną węzłów, jasny węzłów i klientów zdalnych. Usługa Azure Blockchain tworzy sieć łańcucha bloków, która zawiera węzły. Różni klienci jako brama Azure Blockchain Service służy do tworzenia aplikacji łańcucha bloków. Usługa Azure Blockchain oferuje uwierzytelnianie podstawowe lub klucz dostępu jako punktu końcowego rozwoju. Poniżej przedstawiono popularne klientów, można użyć połączenia.

### <a name="metamask"></a>MetaMask

MetaMask jest oparte na przeglądarce portfela (zdalnego klienta), klient i explorer podstawowego kontraktu. W przeciwieństwie do innych portfele przeglądarki MetaMask wprowadza wystąpienia sieci Web 3 w kontekście JavaScript w przeglądarce, działając jako klient RPC, który nawiązuje połączenie z szeroką gamą łańcuchy Ethereum (*mainnet*, *Ropsten testnet*, *Kovan testnet*, lokalnego węzła usługi RPC, itp.). Można skonfigurować niestandardowe RPC, łatwo można połączyć się z usługą łańcucha bloków platformy Azure i uruchomić przy użyciu Remix do opracowywania łańcuchów bloków.

### <a name="geth"></a>Geth

Geth jest interfejs wiersza polecenia do uruchamiania pełnej węzła Ethereum zaimplementowane w rzeczywistym. Nie trzeba uruchomić pełny węzła, ale można uruchomić jej interakcyjną konsolę, która zapewnia środowisko uruchomieniowe JavaScript udostępnianie interfejsu API języka JavaScript do interakcji z usługą Azure Service łańcucha bloków.

## <a name="development-framework-configuration"></a>Konfiguracja platformy programowania

Do opracowywania rozwiązań łańcucha bloków zaawansowanych przedsiębiorstwa, architektura deweloperska jest potrzebne do łączenia się z sieciami różnych łańcucha bloków, Zarządzanie cyklem życia inteligentne kontraktu, zautomatyzować testowanie, wdrażanie inteligentne kontraktu za pomocą skryptów i pożyczkowe konsoli interaktywnej.

Truffle to architektura deweloperska popularnych łańcuch bloków do zapisu, kompilowania, wdrażania i testowania aplikacji zdecentralizowane w łańcuchy Ethereum. Ponadto można traktować Truffle jako struktura, która podejmuje próbę bezproblemowo zintegrować inteligentne kontraktu projektowania i tworzenia tradycyjnych sieci web.

Nawet najmniejszy projektu wchodzi w interakcję z co najmniej dwa węzły łańcucha bloków: Jeden na komputerze dewelopera i inne reprezentujących sieci, w których Deweloper służy do wdrażania aplikacji. Na przykład siecią Ethereum publiczne główną lub Azure Service łańcucha bloków. Truffle zapewnia system zarządzania artefaktów kompilacji i wdrażania dla każdej sieci i w sposób, który upraszcza wdrażanie gotowych aplikacji. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Użyj Truffle, aby nawiązać połączenie z siecią Azure Blockchain Service](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Prywatne transakcji Ethereum kworum

Kworum to protokół oparte na Ethereum rejestru rozproszonego przy użyciu transakcji oraz umowy, prywatność i nowych mechanizmów consensus. Kluczowe ulepszenia za pośrednictwem Go Ethereum obejmują:

* Prywatność — kworum obsługuje transakcje prywatne i umowy prywatne za pomocą separacji stanu publicznych i prywatnych i korzysta z zaszyfrowanego komunikatu peer-to-peer wymiany bezpośrednie przesyłanie danych prywatnych do uczestników sieci.
* Alternatywne mechanizmy Consensus — bez potrzeby dla consensus dowód pracy lub dowodu z grę w otrzymało uprawnienia sieci. Kworum oferuje wiele consensus mechanizmów, które są przeznaczone dla łańcuchów konsorcjum, takie jak TRATWIE i IBFT.  Usługi platformy Azure Blockchain używa mechanizmu consensus IBFT.

* Komunikacja równorzędna udzielania do nich uprawnień — węzeł i węzłem równorzędnym udzielania do nich uprawnień za pomocą kontraktów inteligentnych, zapewniając wyłącznie znane strony może dołączyć do sieci
* Wydajność — kworum oferuje wyższą wydajność niż Geth publiczne

Zobacz [samouczka: Wyślij transakcji za pomocą usługi Azure Service łańcucha bloków](send-transaction.md) przykład prywatnej transakcji.

## <a name="block-explorers"></a>Eksploratory bloku

Eksploratory bloku są przeglądarki łańcucha bloków w trybie online, które wyświetlić zawartość poszczególnych bloku, dane adresu transakcji i historii. Blok podstawowe informacje są dostępne za pośrednictwem usługi Azure Monitor w usłudze Azure Service łańcucha bloków, jednak jeśli potrzebujesz bardziej szczegółowe informacje w czasie projektowania, eksploratorów bloku może być przydatne.  Brak eksploratorów popularnych bloku typu open source, których można użyć. Oto lista eksploratorów bloku, które działają z usługą Azure Blockchain Service:

* [Eksplorator usługi Azure Blockchain](https://web3labs.com/azure-offer) z laboratoriów w sieci Web 3
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Pomiar TPS

Jak łańcuch bloków jest używana w dalszych scenariuszach przedsiębiorstwa, transakcji na drugim szybkość (TPS) ważne jest, aby uniknąć wąskich gardeł i nieefektywności systemu. Stawki za dużo transakcji może być trudne w utrzymaniu w ramach zdecentralizowane łańcucha bloków. Dokładny pomiar TPS może mieć wpływ różne czynniki, takie jak wątek serwera, rozmiar kolejki transakcji, opóźnienie sieci i zabezpieczeń. Jeśli potrzebujesz zmierzyć szybkość TPS podczas tworzenia, popularnych narzędzi typu open source jest [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Kolejne kroki

[Szybki start: Użyj Truffle, aby nawiązać połączenie z siecią Azure Blockchain Service](connect-truffle.md)
