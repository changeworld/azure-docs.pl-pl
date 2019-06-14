---
title: Śledzenie postępu projekty do nauki o danych — danych zespołu dla celów naukowych
description: Jak menedżerowie grupy do nauki o danych, zespół dzierżawy i projektu potencjalnych klientów można śledzić postęp projektu nauki o danych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60252569"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Śledzenie postępu projekty do nauki o danych

Menedżerowie grupy do nauki o danych, zespołów i potencjalnych klientów projektu musisz śledzić postęp projektów, pracy, jaka została przeprowadzona na nich i przez kogo i pozostaje na listy zadań do wykonania. 

## <a name="azure-devops-dashboards"></a>Pulpitów nawigacyjnych DevOps platformy Azure
Jeśli używasz DevOps platformy Azure jest możliwe do tworzenia pulpitów nawigacyjnych, aby śledzić działania i elementy robocze skojarzone z danym projektem Agile. 

Aby uzyskać więcej informacji na temat sposobu tworzenia i dostosowywania pulpitów nawigacyjnych i widżetów na DevOps platformy Azure zobacz następujące zestawy instrukcji:

- [Dodaj i Zarządzaj pulpitów nawigacyjnych](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Dodawanie widgetów do pulpitu nawigacyjnego](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Przykładowy pulpit nawigacyjny

Poniżej przedstawiono prosty przykład pulpitu nawigacyjnego, przeznaczonego do śledzenia działania sprintu projektu nauki o danych Agile, a także liczba zatwierdzeń repozytoriów skojarzone. **Góry z lewej** panelu pokazuje:

- Odliczanie do zera dla bieżącego sprintu 
- Liczba zatwierdzeń dla każdego repozytorium w ciągu ostatnich 7 dni
- element roboczy pod kątem określonych użytkowników. 

Pozostałe panele Pokaż zbiorczego diagramu przepływu (CFD), raport dotyczący wypalenia i burnup dla projektu:

- **Lewym dolnym**:  CFD ilość pracy w danym stanie pokazujący zatwierdzonych w kolorze szarym zatwierdzone w kolorze niebieskim i wykonywane w kolorze zielonym.
- **Górnego prawego**: raport dotyczący wypalenia wykresu pracy od lewej do kompletne i pozostały czas).
- **Na dole z prawej**: burnup wykresu pracy, który został wykonany w porównaniu z łączną ilość pracy.

![pulpit nawigacyjny](./media/track-progress/dashboard.png)

Opis sposobu tworzenia tych wykresach można zobaczyć przewodników Szybki Start i samouczków w [pulpity nawigacyjne](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Kolejne kroki

Wskazówki, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy** znajdują się także. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) artykułu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 
