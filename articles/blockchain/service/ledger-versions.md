---
title: Usługa Azure Blockchain obsługiwane księgi wersji, poprawki i uaktualnienia
description: Omówienie wersje rejestrów obsługiwanych w usłudze Azure Service łańcucha bloków, w tym zasady dotyczące systemów poprawianie i zarządzane przez system i uaktualnień zarządzana przez użytkownika.
services: azure-blockchain
keywords: łańcuch bloków
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399105"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Obsługiwane wersje rejestr Azure Blockchain Service

Usługa Azure Blockchain używa, Ethereum na podstawie [kworum](https://www.goquorum.com/developers) księgowe zaprojektowane w celu przetwarzania transakcji prywatne w obrębie grupy znanych uczestników, zidentyfikowany jako konsorcjum w usłudze Azure Service łańcucha bloków.

Obecnie usługa Azure Blockchain Service obsługuje [kworum wersję 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) i [Menedżera transakcji Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień

Przechowywanie wersji w kworum odbywa się za pośrednictwem głównej ani pomocniczej i poprawki wersji. Na przykład jeśli wersja kworum jest 2.0.1, typ wersji może zostać podzielone na następujące:

|Główne | Pomocnicza  | Poprawki  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Usługi platformy Azure Blockchain automatycznie aktualizuje poprawkę wersjach kworum do istniejących członków uruchomionych w ciągu 30 dni udostępniane z kworum.

## <a name="availability-of-new-ledger-versions"></a>Dostępność nowych wersji księgi

Usługa Azure Blockchain zawiera najnowszą wersję główną i pomocniczą Finanse kworum w ciągu 60 dni są dostępne od producenta kworum. Maksymalnie cztery pomocnicze wersje są dostarczane dla celu wybierać podczas aprowizowania nowego elementu członkowskiego i konsorcjum. Uaktualnianie z do głównych ani pomocniczych wersji nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

[Limity usługi Azure Blockchain](limits.md)
