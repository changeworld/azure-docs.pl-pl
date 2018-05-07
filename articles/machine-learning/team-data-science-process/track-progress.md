---
title: Wykonanie danych nauki projektów — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak naukowca danych można śledzić postęp projektu analizy danych.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: ae0a32e15d86e8c2a9f8359a8caf1d205bd019f3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="track-progress-of-data-science-projects"></a>Śledź postęp projektów analizy danych

Dane nauki grupy Menedżerowie, zespołu potencjalnych klientów i projektu potencjalnych klientów należy śledzić postęp swoje projekty zespołowe, jakie pracy zostało wykonane na nich i przez kogo i pozostanie na liście zadań do wykonania. 

## <a name="vsts-dashboards"></a>Pulpity nawigacyjne programu VSTS
Jeśli używasz programu Visual Studio Team Services (VSTS), jest możliwość tworzenia pulpitów nawigacyjnych do śledzenia działań i elementów roboczych skojarzonych z danym projektem Zwinnego. 

Aby uzyskać więcej informacji na temat sposobu tworzenia i dostosowywania pulpitów nawigacyjnych i widżetów na Visual Studio Team Services zobacz następujące zestawy instrukcji:

- [Dodawanie i zarządzanie pulpity nawigacyjne](https://docs.microsoft.com/vsts/report/dashboards/dashboards)
- [Dodawać widżety do pulpitu nawigacyjnego](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Przykład pulpitu nawigacyjnego

Poniżej przedstawiono prosty przykład pulpit nawigacyjny, który jest oparty na śledzenie działania przebiegu projektu nauki Agile danych, a także liczbie zatwierdzeń skojarzonych repozytoriów. **Góry z lewej** panelu programy:

- odliczania w dół bieżącego przebiegu 
- Liczba zatwierdzeń dla każdego repozytorium w ciągu ostatnich 7 dni
- element roboczy dla określonych użytkowników. 

Pozostałe panele Pokaż diagram przepływu zbiorczej (CFD), raport dotyczący wypalenia i burnup dla projektu:

- **Lewym dolnym rogu okna**: CFD ilość pracy w danym stanie, przedstawiający zatwierdzone w kolorze szarym zostało zatwierdzone na niebiesko i wykonywane na zielono.
- **Z góry po prawej**: postęp wykresu pracy pozostało do kompletne i pozostały czas).
- **Prawy dolny róg**: burnup wykresu pracy, który został wykonany w porównaniu z łączną ilość pracy.

![pulpit nawigacyjny](./media/track-progress/dashboard.png)

Opis sposobu tworzenia tych wykresy, zobacz poradniki Szybki Start i samouczki na [pulpity nawigacyjne](https://docs.microsoft.com/vsts/report/dashboards/).
 
## <a name="next-steps"></a>Kolejne kroki

Wskazówki, które pokazują wszystkie kroki procesu **określonych scenariuszy** podawane są również. Wymieniono i połączone z opisami miniatur w [wskazówki przykład](walkthroughs.md) artykułu. Pokazują one sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 
