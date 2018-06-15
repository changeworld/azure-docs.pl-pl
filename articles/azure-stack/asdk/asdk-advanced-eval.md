---
title: Zaawansowane zadania dotyczące oceny usługi Azure stosu | Dokumentacja firmy Microsoft
description: W tym artykule opisano zaawansowane zadania dotyczące oceny usługi Azure stosu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975825"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Zaawansowane zadania dotyczące oceny usługi Azure stosu Development Kit
Po uzyskały znajomość podstawowych funkcji usługi Azure stosu Development Kit (ASDK) i możliwości, możesz pogłębić zrozumienie dalsze stosu Azure przez testowania bardziej zaawansowanych scenariuszy. Te zadania dotyczące oceny bardziej zaawansowanych pełni opisano w dokumentacji Operator stosu Azure.

> [!NOTE]
> Wiele zadań operator są obsługiwane dla ASDK i środowiska produkcyjnego, wdrożeniach z wieloma węzłami stosu Azure, nie wszystkie scenariusze użycia są obsługiwane dla wdrożeń ASDK. Zobacz [ASDK i różnice stosu Azure wielowęzłowego](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) Aby uzyskać więcej informacji.

## <a name="delegate-offers-in-azure-stack"></a>Delegowanie ofert w usłudze Azure Stack
Jako Operator stosu Azure często chcesz umieścić innym osobom odpowiedzialnym za tworzenie oferty i rejestracji użytkowników. Na przykład jeśli jesteś dostawcą usług można odsprzedawców Rejestracja odbiorców i zarządzać nimi w Twoim imieniu. Jeśli część centralna grupa IT w przedsiębiorstwie, można też zależnych, aby zarejestrować się użytkowników bez interwencji użytkownika.

[Delegowanie oferty Azure stosu](.\.\azure-stack-delegated-provider.md) ułatwia wykonanie tych zadań, co można osiągnąć i zarządzać nimi więcej użytkowników niż jest to możliwe bezpośrednio. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Udostępnić użytkownikom Azure stosu bazy danych SQL
Jako Operator stosu Azure można utworzyć oferty, które pozwalają użytkownikom (dzierżawcami) Utwórz bazy danych SQL, które mogą używać z ich chmury natywnych aplikacji, witryn sieci Web i obciążeń. Zapewniając użytkownikom tych baz danych niestandardowych, na żądanie, oparte na chmurze, można je zapisać czasu i zasobów. 

Użyj karty dostawcy zasobów programu SQL Server do [udostępnić użytkownikom Azure stosu baz danych SQL](.\.\azure-stack-tutorial-sql-server.md) jako usługa Azure stosu. Po zainstalowaniu dostawcy zasobów podłącz je do co najmniej jedno wystąpienie programu SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Udostępnij aplikacji interfejsu API sieci web i użytkownicy Azure stosu
Jako Operator stosu Azure można utworzyć oferty, które pozwalają użytkownikom (dzierżawcami), tworzenie aplikacji usługi Azure Functions i sieci web i interfejsu API. Zapewniając użytkownikom dostęp do tych aplikacji na żądanie, oparte na chmurze, można je zapisać czasu i zasobów.

Dostawcy zasobów usługi aplikacji, aby wdrożyć [udostępnić aplikacji interfejsu API sieci web i użytkownicy Azure stosu](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o oferty usług w usłudze Azure stosu zintegrowanych systemów](.\.\azure-stack-offer-services-overview.md)