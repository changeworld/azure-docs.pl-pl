---
title: Obsługiwane wersje, poprawki i uaktualnienia księgi usługi Azure łańcucha bloków
description: Przegląd obsługiwanych wersji księgi w usłudze Azure łańcucha bloków Service, w tym zasady dotyczące poprawek systemów i uaktualnień zarządzanych przez użytkowników oraz zarządzanych przez użytkownika.
services: azure-blockchain
keywords: łańcucha bloków
author: PatAltimore
ms.author: patricka
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 07ca22ee84dc7579d59d95b1c303476f44f8f043
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286528"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Obsługiwane wersje finansów usługi Azure łańcucha bloków Service

Usługa Azure łańcucha bloków korzysta z opartej na Ethereum księgi [kworum](https://www.goquorum.com/developers) przeznaczonej do przetwarzania transakcji prywatnych w grupie znanych uczestników, identyfikowanej jako konsorcjum w usłudze Azure łańcucha bloków.

Obecnie usługa Azure łańcucha bloków obsługuje [kworum w wersji 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) i [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami

Przechowywanie wersji w kworum odbywa się poprzez wersje główne, pomocnicze i poprawki. Na przykład jeśli wersja kworum to 2.0.1, typ wydania zostanie skategoryzowany w następujący sposób:

|Główna | Średni  | Wysłana  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Usługa Azure łańcucha bloków automatycznie aktualizuje wersje poprawki kworum do istniejących uruchomionych członków w ciągu 30 dni od udostępnienia kworum.

## <a name="availability-of-new-ledger-versions"></a>Dostępność nowych wersji księgi

Usługa Azure łańcucha bloków oferuje najnowsze i pomocnicze wersje księgi kworum w ciągu 60 dni od producenta kworum. Dla konsorcjów, które mogą wybierać z usług w przypadku aprowizacji nowych członków i konsorcjum, udostępniane są maksymalnie cztery pomocnicze wersje. Uaktualnianie z programu do wersji głównej lub pomocniczej nie jest obecnie obsługiwane. Na przykład jeśli korzystasz z wersji 2. x, uaktualnienie do wersji 3. x nie jest obecnie obsługiwane. Podobnie, jeśli korzystasz z wersji 2,2, uaktualnienie do wersji 2,3 nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

[Limity w usłudze Azure łańcucha bloków Service](limits.md)
