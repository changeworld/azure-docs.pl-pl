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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027888"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Obsługiwane wersje rejestr Azure Blockchain Service

Usługa Azure Blockchain używa, Ethereum na podstawie [kworum](https://github.com/jpmorganchase/quorum/wiki) księgowe zaprojektowane w celu przetwarzania transakcji prywatne w obrębie grupy znanych uczestników, zidentyfikowany jako konsorcjum w usłudze Azure Service łańcucha bloków.

Obecnie usługa Azure Blockchain Service obsługuje [kworum wersję 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) i [Menedżera transakcji Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień

Przechowywanie wersji w kworum odbywa się za pośrednictwem głównej wersji, wersji głównych punktów i wersja pomocnicza punktu. Na przykład kworum 2.0.1, wersja 2 w przypadku wersji głównej, 0 to wersji głównych punktów, a 1 jest wersją pomocniczą punktu. Usługa automatycznie poprawki drobnych punktowe księgi. Obecnie uaktualnianie głównych i główne punktowe są nieobsługiwane.

## <a name="availability-of-new-ledger-versions"></a>Dostępność nowych wersji księgi

Usługa Azure Blockchain zawiera najnowsze wersje księgi w ciągu 60 dni są dostępne od producenta rejestr. Maksymalnie cztery główne punktowe są oferowane w celu wyboru podczas inicjowania obsługi nowego elementu członkowskiego i konsorcjum.

## <a name="next-steps"></a>Kolejne kroki

[Limity usługi Azure Blockchain](limits.md)
