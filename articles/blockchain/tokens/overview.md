---
title: Co to są tokeny łańcucha bloków platformy Azure
description: Tokeny łańcucha bloków Azure to platforma jako usługa (PaaS) do wydawania tokenów i zarządzania nimi.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240282"
---
# <a name="what-is-azure-blockchain-tokens"></a>Co to są tokeny usługi Azure Blockchain?

[!INCLUDE [Preview note](./includes/preview.md)]

Tokeny łańcucha bloków Azure to platforma jako usługa (PaaS) do standardowej emisji tokenów i zarządzania nimi w księgach łańcucha bloków na platformie Azure.

Za pomocą tokenów blockchain platformy Azure można tworzyć standardowe tokeny dla rozwiązania blockchain przy użyciu wstępnie utworzonego szablonu tokenu. Można również skomponować własny szablon tokenu za pomocą usługi. Po utworzeniu użyj tokenów łańcucha bloków azure, aby połączyć się i wydać tokeny w łańcuchu bloków. Po wydaniu możesz zarządzać tokenami w wielu sieciach blockchain.

## <a name="templates"></a>Szablony

Użyj tokenów łańcucha bloków azure, aby wybrać wstępnie utworzony szablon tokenu lub utworzyć własny szablon tokenu. Tokeny łańcucha bloków Azure obsługuje możliwości tworzenia szablonu tokenu, który umożliwia tworzenie własnego szablonu tokenu na podstawie obsługiwanych zachowań. Szablony tokenów mogą być używane w większości rozwiązań blockchain, ponieważ mapują na najczęściej używane tokeny. Możesz zacząć od szablonu, spersonalizować go i wdrożyć tokeny dla rozwiązania.

Aby uzyskać więcej informacji na temat szablonów tokenów łańcucha bloków platformy Azure, zobacz [szablony tokenów łańcucha bloków platformy Azure.](templates.md)

## <a name="management"></a>Zarządzanie

Tokeny łańcucha bloków Azure udostępnia usługi Azure portal zarządzania i interfejsów API, aby połączyć się z istniejącą siecią łańcucha bloków. Obecnie możesz połączyć się z [usługą Azure Blockchain Service](../service/overview.md) lub innym łańcuchem bloków z rodziny Ethereum.

Po połączeniu z jedną lub wieloma sieciami łańcucha bloków można używać interfejsów API tokenów łańcucha bloków platformy Azure do wystawiania tokenów i zarządzania nimi do użycia w rozwiązaniu blockchain. Za pomocą interfejsów API można zintegrować zarządzanie tokenami w aplikacjach biznesowych i logiki. Na przykład można użyć interfejsu API REST do zarządzania tokenami zamiast zarządzania tokenami bezpośrednio w łańcuchu bloków.

## <a name="blockchains-and-accounts"></a>Łańcuchy bloków i konta

Tokeny łańcucha bloków Azure udostępnia usługi Azure portal zarządzania i interfejsów API do tworzenia nowych grup i nowych kont blockchain w połączonych sieciach blockchain. Możesz tworzyć nowe konta bezpośrednio w połączonych sieciach, a tokeny łańcucha bloków platformy Azure zarządza kluczami prywatnymi konta w Twoim imieniu. Korzystając z grup, możesz grupować różne konta łańcucha bloków z wielu sieci i zarządzać kontrolą dostępu za pośrednictwem grup.

Aby uzyskać więcej informacji na temat zarządzania kontem tokenów łańcucha bloków platformy Azure, zobacz [Zarządzanie kontem tokenów łańcucha bloków platformy Azure.](account-management.md)

## <a name="token-taxonomy-framework"></a>Framework taksonomii tokenu

Tokeny łańcucha bloków azure jest zbudowany na podstawie standardów fundacji o nazwie Token Taxonomy Framework (TTF). TTF to zestaw edables utworzonych z [tokenu Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI) grupy roboczej tokenu. Grupa robocza TTI definiuje taksonomię biznesową dla tokenów i ich zachowań, które mogą być stosowane we wszystkich głównych księgach, w tym Ethereum, Kworum, Corda i Hyperledger Fabric. Celem grupy roboczej jest stworzenie struktury, która standaryzuje użycie tokenów z punktu widzenia biznesowego w celu uproszczenia i demokratyzacji rozwoju opartego na tokenach. Pozwalając branży zdefiniować te tokeny i ich zachowanie na poziomie biznesowym, szczegółowe implementacji tokenów są pobierane z dala od logiki biznesowej, która manipuluje tokenów.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Aby uzyskać informacje o technologii Azure Blockchain, odwiedź [blog platformy Azure Blockchain,](https://azure.microsoft.com/blog/topics/blockchain/) aby być na bieżąco z ofertami usług blockchain i informacjami od zespołu inżynierów platformy Azure Blockchain.

Aby uzyskać dostęp do wersji zapoznawczej tokenów łańcucha bloków platformy [Azure, skontaktuj się z zespołem tokenów łańcucha bloków azure.](https://aka.ms/PreviewForm)

Aby przekazać opinię o produkcie lub poprosić o nowe funkcje, po głosowaniu na pomysł za pośrednictwem [forum opinii platformy Azure dla łańcucha bloków.](https://aka.ms/blockchainuservoice)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępnych [szablonach tokenów blockchain platformy Azure.](templates.md)
